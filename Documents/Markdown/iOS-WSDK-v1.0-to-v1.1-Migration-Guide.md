This document describes the changes that need to be made when migrating your iOS app from
version 1.0 of the WSDK to version 1.1.

Read the [Introduction](iOS-Introduction.md) to learn more about the iOS WSDK.

# Key Changes

## GPCamera.h:
- **version** property renamed to **firmwareVersion**.
- **protuneDefault** property added to indicate whether protune settings are default.
- **currentDataTime** property added.
- **isNetworkEnabled** method added.

## GPCamera+Decorator.h:
- **isImageStabilizationEnabled** method added.
- **isProtuneAudioEnabled** method added.
- **isPhotoWideDynamicRangeEnabled** method added.
- **isPhotoRawEnabled** method added.

## GPCamera+Media.h:
- **^GPCameraMediaProgressBlock** added to support progress indications on media items.
- Class **GPDeleteResults** added.
- **requestCameraMetadataSessionFilesWithCompletionBlock:** method removed.
- **deleteMedia:** method signature changed to take a progress block.
- **startMediaDownloadOperations** method added.
- **stopMediaDownloadOperations** method added.

## GPCameraConnector.h:
- **GPConnectorDelegate** renamed to **GPCameraConnectorDelegate**.
 
## GPCameraMedia.h:
- **addGroupObject** method removed.
- **objectInGroupAtIndex** method removed.
- **insertObject** method removed.
- **insertGroup:** method removed.
- **removeObjectFromGroupAtIndex** method removed.
- **removeGroupAtIndexes** method removed.
- **replaceObjectInGroupAtIndex** method removed.
- **replaceGroupAtIndexes** method removed.
- **groupMutableArray** method removed.
 
## GPMetadataSessionFile.h: 
- `GPMetadataSessionFile.h` file removed.
 
## GPCameraModelConstants.h:
- **GPCameraModelHero1** renamed to **GPCameraModelUnknown**.
- **GPCameraModelHero5Black **added.
 
## GPCameraScanner.h:
- **GPCameraScannerOption** enum added for specifying scan type.
- **networkType** property removed.
- **version** property removed.
- **hasNetwork** method added.
- **resolveWithTimeout** method added to retrieve additional scan result information.
- **scanOption** property added.
- **initWithNetworkType:** method renamed to **initWithOption**. Accepts the new **GPCameraScannerOption** as a parameter now instead.
 
## GPCameraSettingConstants.h:
- **kGPCameraSetting_PhotoProtuneShutterExposure** renamed to **kGPCameraSetting_PhotoExposureTime**.
- **kGPCameraSetting_MultiShotProtuneShutterExposure** renamed to **kGPCameraSetting_MultiShotExposureTime**.
- **kGPCameraSetting_SetupStreamGopSize** added.
- **kGPCameraSetting_SetupStreamIdrInterval** added.
- **kGPCameraSetting_SetupStreamBitRate** added.
- **kGPCameraSetting_SetupStreamWindowSize** added.
- **kGPCameraSetting_SetupLcd** added.
- **kGPCameraSetting_VideoExposureTime** added.
- **kGPCameraSetting_VideoProtuneIsoMode** added.
- **kGPCameraSetting_PhotoProtuneIsoMin** added.
- **kGPCameraSetting_MultiShotProtuneIsoMin** added.
- **kGPCameraSetting_PhotoSingleWdr** added.
- **kGPCameraSetting_VideoEis** added.
- **kGPCameraSetting_AudioProtune** added.
- **kGPCameraSetting_AudioOption** added.
- **kGPCameraSetting_AudioProtuneOption** added.
- **kGPCameraSetting_PhotoSingleRaw** added.
 
## GPCameraSettingOptionConstants.h:
- **kGPCameraSettingOption_VideoExposureTime** options added.
- **kGPCameraSettingOption_VideoProtuneIsoMode_Max** option added.
- **kGPCameraSettingOption_VideoProtuneIsoMode_Lock** option added.
- **kGPCameraSettingOption_VideoEis** options added.
- **kGPCameraSettingOption_PhotoExposureTime** options added.
- **kGPCameraSettingOption_PhotoSingleWdr** options added.
- **kGPCameraSettingOption_PhotoSingleRaw** options added.
- **kGPCameraSettingOption_PhotoProtuneIsoMin** options added.
- **kGPCameraSettingOption_MultiShotExposureTime** options added.
- **kGPCameraSettingOption_MultiShotProtuneIsoMin** options added.
- **kGPCameraSettingOption_SetupLcd** options added.
- **kGPCameraSettingOption_SetupLcdBrightness** options added.
- **kGPCameraSettingOption_SetupStreamGopSize** options added.
- **kGPCameraSettingOption_SetupStreamIdrInterval** options added.
- **kGPCameraSettingOption_SetupStreamBitRate** options added.
- **kGPCameraSettingOption_SetupStreamWindowSize** options added.
- **kGPCameraSettingOption_AudioOption** options added.
- **kGPCameraSettingOption_AudioProtune** options added.
- **kGPCameraSettingOption_AudioProtuneOption** options added.
 
## GPSDKError.h:
- **GPSDKErrorCodeConnectionRequestRequiresNetworkInterfaceChange** renamed to **GPSDKErrorCodeNetworkInterfaceChangeRequired**.
- **GPSDKErrorCodePairingCanceled** added.
- **GPSDKErrorCodePairingBluetoothNotAvailable** added.
 
## GPVersion.h:
- **generation** property removed.
- **major** property renamed to **majorNumber** and type changed from **NSString*** to **NSNumber***.
- **minor** property renamed to **minorNumber** and type changed from **NSString*** to **NSNumber***.
- **revision** property renamed to **revisionNumber** and type changed from **NSString*** to **NSNumber***.
- **initWithString** method removed.
- **versionWithString** method removed.
- **GPVersion (Catalog)** category and contained methods removed