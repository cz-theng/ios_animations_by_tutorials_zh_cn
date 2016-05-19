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

###隐藏或者展现一个view

![chapter3_03](./images/chapter3_03.png)

到目前为止，你所学的转场动画还只局限在改变view的层级关系上，这也是为什么总是需要一个容器view来表示层级关系。

现在我们来看一个不需要用容器view方式来隐藏和展现一个view。此时，带有转场动画效果的view用其自身作为容器。

下面的代码展示了怎样给隐藏一个子view添加转场动画：

	//hide the view via transition	UIView.transitionWithView(self.newView, duration: 0.33, options: 
	[.CurveEaseOut, .TransitionFlipFromBottom], animations: {
			self.newView.hidden = true 
	}, completion: nil)		
这里给`transitionWithView(_:duration:options:animations: completion:)`传递的第一个参数表示需要隐藏或者呈现的view。接着只要在动画block里面设置view的"hide"属性即可。
###用一个view替换另一个view
![chapter3_04](./images/chapter3_04.png)
用一个view替换另一个view的操作也非常简单。只要在第一个参数中传递要被替换的view，然后将替换的view传给"toView:" ，如下代码：

	//replace via transition	UIView.transitionFromView(self.oldView!, toView: self.newView!, 
	duration: 0.33, options: [.TransitionFlipFromTop],	completion: nil)由此可见强大的UIKit可以带来多么丰富的效果。
在本章中，你学会了一些新的动画效果，比如实践了如何为呈现或者隐藏view添加转场动画。
##组合转场效果
现在让我们接着完成“Bahama Air”项目的登录界面。之前我们已经为登录界面创建了一系列的动画了，包括登录表单的跳动、登录按钮按下的效果等。

下面，我们将模拟一些用户鉴权并动态的呈现一些进度信息。当用户点击登录按钮的时候，根据情况依次展示“Connection”，“Authorizing”以及“Failed”。

![chapter3_05](./images/chapter3_05.png)

如果还没有学习完前面的章节，可以从本章的资源文件家中的开始工程（译者注：BahamaAir-Starter目录）。如果你依次完成了前面章节中的练习题，那么恭喜你，可以在你的工程上继续。

打开" ViewController.swift"可以看到“viewDidLoad()”里面的增加了一个类成员变量`status`来保存要隐藏的图片view,接着的代码创建了一个Label兵将其作为子view加载到`status`上。这里将用`status`想用户展示进度信息。要展示的消息存在类成员变量消息数组`messages`中。

在ViewController中添加如下代码：

	func showMessage(index index: Int) { 
		label.text = messages[index]
				UIView.transitionWithView(status, duration: 0.33, options: 
		[.CurveEaseOut, .TransitionCurlDown], animations: {			self.status.hidden = false 
		}, completion: {_ in			//transition completion		})
	}这个函数一个`index`参数，通过`index`来决定显示在label上的消息内容。
接下来在要添加动画效果的view上调用`transitionWithView(_:duration:options:animations: completion:)`。在动画block里面设置`hidden`为`false`可以以转场动画效果显示标语。
在上面的代码中添加了一个新的动画选项 `.TransitionCurlDown`。这个动画选项的效果是使view就像一摞纸落在地上时效果，效果如下：
![chapter3_06](./images/chapter3_06.png)
现在就可以在适当的地方调用上面的`showMessage(index:)`方法了。找到`login()`里面的block:
	animations: { 
		self.loginButton.bounds.size.width += 80.0	}, completion: nil)
	
这个block是在用户点击登录按钮的时候，让登录按钮弹出来的动画效果的blcok。现在需要在这个闭包中增加一个新的动画闭包，也就是这里的`showMessage(index:)`。将completion参数从nil改变成如下的函数闭包：
	completion: {_ in 		self.showMessage(index: 0)	}
这里的闭包接受一个Boolean类型参数，用于标示动画是否是成功执行完之后的的退出了还是中途被取消了而退出。在上面的例子中，我们并不关心动画是否被执行完成了，所以传递一个"_"参数，从而忽略其值。
在上面的闭包中，通过传递0给`showMessage`来显示消息数组中的第一个元素。
构建并运行工程，点击登录按钮，这里会看到状态标语里面会显示第一个阶段中的消息：

![chapter3_07](./images/chapter3_07.png)

这里你注意到标语是如何向一摞纸一样卷曲下来的么？这通常是让用户注意到一段静态文字的好方法。

> 注意： 有些动画让人觉得太快了，是不是？有时候需要在一个特定的地方按照一定的顺序显示一个动画，或者仅仅是想动画播的满一点，从而可以好好观察动画的播放过程。
>
> 在iPhone模拟其中选择“Debug“-》”Toggle Slow Animations in Frontmost App ”,可以让动画的播放过程慢下来。现在再来看看登录等会是如何展现的。

为了继续下一个动画，，这里首先要保存下标语的初始化位置，这样可以让下一个动画从同样的位置开始播放。

在"ViewController"类中添加如下属性:

	var statusPosition = CGPoint.zero


		