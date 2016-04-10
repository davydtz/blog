---
layout: post
title:  "iOS-PHPhotoLibrary官方文档中文翻译"
date:   2014-12-30 09:00:13
categories: github
permalink: /iOS-PHPhotoLibrary官方文档中文翻译
---

[官方文档地址](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHPhotoLibrary_Class/index.html)

| 继承自       | 遵循           |  导入  | 获取  |
| ------------- |:-------------:| -----:|
| NSObject      | NSObject   |  @import Photos; |iOS8及以后 |

单例类`PHPhotoLibrary`可以获取到用户的照片图库-自带应用"照片"里管理的所有照片，都能获取到，不仅包括存储在本地的照片，甚至可以获取到存储在"iCloud"上的照片。You use this photo library object to perform changes to photo entities—for example, editing asset metadata or content, inserting new assets, or rearranging the members of a collection. (Photo entities are objects that model the items a user works with in the Photos app: instances of the PHAsset, PHAssetCollection, and PHCollectionList classes.) You also use the photo library object to register for messages that Photos sends whenever changes occur to the content or metadata of assets and collections.

### 对图片库进行改动 ###


照片实体是不可修改的，所以如果你想修改这些照片或者收藏，你需要使用图片库单例来执行一个block，在这个block里，你可以创建一个修改请求。你可以使用这些方法([方法列表](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHPhotoLibrary_Class/index.html#//apple_ref/doc/uid/TP40014404-CH1-SW2))来创建这种block。当block里面的方法执行完成并且回掉函数也执行完成了，你的修改请求也成功了。


这几个可以发送修改请求的类:[PHAssetChangeRequest](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetChangeRequest_Class/index.html#//apple_ref/occ/cl/PHAssetChangeRequest), [PHAssetCollectionChangeRequest](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetCollectionChangeRequest_Class/index.html#//apple_ref/occ/cl/PHAssetCollectionChangeRequest), ,[PHCollectionListChangeRequest](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHCollectionListChangeRequest_Class/index.html#//apple_ref/occ/cl/PHCollectionListChangeRequest)，都对应着一个照片实体类，使用这几个类可以修改对应的相册资源：

*   ** 添加新对象 ** 。每一个改变请求类都提供方法来创建其对应的实体。比如，使用[creationRequestForAssetCollectionWithTitle:](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetCollectionChangeRequest_Class/index.html#//apple_ref/occ/clm/PHAssetCollectionChangeRequest/creationRequestForAssetCollectionWithTitle:)方法可以创建一个新的相册。

      如果你想获取到刚刚在block中创建的请求-比如，向相册里添加了一张照片-你可以使用这个请求提供的[PHObjectPlaceholder](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHObjectPlaceholder_Class/index.html#//apple_ref/occ/cl/PHObjectPlaceholder)类。当block完成后，使用placeholder类的[localIdentifier](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHObject_Class/index.html#//apple_ref/occ/instp/PHObject/localIdentifier)属性来获取到这个刚刚创建的类。

*  ** 删除对象。 **  每一个请求类都提供了对应的删除方法来删除对应的资源。比如，使用[deleteCollectionLists:](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHCollectionListChangeRequest_Class/index.html#//apple_ref/occ/clm/PHCollectionListChangeRequest/deleteCollectionLists:)方法删除相册文件夹。

*  **  修改对象。 **  你可以修改创建对应的对象实体来修改相片或者相册。比如，你可以使用[changeRequestForAsset:](https://developer.apple.com/library/ios/documentation/Photos/Reference/PHAssetChangeRequest_Class/index.html#//apple_ref/occ/clm/PHAssetChangeRequest/changeRequestForAsset:)方法来修改某一张照片。
    
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


未完待续。。。