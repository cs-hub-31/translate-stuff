# 理解js的设计模式


##### By [Sukhjinder Arora](https://blog.bitsrc.io/@Sukhjinder) | Oct 16, 2018

[原文](https://blog.bitsrc.io/understanding-design-patterns-in-javascript-13345223f2dd)

当你开始了一个新项目，你不会马上开始编写代码。第一步，你必须定义这个项目解决什么问题和适用范围，然后列出这个项目的特性或者规格。在你开始编码或者正在处理更复杂的项目之后，你应该选择最合适你项目的设计模式。

## 什么是设计模式

在软件工程里，设计模式是软件设计的一种常见问题的可重用解决方案。设计模式是经验丰富的软件开发人员所使用的最佳实践，可以认为是编程模版。

### 为什么使用设计模式

许多程序员要么认为使用设计模式是浪费时间的，要么他们不知道如何正确使用设计模式。但是正确使用了设计模式会帮助你写出可维护性高的代码。

最重要的是，设计模式给软件开发者共同的话题、术语。会让学习了设计模式的初学者更快看懂你写的代码。

举个例子，如果你在项目中使用了装饰器模式，那么新来的程序员马上知道你这段代码正在做什么（译者：前提是这名程序员知道这个设计模式），并且他们会更加专注解决业务问题，而不是试图理解这段代码做的是什么。

现在我们知道什么是设计模式，并且知道了它们为什么那么重要，让我们开始深入各个应用在js的设计模式吧。

<center> * * * </center>

## 模块模式

模块是一段独立的代码，因此我们可以在不影响其他代码的情况下修改模块的代码。模块还允许我们通过变量来创建单独的范围以避免命名空间的污染。当模块与其它代码耦合度低时（译者：类似用依赖导入第三方库的那种程度），我们还可以在其他项目复用模块。

模块是任何js应用程序不可或缺的一部分，有助于保持代码高内聚低耦合。有许多方法可以在JavaScript中创建模块，其中一种是模块模式。

不同于其它的编程语言，js没有修饰符，也就是说你无法将变量声明为私有或公有。因此Module模式也用于模拟封装的概念。

我们可以在js使用IIFE（立即调用函数表达式）、闭包、函数范围来实现模块。例子如下：

	const myModule = (function() {
	  
	  const privateVariable = 'Hello World';
	  
	  function privateMethod() {
	    console.log(privateVariable);
	  }
	  return {
	    publicMethod: function() {
	      privateMethod();
	    }
	  }
	})();
	myModule.publicMethod();
	
由于是IIFE，代码立即执行，并返回对象给myModule对象。由于是闭包，即使IIFE执行完成，但是返回的对象依然能够访问IIFE内定义的函数和变量。

因为，在IIFE内定义的变量和函数基本上在外部不能直接调用的，可以看作是myModule的私有的。

代码执行后，myModule变量像这样：

	const myModule = {
	  publicMethod: function() {
	    privateMethod();
	  }};
	  
因此，我们可以调用publicMethod方法，而它有调用privateMethod方法。例如：
	
	// Prints 'Hello World'
	module.publicMethod();
	
## 揭示模块模式

揭示模块模式是经Christian Heilmann基于模块模式略微改进的一种模式。模块模式的问题是我们必须创建公共的方法，然后才能通过这个公共的方法来调用私有变量和方法。在这个模式中，我们将返回对象的属性映射到我们想要暴露出去的私有方法。这就是揭示模块模式。例如：

	const myRevealingModule = (function() {
	  
	  let privateVar = 'Peter';
	  const publicVar  = 'Hello World';
	  function privateFunction() {
	    console.log('Name: '+ privateVar);
	  }
	  
	  function publicSetName(name) {
	    privateVar = name;
	  }
	  function publicGetName() {
	    privateFunction();
	  }
	  /** reveal methods and variables by assigning them to object     properties */
	return {
	    setName: publicSetName,
	    greeting: publicVar,
	    getName: publicGetName
	  };
	})();
	myRevealingModule.setName('Mark');
	// prints Name: Mark
	myRevealingModule.getName();

这种模式让我们更容易理解我们可以公开访问那些方法和变量，这有助于提高代码可读性。

代码执行后，myRevealingModule像这样：
	
	const myRevealingModule = {
	  setName: publicSetName,
	  greeting: publicVar,
	  getName: publicGetName
	};
	
我们可以调用myRevealingModule.setName（'Mark'），它是对内部publicSetName的引用，而myRevealingModule.getName（）的引用是对内部publicGetName方法。例如：
	
	myRevealingModule.setName('Mark');
	// prints Name: Mark
	myRevealingModule.getName();
	
#### 对比普通模块模式，揭示模块模式的优点如下：

* 通过修改return语句中的一行代码，我们可以将成员从public更改为private，反之亦然。

* 返回的对象里不包含函数定义，所有右侧表达式都在IIFE中定义了，代码清晰易读。

## ES6模块

在es6之前，js没有模块，所以开发者不得不第三方库或者模块模式来实现模块。但在es6，js有了自己模块实现。

ES6的模块用文件来存储。每个文件只能有一个模块。默认情况下，模块内的所有内容都是私有的。开发者可以使用export关键字公开函数、变量和类。模块内的代码始终以严格模式运行。

### 输出模块

有两种方式输出函数和变量声明：

* 在函数和变量的前面使用export关键字。例如：

		// utils.js
		export const greeting = 'Hello World';
		export function sum(num1, num2) {
		  console.log('Sum:', num1, num2);
		  return num1 + num2;
		}
		export function subtract(num1, num2) {
		  console.log('Subtract:', num1, num2);
		  return num1 - num2;
		}
		// This is a private function
		function privateLog() {
		  console.log('Private Function');
		}

* 在代码的末尾加上export关键字，导出我们要公开的函数和变量：
		
		// utils.js
		function multiply(num1, num2) {
		  console.log('Multiply:', num1, num2);
		  return num1 * num2;
		}
		function divide(num1, num2) {
		  console.log('Divide:', num1, num2);
		  return num1 / num2;
		}
		// This is a private function
		function privateLog() {
		  console.log('Private Function');
		}
		export {multiply, divide};
		
### 导入模块

跟导出模块差不多，也有两种方法导入模块：

* 导入特定的方法
		
		// main.js
		// importing multiple items
		import { sum, multiply } from './utils.js';
		console.log(sum(3, 7));
		console.log(multiply(3, 7));
		
* 导入所有
		
		// main.js
		// importing all of module
		import * as utils from './utils.js';
		console.log(utils.sum(3, 7));
		console.log(utils.multiply(3, 7));
		
### 导入和输出的别名

如果要避免命名冲突，可以在导出和导入期间更改导出的名称。例如：

* 重命名导出的方法
		
		// utils.js
		function sum(num1, num2) {
		  console.log('Sum:', num1, num2);
		  return num1 + num2;
		}
		function multiply(num1, num2) {
		  console.log('Multiply:', num1, num2);
		  return num1 * num2;
		}
		export {sum as add, multiply};
	
* 重命名导入的方法
	
		// main.js
		import { add, multiply as mult } from './utils.js';
		console.log(add(3, 7));
		console.log(mult(3, 7));

## 单例模式

单例指的是只能实例化一次的对象。如果不存在，则单例模式会创建类的新实例。 如果存在实例，则它只返回对该对象的引用。 对构造函数的任何重复调用总是会获取相同的对象。

js支持单例模式，它有关于单例模式的实现。在js我们不应该叫单例，应该叫对象字面量。例如：

	const user = {
	  name: 'Peter',
	  age: 25,
	  job: 'Teacher',
	  greet: function() {
	    console.log('Hello!');
	  }
	};
	
因为在js中，每个对象占用一个唯一的内存位置，当我们调用用户对象时，实际上返回的是该对象的引用。

如果我们尝试复制user对象到另一个变量，并且修改其中的值。例如：
	
	const user1 = user;
	user1.name = 'Mark';
	
我们会看到两个对象都被修改，因为js中的对象是引用而不是值传递。所以内存中只有一个对象。例如：
	
	// prints 'Mark'
	console.log(user.name);
	// prints 'Mark'
	console.log(user1.name);
	// prints true
	console.log(user === user1);
	
可以用构造函数实现单例模式。例如：
	
	let instance = null;
	function User() {
	  if(instance) {
	    return instance;
	  }
	  instance = this;
	  this.name = 'Peter';
	  this.age = 25;
	  
	  return instance;
	}
	const user1 = new User();
	const user2 = new User();
	// prints true
	console.log(user1 === user2); 
	
当这个构造函数被调用，它会检查instance是否存在。如果对象不存在，它会分配值给instance变量。如果存在，返回已存在的对象。

也可以使用模块模式实现单例。例如：

	const singleton = (function() {
	  let instance;
	  
	  function init() {
	    return {
	      name: 'Peter',
	      age: 24,
	    };
	  }
	  return {
	    getInstance: function() {
	      if(!instance) {
	        instance = init();
	      }
	      
	      return instance;
	    }
	  }
	})();
	const instanceA = singleton.getInstance();
	const instanceB = singleton.getInstance();
	// prints true
	console.log(instanceA === instanceB);
	
在上面的代码中，我们用singleton.getInstance方法创建了新的instance实例。如果实例已存在，则此方法返回已存在实例，如果实例不存在，则通过调用init（）函数创建新实例。

## 工厂模式

工厂模式使用工厂方法创建对象，而不指定所创建对象的确切类或构造函数。

工厂模式用于创建对象，不暴露实例化逻辑。当我们需要根据特定条件生成不同的对象时，可以使用此模式。例如：

	class Car{
	  constructor(options) {
	    this.doors = options.doors || 4;
	    this.state = options.state || 'brand new';
	    this.color = options.color || 'white';
	  }
	}
	class Truck {
	  constructor(options) {
	    this.doors = options.doors || 4;
	    this.state = options.state || 'used';
	    this.color = options.color || 'black';
	  }
	}
	class VehicleFactory {
	  createVehicle(options) {
	    if(options.vehicleType === 'car') {
	      return new Car(options);
	    } else if(options.vehicleType === 'truck') {
	      return new Truck(options);
	      }
	  }
	}
	
我创建了car和truck类（带有一些默认值），用于创建新的汽车和卡车对象。然后我定义了VehicleFactory类，用于根据对象中收到的vehicleType属性创建并返回一个新对象。

	const factory = new VehicleFactory();
	const car = factory.createVehicle({
	  vehicleType: 'car',
	  doors: 4,
	  color: 'silver',
	  state: 'Brand New'
	});
	const truck= factory.createVehicle({
	  vehicleType: 'truck',
	  doors: 2,
	  color: 'white',
	  state: 'used'
	});
	// Prints Car {doors: 4, state: "Brand New", color: "silver"}
	console.log(car);
	// Prints Truck {doors: 2, state: "used", color: "white"}
	console.log(truck);
	
我是实例化了VehicleFactory类，之后我们调用factory.createVehicle方法创建car和truck对象，并且传递vehicleType属性值为car或truck的options对象。

## 装饰器模式

装饰器模式用于扩展对象的功能，而无需修改现有的类或构造函数。此模式可用于向对象添加新功能，无需修改它们的基础代码。

一个简单的例子：

	function Car(name) {
	  this.name = name;
	  // Default values
	  this.color = 'White';
	}
	// Creating a new Object to decorate
	const tesla= new Car('Tesla Model 3');
	// Decorating the object with new functionality
	tesla.setColor = function(color) {
	  this.color = color;
	}
	tesla.setPrice = function(price) {
	  this.price = price;
	}
	tesla.setColor('black');
	tesla.setPrice(49000);
	// prints black
	console.log(tesla.color);
	
这种模式更实际的例子是：

比方说，汽车的成本取决于它的功能数量。 如果没有装饰器模式，我们必须为不同的功能组合创建不同的类，每个类都有一个成本方法来计算成本。例如：

	class Car() {
	}
	class CarWithAC() {
	}
	class CarWithAutoTransmission {
	}
	class CarWithPowerLocks {
	}
	class CarWithACandPowerLocks {
	}

但有了装饰器模式，我们可以基于car类创建对象，并且使用装饰器函数添加不同的方法。例如：

	class Car {
	  constructor() {
	  // Default Cost
	  this.cost = function() {
	  return 20000;
	  }
	}
	}
	// Decorator function
	function carWithAC(car) {
	  car.hasAC = true;
	  const prevCost = car.cost();
	  car.cost = function() {
	    return prevCost + 500;
	  }
	}
	// Decorator function
	function carWithAutoTransmission(car) {
	  car.hasAutoTransmission = true;
	   const prevCost = car.cost();
	  car.cost = function() {
	    return prevCost + 2000;
	  }
	}
	// Decorator function
	function carWithPowerLocks(car) {
	  car.hasPowerLocks = true;
	  const prevCost = car.cost();
	  car.cost = function() {
	    return prevCost + 500;
	  }
	}
	
首先，我们创建了用于创建car对象的基类Car。然后，我们为要添加到其上的要素创建装饰器，并将Car对象作为参数传递。 然后我们覆盖该对象的cost函数，该函数返回汽车的更新成本，并向该对象添加新属性以指示添加了哪个特征。

为了添加新功能，我们可以这样做：

	const car = new Car();
	console.log(car.cost());
	carWithAC(car);
	carWithAutoTransmission(car);
	carWithPowerLocks(car);
	
最后，我们这样子计算成本：
	
	// Calculating total cost of the car
	console.log(car.cost());
	
## 结论

我们已经了解部分设计模式在js中的实现，但是还有部分在本文没有涉及。

虽然了解审设计模式很重要，但是不要过度使用它们。在使用某个设计模式之前，应该考虑是否能解决你的痛点。要了解模式是否适合你，你应该研究设计模式以及该设计模式的应用。


