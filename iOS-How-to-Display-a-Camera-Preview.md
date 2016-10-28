# How to Display a Camera Preview

The GoPro WSDK allows you to get a preview of what the camera is viewing by setting the 
`videoView` property of a `GPCamera`'s `GPCameraPlayer` instance. You can control 
the stream status with the `GPCameraPlayer`'s `start` and `stop` methods.
When in streaming mode, the view will show the camera preview.

## Displaying the Preview

Follow these steps to display a camera preview:

1. Get a `GPCameraPlayer` instance via the `player` property of a `GPCamera` instance.
2. Set the `videoView` property of a `GPCameraPlayer` instance to a `UIView` that you have created.
3. Call `[GPCameraPlayer start]`

### Objective-C

```objc
GPCamera *myCamera; //defined elsewhere
UIView *myView; //defined elsewhere

GPCameraPlayer *cameraPlayer = [myCamera player];
[cameraPlayer setVideoView:myView];
[cameraPlayer start];

// later you can stop streaming
[cameraPlayer stop];
```

## Streaming Notifications

The live video stream may change state because of user activity, changes to camera mode, or changes in connectivity. Your app can detect these changes to the camera's streaming state using Key-Value Observing (KVO) on the `status` property of the `GPCameraPlayer` object.

Remember when using KVO that your app must call `removeObserver:forKeyPath:` when you are done observing this player instance or the object acting as the observer goes out of scope.

The following example shows how an app might respond to video stream status changes to update its UI.

### Objective-C

```objc
GPCamera *camera; //defined elsewhere

- (void)viewDidLoad
{
    [super viewDidLoad];
    self.cameraPlayer = [camera player];

    [cameraPlayer addObserver:self forKeyPath:@"status" options:NSKeyValueObservingOptionNew context:(__bridge void *)(self)];
}

- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context
{
    if (context == (__bridge void *)(self))
    {
        if (object == self.cameraPlayer && [keyPath isEqualToString:@"status"])
        {
            switch (self.cameraPlayer.status)
            {
                case GPCameraPlayerStatus_Stop:
                case GPCameraPlayerStatus_Pause:
                    [self.cameraPlayer setVideoView:nil];
                    break;

                case GPCameraPlayerStatus_Streaming:
                    [self.cameraPlayer setVideoView:self.cameraPreviewCell.contentView];
                    break;
                
                case GPCameraPlayerStatus_Rendering:
                    NSLog(@"UIView should be showing preview video");
                    break;
            
                case GPCameraPlayerStatus_Failed:
                    NSLog(@"Preview Streaming failed: %@", self.cameraPlayer.error);
                    break;
            }
        }
    }
}
```

## Grabbing a snapshot of the preview stream

With a little additional code that is not included in the WSDK, your application can capture a snapshot of your GoPro's video preview stream. Start by copying the `GPCameraPlayerFrameCapture` class from below into your application. This class is demonstrated in the sample application in the `GPCameraScreenshotViewController.m` file.

`GPCameraPlayerFrameCapture.h`

```objc
#import <GoProSDK/GoProSDK.h>

/**
 * Use instances of this class to capture a snaphot of your GoPro's
 * video preview stream.
 *
 * Usage:
 *
 * GPCameraPlayer *player; //defined elsewhere
 * GPCameraPlayerFrameCapture *capture = [GPCameraPlayerFrameCapture new];
 * [player addOutput:capture];
 *
 * Then later, when you want to capture a snapshot:
 * UIImage *snapshot = [capture capture];
 */
@interface GPCameraPlayerFrameCapture : GPCameraPlayerVideoDataOutput

/**
 * Returns a UIImage snapshot of the preview stream. May return nil
 * if stream was unavailable or in the case of error.
 */
- (UIImage *)capture;

@end
```

`GPCameraPlayerFrameCapture.m`

```objc
#import "GPCameraPlayerFrameCapture.h"

@interface GPCameraPlayerFrameCapture () <GPCameraPlayerVideoDataOutputDelegate>

@end

@implementation GPCameraPlayerFrameCapture
{
    CMBufferQueueRef _queue;
    CIContext *_context;
}

- (instancetype)init
{
    self = [super init];
    if (self)
    {
        CMBufferQueueCreate(kCFAllocatorDefault, 0, CMBufferQueueGetCallbacksForUnsortedSampleBuffers(), &_queue);
        _context = [CIContext contextWithOptions:nil];
        
        self.delegate = self;
    }
    return self;
}

- (void)output:(GPCameraPlayerOutput *)output didOutputSampleBuffer:(CMSampleBufferRef)sampleBuffer
{
    // Keep the most recent sample buffer
    
    CMBufferRef oldSampleBuffer = CMBufferQueueDequeueAndRetain(_queue);
    if (oldSampleBuffer) {
        CFRelease(oldSampleBuffer);
    }
    CMBufferQueueEnqueue(_queue, sampleBuffer);
}

- (UIImage *)capture
{
    UIImage *image = nil;
    
    CMSampleBufferRef sampleBuffer = (CMSampleBufferRef)CMBufferQueueDequeueAndRetain(_queue);
    if (sampleBuffer)
    {
        CVPixelBufferRef pixelBuffer = CMSampleBufferGetImageBuffer(sampleBuffer);
        NSUInteger width = CVPixelBufferGetWidth(pixelBuffer);
        NSUInteger height = CVPixelBufferGetHeight(pixelBuffer);
        
        CIImage *coreImage = [[CIImage alloc] initWithCVPixelBuffer:pixelBuffer options:nil];
        CGImageRef imageRef = [_context createCGImage:coreImage fromRect:CGRectMake(0, 0, width, height)];
        
        image = [UIImage imageWithCGImage:imageRef];
        
        CFRelease(sampleBuffer);
        CFRelease(imageRef);
    }
    
    return image;
}

@end

```

