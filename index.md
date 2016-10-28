# Getting Started 

To get started, open and run the getting started sample application, 
which illustrates the following functionality:

* Camera discovery
* Live preview streaming
* Triggering the shutter
* Changing camera modes
* Changing video settings (resolution and FPS)
* Fetching the media list from SD card

**Note:** You will need an iOS device (iPhone or iPad) with Bluetooth LE capability.

## Set Up Xcode and Cocoapods

First, you will need to set up Xcode and CocoaPods. Follow these steps:

1. Download and install [Xcode](https://developer.apple.com/xcode/download/) from the App Store. 
2. Install [CocoaPods](https://cocoapods.org) version 1.0.0 or newer, a dependency manager for Swift and Objective-C Cocoa projects. Follow these steps:
    1. Open up the **Terminal** application.
    2. Type these commands:

            sudo gem install cocoapods
            pod setup

## Pairing the Camera and Device

The first time you pair the camera and the device, you must use [Capture](https://shop.gopro.com/softwareandapp),
the official GoPro iOS app. Follow the
instructions at [How to Pair the Camera with the GoPro App](https://www.gopro.com/help/articles/Block/How-to-Pair-the-Camera-with-Capture).

**Note:** You only need to use the official GoPro app the first time you pair a camera and a device.
During first time pairing, the GoPro app sets up camera-related settings in your device, such as
storing the SSID and password. 
Any future pairings are referred to as re-connection and the official GoPro app is not required. 
You can turn on the Wi-Fi access point on the camera and connect to it from the mobile device you are using.

**Note:** The mobile device may attempt to connect to other networks. If it successfully connects to another network, 
it will lose the connection with the camera.

## Running the Sample Application

Because the sample application project uses CocoaPods, you must open it through 
the Terminal initially, and not through the Finder. After the pods have been installed, you will be able to open the workspace through Xcode and the Finder as usual.

Follow these steps to set up the sample app:

1. In the Terminal, change directory to the **Example** folder in the iOS WSDK folder.
2. Type this command to import and install the required libraries:

        pod install

3. Open the sample application workspace with this command:

        open WSDK.xcworkspace

Next, you will build and run the sample app project in Xcode. 

**Note:** The instructions below are for running the code
on an iOS device. You can also run the app through the simulator, but 
discovery and pairing will work differently than
on a device because the simulator does not support Bluetooth.

1. From the **Product** menu, choose **Build**. If you get a signing error, click the **Fix** button. If you are using Xcode 8 and iOS 10, a team will be required for signing. This dropdown can be set to a sandbox team.
2. Attach an iOS device to your computer through USB. 
3. From the **Product** menu, choose **Run**. (Or click the Run triangle.) The WSDK app should appear on your device.
4. You will see a list of cameras, which will be empty. Swipe down on the screen to refresh the list. Your camera should be listed now. </br>
    ![Camera list](cameras.png)
5. Tap on your camera in the list. You will now see the **Info** tab, which contains a preview video and other information.</br>
    ![Info screen](details.png)
6. Select a different mode, such as **Photo**. The camera will switch modes.
7. Tap on **Capture** to start the video (in video mode) or to take a photo (in photo mode).
8. Tap on the **Media** button at the bottom to see a list of media on the SD card. The first time it will be empty, so swipe down to refresh the list.</br>
    ![Media screen](media.png)
9. Tap on **Video Settings** to change the video settings. You will see them update in the camera.
    ![Settings screen](settings.png)

## Examining the Code

Let's look at the code to see how the various features work.

### Camera Discovery

To write code for the camera discovery process, follow these steps:

1. Create an object responsible for discovering and connecting to the camera. Have it conform to the `GPCameraScannerListener` and `GPCameraConnectorDelegate` protocols.
2. Initialize a `GPCameraScanner` with the `initWithOption:` initializer with the desired `GPCameraScannerOption`. 
3. Set the `delegate` property of the `GPCameraScanner` instance to `self`.
4. Call the `start` method on the scanner. `GPCameraScanResult` objects are asynchronously returned via the `GPCameraScannerListener` delegate methods. You can retrieve basic information about a discovered camera including `displayName`, `identifier`, and `model`.

#### Objective-C

```objc
self.cameraScanner = [[GPCameraScanner alloc] initWithOption:GPCameraScannerOptionWiFi];
self.cameraScanner.delegate = self;
[self.cameraScanner start];
```

There are three methods in the `GPCameraScannerListener` protocol. `scanner:didFindCamera:` is called when a camera is discovered over your preferred network. `scanner:didRemoveCamera:` is called if a camera is removed, lost connectivity, or dropped signal strength. Lastly, `scanner:didUpdateCamera:` is called if a camera is updated in any manner.

### Camera Connection

Once the user has selected a camera, use these steps to connect to it.

1. Call `stop` on your `GPCameraScanner` instance.
2. Invoke `connectionRequestForCameraScanResult:` on the `GPCameraScanner` with the desired `GPCameraScanResult` object as a parameter. A `GPConnectionRequest` object is returned.
3. Create an instance of `GPCameraConnector`.
4. Invoke the `connect:` method on the `GPCameraConnector` with the
`GPConnectionRequest` passed in as a parameter.
5. Your `GPCameraConnectorDelegate` methods will be called to indicate the outcome of the connection request. Once you get a `GPCamera` object, you are connected to the camera that it represents.

The following snippet gets the `GPConnectionRequest` and connects to the camera.

#### Objective-C

```objc
// user has selected a camera scan result to connect to from a tableview

self.cameraCollection.selectedCamera = nil;
[self.cameraScanner stop];

GPCameraScanResult *scanResult = self.cameraScanner.scanResults[indexPath.row];
GPConnectionRequest *connectionRequest = [self.cameraScanner connectionRequestForCameraScanResult:scanResult];

[self.connector connect:connectionRequest];
```

### Controlling Recording

Once you are connected to the camera, you can control the shutter and other camera functionality
with the `GPCamera (Control)` class. Use these steps to control the shutter:

1. Set the camera settings using the `GPCamera` class.
2. Set the camera mode using the `GPCameraMode` class.
3. Use the `GPCamera setShutter:` method and pass it the value `YES` to start capturing.

## Notes for Creating Your Own App

### Disable AppTransportSecurity 

For iOS 9, all unsecured HTTP traffic from iOS apps is disabled, as a part of App Transport Security (ATS).
Apple treats the communication between iOS devices and the GoPro camera as unsecured HTTP traffic,
which mean that with ATS enabled, HTTP requests to the GoPro camera will result in errors like this:

    Error Domain=NSURLErrorDomain Code=-1004 "Could not connect to the server." UserInfo=0x12ed5bad0 {NSUnderlyingError=0x12ee495b0 "The operation couldn’t be completed. (kCFErrorDomainCFNetwork error -1004.)"

or

    NSURLSession/NSURLConnection HTTP load failed (kCFStreamErrorDomainSSL, -9802)

or

    App Transport Security has blocked a cleartext HTTP (http://) resource load since it is insecure. Temporary exceptions can be configured via your app's Info.plist file.

Since the WSDK communicates via a local network, iOS 10 introduced a new key specifically for this case: `NSAllowsLocalNetworking`. In order to support customers who use both iOS 9 and iOS 10, Apple has stated that using the following settings will not result in a rejection from an app store submission provided you supply a reason such as, "My app disables ATS to communicate with a GoPro camera over a local network".

To disable ATS, open `Info.plist`, and add the following lines:

    <key>NSAppTransportSecurity</key>
	 <dict>
		<key>NSAllowsLocalNetworking</key>
		<true/>
		<key>NSAllowsArbitraryLoads</key>
		<true/>
		<key>NSExceptionDomains</key>
		<dict>
			<key>example.com</key>
            <dict>
                ...
            </dict>
		</dict>
	</dict>

**Note:** In the sample app, ATS is disabled.

### Implement a UI to Help with Device Pairing

Your app's users will need to change the Wi-Fi settings of their device in order to 
connect to the camera's Wi-Fi. We recommend that your app creates a user experience
that leads the user through the steps necessary to do this.

For an example on how to do this, look at the GoPro official iOS app, Capture. The screenshots
below illustrate how this is done in Capture.

![GoPro app pairing: screen 1](capture_01.png)

![GoPro app pairing: screen 2](capture_02.png)

![GoPro app pairing: screen 2](capture_03.png)

![GoPro app pairing: screen 2](capture_04.png)


