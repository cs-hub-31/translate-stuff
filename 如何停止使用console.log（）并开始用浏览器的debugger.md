# 如何停止使用console.log（）并开始用浏览器的debugger

##### By [Parag Zaveri](https://medium.com/@parag.g.zaveri) | Oct 1, 2018

[原文](https://medium.com/datadriveninvestor/stopping-using-console-log-and-start-using-your-browsers-debugger-62bc893d93ff)

当我开始我的软件开发工程师之旅时，发现在这个过程中经历了许多困难。其中一个最常见的困难就是每个新手都会遇到的-调试。起初，当我发现可以打开控制台查看console.log()输出的值以定位bug，我像是发现圣地一样哈皮。但是这种方法是低效的。来几个我喜欢并且比较幽默的例子：

	console.log(‘Total Price:’, total) //In an effort to see if the value was stored
	console.log(‘Here’) //If my program execution reached a certain function

我想大多数开发者开始意识到这并不是调试你程序的好方法。还有更好的方式。

这个好方法就是浏览器的调试工具。在本文中，我使用chrome开发者工具。

在这篇文章中，我将介绍如何使用断点，单步执行代码，设置监视表达式以及在Chrome开发者工具中应用修复程序。

为了更好地完成本教程，你需要使用我的实例代码。[点击这里](https://chromedevtoolsdemo.herokuapp.com)(需要翻墙)

## 第一步：重现bug

我们首先执行一系列会出bug的操作。

1、在本例中，我们使用有bug的小费计算器。

2、在‘Entree1’输入12

3、在‘Entree2’输入8

4、在‘Entree3’输入10

5、在‘Tax’输入10

6、在‘Tip’处选择20%

7、点击计算账单按钮，一共应该是39.6，然而，我们得到不同的答案，结果是14105.09。。。yikes！

![](https://cdn-images-1.medium.com/max/800/1*r-TVPOq2bvKB1clw9vgCHg.png)

## 第二步：学习使用资源面版

在chrome中调试，你需要打开开发者工具。按下Command+Option+I (Mac) 或者 Control+Shift+I (Linux)就可以打开了.

![](https://cdn-images-1.medium.com/max/800/1*t3SETtaOVas1trQfjRO4gw.png)

打开资源面版，如上图。文件导航栏、源代码编辑器、调试栏。在第三步之前熟悉熟悉吧。

## 第三步：设置你的断点

在使用断点之前，让我先用console来调试。很明显，可以通过以下操作来调试：

![](https://cdn-images-1.medium.com/max/800/1*ZLrHNgLfA0_ImUT-bjiN-w.png)

幸运的事，在浏览器上不需要再像这样开发了。相反，我们可以设置断点一步一步地看我们代码的运行情况。

来看看如何设置断点，断点是浏览器查找的内容，以便知道何时暂停代码的执行并允许您有机会对其进行调试。

出于教学目的，我们使用设置鼠标事件在程序的最开始设置断点。

在调试器那一栏上，展开“事件侦听器断点”的视图。 从那里，展开“鼠标”。 然后勾选“点击”按钮。

现在，当你单击Calculate Bill按钮时，调试器将在第一个函数“onClick（）”的第一行暂停执行。 如果调试器位于其他任何位置，请按下播放按钮，调试器将跳过它。

## 第四步：开始调试

在所有调试工具中，都有两个执行代码的按钮。开发者可以进入函数里面或者跳过函数。

不遗漏每一个方法的每一行代码。

<center>![](https://cdn-images-1.medium.com/max/800/1*HaePgs1Jyqw1L-wcCiQk0A.png)</center>
<center>进入方法里面执行代码</center>

跳过方法

<center>![](https://cdn-images-1.medium.com/max/800/1*07byHc3enj1vgrapehg4Bg.png)</center>
<center>跳过方法执行下一行代码</center>

这是我单步执行代码的示例。 在范围选项卡下，前三个输入框的值显示在右侧。

![](https://cdn-images-1.medium.com/max/800/1*EfVOw-IfVMScANFDGn92mw.png)

## 第五步：在行数那里设置断点

真是太好用了，但是有个问题。这样设置断点有点太笨了，通常我们只需要在某个地方查看。有个好的方法，可以在某一行的行数那里设置断点

作者的画外音：这个功能让我彻底抛弃console方法并爱上了调试工具。

只要点击代码行号就可以设置代码行断点了。然后运行你的代码，调试器就会停在你设置断点的地方。

提示：如果遇到问题，请确保取消选中鼠标下的单击复选框。

![](https://cdn-images-1.medium.com/max/800/1*boS5jNmWpJQMc4o5VHReWA.png)

如你所见，我的subtotal值显示为“10812”。我的entree值在面板显示了出来并且也计算出来了。

emmm。。。似乎是进行了字符串拼接。

让我们再看几个表达式搞清楚整个流程。

## 第六步：创建监视表达式

现在我们知道我们的entree值并没有正确加在一起，让我们在每一个值上设置监视表达式。

监视表达式将提供有关代码中任何给定变量或表达式的更多信息。

要将值定义为“监视”，请单击最顶部的监视窗格，并在打开后单击+符号。 在这里，你可以键入变量名称或其他表达式。

在这个例子中，我将设置在我第一个entree值上监视值和类型。

![](https://cdn-images-1.medium.com/max/800/1*kQDNWSdmUhXrpFyOaY9vHA.png)

Aha！我想我找到bug了。我的第一个值储存为字符串！似乎是querySelector方法搞的鬼。也可以搞搞其他的值。让我们更进一步调试然后在DevTools中改我们的代码。

## 第七步：修复

经过进一步的审查，querySelector方法绝对是罪魁祸首。

![](https://cdn-images-1.medium.com/max/800/1*Bg6oJPpIZKnBywUG3U_l1w.png)

那么我们如何解决这个问题呢？ 好吧，我们可以简单地使用例如Number（getEntree1（））将字符串强制转换为数字值，如第74行所示。

为了实际编辑代码，您需要转到'sources'左侧的'elements'面板。 如果您看不到javascript代码，则需要扩展脚本标记。 从那里，右键单击代码并选择“编辑为html”。

![](https://cdn-images-1.medium.com/max/800/1*NPHg0e_aRlVkNYEbQQCITw.png)

如果你使用的是工作区，则可以轻松保存代码并立即查看。如果不是，则需要使用名令command+s (mac) or control+s (linux)来保存。

然后你可以看你的本地代码的变化。

![](https://cdn-images-1.medium.com/max/800/1*WPiRbg5uZXh11NCxr1U_2A.png)

小老弟你康康～～

<center> * * * </center>

本文的调试方法受到Kayce Basques[这篇文章](https://developers.google.com/web/tools/chrome-devtools/javascript/)的启示.

Demo:[https://github.com/paragzaveri/chromeDevTools](https://github.com/paragzaveri/chromeDevTools)

