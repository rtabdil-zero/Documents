# Introduction

The GoPro iOS Wireless SDK (WSDK) allows you to create iOS apps that can communicate with
GoPro wireless cameras, providing functionality such as:

* Discovering available cameras
* Selecting the camera mode (video, photo, etc.) and other settings
* Taking photos
* Starting and stopping video recording
* Streaming preview video
* Reading media files that are on the camera

This documentation gives an overview of the SDK. The detailed API reference material
with classes and methods is included in the SDK downloads, in the documentation folder.
Open index.html to view the API reference documentation.

You can get started with step-by-step tutorials. 

* [Getting Started](iOS-Getting-Started.md): How to set up the IDE and run a sample project that does camera discovery and other basic functionality
* [How to Manage Shutter Modes](iOS-How-to-Manage-Shutter-Modes.md): How to set the camera for modes such as video, photo, and multishot.
* [How to Display a Camera Preview](iOS-How-to-Display-a-Camera-Preview.md): How to show a preview of what the camera currently sees.
* [How to Display What Media is on the Camera's SD Card](iOS-How-to-Display-Media-from-the-SD-Card.md): How to get a list of media files on the camera and display thumbnails for them
* [How to Change the Camera Settings](iOS-How-to-Change-the-Camera-Settings.md): How to change settings such as resolution, frames per second, and whether to adapt to low light.

The GoPro iOS SDK consists of two components:

* **GoProSDK.framework**, which contains classes to access and control cameras.
* **GoProSDKResources.bundle**, which contains detail settings for each camera model that is supported by the SDK.

## Current Version of the SDK

The current version is 1.1. The [iOS SDK v1.0 to v1.1 Migration Guide](https://developer.gopro.com/s/article/iOS-SDK-v1-0-to-v1-1-Migration-Guide)
describes the changes from the previous 1.0 version.

## Supported iOS Versions

The GoPro iOS WSDK requires iOS 8.0 or later. It is compatible with iPhone, iPad, and iPod touch.

## Dependencies

The GoPro SDK depends on the following open-source libraries. These libraries are included as CocoaPods:
* AFNetworking
* CocoaAsyncSocket
* PSAlertView
* GZIP
* ProtocolBuffers

## Limitations

The following are known limitations for the iOS WSDK:

* The commands sent to the WSDK should be serialized. Sending multiple commands simultaneously will lead to inconsistent results.
* The first pairing experience must always be through the GoPro Official application.
* The camera firmware does not support robust error handling at this time. We strongly recommend referring to `camera_video_resolution_and_camera_settings.xlsx` for valid combinations. This file can be found in the `Documents` folder of the iOS WSDK.
* The resolution of the live preview stream is not configurable
* There is currently no direct API to access compressed video data directly. 
