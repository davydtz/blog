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


这几个可以发送修改请求的类:`PHAssetChangeRequest`, `PHAssetCollectionChangeRequest`, ,`PHCollectionListChangeRequest`，都对应着一个照片实体类，使用这几个类可以修改对应的相册资源：

未完待续。。。