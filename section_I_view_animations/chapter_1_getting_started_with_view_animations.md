# 第一章：View动画初体验
在这一章，你降探索动画效果无底的界限。然后，请不要被本章的名称迷惑了：从如此强大和丰富的API开始意味着有大量的精彩片段去探索！

在这一章和其附带的练习工程，你将学到如下知识：
* 为酷效设置舞台
* 创建移动和淡入淡出效果
* 调整动画位置
* 翻转和重复动画

这个过程中将要通过一堆生疏概念，但是，我保证会很有趣，你准备好接受挑战了么？

![chapter1_01](./images/chapter1_01.png)
好了，现在开始吧。

## 第一个动画
打开本章对应的资源文件夹里面的开始工程（译者注：BahamaAir-Starter 目录）。构建并运行这个Xcode工程；将会
看到一个虚构的航空公司的登录界面，如下这样：

![chapter1_02](./images/chapter1_02.png)

这个app现在还没有太多功能： 只展示了一个带有一个标题和两个输入框登录表格以及一个大大的按钮。

除此之外还有一个漂亮的背景图片和四朵云。这几朵云已经连接到代码中的outlet变量了。

打开 *ViewController.swift* 看下，在文件的开头你将看到连接的outlets和类成员变量。再往下 看`viewDidLoad()`里面的一些初始化UI的代码，现在的工程已经准备好接下来的动画效果做改造了！

毫无疑问，通过上面的介绍，你已经准备好修改代码来查看效果了。

你的首要任务就是当用户打开应用时，将这个表单动态的呈现出来。由于现在这个App一打开的时候，就可以看到表单，因此首先要将这些元素移动到屏幕外面。

先在`viewWillAppear()`里面添加如下代码：

	heading.center.x -= view.bounds.width 
	username.center.x -= view.bounds.width 
	password.center.x -= view.bounds.width

这将把表单里面的各个元素移动到屏幕边缘的外面，效果如下：

![chapter1_03](./images/chapter1_03.png)

由于上面的代码将会在view controller出现之前执行，因此之前看到的输入框和标题就消失了。

构建并运行工程确保效果达成，如下图效果：

![chapter1_04](./images/chapter1_04.png)

完美。现在可以为这些表单组件出现在之前的位置增加动画效果了。

将如下代码添加到 `viewDidAppear()`：

	UIView.animateWithDuration(0.5, animations: {
		self.heading.center.x += self.view.bounds.width	})
	
通过调用UIView的类方法 `animateWithDuration(_:animations:)` 给标题添加动画效果。动画会立马开始并持续半秒钟；动画持续时间通过代码中的第一个参数来设置，是不是很简单。animations参数闭包中的修改将会被UIKit作为动画效果来展现。

构建并运行程序，将会看到标题缓缓的滑入到界面中：

![chapter1_05](./images/chapter1_05.png)

由于`animateWithDuration(_:animations:)`是个类方法，因此不限于只为一个view添加动画效果，实际上可以在animations的闭包里面为n多view添加动画效果。

同时吧下面的代码加入到animations 闭包中：

	self.username.center.x += self.view.bounds.width
	
再次构建并运行程序，将看到"username"输入框滑动到界面中：

![chapter1_06](./images/chapter1_06.png)

看到这两个view都带有动画效果虽然很酷，但是你可能注意到这两个view在同步移动过来有点不自然，像僵尸一样机械
的运动。

如果每个view都能按照他自己的节奏移动的话，比如一个比另一个延迟一段时间，这样效果是不是更生动呢？

首先将上面增加的"username"输入框的动画删除掉：

	UIView.animateWithDuration(0.5, animations: { 
		self.heading.center.x += self.view.bounds.width 
		
		~~self.username.center.x += self.view.bounds.width~~	})
	
然后，将如下代码添加到`viewDidAppear()`:

	UIView.animateWithDuration(0.5, delay: 0.3, options: [], animations: { 
		self.username.center.x += self.view.bounds.width	}, completion: nil)
这个类方法和之前的是类似的，不过新增了几个参数来自定义动画效果：
1. duration: 动画的持续时间
2. delay: UIKit延迟多久才开始执行动画，单位是秒
3. optons: 一个用来自定义动画选项的集。动画选项将会在后面介绍，限制只要传递一个空的“[]”表示“无选项”就可以了。
4. animations: 表示动画内容的闭包
5. completion: 当动画执行完后的执行的闭包；这个参数通常用于当你希望在动画执行完后执行一些清除工作或者连接其他动画时使用。

