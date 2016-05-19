#IOS 最全动画教程（基础）
大家好，做了这么久的iOS开发，没有系统性的总结过iOS某方面的知识点，总觉得有些地方还是不太了解，查阅各种资料，加上平时开发的经验，因此准备写关于动画系列的总结。作为开发者，我希望大家能尽可能的提高自己在APP动画方面的一些体验，多玩玩一些比较好的APP，在了解iOS动画的知识后，可以试着仿写一些动画设计。本教程的前半部分，我会大概介绍下IOS动画的整体框架，分为哪几个部分，好让开发者可以了解动画的大概，从一个过来人讲，动画在很多人的眼里，可能最明白的就是，将视图A从A点移动到B点，改变视图A的Frame。很多概念都很模糊，相信大家看了我的这个教程后，会走出动画方面的迷茫。后半部分的话，会以一些动画实例来演示如何利用我们的简单的动画组合起来实现一些复杂的动画效果。
## UIView动画
### 1、UIView 类方法动画
```[UIView beginAnimations:<#(nullable NSString *)#> context:<#(nullable void *)#>]```  
这行代码在`UIView`的`UIViewAnimation`分类里面，IOS2.0以上就已经支持了。鉴于目前`UIView`的动画实现基本都用Block方式实现，这里就不多叙述分类方法了。以免让大家更加混淆，增加学习动画的成本。
### 2、UIView Block动画
大家可以翻到`UIView.h`头文件里面去看一下，其中有个`UIViewAnimationWithBlocks`的分类，就是对`UIView`Block动画的扩展。
  
2.1 `UIView`属性动画的Block，提供了动画执行时间 `duration `,动画延时执行时间`delay `，动画执行Block代码块，已经动画完成回调block块`completion `。可以执行动画的属性有以下几种，
> * 大小变化(frame)
> * 拉伸变化(bounds)
> * 中心位置(center)
> * 旋转(transform)
> * 透明度(alpha)
> * 背景颜色(backgroundColor)
> * 拉伸内容(contentStretch)

对Block不熟悉的朋友可以去看下IOS Block相关知识。
```+ (void)animateWithDuration:(NSTimeInterval)duration delay:(NSTimeInterval)delay options:(UIViewAnimationOptions)options animations:(void (^)(void))animations completion:(void (^ __nullable)(BOOL finished))completion
```

2.2 `UIView`弹簧动画的Block,iOS7.0后新增Spring动画（iOS系统动画大部分采用Spring Animation，适用于所有可被添加动画效果的属性）  ```
	[UIView animateWithDuration:(NSTimeInterval)//动画持续时间
                       delay:(NSTimeInterval)//动画延迟执行的时间
      usingSpringWithDamping:(CGFloat)//震动效果，范围0~1，数值越小震动效果越明显
       initialSpringVelocity:(CGFloat)//初始速度，数值越大初始速度越快
                     options:(UIViewAnimationOptions)//动画的过渡效果
                  animations:^{  
                     //执行的动画  
	 }  
                  completion:^(BOOL finished) {  
                     //动画执行完毕后的操作  
	 }];
```
2.3 `UIView`关键帧动画Block，IOS7+
```
[UIView animateKeyframesWithDuration:(NSTimeInterval)//动画持续时间
                                delay:(NSTimeInterval)//动画延迟执行的时间
                              options:(UIViewKeyframeAnimationOptions)//动画的过渡效果
                           animations:^{
                         //执行的关键帧动画
 }
                           completion:^(BOOL finished) {
                         //动画执行完毕后的操作
 }];
```
2.4 `UIView`转场动画Block
> * 单个视图的转场动画
```
+ (void)transitionWithView:(UIView *)view duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options animations:(void (^ __nullable)(void))animations completion:(void (^ __nullable)(BOOL finished))completion
```
> * 从旧视图转到新视图的动画效果
```
+ (void)transitionFromView:(UIView *)fromView toView:(UIView *)toView duration:(NSTimeInterval)duration options:(UIViewAnimationOptions)options completion:(void (^ __nullable)(BOOL finished))completion
```

## Core Animation 核心动画
[Core Animation官方说明](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Introduction/Introduction.html)  
###IOS UI框架关系图
![](https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreAnimation_guide/Art/ca_architecture_2x.png)  
> * 从图中可以发现，最底层是硬件层，而我们最常用的UIKit框架层位于最顶部，下面有`Core Animation` 层，该层是作用于CALayer的。  

