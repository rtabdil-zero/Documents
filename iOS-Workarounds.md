# Workarounds

The following workarounds are available to handle WSDK limitations.

* [How to check if the device becomes too hot](#how-to-check-if-the-device-becomes-too-hot)
* [How to get faster notification of camera disconnection](#how-to-get-faster-notification-of-camera-disconnection)
* [How to handle the camera disconnecting while downloading large files](#how-to-handle-the-camera-disconnecting-while-downloading-large-files)

## How to check if the device becomes too hot
Although the WSDK does not provide a method to directly check if the system becomes too hot while operating the device in a hot environment, you can still check for the system hot status update through the `GPCameraDelegate` protocol's `camera:didUpdateStatus:` method.

```objc
GPCamera *camera; // defined elsewhere

-(void)viewDidLoad {
	camera.delegate = self;
}

#pragma mark - GPCameraDelegate
- (void)camera:(GPCamera *)camera didUpdateStatus:(NSArray<NSString *> *)statusKeys
{
    if([statusKeys containsObject:kGPCameraStatus_SystemHot]) {
        NSLog(@"The system is too hot!");
    }
}
```

## How to get faster notification of camera disconnection
Should you need to check for poor communication with the device prior to receiving the notification and prevent a gap in communication between your app and the device, add the following class:

### GPConnectivityIndicator.h
```objc
@protocol GPConnectivityIndicatorDelegate;

@interface GPConnectivityIndicator : NSObject
@property (strong, nonatomic) id <GPConnectivityIndicatorDelegate> delegate;
@end

@protocol GPConnectivityIndicatorDelegate <NSObject>
-(void)interruptionExperienced;
-(void)connectionRestored;
@end
```

### GPConnectivityIndicator.m
```objc
#import "GPConnectivityIndicator.h"

@implementation GPConnectivityIndicator

NSTimer *timer;
BOOL disconnectionReported;

-(instancetype)init {
    self = [super init];
    disconnectionReported = NO;
    return self;
}

-(void)startConnectionTimer {
    if(disconnectionReported) {
        [self.delegate connectionRestored];
        disconnectionReported = NO;
    }
    timer = [NSTimer scheduledTimerWithTimeInterval:2.0 target:self selector:@selector(notifyDelegateOfFailure) userInfo:nil repeats:false];
    [[NSRunLoop currentRunLoop] addTimer:timer forMode:UITrackingRunLoopMode];
}

-(void)notifyDelegateOfFailure {
    disconnectionReported = YES;
    [self.delegate interruptionExperienced];
}

-(void)resetTimer {
    // start timer over
    if([timer isValid]) {
        [timer invalidate];
        timer = nil;
    }
    [self startConnectionTimer];
}
@end
```

The idea behind this class is to start a timer when the view loads, and reset it every time there is a status update from the camera. These status updates come several times a second, so if 2 seconds elapse between status updates, the timer will expire and notify its delegate of the loss in communication. You can then use this to provide some interface indication that the application is experiencing a loss in communication with the device.

To use this class:

1. Initialize a `GPConnectivityIndicator` in your view controller.
2. Set the `GPConnectivityIndicator`'s delegate to self and implement the `GPConnectivityIndicatorDelegate` methods.
3. Call `GPConnectivityIndicator.startConnectionTimer` method.
4. Set your `GPCamera`'s delegate to `self`
5. In the `GPCameraDelegate`'s `camera:didUpdateDateAndTime:` method since this will be called most frequently. In this implementation, you will call the `GPConnectivityIndicator.resetTimer` method.

```objc
GPCamera *camera;  // defined elsewhere

GPConnectivityIndiator *conn;

- (void)viewDidLoad {
    [super viewDidLoad];
    conn = [GPConnectivityIndicator new];
    conn.delegate = self;
    camera.delegate = self;
    [conn startConnectionTimer];
}

// Selector for notification
-(void)cameraStateChanged:(NSNotification *)note {
	[conn resetTimer];
}

// Delegate method implementations
-(void)interruptionExperienced {
    NSLog(@"Connection interrupted!");
}

-(void)connectionRestored {
    NSLog(@"Connection restored!");
}

// GPCameraDelegate Implementation
-(void)camera:(GPCamera *)camera didUpdateDateAndTime:(NSDate *)date {
    [conn resetTimer];
}
```

## How to handle the camera disconnecting while downloading large files

When downloading large files, your camera may disconnect resulting in an error downloading a file. To mitigate this issue, we recommend you use the following steps.

1. Set your class as a `NSURLSessionDownloadDelegate`.
2. Instantiate an `NSURLSession` with `self` as the delegate.
3. Implement the `URLSession:task:didCompleteWithError:` delegate method.
4. In the delegate method, check for the `NSError` for the `NSURLSessionDownloadTaskResumeData` key in the `userInfo` property.
5. Start a new `NSURLSessionDownloadTask` with the `NSURLSession.downloadTaskWithResumeData:` method and pass in the resume data from the `userInfo`.

### Objective-C

```objc
NSURL *someUrl;     // defined elsewhere

-(void)viewDidLoad {
	NSURLSessionConfiguration *sessionConfig = [NSURLSessionConfiguration defaultSessionConfiguration];
	NSURLSession *session = [NSURLSession sessionWithConfiguration:sessionConfig delegate:self delegateQueue:nil];
	[[session downloadTaskWithURL:someUrl] resume];
}

-(void)URLSession:(NSURLSession *)session task:(NSURLSessionTask *)task didCompleteWithError:(NSError *)error {
    NSData *resumeData = [[error userInfo] objectForKey:NSURLSessionDownloadTaskResumeData];
    if(resumeData) {
        [[session downloadTaskWithResumeData:resumeData] resume];
    }
}
```