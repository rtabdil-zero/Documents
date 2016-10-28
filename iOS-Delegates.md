# Delegates

The new version of the WSDK provides the use of delegate methods to get updates from the camera. This action was previously accomplished by listening for the `kGPCameraStateChangedNotification` notification through the `NSNotificationCenter` which has since been deprecated and will be removed in a future version. There are two new delegates. The `GPCameraDelegate` provides updates to the camera's status, while the `GPCameraSettingDelegate` will provide updates to the camera's settings.

## GPCameraDelegate

The newest version of WSDK includes a new method of retrieving updates to the state of the camera via the `GPCameraDelegate`. Included in this protocol are the following methods:

- `- (void)cameraDidDisconnect:`: Invoked when the connection to the camera has been terminated.
- `- (void)camera:didUpdateStatus:`: Invoked when the state of the camera changed.
- `- (void)camera:didUpdateDateAndTime:`: Invoked when the date and time on the camera changed. This method could be invoked frequently.
- `- (void)camera:didUpdateModeGroup:mode:`: Invoked when the camera mode group and/or mode changes.
- `- (void)camera:didUpdatePowerState:`: Invoked when the camera is powered on or off.

To use this protocol:

1. Instantiate a `GPCamera` object.
2. Set `GPCamera.delegate` to `self`.
3. Implement the `GPCameraDelegate` methods.

### Objective-C

```objc
- (void)cameraDidDisconnect:(GPCamera *)camera
{
    // The camera was disconnected.
}

- (void)camera:(GPCamera *)camera didUpdateStatus:(NSArray<NSString *> *)statusKeys
{
    // One or more statuses were changed.
}

-(void)camera:(GPCamera *)camera didUpdateDateAndTime:(NSDate *)date {
    // The camera's date/time was updated.
}

- (void)camera:(GPCamera *)camera didUpdateModeGroup:(GPCameraModeGroup *)modeGroup mode:(GPCameraMode *)mode
{
    // Perform an action to notify the user that the mode and/or mode group has been changed.
}

- (void)camera:(GPCamera *)camera didUpdatePowerState:(BOOL)on
{
    // The power state was changed.
}
```

## GPCameraSettingDelegate

The latest WSDK version brings a new `GPCameraSettingDelegate` protocol which contains functions that are called when particular settings are updated on the camera. The delegate methods that are now available are as follows:

- `- (void)camera:didUpdateCurrentOptionForSettings:`: This method is invoked when the current option for one or more settings have changed.
- `- (void)camera:didUpdateValidOptionsForSettings:`: This method is invoked when the valid options for one or more settings have changed.
- `- (void)camera:didEnableSettings:`: This method is invoked when one or more settings have become enabled.
- `- (void)camera:didDisableSettings:`: This method is invoked when one or more settings have become disabled.

To use this protocol:

1. Instantiate a `GPCamera` object.
2. Set `GPCamera.settingDelegate` to `self`.
3. Implement the `GPCameraSettingDelegate` methods.

### Objective-C

```objc
- (void)camera:(GPCamera *)camera didEnableSettings:(NSArray<NSString *> *)settingKeys
{
    // Contains settings that have been enabled.
}

- (void)camera:(GPCamera *)camera didDisableSettings:(NSArray<NSString *> *)settingKeys
{
    // Contains settings that have been disabled.
}

- (void)camera:(GPCamera *)camera didUpdateValidOptionsForSettings:(NSArray<NSString *> *)settingKeys
{
    // Contains settings which have had valid options updated.
}

- (void)camera:(GPCamera *)camera didUpdateCurrentOptionForSettings:(NSArray<NSString *> *)settingKeys
{
    // Contains settings which have had current options updated.
}
```