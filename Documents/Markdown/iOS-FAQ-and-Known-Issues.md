# Frequently Asked Questions

## How do I pair the camera for the first time?

Use the [GoPro official iOS app](https://itunes.apple.com/us/app/gopro-app/id561350520?mt=8) 
to pair your device to the camera the first time.

See [How to Pair the Camera with the GoPro App](https://www.gopro.com/support/articles/how-to-pair-the-camera-with-the-gopro-app).

Currently, you cannot pair the camera to the device for the first time through the WSDK.

## How do I configure the camera’s Wi-Fi settings (SSID/Password)?

When you connect the camera using the GoPro official app for the first time, 
it asks you to set up/configure the Wi-Fi settings. 
After you configure them, you cannot change these settings. 
If you want to change them, you need to reset the camera and then go through 
the first-time pairing process again. For more information
on how to reset the camera, see 
[How to Reset the Camera to Default Settings](https://www.gopro.com/support/articles/how-to-reset-the-camera-to-factory-defaults).

Currently, you cannot configure the camera's Wi-Fi settings through the WSDK.

## After I have paired the camera using the GoPro official app, how do I pair/re-pair using GoPro WSDK?

Once you have succesfully paired for the first time, you can use the WSDK to search for available 
cameras and connect to one of them. See the [Getting Started](iOS-Getting-Started.md#camera-discovery) tutorial.

## How can I display live streaming from a HERO4 camera?

First, pass a **UIView** object into the **setStreamingVideoView** method 
of the **GPCamera** class. Then call the **startStreaming** on the **GPCamera** object.

See the [How to Display a Camera Preview](iOS-How-to-Display-a-Camera-Preview.md) tutorial.

## How do I get the camera model?

The camera model can be found from the **modelName** property of the **GPCamera** class.

## How do I set the camera time?

The **GPCamera** class as the following method that can be used to set the date and time:

    - (void)setDateTime:(NSDate *)date completionBlock:(void (^)(NSError *error))block;

## How do I check if the camera is busy?

The camera is considered busy if it is currently experiencing high CPU usage.
The **GPCamera** class has a **busy** property with an **isBusy** getter to check the 
camera busy status. 

## How do I get the list of GoPro media?

The GoPro WSDK can fetch a list of media files on the camera's SD card and provide a thumbnail image 
for each file, as well as a URL to the high resolution and low resolution versions of each file. 
Once you have the URL, you can download and display it however you like. 

See the [How to Display What Media is on the Camera's SD Card](iOS-How-to-Display-Media-from-the-SD-Card.md) tutorial.

## How should I handle camera settings and modes?

The GoPro WSDK allows you to retrieve and set the camera settings, such as
video resolution, video frames per second, and
video low light setting. See the [How to Change the Camera Settings](iOS-How-to-Change-the-Camera-Settings.md) tutorial.

We recommend that you retrieve and display the supported modes and submodes of the each camera
using the WSDK, as shown in the [How to Manage Shutter Modes](iOS-How-to-Manage-Shutter-Modes.md) tutorial.
This way, you are guaranteed to only show the user the modes that are supported by that camera model.

Refer to the file **camera_settings_and_video_resolution_settings.xls** in the **Documents** folder 
for details for each supported camera model.

## How do I offload files from the camera?

There are three ways you can offload files:

* **Card reader:** Remove the SD card from the camera, insert it into a card reader, 
    and attach the card reader to your computer.
* **USB cable:** Connect the camera to your computer via USB cable. If you are connected to the 
    camera over Wi-Fi, the Wi-Fi connection is lost.
* **Over Wi-Fi:** See the [How to Display What Media is on the Camera's SD Card](iOS-How-to-Display-Media-from-the-SD-Card.md) tutorial.

**Note:** You cannot offload any media file from the SD card while you are recording.

## What are the compatible SD Cards?

See [microSD Card Considerations](http://www.gopro.com/help/articles/Block/microSD-Card-Considerations)
for a list of and details about compatible SD cards.

## How do I get the camera orientation?

Currently, the WSDK does not support retrieving the camera orientation.

## What does the **GPCameraScannerOption** parameter of the **initWithOption** method of **GPCameraScanner** do? 

Although the parameter is intended to specify either Wi-Fi or Bluetooth operation, currently only Wi-Fi is supported. You should always set the **GPCameraScannerOption** parameter to **GPCameraScannerOptionWiFi**.

## Known Issues

The following are known issues for the iOS WSDK:

* Deleting one chaptered video deletes all chapters.
* GoPro Cameras do not support SSL requests. Accordingly please set “Allow Arbitrary Loads” to YES in app’s info.plist. See [Getting Started](iOS-Getting-Started.md#disable-apptransportsecurity) for more information.
* **GPCameraScanResult.displayName** returns the model name instead of the user-entered name.
* Low light setting in the sample app does not match the one set on the camera.
* **GPCamera.setPower:** must be called on the main thread. The behavior is undefined when this method is called from any other thread.

## Limitations

The following are known limitations for the iOS WSDK:

* The GoProSDK framework is not compatible with Swift. Swift support will be added in a future release.
* The commands sent to the WSDK should be serialized. Sending multiple commands simultaneously will lead to inconsistent results.
* The first pairing experience must always be through the GoPro official application.
* The camera firmware does not support robust error handling at this time. We strongly recommend referring to `camera_video_resolution_and_camera_settings.xlsx` for valid combinations.
* The resolution of the live preview stream is not configurable. Currently only a low resolution stream (240p) is supported.
* There is currently no direct API method to access compressed video data.
