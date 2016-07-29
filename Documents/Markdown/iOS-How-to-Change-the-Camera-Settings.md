# Working with Camera Settings

## Reading Camera Settings

There are many different camera settings you can experiment with. Different settings are relevant to different camera modes. Examples of settings that are applicable to the video mode are video resolution, frames per second, and the "low light" toggle. The names for these settings are represented by **kGPCameraSetting_VideoResolution**, **kGPCameraSetting_VideoFps**, and **kGPCameraSetting_VideoLowLight**. The complete list of string constants is defined in `GPCameraSettingConstants.h`.

The following example demonstrates how to read the camera's current resolution setting:

### Objective-C

```objc
    GPCamera *camera;       // defined elsewhere

    GPCameraSetting *resolutionSetting = [camera settingForKey:kGPCameraSetting_VideoResolution];
    NSLog(@"Setting %@ has valid values %@.", resolutionSetting.displayName, resolutionSetting.currentlyValidOptions);
    
    GPCameraSettingOption *currentResolution = [camera currentOptionForSetting:resolutionSetting];
    NSLog(@"Resolution is set to: %@ / enum value %ld", currentResolution.displayName, (long)currentResolution.value);
```

And here is the corresponding log output:

```
Setting Resolution has valid values (
    "<GPCameraSettingOption: 0x136df6760 displayName: 4K, value: 1>",
    "<GPCameraSettingOption: 0x136df6880 displayName: 2.7K, value: 4>",
    "<GPCameraSettingOption: 0x136df6790 displayName: 1440, value: 7>",
    "<GPCameraSettingOption: 0x136df6980 displayName: 1080 SuperView, value: 8>",
    "<GPCameraSettingOption: 0x136df69e0 displayName: 1080, value: 9>",
    "<GPCameraSettingOption: 0x136df6a90 displayName: 960, value: 10>",
    "<GPCameraSettingOption: 0x136df69b0 displayName: 720 SuperView, value: 11>",
    "<GPCameraSettingOption: 0x136df6bc0 displayName: 720, value: 12>",
    "<GPCameraSettingOption: 0x136df6bf0 displayName: WVGA, value: 13>"
).

Resolution is set to: 1080 / enum value 9
```

## Writing Camera Settings

To set a camera setting, pass a **GPCameraSettingOption** to **GPCamera**'s **setOption:forSetting:completionBlock:** method. To get the desired **GPCameraSettingOption**, currently it's best to render a UI that lets the user select between valid options. At this time there is no way to directly access **GPCameraSettingOption**s directly with constants.

In the following example, the user has selected their desired resolution and we have already figured out the index of their selection from the UI elements.

### Objective-C

```objc
- (void)updateResolutionForIndex:(NSInteger)index ofCamera:(GPCamera *)camera {
    GPCameraSetting *resolution = [camera settingForKey:kGPCameraSetting_VideoResolution];
    GPCameraSettingOption *newResolution = resolution.currentlyValidOptions[index];
    [camera setOption:newResolution forSetting:resolution completionBlock:^(NSError *error) {
        if (error) {
            // handle the error
        } else {
            NSLog(@"Sent request to camera to change resolution");
        }
    }];
}
```

### A Note on Completion Blocks

Note that completion blocks passed to methods that modify the **GPCamera** will be called before the change has actually been accepted by the camera. Execution of the completion block indicates that the SDK has finished its communication to the camera. Due to the way the SDK communicates with the camera, the settings change will not be effective until the SDK is notified via the **kGPCameraStateChangedNotification** notification.