上面添加的代码中，通过添加delay参数使得"username"输入框的动画在标题动画开始后的0.3秒后才开始执行。

构建并运行代码，将看到如下效果：

![chapter1_07](./images/chapter1_07.png)

怎么样？现在效果更好了吧，现在还需要把"passwd"输入框也加上动画效果。

把如下代码添加到`viewDidAppear()`中：

	UIView.animateWithDuration(0.5, delay: 0.4, options: [], animations: { 
		self.password.center.x += self.view.bounds.width	}, completion: nil)
	
这里和“username”	输入框一样也增加一个延迟，只是要更长一些。

再次构建和运行程序，观察动画完成的次序：

![chatper1_08](./images/chapter1_08.png)

以上就是用UIKit为屏幕上的view增加动画要做的工作。

以上还只是开始，在后面的章节中，你将学到更多精彩的动画效果。

## 可以添加动画的属性
既然你已经了解到增加动画是如此简单，可能已经急于去了解还可以为view增加哪些动画效果。

这节将会介绍UIView中的哪些属性可以增加动画，并通过实际例子来展示效果。

并不是所有的属性都可以添加动画，但是所有的View动画，不论是简单动画还是复杂动画，可以添加动画效果的都罗列在下面的章节中。

### 位置和大小
![chapter1_09](./images/chapter1_09.png)

通过为view的位置和frame添加动画可以呈现出增长、收缩或者和之前一样的移动效果。下面是可以修改从而改变的view的位置和大小的属性：

* *bounds*：修改这个属性可以制造view的frame下的内容的动画
* *frame*: 修改这个属性可以制造view的移动和缩放动画
* *center*：修改这个属性可以制造移动view动另外一个地方的移动动画* 

别忘了Swift可以修改结构体的单个成员变量。这意味着你可以修改一个view的垂直坐标（center.y）使得其上下移动或者缩放通过frame.size.width属性。

###外观

![chapter1_10](./images/chapter1_10.png)

你可以通过修改背景或者设置半透明来改变view的外观。

* *backgroundColor*: 通过修改view的这个属性，可以随着时间的增长逐渐修改背景的色彩
* *alpah*：通过修改这个属性，可以实现淡入和淡出的效果

###变形
![chapter1_11](./images/chapter1_11.png)

变形和上面一样通过需改view的大小和位置来改变了view的形状。
* *transform*: 在animation的block中修改这个属性，可以展现view的旋转、缩放动画。  

这样通过修改缩放因子或者旋转角度就可以实现高效的修改view的形状而不用通过修改view的中心点和外框大小。

这些看起像是很基本的动画组成部分，但是你将会被他们实现的效果所震撼。

##动画选项
回头再看上面的代码，对于options参数都是传递“[]”表示没有选项。

options让你可以自定义UIKit创建动画的属性。上面已经通过delay和duration来设置动画的表现效果，除此之外还可以通过这个options更多的控制动画的表现。`UIViewAnimationOptions`罗列了所有的option，可以通过组合这些option来控制动画。

###重复
首先来下面的两个动画选项：
* *.Repeat*： 这个选项可以设置动画反复不停的播放
* *.Autoreverse*: 这个选项和`.Reapeat`进行组合，可以实现动画先正向播放然后在方向播放反复的播放

通过增加`.Repeat`修改之前 `viewDidAppear()`里面的代码：

	UIView.animateWithDuration(0.5, delay: 0.4, options: .Repeat, animations: { 
		self.password.center.x += self.view.bounds.width	}, completion: nil)
构建并运行查看新增的效果：
![chapter1_12](./images/chapter1_12.png)

表单的标签和"username"输入框飞入并最终安定到屏幕的中心，但是“password”输入框则不停的播放从外面飞入屏幕的动画。

再次修改上面的代码并加入".Repeat"和".Autoreverse"到options参数：

	UIView.animateWithDuration(0.5, delay: 0.4, options: [.Repeat, .Autoreverse], animations: {
			self.password.center.x += self.view.bounds.width 
	}, completion: nil)
	
如果想组合多个option，可以通过将各个option通过逗号组合并放到中括号括起来，然后传递给options参数。

