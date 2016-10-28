# How to Display What Media is on the Camera's SD Card

The GoPro WSDK can fetch a list of media files on the camera's SD card and provide a thumbnail image URL 
for each file, as well as a URL to the high resolution and low resolution versions of each file. 
Once you have the URL, you can download and display it however you like. 

## Fetching the List of Media items

Media items are represented by the `GPCameraMedia` class. This class provides basic details for both photos and videos like file name, file size, and media type. To fetch the list of `GPCameraMedia` objects for your camera's SD card, call the `requestMediaWithCompletionBlock` method of a `GPCamera` instance:

### Objective-C

    GPCamera *camera;   // defined elsewhere
    [camera requestMediaWithCompletionBlock:^(NSArray<GPCameraMedia *> *mediaItems, NSError *error) {
        if (error) {
            // your app should handle this error
        } else {
            // display the media...
            for (GPCameraMedia *item in mediaItems) {
                NSLog(@"Got media %@ of type %lu", item.name, (unsigned long)item.type);
            }
        }
    }];


**NOTE:** The WSDK currently does not offer a method to get the most recently-saved media item. To access the most recently-saved media item, filter the results of `requestMediaWithCompletionBlock` to find the `GPCameraMedia` with the latest `createDate` property.

## Displaying Media

### Getting Media URLs

The GoPro WSDK provides access to the media content itself via an `NSURL`. You can get full and lower-resolution versions of a media item from a `GPCamera` instance:

#### Objective-C

    GPCamera *camera;           // defined elsewhere
    GPCameraMedia *mediaItem;   // defined elsewhere
    NSURL *hiRes = [camera highResolutionURLForMedia:mediaItem];
    NSURL *loRes = [camera lowResolutionURLForMedia:mediaItem];

### Displaying Thumbnails

GoPro cameras generate thumbnails for photo and video media types. To get a URL for a thumbnail for a particular media item, call `GPCamera`'s `thumbnailURLForMedia:` method as shown below. Note that your app should take care to load the image data from an appropriate thread.

#### Objective-C

```objc
GPCamera *camera;           // defined elsewhere
GPCameraMedia *media;       // defined elsewhere
UIImageView *thumbnailView; // defined elsewhere

NSURL *thumbnailUrl = [camera thumbnailURLForMedia:media];
UIImage *thumbnailImage = [UIImage imageWithData:[NSData dataWithContentsOfURL:thumbnailUrl]];

thumbnailView.image = thumbnailImage;
```

## Getting additional information about videos

Videos stored on the camera have additional information that is not loaded as a part of the `GPCameraMedia` object. This includes video length, video frame rate, and other video-specific details. To access these properties, use the methods beginning with "request" in the `GPCamera (Media)` category (see `GPCamera+Media.h`) as shown in the example below.

### Objective-C

```objc
if (mediaItem.type != GPCameraMediaType_Photo) {
    [camera requestVideoMetadataForMedia:mediaItem completionBlock:^(GPCameraMediaMetadata *metadata, NSError *error) {
        NSLog(@"Media %@ is %f seconds long", mediaItem.name, metadata.duration);
    }];
    
    [camera requestVideoFrameRateForMedia:mediaItem completionBlock:^(double frameRate) {
        NSLog(@"Media %@ was recorded @ %f frames per second", mediaItem.name, frameRate);
    }];
    
    [camera requestVideoHighResolutionPlayabilityForMedia:mediaItem completionBlock:^(GPCameraMediaPlayability playability) {
        if (playability == GPCameraMediaPlayability_Yes) {
            NSLog(@"Media %@ is playable at hi-res!", mediaItem.name);
        } else {
            // your app should handle other GPCameraMediaPlayability values appropriately.
        }
    }];
}
```

**NOTE:** If your app needs to pull multiple pieces of video information into a single result, you can combine these asynchronous calls using something like [PromiseKit](http://promisekit.org) or by following the GCD-based example in `GPCameraRollViewController` of the GoPro WSDK Sample Application.

## How to delete media from a camera

WSDK provides a method to delete either a single media object or all media from the device.  In the examples below, we will take a look at how this is accomplished.

Deleting a single media object:

### Objective-C

```objc
GPCamera *myCamera;   // defined elsewhere
GPCameraMedia *media; // defined elsewhere

[myCamera deleteMedia:media progress:nil completionBlock:^(NSError *error) {
    if (!error) {
        // Media has been successfully deleted
    }
    else {
	    // handle the error.
	    NSLog(@"Error deleting media: %@",error);
    }
}];
```

Deleting all media on an SD card:

###Objective-C

```objc
GPCamera *myCamera; // defined elsewhere

[myCamera deleteAllMediaWithCompletionBlock:^(NSError *error) {
    if (!error) {
        // All media has been deleted from the SD card
    }
    else {
        // handle your error.
        NSLog(@"Error deleting media: %@",error);
    }
}];
```

## Chaptered Videos

Videos which become large in size will be broken up into chapters. This occurs once a recorded video reaches approximately 4 gigabytes in size. Since the WSDK does not group these chapters at this point in time, you have to look at the file name in order to determine which videos are grouped together.

As an example, if a 4K video is recorded for approximately 25 minutes, it will be broken into three chapters as three separate videos on your media roll. The chapters will display in the following order: `GOPR8718.MP4`, `GP018718.MP4`, and `GP028718.MP4`.  

The first video in a group is named `GOPRxxxx.MP4` where `xxxx` is a four digit number. The next videos are named `GPnnxxxx.MP4` where `nn` starts at `01` and is incremented for each chapter and `xxxx` is the same four digit number as the first chapter.