Next, make use of the `GPCameraPlayerFrameCapture` class by following this code example:

```
#import "GPCameraPlayerFrameCapture.h"

// hold a reference to the capture object
@property (strong, nonatomic) GPCameraPlayerFrameCapture *previewCapture;
@property (strong, nonatomic) GPCameraPlayer *cameraPlayer;

- (void)setCameraPlayer:(GPCameraPlayer *)cameraPlayer
{
    if (_cameraPlayer)
    {
        [_cameraPlayer removeObserver:self forKeyPath:kGPCameraPlayerStatusKey context:(__bridge void *)(self)];
    }
    
    if (cameraPlayer)
    {
        [cameraPlayer addObserver:self forKeyPath:kGPCameraPlayerStatusKey options:NSKeyValueObservingOptionNew context:(__bridge void *)(self)];
        
        // create a new GPCameraPlayerFrameCapture instance for each GPCameraPlayer instance
        self.previewCapture = [GPCameraPlayerFrameCapture new];
        // add the capture object to the camera player's output
        [cameraPlayer addOutput:self.previewCapture];
    }
    
    _cameraPlayer = cameraPlayer;
}

- (void)takeSnapshot {
    // when your app wants a snaptshot, just call the capture method.
    UIImage *image = [self.previewCapture capture];
    
    if (image) {
        // Do something neat with the image :)
    } else {
        // A nil image may be returned. Your app should handle this case gracefully.
    }
}
```

## Accessing Video and Audio Raw Data

The WSDK provides a class called `GPCameraPlayerRawDataOutput` that allows you to access the raw video data that is produced by the camera. This feature was provided to developers in order to allow them to stream the video to external devices. Although it provides you with the data, you will have to process each individual transport stream packet yourself.

In order to use this feature, follow these steps:

1. Retrieve an instance of a `GPCameraPlayer` through a `GPCamera`'s `player` property.
2. Initialize a new `GPCameraPlayerRawDataOutput` and set its delegate to `self`.
3. Implement the `output:didOutputBuffer:length:` method and `outputDidDropData:` method to conform to the `GPCameraPlayerRawDataOutputDelegate` protocol.
4. Parse the bytes from the `output:didOutputBuffer:length:` method.

When parsing the bytes, you will have to extract the raw H.264/AAC data to get the presentation timestamp for each individual transport stream packet. If you would like to learn more about this process, this [MPEG Transport Stream](https://en.wikipedia.org/wiki/MPEG_transport_stream) Wikipedia page and [Packetized Elementary Stream](https://en.wikipedia.org/wiki/Packetized_elementary_stream) Wikipedia page will be of help.

To maintain optimal performance, the buffer provided via the `output:didOutputBuffer:length:` method directly references a pool of memory that may need to be reused. Therefore, this method must be efficient; otherwise, the player will no longer be able to copy LTP datagrams and data will be dropped.

If your application is causing data to be dropped by not returning the control to the calling function quickly because it needs to access the data in the buffer for a long period of time, consider copying the data into a new buffer and immediately return the control to the calling function so that the memory the buffer references can be reused.

In the following example, we create a new raw data output class and add that to our camera player. 

### Objective-C

```objc
GPCamera *camera;  // defined elsewhere

GPCameraPlayer *player = [camera player];
GPCameraPlayerRawDataOutput *output = [GPCameraPlayerRawDataOutput new];

if ([player canAddOutput:output]) {
    output.delegate = self;
    [player addOutput:output];
}
```

Once the output is added and the delegate is set, you will need to implement the `GPCameraPlayerRawDataOutputDelegate` methods in order to get the raw data from the camera player.

### Objective-C
```objc
- (void)output:(GPCameraPlayerOutput *)output didOutputBuffer:(const uint8_t *)bytes length:(size_t)length {
    // The output buffer is the LTP payload which contains audio/video data incapsulated
    // in MPEG transport stream (TS) packets. In order to extract audio and video clients
    // need to parse the TS packts (https://en.wikipedia.org/wiki/MPEG_transport_stream)
    
    // TS packets are always 188 bytes
    for (size_t pos = 0; pos < length; pos += 188, bytes += 188)
    {
        // check sync byte
        if (bytes[0] != 0x47)
        {
            NSLog(@"sync byte expected");
            continue;
        }
        
        // parse the 13-bit packet identifier (PID)
        uint16_t pid = ((uint16_t)(bytes[1] & 0x1F) << 8) | bytes[2];
        switch (pid) {
                
            case 4113:
                NSLog(@"TS packet contains video data");
                // Parse the TS packet to extract the raw H.264 video data
                break;
                
            case 4352:
                NSLog(@"TS packet contains audio data");
                // Parse the TS packet to extract the raw ACC audio data
                break;
                
            default:
                break;
        }
    }
}

- (void)outputDidDropData:(GPCameraPlayerOutput *)output {
    NSLog(@"Dropped: %@",output);
}
```
Because `outputDidDropData:` is called on the same thread that is responsible for outputting audio/video data, it must be efficient to prevent further performance problems, such as additional dropped data.
