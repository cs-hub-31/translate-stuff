# 鲜为人知的JavaScript功能

##### By [Viral Shah](https://blog.usejournal.com/@viral_shah) | Nov 26, 2018

[原文](https://blog.usejournal.com/little-known-features-of-javascript-901665291387)

js一门很容易入门但是很难精通的语言。我很认同这句话。这是因为js是一门古老的语言同时也是一门很灵活的语言。有着一堆神秘的语法和过时的功能。我已经使用js很多年了，迄今为止，我时不时地会发现一些我从未知道的隐藏语法或技巧。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/whatthejsisthat.jpg?raw=true)

我试图列出一些鲜为人知的js特性。虽然有一些特性在严格模式下是不能用的，但是它依然是一种不错的js特性。然而，请注意，我不建议你全使用上我介绍的特性。虽然它们很酷，但毕竟是鲜为人知的特性，你的同事可能会看不懂。

## void 操作符

js有一元运算符。你可能已经见过了，像 void(0) 或者 void0 。它只有一个目的 - 在右边评估表达式并返回undefined。使用0只是一种惯例。不一定要使用0.它可以是任何有效的表达式，如

	void <expression>
	

它仍然返回undefined。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/voidOperator.png?raw=true)

为什么要这样子返回undefined，直接返回undefined不好么？看起来这个特性很多余，不是么？

### 有趣的事实

好吧，事实证明，在ES5之前，你实际上可以在大多数浏览器中为undefined重新分配值。类似这样

	undefined =“abc”
	
使用void的话会始终保持返回正确的undefined。

## 构造函数的括号是可选的

对的，你没看错，当调用构造函数的时候，我们在类名后面加的圆括号——完全是可选的！😮（前提是你不需要传递参数）

下面的代码样式都被认为是有效的JS语法，并且会给你完全相同的结果！

![](https://github.com/shArpyYAo/markdown-photo/blob/master/constructorBrackets.png?raw=true)

## IIFE的括号可以被跳过

IIFE的语法让我感觉总是有点奇怪。

IIFE所有的括号都有什么用？

那些额外的括号只是为了告诉js解析器，即将到来的代码是函数表达式而不是函数。知道了这些，来想一想，有很多方法可以跳过这些额外的括号，仍然可以制作有效的IIFE。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/IIFEBrackets.png?raw=true)

void操作符告诉解析器这段代码是函数表达式。因此，我们可以省略包着函数的括号。你猜怎么着？我们可以使用任何一元运算符（void，+ ,!， - 等），这仍然有效。

真酷！

然而，如果你观察再仔细点，你会有个疑问，

一元运算符不会影响IIFE返回的结果吗？

好吧，它会影响结果。但是有个好消息，如果你关心返回的结果并且把结果放在某个变量中，那么你首先不需要额外的括号。

这就对了！

![](https://github.com/shArpyYAo/markdown-photo/blob/master/IIFEUnary.png?raw=true)

我们添加这些括号只是为了更好的代码可读性。

## with 声明

你知道js有with声明块？with一直是js的关键词。语法如下：

	with (object)
	   statement 
	// for multiple statements add a block
	with (object) {
	   statement
	   statement
	   ...
	}
	
with可以对传递过来的对象进行解析，在with 块里可以直接使用对象的属性。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/withObject.png?raw=true)

### 有趣的事实

with块看起来很酷对不对？甚至比object destructuring还好。

但是，并不是这样的。

通常不鼓励使用with语句，因为它已被弃用。 在严格模式下完全禁止。 事实证明，with块会增加语言中的一些性能和安全性问题。Bummer

## 构造函数

function语句并不是定义新函数的唯一方法；你可以使用【Function（）】这个构造函数和new运算符动态定义函数。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/functionConstruction.png?raw=true)

构造函数的最后一个参数是字符串化代码，就是你写的函数。其他参数是该函数的参数。

### 有趣的事实

在js，Function构造函数是所有构造函数的构造函数。也包括Object的构造函数。并且Function构造函数的构造函数就是它自身。因此，调用object.constructor.constructor...足够多次后，在js中，最终会在任何对象返回Function构造函数。

## Function 的属性

我们知道在js中，Functions是首个对象。因此，没有人能阻止我们在Function对象上添加自己的属性。这是可以的，但是很少会这样做。

那有什么场景会应用到呢？

有一些很好的用例。例如，

让Functions可配置化

我们有个greet函数。我们想要让我们的函数在不同的环境下打印不同语言的问候语。这个环境是可以配置的。我们可以在某处维护个全局环境变量，或者使用上文所说的添加自定义属性

![](https://github.com/shArpyYAo/markdown-photo/blob/master/ConfigurableFunctions.png?raw=true)

### Function的静态变量

另一个相似的例子。假设你想要实现一个生成一系列有序数字的数字生成器。通常来说，你会用Class或者IIFE，在里面维护一个计数变量，然后输出有序的值。这样我们就可以限制对计数器的访问，并避免使用额外的变量来污染全局空间。

但是，如果我们希望灵活地读取甚至修改计数器并且不污染全局空间呢？

好吧，我们仍然可以创建一个Class，带有一个计数器变量和一些额外的方法来读取它; 或者我们可以懒一点，只需在函数上使用属性。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/FunctionWithStaticVariables.png?raw=true)

