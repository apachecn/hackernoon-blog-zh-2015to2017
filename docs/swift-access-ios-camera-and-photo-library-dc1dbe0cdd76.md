# SWIFT —访问 iOS 相机和照片库

> 原文：<https://medium.com/hackernoon/swift-access-ios-camera-and-photo-library-dc1dbe0cdd76>

## 在 Swift 中完成的复制粘贴解决方案

![](img/e255e5ce9b8911a52565fbbc559469f8.png)

访问 iOS 相机和照片库可能是我们开发的几乎每个应用程序中最常见的功能之一。这就是为什么我们必须确保我们做得正确，并且我们有一个随时可以重用的自定义类。

在本教程中，我将向您展示如何在 [Swift](https://hackernoon.com/tagged/swift) 中创建自定义类，并且在您需要时随时可以使用。如果你懒于阅读整篇教程，你可以在这篇文章的底部下载一个要点文件。我将把这个类命名为 **CameraHandler.swift.**

# 访问 iOS 相机和照片图库

我将从创建两个函数开始，第一个将被命名为 camera()，第二个将被命名为 photoLibrary()。对于这两种情况，我们都使用了[**UIImagePickerController**](https://developer.apple.com/documentation/uikit/uiimagepickercontroller)类，我们需要做的就是将 **sourceType** 属性更改为合适的属性。

## **摄像机()**

```
func camera()
    {
        if UIImagePickerController.isSourceTypeAvailable(.camera){
            let myPickerController = UIImagePickerController()
            myPickerController.delegate = self;
            myPickerController.sourceType = .camera
            currentVC.present(myPickerController, animated: true, completion: nil)
        }

    }
```

## **照片库()**

```
func photoLibrary()
    {

        if UIImagePickerController.isSourceTypeAvailable(.photoLibrary){
            let myPickerController = UIImagePickerController()
            myPickerController.delegate = self;
            myPickerController.sourceType = .photoLibrary
            currentVC.present(myPickerController, animated: true, completion: nil)
        }
    }
```

如您所见，sourceType 更改为**。摄像机**和**。照片图库**类型。我们将需要**UIImagePickerControllerDelegate**和**UINavigationControllerDelegate**，以便我们截取用户选择的图像。下面我将解释一下 **currentVC** 属性。

# 创建 UIActionSheet

接下来，我们要做的是创建一个函数，在一个简单的[**ui action sheet**](https://developer.apple.com/documentation/uikit/uiactionsheet)中向用户呈现这两个选项。

## **showActionSheet()**

```
func showActionSheet(vc: UIViewController) {
        currentVC = vc
        let actionSheet = UIAlertController(title: nil, message: nil, preferredStyle: .actionSheet)

        actionSheet.addAction(UIAlertAction(title: "Camera", style: .default, handler: { (alert:UIAlertAction!) -> Void in
            self.camera()
        }))

        actionSheet.addAction(UIAlertAction(title: "Gallery", style: .default, handler: { (alert:UIAlertAction!) -> Void in
            self.photoLibrary()
        }))

        actionSheet.addAction(UIAlertAction(title: "Cancel", style: .cancel, handler: nil))

        vc.present(actionSheet, animated: true, completion: nil)
    }
```

这是您需要调用的唯一函数，以便显示 iOS 相机和照片库。它显示了一个简单的 UIActionSheet，两个选项都可用。此外，我们正在传递一个名为 **vc** 的参数，该参数将被传递给 **currentVC** 私有属性。我们这样做是为了直接从类内部处理控制器的表示。

> *注:如果设备不支持相机或照片库，当您按下某个选项时，将不会有任何反应。例如，在模拟器上测试相机功能。*

# 委托方法

在文件的末尾，我们将创建一个 [**扩展**](http://theappspace.com/i-%E2%9D%A4-swift-part-1-organize-uiviewcontroller-classes-by-using-extensions/) ，我们将调用属于 UIImagePickerControllerDelegate 的委托方法**difinishpickingmedianifo**和**imagepickercontrollerdicancel**。

```
extension CameraHandler: UIImagePickerControllerDelegate, UINavigationControllerDelegate{
    func imagePickerControllerDidCancel(_ picker: UIImagePickerController) {
        currentVC.dismiss(animated: true, completion: nil)
    }

    func imagePickerController(_ picker: UIImagePickerController, didFinishPickingMediaWithInfo info: [String : Any]) {
        if let image = info[UIImagePickerControllerOriginalImage] as? UIImage {
            self.imagePickedBlock?(image)
        }else{
            print("Something went wrong")
        }
        currentVC.dismiss(animated: true, completion: nil)
    }
}
```

我们现在能够从照片库中获得挑选的图像或从相机中捕获的图像。为了使事情更简单明了，我创建了一个名为 **imagePickedBlock()** 的闭包，它将为我们提供我们需要的图片。这里是你需要的所有属性。

```
static let shared = CameraHandler()

fileprivate var currentVC: UIViewController!

//MARK: Internal Properties
var imagePickedBlock: ((UIImage) -> Void)?
```

# 怎么用？

我们已经完成了类的创建，现在我们需要使用它。像这样的类的优点是易于重用。

```
CameraHandler.shared.showActionSheet(vc: self)
CameraHandler.shared.imagePickedBlock = { (image) in
    /* get your image here */
}
```

这就是本教程中向您展示如何在 Swift 3 中访问 iOS 相机和照片库的内容，我真的希望它对您有所帮助。如果你有任何问题，请在评论区分享这篇文章作为支持或评论。

# 完整的要点文件

## 这就是本教程的内容，如果对你有帮助，请👏或者分享这个故事，让其他像你一样的人也能找到它。感谢您的关注！🚀

[![](img/79c8aca42472a7e427ec524f7bc604fb.png)](http://bit.ly/2KkkwGL)

## 查看我的最新项目:

[](https://apps.apple.com/app/football-score-prediction-bets/id1517623538) [## 足球比分预测赌注

### 我们提供专业的足球比分预测投注提示，胜率高。每个比赛模拟包含一个…

apps.apple.com](https://apps.apple.com/app/football-score-prediction-bets/id1517623538) [](https://play.google.com/store/apps/details?id=betting.tips.goals) [## 足球博彩技巧和比分——赌进球 Google Play 上的应用程序

### 我们提供高胜率的专业足球技巧。每个比赛模拟包含一个预测，代表…

play.google.com](https://play.google.com/store/apps/details?id=betting.tips.goals) [](https://apps.apple.com/app/zoom-profile-picture-stories/id1514925024) [## 缩放个人资料图片和故事

### ZoomPic for Instagram 是匿名查看大型高清个人照片和故事的最简单、最方便的方式…

apps.apple.com](https://apps.apple.com/app/zoom-profile-picture-stories/id1514925024) [](https://apps.apple.com/app/long-video-story-for-instagram/id1511499427) [## Instagram 的长视频故事

### Instagram 的 Long Story 是一个非常棒的工具，允许您录制或选择任意长度的视频并进行转换…

apps.apple.com](https://apps.apple.com/app/long-video-story-for-instagram/id1511499427) 

## 阅读更多我在媒体上的作品:

[](https://hackernoon.com/introducing-clean-swift-architecture-vip-770a639ad7bf) [## 引入干净的 Swift 架构(VIP)

### 忘了 MVC 吧，现在！

hackernoon.com](https://hackernoon.com/introducing-clean-swift-architecture-vip-770a639ad7bf) [](https://medium.freecodecamp.org/how-you-can-use-the-google-maps-sdk-with-ios-using-swift-4-a9bba26d9c4d) [## 使用 Swift 4 在 iOS 上使用 Google Maps SDK 的终极指南

### 许多 iOS 应用程序使用谷歌地图。这是一个非常普遍的特点，所以我决定准备一个终极指南…

medium.freecodecamp.org](https://medium.freecodecamp.org/how-you-can-use-the-google-maps-sdk-with-ios-using-swift-4-a9bba26d9c4d) [](/theappspace/swift-custom-uiview-with-xib-file-211bb8bbd6eb) [## SWIFT —带有 XIB 文件的自定义 UIView

### 用 XIB 文件定制 UIView 是 iOS 开发中非常常见的做法。自定义 UIView 类不包含 XIB 文件…

medium.com](/theappspace/swift-custom-uiview-with-xib-file-211bb8bbd6eb) [](https://hackernoon.com/how-to-add-spotlight-support-to-your-ios-app-4a89054aff89) [## 如何将 Spotlight 支持添加到您的 iOS 应用程序

### Swift 教程将使您的应用程序在 Spotlight search 中可用

hackernoon.com](https://hackernoon.com/how-to-add-spotlight-support-to-your-ios-app-4a89054aff89) [](https://hackernoon.com/core-data-relationships-d813ed66ba8c) [## 核心数据关系

### 理解一对一和一对多关系

hackernoon.com](https://hackernoon.com/core-data-relationships-d813ed66ba8c) [](https://hackernoon.com/understanding-auto-layout-in-xcode-9-2719710f0706) [## 了解 Xcode 9 中的自动布局

### 所有你需要知道的关于自动布局

hackernoon.com](https://hackernoon.com/understanding-auto-layout-in-xcode-9-2719710f0706) 

## 订阅我的时事通讯: