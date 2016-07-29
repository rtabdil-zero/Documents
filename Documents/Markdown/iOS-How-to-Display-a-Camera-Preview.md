# How to Display a Camera Preview

The GoPro WSDK allows you to get a preview of what the camera is viewing by setting the 
**videoView** property of a **GPCamera**'s **GPCameraPlayer** intance. You can control 
the stream status with the **GPCameraPlayer**'s **start** and **stop** methods.
When in streaming mode, the view will show the camera preview.

## Displaying the Preview

Follow these steps to display a camera preview:

1. Get a **GPCameraPlayer** instance via the **player** property of a **GPCamera** instance.
2. Set the **videoView** property of a **GPCameraPlayer** instance to a **UIView** that you have created.
3. Call **[GPCameraPlayer start]**

### Objective-C

```objc
GPCamera *myCamera; //defined elsewhere
UIView *myView; //defined elsewhere

myCamera.cameraPlayer.videoView = myView;
[myCamera.cameraPlayer start];

// later you can stop streaming
[myCamera.cameraPlayer stop];
```

## Streaming Notifications

The live video stream may change state because of user activity, changes to camera mode, or changes in connectivity. Your app can detect these changes to the camera's streaming state using Key-Value Observing (KVO) on the **status** property of the **GPCameraPlayer** object.

Remember when using KVO that your app must call **removeObserver:forKeyPath:** when you are done observing this player instance or the object acting as the observer goes out of scope.

The following example shows how an app might respond to video stream status changes to update its UI.

### Objective-C

```objc
- (void)viewDidLoad
{
    [super viewDidLoad];
    
    GPCamera *camera; //define elsewhere
    self.cameraPlayer = camera.cameraPlayer;

    [camera.cameraPlayer addObserver:self forKeyPath:@"state" options:0 context:nil];
}

- (void)observeValueForKeyPath:(NSString *)keyPath ofObject:(id)object change:(NSDictionary *)change context:(void *)context
{
    if (object == self.cameraPlayer && [keyPath isEqualToString:@"state"])
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
```

## Grabbing a snapshot of the preview stream

With a little additional code that is not included in the WSDK, your application can capture a snapshot of your GoPro's video preview stream. Start by copying the **GPCameraPlayerFrameCapture** class from below into your application.

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

Next, make use of the **GPCameraPlayerFrameCapture** class by following this code example:

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