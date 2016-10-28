# Frequently Asked Questions

## How do I pair the camera for the first time?

Use the [GoPro Capture App](https://itunes.apple.com/us/app/gopro-app/id561350520?mt=8) 
to pair your device to the camera the first time.

See [How to Pair the Camera with the GoPro App](https://www.gopro.com/help/articles/Block/How-to-Pair-the-Camera-with-Capture).

Currently, you cannot pair the camera to the device for the first time through the WSDK.

## How do I configure the camera’s Wi-Fi settings (SSID/Password)?

When you connect the camera using the GoPro official app for the first time, 
it asks you to set up/configure the Wi-Fi settings. 
After you configure them, you cannot change these settings. 
If you want to change them, you need to reset the camera and then go through 
the first-time pairing process again. For more information
on how to reset the camera, see 
[How to Reset the Camera to Default Settings](https://www.gopro.com/help/articles/How_To/How-to-Reset-the-Camera-to-Default-Settings).

Currently, you cannot configure the camera's Wi-Fi settings through the WSDK.

## After I have paired the camera using the GoPro official app, how do I pair/re-pair using GoPro WSDK?

Once you have succesfully paired for the first time, you can use the WSDK to search for available 
cameras and connect to one of them. See the [Getting Started](iOS-Getting-Started.md#camera-discovery) tutorial.

## How can I display live streaming from a GoPro camera?

The WSDK provides the ability to live stream a preview for the camera you are currently connected to. See the [How to Display a Camera Preview](iOS-How-to-Display-a-Camera-Preview.md) tutorial.

## How do I get the camera model?

The camera model can be found from the `modelName` property of the `GPCamera` class.

## How do I set the camera time?

The `GPCamera` class has the following method that can be used to set the date and time:

    - (void)setDateTime:(NSDate *)date completionBlock:(void (^)(NSError *error))block;

## How do I check if the camera is busy?

The camera is considered busy if it is currently experiencing high CPU usage.
The `GPCamera` class has a `busy` property with an `isBusy` getter to check the 
camera busy status. 

## How do I get the list of GoPro media?

The GoPro WSDK can fetch a list of media files on the camera's SD card and provide a thumbnail image URL
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

Refer to [HERO4 Video Resolutions](https://developer.gopro.com/s/article/HERO4-Video-Resolutions) and [HERO5 Video Resolutions](https://developer.gopro.com/s/article/HERO5-Video-Resolutions)
for details for each supported camera model.

## How do I offload files from the camera?

There are three ways you can offload files:

* **Card reader:** Remove the SD card from the camera, insert it into a card reader, 
    and attach the card reader to your computer.
* **USB cable:** Connect the camera to your computer via USB cable. If you are connected to the 
    camera over Wi-Fi, the Wi-Fi connection is lost.
* **Over Wi-Fi:** See the [How to Display What Media is on the Camera's SD Card](iOS-How-to-Display-Media-from-the-SD-Card.md) tutorial.
* **Quik Key:** You can save space on your iPhone by using GoPro's [Quik Key Mobile microSD Card Reader](http://shop.gopro.com/accessories-2/quik-key-iphoneipad-mobile-microsd-card-reader/AMCRL-001.html) to transfer any media that is stored on your GoPro device.

**Note:** You cannot offload any media file from the SD card while you are recording.

## What are the compatible SD Cards?

See [microSD Card Considerations](http://www.gopro.com/help/articles/Block/microSD-Card-Considerations)
for a list of and details about compatible SD cards.

## How do I display how much space is left on the SD card?
The `GPCamera` class contains a property `freeSpaceRemaining` that will display an estimate of how much space is left on an SD card in kilobytes.

## How do I get the camera orientation?

Currently, the WSDK does not support retrieving the physical orientation of the camera. However, it is possible to adjust the camera's orientation setting to allow for recording in different positions (e.g. hanging the camera upside down). This setting has 3 options, ***up***, ***down***, and ***auto***. Setting the option to auto will detect which way the camera is oriented when the recording starts, and will keep that setting. If you start recording while the camera is upside down and turn it right side up during recording, it will not adjust its orientation at all.

To find this setting for your device, you must first obtain the `GPCameraSetting` by calling the `settingForKey:kGPCameraSetting_SetupOrientation` method on the `GPCamera` class. You can then call the `currentOptionForSetting:(GPCameraSetting *)` on your `GPCamera` class to figure out what setting your device is currently set to.

To learn more about how to get and set settings, see the [How to Change the Camera Settings](iOS-How-to-Change-the-Camera-Settings.md) tutorial.

## What does the **GPCameraScannerOption** parameter of the **initWithOption** method of **GPCameraScanner** do? 

Although the parameter is intended to specify either Wi-Fi or Bluetooth operation, currently only Wi-Fi is supported to initiate a connection to the camera. When choosing the `GPCameraScannerOptionAll` connection option, you will be able to set the `GPCamera.setWifiPower:completionBlock:` to `NO` after you have connected to ensure the commands get sent to the camera over BLE.

## A Note on Completion Blocks

Note that completion blocks passed to methods that modify the `GPCamera` will be called before the change has actually been accepted by the camera. Execution of the completion block indicates that the SDK has finished its communication to the camera. Due to the way the SDK communicates with the camera, the settings change will not be effective until the SDK is notified via the `GPCameraDelegate`.

## Compatibility
Requires iOS 8.0 or later. Compatible with iPhone, iPad, and iPod touch.

**Note:** The iPad 2 is currently unsupported.

## Swift Support

If you want to use the WSDK in a Swift project, you will have to perform a few additional steps in the setup process.

1. Add `use_frameworks!` in your project's target in your `Podfile`.
2. Add an Objective-C bridging header to your project. You can do this by adding a new file to your project, selecting **CocoaTouch Class** under **iOS > Source**. When creating the file, make sure that **Objective-C** is selected as your language. The name of the file will not matter, because you will delete it right away.
3. After creating the new class, Xcode will display a prompt asking if you wish to configure a bridging header. Click the **Create Bridging Header** button to create the bridging header.
4. Inside your new bridging header, add `#import <GoProSDK/GoProSDK.h>` and build your project. You will now be able to use the WSDK in your project.

**Note:** Although you can currently use the framework in a Swift project, you cannot use it when creating a framework. This support will be added in future releases.

## Known Issues

The following are known issues for the iOS WSDK:

* Deleting one chaptered video deletes all chapters.
* GoPro Cameras do not support SSL requests. Accordingly please set “Allow Arbitrary Loads” to YES in app’s info.plist. See [Getting Started](iOS-Getting-Started.md#disable-apptransportsecurity) for more information.
* `GPCameraScanResult.displayName` returns the model name instead of the user-entered name.
* A `GPCamera`'s identifier may be different across platforms. You may notice that what an iOS device displays will be different than what an Android device displays.
* There is currently no direct method in the WSDK to check if the device becomes too hot.
* The `kGPCameraConnectionLostNotification` notification can take up to 30 seconds to be sent from the device to an App. This is due to the fact that the WSDK continues to attempt to reconnect to the camera.
* You may experience issues discovering and/or connecting to some cameras. This is an issue that will be addressed in a future firmware release.
* When downloading large files from the camera, the camera may disconnect and cause errors downloading the file.

**Note:** For some of the known issues, we have included certain workarounds that can be implemented. See [Workarounds](iOS-Workarounds.md) for more information.

## Limitations

The following are known limitations for the iOS WSDK:

* The commands sent to the WSDK should be serialized. Sending multiple commands simultaneously will lead to inconsistent results.
* The first pairing experience must always be through the GoPro official application.
* The camera firmware does not support robust error handling at this time. We strongly recommend referring to [HERO4 Video Resolutions](https://developer.gopro.com/s/article/HERO4-Video-Resolutions) and [HERO5 and HERO5 Session Video Resolutions](https://developer.gopro.com/s/article/HERO5-and-HERO5-Session-Video-Resolutions) for valid combinations of resolutions and which settings are available to prevent these errors.
* The resolution of the live preview stream is not configurable. Currently only a low resolution stream (480p) is supported for the Hero5 and Hero5 Session.
