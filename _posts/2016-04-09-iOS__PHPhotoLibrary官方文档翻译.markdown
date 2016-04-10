---
layout: post
title:  "iOS-PHPhotoLibrary官方文档中文翻译"
date:   2014-12-30 09:00:13
categories: github
permalink: /iOS-PHPhotoLibrary官方文档中文翻译
---

[官方文档](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHPhotoLibrary_Class/index.html)

[官方DEMO下载](https://developer.apple.com/library/ios/samplecode/UsingPhotosFramework/Listings/SamplePhotosApp_main_m.html#//apple_ref/doc/uid/TP40014575-SamplePhotosApp_main_m-DontLinkElementID_21)

| 继承自       | 遵循           |  导入  | 获取  |
| ------------- |:-------------:| -----:|
| NSObject      | NSObject   |  @import Photos; |iOS8及以后 |

单例类`PHPhotoLibrary`可以获取到用户的照片，自带应用"照片"里管理的所有照片和相册，它都能获取到，不仅包括存储在本地的照片，甚至可以获取到存储在"iCloud"上的照片。你可以用这个对象修改照片，插入新照片，或重新整理相册。(照片实体就是这些类的实例:[PHAsset](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAsset_Class/index.html#//apple_ref/occ/cl/PHAsset),[PHAssetCollection](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetCollection_Class/index.html#//apple_ref/occ/cl/PHAssetCollection),[PHCollectionList](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHCollectionList_Class/index.html#//apple_ref/occ/cl/PHCollectionList))。你也可以用照片库对象注册通知，当相册发生改变时，你可以收到通知。

*  PHAsset：一张照片或一个视屏
*  PHAssetCollection：一个相册
*  PHCollectionList：一个文件夹，里面有相册列表


### 发出修改请求，操作图库 ###


照片实体是不可修改的，所以如果你想修改图库内容，你需要用`[PHPhotoLibrary sharedPhotoLibrary]`单例来执行一个方法，在这个方法里自带block参数，你要做的就是在这个block里执行对图库的修改操作。具体方法的使用在这里([方法列表](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHPhotoLibrary_Class/index.html#//apple_ref/doc/uid/TP40014404-CH1-SW2))。当Photos运行完block中的修改时，你的修改就生效了，接下来会调用回调函数。


这几个可以发送修改请求的类:[PHAssetChangeRequest](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetChangeRequest_Class/index.html#//apple_ref/occ/cl/PHAssetChangeRequest)(操作相片或视频), [PHAssetCollectionChangeRequest](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetCollectionChangeRequest_Class/index.html#//apple_ref/occ/cl/PHAssetCollectionChangeRequest)(操作相册) ,[PHCollectionListChangeRequest](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHCollectionListChangeRequest_Class/index.html#//apple_ref/occ/cl/PHCollectionListChangeRequest)(操作文件夹，文件夹里有相册列表)，都对应着一个照片实体类，使用这几个类可以修改对应的相册资源：

*   ** 添加新对象 ** 。`PHAssetChangeRequest`可以调用方法:[creationRequestForAssetFromImage: :](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetChangeRequest_Class/index.html#//apple_ref/occ/clm/PHAssetChangeRequest/creationRequestForAssetFromImage:)来创建一个新的照片或视频；`PHAssetCollectionChangeRequest`可以调用方法:[creationRequestForAssetCollectionWithTitle: ](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetCollectionChangeRequest_Class/index.html#//apple_ref/occ/clm/PHAssetCollectionChangeRequest/creationRequestForAssetCollectionWithTitle:)来创建一个新的相册;`PHCollectionListChangeRequest`可以调用方法:[creationRequestForCollectionListWithTitle:](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHCollectionListChangeRequest_Class/index.html#//apple_ref/occ/clm/PHCollectionListChangeRequest/creationRequestForCollectionListWithTitle:)来创建一个文件夹。


	  如果你想获得刚刚新建的对象，请使用[PHObjectPlaceholder](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHObjectPlaceholder_Class/index.html#//apple_ref/occ/cl/PHObjectPlaceholder)类。当block完成后，使用placeholder类的[localIdentifier](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHObject_Class/index.html#//apple_ref/occ/instp/PHObject/localIdentifier)属性来获取到这个刚刚创建的类。具体用法请参阅它的文档。

*  ** 删除对象。 **  每一个请求类都提供了对应的删除方法来删除对应的资源。比如，`PHAssetCollectionChangeRequest`调用[deleteCollectionLists:](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHCollectionListChangeRequest_Class/index.html#//apple_ref/occ/clm/PHCollectionListChangeRequest/deleteCollectionLists:)方法可以删除相册文件夹。

*  **  修改对象。 **  你可以用对应的类来修改相片或者相册。比如，你可以使用`PHAssetChangeRequest`的[changeRequestForAsset:](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetChangeRequest_Class/index.html#//apple_ref/occ/clm/PHAssetChangeRequest/changeRequestForAsset:)方法来修改某一张照片的属性，比如是否被收藏。
    
    执行完修改请求之后，使用它的属性或者实例方法来修改对应相册和相片的特点。比如，要设置一张照片的[favorite](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAsset_Class/index.html#//apple_ref/occ/instp/PHAsset/favorite)属性，你可以设置那张照片的改变请求的[favorite](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAsset_Class/index.html#//apple_ref/occ/instp/PHAsset/favorite)属性。要添加一个相册，用相册的改变请求调用 addAssets:方法。
    
    
    
    
  Use a change block to combine several changes to the photo library into a single atomic update. Listing 1 illustrates using a change block to create an asset from an image and add that asset to an album.
  
  **  Listing 1 **  新建一张图片并把它加到相册里去

	  

    - (void)addNewAssetWithImage:(UIImage *)image toAlbum:(PHAssetCollection *)album
    {
        [[PHPhotoLibrary sharedPhotoLibrary] performChanges:^{
            // Request creating an asset from the image.
            PHAssetChangeRequest *createAssetRequest = [PHAssetChangeRequest creationRequestForAssetFromImage:image];
     
            // Request editing the album.
            PHAssetCollectionChangeRequest *albumChangeRequest = [PHAssetCollectionChangeRequest changeRequestForAssetCollection:album];
     
            // Get a placeholder for the new asset and add it to the album editing request.
            PHObjectPlaceholder *assetPlaceholder = [createAssetRequest placeholderForCreatedAsset];
            [albumChangeRequest addAssets:@[ assetPlaceholder ]];
     
        } completionHandler:^(BOOL success, NSError *error) {
            NSLog(@"Finished adding asset. %@", (success ? @"Success" : error));
        }];
    }



>注意：
> [performChanges:completionHandler:](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHPhotoLibrary_Class/index.html#//apple_ref/occ/instm/PHPhotoLibrary/performChanges:completionHandler:)和[performChangesAndWait:error:](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHPhotoLibrary_Class/index.html#//apple_ref/occ/instm/PHPhotoLibrary/performChangesAndWait:error:)里面的方法在调用的时候，系统会弹出警告框请求获取"相册"的使用权限，如果你的app需要一次提交多个改变，请将他们放在这一个block里。
>
>
>  例如，想要一次性的添加好几张照片，你可以在[creationRequestForAssetFromImage:](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetChangeRequest_Class/index.html#//apple_ref/occ/clm/PHAssetChangeRequest/creationRequestForAssetFromImage:)方法里创建多个[PHAssetChangeRequest](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetChangeRequest_Class/index.html#//apple_ref/occ/cl/PHAssetChangeRequest)对象。
>  
>　For example, to add several new images in one batch, extend the code in Listing 1 to create multiple PHAssetChangeRequest objects using the creationRequestForAssetFromImage: method. To edit the content of multiple existing photos, create multiple PHAssetChangeRequest objects and set the contentEditingOutput property on each to an independent PHContentEditingOutput object.

###  监听 ###

如果想要在相册发生改动的时候收到通知，使用[registerChangeObserver: ](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHPhotoLibrary_Class/index.html#//apple_ref/occ/instm/PHPhotoLibrary/registerChangeObserver:)方法来指定一个观察者对象。每当你使用fetch方法去检检索相册时，比如[fetchAssetsWithOptions:](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAsset_Class/index.html#//apple_ref/occ/clm/PHAsset/fetchAssetsWithOptions:),Photo会自动注册观察者。fetch方法执行完之后，结果集中的任何对象发生改变，你都会收到通知，包括增加，移除，重新排列等。


处理改变的更多细节，请参考[PHPhotoLibraryChangeObserver Protocol Reference](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHPhotoLibraryChangeObserver_Protocol/index.html#//apple_ref/doc/uid/TP40014405)