###Core Animation类图以及常用字段。
![](http://img.my.csdn.net/uploads/201507/23/1437617562_3190.png)

常用属性 
> * duration : 动画的持续时间 
> * beginTime : 动画的开始时间 
> * repeatCount : 动画的重复次数 
> * autoreverses : 执行的动画按照原动画返回执行 
> * timingFunction : 控制动画的显示节奏系统提供五种值选择，分别是：  

> > 
	kCAMediaTimingFunctionLinear 线性动画
	kCAMediaTimingFunctionEaseIn 先慢后快（慢进快出）
	kCAMediaTimingFunctionEaseOut 先块后慢（快进慢出）
	kCAMediaTimingFunctionEaseInEaseOut 先慢后快再慢
	kCAMediaTimingFunctionDefault 默认，也属于中间比较快
	
> * path：关键帧动画中的执行路径 
> * type ： 过渡动画的动画类型，系统提供了四种过渡动画。 

> >
	kCATransitionFade 渐变效果 
	kCATransitionMoveIn 进入覆盖效果 
	kCATransitionPush 推出效果 
	kCATransitionReveal 揭露离开效果 
	
> * subtype : 过渡动画的动画方向  

> >
	kCATransitionFromRight 从右侧进入 
	kCATransitionFromLeft 从左侧进入 
	kCATransitionFromTop 从顶部进入 	
	kCATransitionFromBottom 从底部进入
	
### 1.基础动画（CABasAnimation）
继承`CAPropertyAnimation`类，从名字来看就知道是属性动画，基础动画主要提供了对于CALayer对象中的可变属性进行简单动画的操作。比如：位移、透明度、缩放、旋转、背景色等等。
### 2.关键帧动画（CAKeyframeAnimation）
继承`CAPropertyAnimation`类，计算关键帧动画的类，属性有`Values`,`path`,`keyTimes`,`timingFunctions`,`calculationMode`,`tensionValues`,`continuityValues`,`biasValues`,`rotationMode` 
`CAKeyframeAnimation`是`CApropertyAnimation`的子类，跟`CABasicAnimation`的区别是：`CABasicAnimation`只能从一个数值`fromValue`变到另一个数值`toValue`，而`CAKeyframeAnimation`会使用一个`NSArray`保存这些数值。
属性分析：
> * `values`：就是上述的`NSArray`对象。里面的元素称为”关键帧”(`keyframe`)。动画对象会在指定的时间`duration`内，依次显示`values`数组中的每一个关键帧。  
> * `path `：可以设置一个`CGPathRef` `CGMutablePathRef`,让层跟着路径移动。`path`只对`CALayer`的`anchorPoint`和`position`起作用。如果你设置了`path`，那么`values`将被忽略。  
> * `keyTimes `：可以为对应的关键帧指定对应的时间点,其取值范围为0到1.0,`keyTimes`中的每一个时间值都对应`values`中的每一帧.当`keyTimes`没有设置的时候,各个关键帧的时间是平分的。

**说明：** `CABasicAnimation`可看做是最多只有2个关键帧的`CAKeyframeAnimation`

calculationMode常量
> * kCAAnimationLinear
> * kCAAnimationDiscrete
> * kCAAnimationPaced
> * kCAAnimationCubic
> * kCAAnimationCubicPaced

rotationMode常量
> * kCAAnimationRotateAuto
> * kCAAnimationRotateAutoReverse

### 3.组动画（CAAnimationGroup）

继承`CAAnimation`类，包含一系列动画子类的动画组，称为组合动画。  
```@property(nullable, copy) NSArray<CAAnimation *> *animations;```
一个`CAAnimation`类型的动画数组，比如包含了基础动画里面的放大和颜色变化的动画或是位移变化的动画等。

### 4.过渡动画（CATransition）
继承`CAAnimation`类，称为过渡动画，过渡顾名思义就是视图之间的切换产生的动画，视图A切换到B，A会消失而现实B，这之间的动画就是过渡动画，这种情况也很多，比如在`UIVContrller`切换时候，每个控制器对应视图的切换动画。当往 `UINavigationController`里面push一个`UIViewController`时候，会产生从右往左的视图切换的动画效果，这个也称为过渡动画，当然我们是可以修改系统默认的push动画的方式的。属性有：`type`,`subtype`,`startProgress`,`endProgress`,`filter`

type 类型常量
> * kCATransitionFade
> * kCATransitionMoveIn
> * kCATransitionPush
> * kCATransitionReveal

subtypes 过渡类型常量
> * kCATransitionFromRight
> * kCATransitionFromLeft
> * kCATransitionFromTop
> * kCATransitionFromBottom

### 5.弹簧动画（CASpringAnimation）
![](http://upload-images.jianshu.io/upload_images/318313-9fb700de65aa5273.gif?imageMogr2/auto-orient/strip)

继承`CABasicAnimation` 类，称为弹簧动画。IOS9+，属性有:  

	@property CGFloat mass; // 弹簧质量，必须大于0，默认值为1，（影响弹簧的惯性，质量越大，弹簧惯性越大，运动的幅度越大）
	@property CGFloat stiffness;// 弹簧弹性系数，必须大于0，默认为100,（弹性系数越大，弹簧的运动越快）
	@property CGFloat damping;// 弹簧阻尼系数，大于或等于0，默认为10,（阻尼系数越大，弹簧的停止越快）
	@property CGFloat initialVelocity; // 初始速率（弹簧动画的初始速度大小，弹簧运动的初始方向与初始速率的正负一致，若初始速率为0，表示忽略该属性）
	@property(readonly) CFTimeInterval settlingDuration; // 结算时间（根据动画参数估算弹簧开始运动到停止的时间，动画设置的时间最好根据此时间来设置）
	
CASpringAnimation和UIView的SpringAnimation对比：
> * CASpringAnimation 可以设置更多影响弹簧动画效果的属性，可以实现更复杂的弹簧动画效果，且可以和其他动画组合。
> * UIView的SpringAnimation实际上就是通过CASpringAnimation来实现。


以上就是IOS动画的简要介绍，不足的地方欢迎补充。最后附上几个比较好的帖子：  
> 1. [IOS动画篇：核心动画](http://www.cocoachina.com/ios/20160517/16290.html)  
> 2. [IOS动画浅汇](http://www.cocoachina.com/ios/20160311/15660.html)  
> 3. [IOS开发UI篇--IOS动画（Core Animation）总结](http://blog.csdn.net/yixiangboy/article/details/47016829)  
> 4. [iOS动画学习总结](http://www.jianshu.com/p/a4816cdfecc9)
