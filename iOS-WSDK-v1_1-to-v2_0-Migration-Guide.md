This document describes the changes that need to be made when migrating your iOS app from
version 1.1 of the WSDK to version 2.0.

Read the [Introduction](iOS-Introduction.md) to learn more about the iOS WSDK.

## GPCamera.h:
- `freeSpaceRemaining` property added to indicate amount of free storage remaining on the camera's storage card in KB
- `GPCameraDelegate` protocol added with the following methods:
	- `cameraDidDisconnect:`
	- `camera:didUpdateStatus:`
	- `camera:didUpdateDateAndTime:`
	- `camera:didUpdateModeGroup:`
	- `camera:didUpdatePowerState:`

To see how this protocol is used, see [How to Manage Shutter Modes](iOS-How-to-Manage-Shutter-Modes.md)

- `kGPCameraSettingsFilterStateChangedNotification` constant removed.

## GPCamera+Decorator.h:
- `wifiPowerStatus` method added.
- `-(GPGPSLockStatus)gpsLockStatus` method added.
- `GPGPSLockStatus` enum added.
 
## GPCamera+Media.h:
- `requestMediaWithOptionalProperties` method added to request the list of files on the camera with optional properties.
- Added `deleteTagMoment:media:completionBlock:` for deleting HiLight tag.
- Added `tagPhotoMedia:completionBlock:` for tagging photo.
- Added `deleteTagPhotoMedia:completionBlock:` for deleting HiLight tag from photo.
 
## GPCamera+Exposure.h:
- `exposureSupported` added.
- `exposureEnabled` added.
- `exposureMode` added.
- `exposurePointOfInterest` added.
- `setExposureMode` added.
- `resetExposureWithCompletionBlock` added.
- `setExposurePointOfInterest` added. 

## GPCamera+Settings.h:

- `GPCameraSettingDelegate` protocol added with the following methods.
	- `camera:didUpdateCurrentOptionForSettings:`
	- `camera:didUpdateValidOptionsForSettings:`
	- `camera:didEnableSettings:`
	- `camera:didDisableSettings:`

To see how this protocol is used, see [How to change the Camera Settings](iOS-How-to-Change-the-Camera-Settings.md)

## GPCameraScanner.h
- `scanner:didRemoveCamera:` correctly called for devices discovered through BLE.
 
## GPCameraMedia.h:
- `optionalProperties` method added to retrieve the list of optional media properties.
- `GPCameraMediaOptionalPropertyClip` string constant added.
- `GPCameraMediaOptionalPropertyHighlightCount` string constant added.
- `GPCameraMediaOptionalPropertyHighlightTags` string constant added.
- `GPCameraMediaOptionalPropertyRaw` string constant added.
- `GPCameraMediaOptionalPropertyWDR` string constant added.
- `GPCameraMediaOptionalPropertyEIS` string constant added.
- `GPCameraMediaOptionalPropertyTranscode` string constant added.
- `GPCameraMediaOptionalPropertyProtuneAudio` string constant added.
- `tagCount` property added.
- `requestMediaWithOptionalProperties:completionBlock` method removed.
 
## GPCameraModeGroup.h:
 - `GPCameraModeType_Video_Video` renamed to `GPCameraModeType_Video`.
 - `GPCameraModeType_Video_TimeLapse` renamed to `GPCameraModeType_VideoTimeLapse`.
 - `GPCameraModeType_Video_Photo` renamed to `GPCameraModeType_VideoPlusPhoto`.
 - `GPCameraModeType_Video_Looping` renamed to `GPCameraModeType_VideoLooping`.
 - `GPCameraModeType_Photo_Single` renamed to `GPCameraModeType_PhotoSingle`.
 - `GPCameraModeType_Photo_Continuous` renamed to `GPCameraModeType_PhotoContinuous`.
 - `GPCameraModeType_Photo_Night` renamed to `GPCameraModeType_PhotoNight`.
 - `GPCameraModeType_Photo_SelfTimer` renamed to `GPCameraModeType_PhotoSelfTimer`.
 - `GPCameraModeType_Multishot_Burst` renamed to `GPCameraModeType_PhotoBurst`.
 - `GPCameraModeType_Multishot_TimeLapse` renamed to `GPCameraModeType_PhotoTimeLapse`. 
 - `GPCameraModeType_Multishot_NightLapse` renamed to `GPCameraModeType_PhotoNightLapse`.
 - `modeGroup` method added. 

## GPCameraModelConstants.h:
- `GPCameraModelHero5Session` added.

## GPCameraPlayer.h:

- `pause` method is deprecated and `stop` should be used instead.

## GPCameraSetting.h:

- `isEnabled` exposed to indicate whether a setting is enabled on the camera.
- `key` exposed to indicate the option key from the list described in `GPCameraSettingConstants.h`.
- `(GPCameraSettingOption*)optionWithValue:(NSUInteger)value` method removed.

## GPCameraStatusConstants.h:
- `kGPCameraStatus_WirelessApState` added.
- `kGPCameraStatus_LiveviewExposureSelectType` added.
- `kGPCameraStatus_LiveviewExposureSelectX` added.
- `kGPCameraStatus_LiveviewExposureSelectY` added.
- `kGPCameraStatus_SystemInternalBatteryPercentage` added.

## GPCameraSettingConstants.h:
- Added HERO5 settings and setting option constants.
- `kGPCameraSetting_SetupDiveSensitivity` constant added.
- `kGPCameraSetting_SetupLedV2` constant added.

## GPCameraSettingOptionConstants.h
- `kGPCameraSettingOption_AudioProtuneOption_OFF` constant added.
- `kGPCameraSettingOption_MultishotCurrentMode_Time_Lapse_Photo` constant added.- `kGPCameraSettingOption_MultiShotResolution_7MP_Med` constant added.
- `kGPCameraSettingOption_MultiShotResolution_7MP_Wide` constant added.- `kGPCameraSettingOption_SetupDiveSensitivity_OFF` constant added.
- `kGPCameraSettingOption_SetupDiveSensitivity_ON` constant added.- `kGPCameraSettingOption_SetupLedV2_Front_OFF` constant added.
- `kGPCameraSettingOption_SetupLedV2_OFF` constant added.
- `kGPCameraSettingOption_SetupLedV2_ON` constant added.- `kGPCameraSettingOption_VideoResolution_480` constant added.
- `kGPCameraSettingOption_AudioProtune_OFF` constant removed.
- `kGPCameraSettingOption_AudioProtune_ON` constant removed.
- `kGPCameraSettingOption_SetupLed_Front_OFF` constant removed.

## GPCameraPlayerRawDataOutput.h:
- Added `GPCameraPlayerRawDataOutput`, a subclass of `GPCameraPlayerOutput` that vends raw video/audio data carried in the camera live stream.

## GPCameraPlayerMetadataOutput.h:
- Changed `GPCameraPlayerMetadataOutputDelegate` to output a byte array instead of NSData:  `output:didOutputBuffer:length:`.

## GPSDKError.h:
- `GP_DEPRECATED DEPRECATED_ATTRIBUTE` defined.
- `GPDEPRECATED_MSG(msg) DEPRECATED_MSG_ATTRIBUTE(msg)` defined.