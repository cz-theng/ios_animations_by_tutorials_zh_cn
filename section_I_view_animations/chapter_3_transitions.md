# 第三章：转场效果
通过前面的两章两章的学习，你已经学会如何使用view的属性如位置、透明度等进行动画播放。但是对于view的进出动画要怎么做呢？

使用之前章节中用的将view移入或者移除用户视野是一个可行的方法。然后本章将会教授另一种改变view呈现状态的动画方法：转场动画。

转场效果是一种view的预定义动画，这些预定义动画不会在view开始到结束中间产生效果，相反的，他只会影响view出现时的效果。

## 转场示例
为了更好的理解何时该使用转场动画，这一小节会先带你领略各种使用转场动画的场景。

###添加一个新的View

![chatper3_01](./images/chapter3_01.png)

通过调用和之前章节类似的API，可以为屏幕上新出现的view增加动画，不同的时，这次需要制定一个名为"animation container view"转场动画效果.

这个转场动画将作用在容器View和其容纳的所有子View上，请看如下代码片段：

	var animationContainerView: UIView?	override func viewDidLoad() {		//set up the animation container 
		animationContainerView = UIView(frame: view.bounds) 		animationContainerView!.frame = view.bounds view.addSubview(animationContainerView!)	}	override func viewDidAppear(animated: Bool) {		super.viewDidAppear(animated)
		//create new view		let newView = UIImageView(image: UIImage(named: "banner")!) newView.center = animationContainerView!.center	
		//add the new view via transition		UIView.transitionWithView(animationContainerView!, 
			duration: 0.33,			options: [.CurveEaseOut, .TransitionFlipFromBottom], animations: {				self.animationContainerView!.addSubview(newView) 
		}, completion: nil)	}
这里假设应用中有这样的一个场景，在你的应用的"View Controller"里面“viewDidLoad()”里面创建一个名叫“animationContainerView"的View，然后将这个view添加到界面中并做好定位。
然后，现在你需要再创建一个带有转场效果的新View，假设是新View为“newView”。
通过调用`transitionWithView(_:duration: options:animations:completion:)`创建转场动画，这里创建的代码于UIView中的标准动画几乎完全一致，所不同的就是新增了一个"view"参数，用来表示转场动画的容器View。
这里还用到了一个没有用过的动画选项：.TransitionFlipFromBottom ，它和前面篇章中介绍的动画选项一样是一个预定义的值。".TransitionFlipFromBottom"使得view从屏幕的底部飞出来，就想从一扇门中飞出来时的那个门一样。
最后，在动画的block中为容器View添加上要新增的子View，从而使其按照预定的动画效果出现。
这里罗列了所有的转场动画的效果：
	.TransitionFlipFromLeft 
	.TransitionFlipFromRight 
	.TransitionCurlUp 
	.TransitionCurlDown 
	.TransitionCrossDissolve 
	.TransitionFlipFromTop 
	.TransitionFlipFromBottom

###删除一个view

![chapter3_02](./images/chapter3_02.png)

为从屏幕中移除一个View添加动画效果和上面的新增一个View的动画效果是类似的。只要将动画效果block里面的函数改成`removeFromSuperview()`就可以了，如：

	//remove the view via transition	UIView.transitionWithView(animationContainerView!, 
		duration: 0.33,		options: [.CurveEaseOut, .TransitionFlipFromBottom], animations: {			self.newView.removeFromSuperview() 
		}, 
		completion: nil)
和前面的例子一样，闭包中的转场动画效果将会作用在“newView”的退出时。		
		