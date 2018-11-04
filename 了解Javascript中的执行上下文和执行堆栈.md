# 了解Javascript中的执行上下文和执行堆栈


##### By [Sukhjinder Arora](https://blog.bitsrc.io/@Sukhjinder) | Aug 28, 2018

[原文](https://blog.bitsrc.io/understanding-execution-context-and-execution-stack-in-javascript-1c9ea8642dd0)

如果你是或者你想要成为一名js开发者，那么你必须了解js程序内部的运作。理解执行上下文和执行堆栈对于理解js的其它概念（如提升，范围和闭包）至关重要。

正确地理解执行上下文和执行堆栈将帮助你更好地使用js开发应用。

废话少说，让我们开始吧：）

<center> * * * </center>

## 什么是执行上下文？

简单来说，执行上下文是预估和执行当前环境下js代码的抽象概念。每当在js中运行代码时，它都在执行上下文中运行。

（译者：emmm，就是执行上下文包含了追踪当前正在执行的代码的全部状态。）

### 执行上下文的类型

在js中有三种执行类型

* 全局执行上下文——这是默认或者说基础执行上下文。函数外的代码就处于全局执行上下文中。它做了两件事：它创建了window对象（在浏览器环境下），也就是全局对象。并把this指向全局对象。在程序里面只能有一个全局上下文。

* 函数执行上下文——每次函数被调用，都会为这个函数创建一个新的上下文。每个函数都有自己的上下文，但是只有被调用的时候才会被创建。可以有很多个函数执行上下文。每当创建一个新的函数执行上下文，js引擎都会按照定义好的顺序执行一系列的步骤，我将会在下文中讨论。

* Eval 函数的执行上下文——eval函数执行的时候也会为它里面的代码创建上下文，但是这个方法用的少，在本文略过。

## 执行堆栈

执行堆栈在其它语言中被称为“调用栈”，是一种先进后出的一种数据结构，在代码执行期间被用于存储所有的执行上下文。

当js引擎开始解析js代码时，会先创建全局执行上下文并且放在当前执行堆栈中。每当引擎遇到函数调用的代码时，都会创建该函数的上下文并推入当前执行堆栈中。

引擎执行位于执行堆栈顶部的方法。当方法执行完毕，执行堆栈pop掉最顶部的上下文，接着引擎继续执行堆栈顶部的方法。

用代码示范一下：
	
	let a = 'Hello World!';
	function first() {
	  console.log('Inside first function');
	  second();
	  console.log('Again inside first function');
	}
	function second() {
	  console.log('Inside second function');
	}
	first();
	console.log('Inside Global Execution Context');
	
![](https://cdn-images-1.medium.com/max/1000/1*ACtBy8CIepVTOSYcVwZ34Q.png)

<center>上述代码的执行上下文堆栈。</center>

当上述代码在浏览器内加载，js引擎就会创建一个全局执行上下文并且把它推入当前执行堆栈中。当调用first（）时，js为该函数创建一个新的执行上下文，并且把它推入到当前执行堆栈。

当second（）方法被first（）调用，js引擎为该方法创建一个新的执行上下文并把它推入当前执行堆栈。当second（）执行完毕，这个方法的上下文就被执行堆栈推出，并且执行下一个执行上下文，也就是first（）。

当first（）执行完毕，重复以上步骤。一旦执行了所有代码，JavaScript引擎就会从当前堆栈中删除全局执行上下文。

## 执行上下文如何创建？

直到现在，我们已经知道js引擎如何管理执行上下文的了，现在让我们了解下执行上下文如何被js创建的。

创建执行上下文有两个阶段：1）创建阶段，2）执行阶段（译者：？？？懵逼脸）。

### 创建阶段

在执行任何JavaScript代码之前，执行上下文将经历创建阶段。在创建阶段会发生三件事：

1. this绑定
2. 词法环境（LexicalEnvironment）创建
3. 变量环境（VariableEnvironment）创建

（译者：VariableEnvironment和LexicalEnvironment译者也是第一次听到，惭愧，大学没学过编译原理，在js中还有个this绑定，似乎是js特有）

因此，执行上下文在概念上可以这样表示，如下：
	
	ExecutionContext = {
	  ThisBinding = <this value>,
	  LexicalEnvironment = { ... },
	  VariableEnvironment = { ... },
	}
	
#### this绑定

在全局执行上下文中，this值指向全局对象（在浏览器内是window对象）。

在函数执行上下文中，this的值取决于函数的调用的时候的情况。如果它由对象引用调用，this值就是该对象，否则this值指向全局或者为undefined（在严格模式下）。

例如：
	
	let person = {
	  name: 'peter',
	  birthYear: 1994,
	  calcAge: function() {
	    console.log(2018 - this.birthYear);
	  }
	}
	person.calcAge(); 
	// 'this' refers to 'person', because 'calcAge' was called with //'person' object reference
	let calculateAge = person.calcAge;
	calculateAge();
	// 'this' refers to the global window object, because no object reference was given

#### 词法环境

官方es6文档对词法环境有如下解释：

> 词汇环境是一种规范类型，用于根据ECMAScript代码的词法嵌套结构定义标识符与特定变量和函数的关联。词汇环境由environment record（译者：实在不知道咋翻）和外部词汇环境的可能为null的引用组成。 

（译者：硬翻的，有点怪）

简而言之，词汇环境是一种包含标识符变量映射的结构（此处标识符指的是变量/函数的名称，变量是对实际对象【包括函数类型的对象】或原始值的引用）。

现在，词法环境由两部分组成：

（1）environment record

（2）外部环境的引用

1、environment record是存放变量和函数声明的一个地方

2、对外部环境的引用意味着它可以访问其外部词汇环境。

有两种类型的词法环境：

* 一种是全局环境（在全局执行上下文里），它没有外部环境的引用。它的外部环境引用为null。它有全局对象（window对象）和关联的方法和属性（例如数组方法），以及任何用户定义的全局变量，并且this的值指向全局对象。

* 一种是函数环境，它存放用户在函数里定义的变量。并且外部环境可以指向全局环境，或者是外层函数环境。

笔记——对于函数环境，environment record还包含一个arguments对象，该对象包含索引和传递给函数的参数之间的映射以及传递给函数的参数的长度（数量）。例如，下面函数的参数对象如下所示：
	
	function foo(a, b) {
	  var c = a + b;
	}
	foo(2, 3);
	// argument object
	Arguments: {0: 2, 1: 3, length: 2},
	
environment record也有两种类型：

* 声明性environment record存储变量，函数和参数。 一个函数环境包含声明性environment record。

* 对象environment record用于定义在全局执行上下文中出现的变量和函数的关联。全局环境包含对象environment record。

抽象地说，词法环境在伪代码中看起来像这样：

	GlobalExectionContext = {
	  LexicalEnvironment: {
	    EnvironmentRecord: {
	      Type: "Object",
	      // Identifier bindings go here
	    }
	    outer: <null>
	  }
	}
	FunctionExectionContext = {
	  LexicalEnvironment: {
	    EnvironmentRecord: {
	      Type: "Declarative",
	      // Identifier bindings go here
	    }
	    outer: <Global or outer function environment reference>
	  }
	}
	
#### 变量环境

它也是一个词法环境，其EnvironmentRecord包含由此执行上下文中的VariableStatements创建的绑定。

如上所述，变量环境也是一个词汇环境，因此它具有上面定义的词法环境的所有属性。

在es6，词法环境和变量环境的不同在于前者用于存储函数声明和变量（let和const）绑定，而后者用于存储变量（var）的绑定。

来看个例子：
	
	let a = 20;
	const b = 30;
	var c;
	function multiply(e, f) {
	 var g = 20;
	 return e * f * g;
	}
	c = multiply(20, 30);
	
然后执行上下文会像这样：

	GlobalExectionContext = {
	  ThisBinding: <Global Object>,
	  LexicalEnvironment: {
	    EnvironmentRecord: {
	      Type: "Object",
	      // Identifier bindings go here
	      a: < uninitialized >,
	      b: < uninitialized >,
	      multiply: < func >
	    }
	    outer: <null>
	  },
	  VariableEnvironment: {
	    EnvironmentRecord: {
	      Type: "Object",
	      // Identifier bindings go here
	      c: undefined,
	    }
	    outer: <null>
	  }
	}
	FunctionExectionContext = {
	 
	  ThisBinding: <Global Object>,
	  LexicalEnvironment: {
	    EnvironmentRecord: {
	      Type: "Declarative",
	      // Identifier bindings go here
	      Arguments: {0: 20, 1: 30, length: 2},
	    },
	    outer: <GlobalLexicalEnvironment>
	  },
	VariableEnvironment: {
	    EnvironmentRecord: {
	      Type: "Declarative",
	      // Identifier bindings go here
	      g: undefined
	    },
	    outer: <GlobalLexicalEnvironment>
	  }
	}
	
笔记——函数multiply调用时才会创建函数执行上下文。

正如你所注意到的一样，let和const定义的变量没有绑定任何值，但var定义的变量为undefined

这是因为在创建阶段，扫描代码寻找变量和函数声明时，函数声明完全存储在环境中，但变量最初设置为undefined（var）或保持为为初始化（let、const）。

（译者：就是var会声明提升，而let和const不会）

这就是为什么你可以在变量声明前访问到var定义的变量，而访问let和const定义的变量则会抛出引用错误。

这就是js的变量提升。

### 执行阶段

这是整篇文章中最简单的部分。 在此阶段，完成对所有这些变量的分配，最后执行代码。

笔记——在执行阶段，如果js引擎在源代码声明的实际位置找不到let变量的值，那么它将为其分配undefined值。

## 结论

现在，我们已经了js的部分执行原理，虽然理解了这些概念不一定能让你成为出色的js开发者，但是明白了上述的概念能让你更好理解js的其它概念，例如变量提升、闭包。