现在是中场休息时间，我们等你回来。如果你想接着继续，你是一个勇敢的战士，我向你致敬。

继续吧

## Arguments属性

我相信大多数人都知道函数中的参数对象。 它是一个像对象一样的数组（译者：emmm），在所有函数中都能访问到。它具有在调用函数时传递的参数列表，也有其他一些有趣的属性。

* arguments.callee：指向当前调用函数
* arguments.callee.caller：指向当前被调函数的调用者。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/ArgumentsProperties.png?raw=true)

注意：虽然ES5禁止在严格模式下使用callee和caller，但在许多编译库中仍然常见。 所以，值得学习它们。

## 标记模版字面量

除非你是井底之蛙，你一定听说过模版字面量。模版字面量时es6的一个很酷的补充。然而，你听说过Tagged模版字面量么（Tagged Template Literals）？

![](https://github.com/shArpyYAo/markdown-photo/blob/master/TaggedTemplateLiterals.png?raw=true)

Tagged模板字面量允许您通过向模板字面量添加自定义标记来更好地控制将模板字面量解析为字符串。Tagged只是一个获取字符串模板解析后的所有字符串和值的数组的解析器函数。Tagged函数最终返回字符串。

在下面的示例中，我们的自定义标记 - highlight，解释模板文字的值，并使用

	<mark>

元素将解释的值包装在结果字符串中，以突出显示。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/Tagged.png?raw=true)

## Getter & Setter

在大多数情况下，js对象时很简单的。比如说有一个对象叫user，我们尝试使用user.age来读取age属性时，如果有定义并赋值了，就有返回。如果没有，就返回undefined。简单。

但是，它不一定非常简单。 JavaScript对象具有Getters和Setter的概念。 我们可以编写自定义的Getter函数来返回我们想要的任何内容，而不是直接返回对象上的值。 设置值也是一样的。

这使我们可以在获取或设置属性时可以灵活的创造虚拟属性、验证属性。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/GettersSetters.png?raw=true)

Getters和Setters不是es5的特性，它们是一直都存在的特性。es5只是添加了语法糖。

## 逗号操作符

JavaScript有一个逗号运算符。 它允许我们在一行中编写由逗号分隔的多个表达式，并返回最后一个表达式的结果
	
	// syntax
	let result = expression1, expression2,... expressionN
	
来看看上面的例子，这里将会解析所有的表达式，并把result变量赋予expressionN返回的值。

你可能在循环里面使用过了
	
	for (var a = 0, b = 10; a <= 10; a++, b--)
	
它可以帮助我们把语句写在一行

	function getNextValue() {
	    return counter++, console.log(counter), counter
	}
	
或者写lamda表达式

	const getSquare = x => (console.log (x), x * x)
	
## + 加操作符

想要知道如何便捷地把字符串转换成number？

只要在字符串前面加个+号。

Plus运算符也适用于负，八进制，十六进制，指数值。 更重要的是，它甚至可以将Date或Moment.js对象转换为时间戳！

![](https://github.com/shArpyYAo/markdown-photo/blob/master/PlusOperator.png?raw=true)

## !! Bang Bang 操作符

好的，严格来讲，它不是一个单独的JavaScript运算符。 它只是两次使用的JavaScript否定运算符。

但是Bang Bang听起来很酷！Bang Bang是一个可以讲任何表达式转换成布尔值的方法。

如果表达式是一个true，则返回true；否则返回false。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/BangBangOperator.png?raw=true)

## ~ 位运算作符

来看一个没人关注的位运算操作符。我们什么时候用它呢？

好吧，有个日常用例。

当与数字一起使用时，Tilde运算符有效，像这样

	~N => -（N + 1）。

仅当N == -1时，此表达式的计算结果为“0”

我们可以通过在indexOf（...）函数前面放置〜来进行布尔检查是否有一项存在。String或Array中都可以用这个。

![](https://github.com/shArpyYAo/markdown-photo/blob/master/TildeOperator.png?raw=true)

注意：ES6和ES7分别在String＆Array中添加了一个新的.includes（）方法。当然，它比位运算运算符更清晰，以检查项目是否存在于Array或String中。

## 标签语句

js具有标签语句的概念。它允许我们在js中命名循环和块。然后，我们可以使用这些标签在使用break或continue时使用。

带标签的语句在嵌套循环中特别方便。但是我们也可以使用它们来简单地将代码组织成块或创建一个可退出的块

![](https://github.com/shArpyYAo/markdown-photo/blob/master/LabelledStatements.png?raw=true)

 注意：不像其他的语言，js没有goto语句。因此，我们只能使用labels来搭配break和continue使用。


	