>> 注意： 如果只设置一个option，Swift语法中，可以省略外面的中括号。然后你也可以不省略，从而暗示以后可能
增加的内容。这样，通过`[]`表示没有option,`[.Reapeat]`表示单个option,`[.Repeat, .Autorepeat]`表示多个option。

再次构建和运行，这次"password"输入框会进入退出反复的播放。

### 缓动动画
在真实生活中，东西不会突然开始或者停止运动，类似汽车、火车等物理对象都是慢慢的加速到目标速度，然后再缓缓的减速到完全停止，除非撞墙了才会立即停止下来。

下面的图描述了或者从启动到到达目的的过程：

![chapter1_13](./images/chapter1_13.png)

为了让你的动画更真实，你可以构建同样的缓慢启动和缓慢停下来的效果，或者叫术语中的"ease-in"和“ease-out”.

总共有四种缓动效果：

* *.Linear*：这个选项适用于没有加速度的动画，目前只有第三章的内容需要用这个效果
* *.CurveEaseIn*: 这个选项适用于开始时的加速动画
* *.CurveEaseOut*: 这个选项使用与结束时的减速动画
* *.CurveEaseInOut*:这个选项组合了上面的加速动画和简书动画

为了能更好的理解这些选项的效果，可以在上面的工程代码中添加这些选项，来看效果。

为上面的"password"输入框添加一个新的option：

	UIView.animateWithDuration(0.5, delay: 0.4,	options: [.Repeat, .Autoreverse, .CurveEaseOut], animations: {			self.password.center.x += self.view.bounds.width 
		}, completion: nil)
	
创建并运行，注意观察“password”输入框在进入后的慢慢退出屏幕的效果。

![chapter1_14](./images/chapter1_14.png)
增加了这个效果以后，整个动画看上去就更真实生动了。

现在来看与之相反的ease-in效果，通过将上面的代码中的.CurveEaseOut替换成.CurveEaseIn如下所示：

	UIView.animateWithDuration(0.5, delay: 0.4,	options: [.Repeat, .Autoreverse, .CurveEaseIn], animations: {
				self.password.center.x += self.view.bounds.width 
		}, completion: nil)

构建并运行，观察其如何机械的从最右边跳回去。这样依旧有点有点不自然并且也没有之前的好看。

最后使用.CurveEaseInOut： 组合了上面两钟效果产生的更自然的缓动效果，修改后尝试下。同事.CurveEaseInOut也是UIKit默认的动画选项。

在继续之前，将options修改成原来的`[]`：

	UIView.animateWithDuration(0.5, delay: 0.4, options: [], animations: {
			self.password.center.x += self.view.bounds.width 
		}, completion: nil)

现在你已经了解了最基本的动画工作方式，已经准备好去学习更复杂的动画效果。

从A点到B点的动画如何实现？ 是不是非常简单呢？

下一章，你讲学院怎样让你的动画拥有弹簧效果。

## 习题
如果这章是你第一次在iOS上实现动画效果，你可能有点晕乎。别担心，不论你的基础怎样，接着后面几章会慢慢变的清晰。

一路走到这，我们准备了一个非常简单的习题，通过完成这个习题，你讲创建出你自己的动画效果。

###习题一： 淡入云朵
在ViewController(译者注：代码中的类ViewController)你有4个outlets： cloud1、cloud2、 cloud3、cloud4 你的任务就是在应用启动的时候将这些云朵淡入出来。

可能有多种方式去实现，下面是一些可以帮助你的基本步骤：

1. 在`viewWillAppear()`里面设置所有云朵的`alpha`的值为"0.0"
2. 在`viewDidAppear()`里面对云朵调用`animateWithDuration(_:delay:options:animations:completion:)`. 分别将云朵动画持续时间设置成0.5并依次设置delay为0.5s、0.7s、0.9s 以及1.1s。
3. 在每个animations闭包里面，将云朵的alpah设置成1.0.这个设置将是云朵可见

运行时，可以看到云朵一个接着一个慢慢呈现出来。

![chapter1_15](./images/chapter1_15.png)

基本上所有的元素都有动画效果，处理最后的登录按钮。

![chapter1_16](./images/chapter1_16.png)

不要心急，我们会在下面的章节里面来实现。

![chapter1_17](./images/chapter1_17.png)	

