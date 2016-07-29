# How to Display What Media is on the Camera's SD Card

The GoPro WSDK can fetch a list of media files on the camera's SD card and provide a thumbnail image 
for each file, as well as a URL to the high resolution and low resolution versions of each file. 
Once you have the URL, you can download and display it however you like. 

## Fetching the List of Media items

Media items are represented by the **GPCameraMedia** class. This class provides basic details for both photos and videos like file name, file size, and media type. To fetch the list of **GPCameraMedia** objects for your camera's SD card, call the **requestMediaWithCompletionBlock** method of a **GPCamera** instance:

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


NOTE: The WSDK currently does not offer a method to get the most recently-saved media item. To access the most recently-saved media item, filter the results of **requestMediaWithCompletionBlock** to find the **GPCameraMedia** with the latest **createDate** property.

## Displaying Media

### Getting Media URLs

The GoPro WSDK provides access to the media content itself via an **NSURL**. You can get full and lower-resolution versions of a media item from a **GPCamera** instance:

#### Objective-C

    GPCamera *camera;           // defined elsewhere
    GPCameraMedia *mediaItem;   // defined elsewhere
    NSURL *hiRes = [camera highResolutionURLForMedia:mediaItem];
    NSURL *loRes = [camera lowResolutionURLForMedia:mediaItem];

### Displaying Thumbnails

GoPro cameras generate thumbnails for photo and video media types. To get a URL for a thumbnail for a particular media item, call **GPCamera**'s **thumbnailURLForMedia:** method as shown below. Note that your app should take care to load the image data from an appropriate thread.

#### Objective-C

```objc
GPCamera *camera;           // defined elsewhere
GPCameraMedia *media;       // defined elsewhere
UIImageView *thumbnailView; // defined elsewhere

NSURL *thumbnailUrl = [camera thumbnailURLForMedia:media];
NSError *thumbnailLoadError;
UIImage *thumbnailImage = [UIImage imageWithData:[NSData dataWithContentsOfURL:thumbnailUrl]];

if (thumbnailLoadError) {
    // your app should handle the image load error
} else {
    thumbnailView.image = thumbnailImage;
}
```

## Getting additional information about videos

Videos stored on the camera have additional information that is not loaded as a part of the **GPCameraMedia** object. This includes video length, video frame rate, and other video-specific details. To access these properties, use the methods beginning with "request" in the **GPCamera (Media)** category (see `GPCamera+Media.h`) as shown in the example below.

NOTE: If your app needs to pull multiple pieces of video information into a single result, you can combine these asynchronous calls using something like [PromiseKit](http://promisekit.org) or by following the GCD-based example in **GPCameraRollViewController** of the GoPro WSDK Sample Application.

### Objective-C

```objc
if (mediaItem.type == GPCameraMediaType_Video) {
    [camera requestVideoMetadataForMedia:mediaItem completionBlock:^(GPCameraMediaMetadata *metadata, NSError *error) {
        NSLog(@"Video %@ is %f seconds long", mediaItem.name, metadata.duration);
    }];
    
    [camera requestVideoFrameRateForMedia:mediaItem completionBlock:^(double frameRate) {
        NSLog(@"Video %@ was recorded @ %f frames per second", mediaItem.name, frameRate);
    }];
    
    [camera requestVideoHighResolutionPlayabilityForMedia:mediaItem completionBlock:^(GPCameraMediaPlayability playability) {
        if (playability == GPCameraMediaPlayability_Yes) {
            NSLog(@"Video %@ is playable at hi-res!", mediaItem.name);
        } else {
            // your app should handle other GPCameraMediaPlayability values appropriately
        }
    }];
}
```
