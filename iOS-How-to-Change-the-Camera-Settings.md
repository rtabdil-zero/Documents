# Working with Camera Settings

## Reading Camera Settings

There are many different camera settings you can experiment with. Different settings are relevant to different camera modes. Examples of settings that are applicable to the video mode are video resolution, frames per second, and the "low light" toggle. The names for these settings are represented by `kGPCameraSetting_VideoResolution`, `kGPCameraSetting_VideoFps`, and `kGPCameraSetting_VideoLowLight`. The complete list of string constants is defined in `GPCameraSettingConstants.h`.

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

To set a camera setting, pass a `GPCameraSettingOption` to `GPCamera`'s `setOption:forSetting:completionBlock:` method. To get the desired `GPCameraSettingOption`, currently it's best to render a UI that lets the user select between valid options. At this time there is no way to directly access `GPCameraSettingOption` objects directly with constants.

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

## GPCameraSettingDelegate

The latest WSDK version brings a new `GPCameraSettingDelegate` protocol which contains functions that are called when particular settings are updated on the camera. 

See [Delegates](iOS-Delegates.md) for more information.

## Electronic Image Stabilization

The new HERO5 provides the ability to use Electronic Image Stabilization (EIS) to increase the quality of your videos by eliminating any visible noise that is generated through minor vibrations. In order to encourage the production of the best content, this feature is set to ***on*** by default.

The WSDK provides methods to check the currently selected EIS option as well as change it if desired. In order to do so you will need to use a `GPCamera` object that has been paired through the official GoPro app.  From there, you can use the `settingForKey:(NSString *)key` method with the `kGPCameraSetting_VideoEis` key to get the setting from the camera. Once you have the setting, you can read and mutate options as need be. 

Once you have acquired the setting from the camera, you can then use the `currentlyValidOptions` or `allOptions` method to get a list of all the options that you can use to adjust the setting as you would like. Since there is currently no way of accessing an option directly, it is recommended that you store the options in an NSDictionary using their display names as keys or in an NSArray. This will make it easy for you to reference them as you need them.

After the options are stored, you can then use the `setOption:forSetting` method of the `GPCamera` class to modify the settings as needed.

**Note:** EIS is not currently available in 4K resolution or at a frame rate above 60 frames per second.

## Raw Photo Format

In addition to the new EIS setting, the HERO5 also brings the ability to capture a raw photo in addition to a `.JPG` when taking photos. Although WSDK doesn't provide a method to request the raw photos, they will still be available on your SD Card as `.GPR` files (GoPro Raw). It is important to remember that raw photos are significantly larger than their compressed `.JPG` counterparts. In order to access these `.GPR` files, you will have to insert the SD card into your computer and the raw photos will be available in the `/DCIM/100GOPRO/` folder by default.

Although the photos are much larger, they make much better candidates for images which will go through post-processing. This is due to the fact that the additional space that is taken up is filled with a lot more information. This information can then be adjusted and provide much nicer photo post adjustments.

This setting can be turned on or off through the WSDK. It is accessed in a similar fashion to accessing the Electronic Image Stabilization setting, as previously described. The only difference being that the GoPro setting key is `kGPCameraSetting_PhotoSingleRaw`.

## GPS
The HERO5 has an internal GPS which can be used to indicate where photos and videos were recorded. This data is encoded into the media as additional metadata. Although the WSDK does not provide a method to retrieve location data from media, it provides functionality to turn the GPS feature on the device on and off. This is accomplished by using the same method that was mentioned for the other setting examples and using the `kGPCameraSetting_SetupGps` key.
