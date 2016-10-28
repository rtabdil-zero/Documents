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
with classes and methods is included in the SDK downloads, in the `Documentation` folder.
Open `index.html` to view the API reference documentation.

You can get started with step-by-step tutorials. 

* [Getting Started](iOS-Getting-Started.md): How to set up the IDE and run a sample project that does camera discovery and other basic functionality
* [How to Manage Shutter Modes](iOS-How-to-Manage-Shutter-Modes.md): How to set the camera for modes such as video, photo, and multishot.
* [How to Display a Camera Preview](iOS-How-to-Display-a-Camera-Preview.md): How to show a preview of what the camera currently sees.
* [How to Display What Media is on the Camera's SD Card](iOS-How-to-Display-Media-from-the-SD-Card.md): How to get a list of media files on the camera and display thumbnails for them
* [How to Change the Camera Settings](iOS-How-to-Change-the-Camera-Settings.md): How to change settings such as resolution, frames per second, and whether to adapt to low light.
* [Delegates](iOS-Delegates.md): How to get updates from the camera through delegate methods.
* [Workarounds](iOS-Workarounds.md): Workarounds, such as how to check if the device becomes too hot and how to get faster notification of camera disconnection.

The GoPro iOS SDK consists of two components:

* `GoProSDK.framework`, which contains classes to access and control cameras.
* `GoProSDKResources.bundle`, which contains detail settings for each camera model that is supported by the SDK.

## Current Version of the SDK

The current version is 2.0. The [iOS SDK v1.1 to v2.0 Migration Guide](iOS-WSDK-v1_1-to-v2_0-Migration-Guide.md)
describes the changes from the previous 1.1 version.

See [iOS SDK v1.0 to v1.1 Migration Guide](iOS-WSDK-v1_0-to-v1_1-Migration-Guide.md) for previous changes to the WSDK.

## Supported iOS Versions

The GoPro iOS WSDK requires iOS 8.0 or later. It is compatible with iPhone, iPad, and iPod touch.

**Note:** Currently, the iPad 2 is not supported by the GoPro iOS WSDK.

## Dependencies

The GoPro SDK depends on the following open-source libraries. These libraries are included as CocoaPods:

* CocoaAsyncSocket
* GZIP
* ProtocolBuffers
