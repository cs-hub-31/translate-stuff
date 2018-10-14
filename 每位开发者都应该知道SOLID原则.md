# 每位开发者都应该知道SOLID原则

##### By [Chidume Nnamdi](https://blog.bitsrc.io/@kurtwanger40) | Oct 9, 2018

[原文](https://blog.bitsrc.io/solid-principles-every-developer-should-know-b3bfa96bb688)

面向对象的编程类型为软件开发带来了新的设计。

这使开发人员能够在一个类中组合具有相同目的/功能的数据，来实现单独的一个功能，不必关心整个应用程序如何。

但是，这种面向对象的编程还是会让开发者困惑或者写出来的程序可维护性不好。

为此，Robert C.Martin指定了五项指导方针。遵循这五项指导方针能让开发人员轻松写出可读性和可维护性高的程序

这五个原则被称为S.O.L.I.D原则（首字母缩写词由Michael Feathers派生）。

* S：单一责任原则
* O：开闭原则
* L：里式替换
* I：接口隔离
* D：依赖反转

我们在下文会详细讨论它们

笔记：本文的大多数例子可能不适合实际应用或不满足实际需求。这一切都取决于您自己的设计和用例。这都不重要，关键是您要了解明白这五项原则。

提示：SOLID原则旨在用于构建模块化、封装、可扩展和可组合组件的软件。Bit是一个帮助你践行这些原则的强大工具：它可以帮助您在团队中大规模地在不同项目中轻松隔离，共享和管理这些组件.来试试吧。

[Bit](https://bitsrc.io)

你也可以在[这里](https://blog.bitsrc.io/introducing-bit-writing-code-in-the-age-of-code-components-fd8512a9aa90)学习更多关于SOLID原则和Bit的东西。

##  单一责任原则

> “......你有一份工作” - Loki来到雷神的Skurge：Ragnarok
> 
> 一个类只实现一个功能

一个类应该只负责一件事。如果一个类负责超过一件事，就会变得耦合。改功能的时候会影响另外一个功能。

* 笔记：该原则不仅适用于类，还适用于软件组件和微服务。

举个例子，考虑这个设计：

	class Animal {
	    constructor(name: string){ }
	    getAnimalName() { }
	    saveAnimal(a: Animal) { }
	}
	
这个Animal类违反了SRP（单一责任原则）

怎么违反了呢？

SRP明确说明了类只能完成一项功能，这里，我们把两个功能都加上去了：animal数据管理和animal属性管理。构造函数和getAnimalName方法管理Animal的属性，然而，saveAnimal方法管理Animal的数据存储。

这种设计会给以后的开发维护带来什么问题？

如果app的更改会影响数据库的操作。必须会触及并重新编译使用Animal属性的类以使app的更改生效。

你会发现这样的系统缺乏弹性，像多米诺骨牌一样，更改一处会影响其他所有的地方。

让我们遵循SRP原则，我们创建了另外一个用于数据操作的类：

	class Animal {
	    constructor(name: string){ }
	    getAnimalName() { }
	}
	class AnimalDB {
	    getAnimal(a: Animal) { }
	    saveAnimal(a: Animal) { }
	}
	
“我们在设计类时，我们应该把相关的功能放在一起，所以当他们需要发生改变时，他们会因为同样的原因而改变。如果是因为不同的原因需要改变它们，我们应该尝试把它们分开。” - Steven Fenton

遵循这些原则让我们的app变得高内聚。

## 开闭原则

> 软件实体（类，模块，函数）应该是可以扩展的，而不是修改。
> 

继续看我们的Animal类

	class Animal {
	    constructor(name: string){ }
	    getAnimalName() { }
	}
	
我们想要遍历动物列表并且设置它们的声音。

	//...
	const animals: Array<Animal> = [
	    new Animal('lion'),
	    new Animal('mouse')
	];
	function AnimalSound(a: Array<Animal>) {
	    for(int i = 0; i <= a.length; i++) {
	        if(a[i].name == 'lion')
	            return 'roar';
	        if(a[i].name == 'mouse')
	            return 'squeak';
	    }
	}
	AnimalSound(animals);
	
AnimalSound函数并不符合开闭原则，因为一旦有新动物出现，它需要修改代码。

如果我们加一条蛇进去，🐍：

	//...
	const animals: Array<Animal> = [
	    new Animal('lion'),
	    new Animal('mouse'),
	    new Animal('snake')
	]
	//...
	
我们不得不改变AnimalSound函数：

	//...
	function AnimalSound(a: Array<Animal>) {
	    for(int i = 0; i <= a.length; i++) {
	        if(a[i].name == 'lion')
	            return 'roar';
	        if(a[i].name == 'mouse')
	            return 'squeak';
	        if(a[i].name == 'snake')
	            return 'hiss';
	    }
	}
	AnimalSound(animals);
	
每当新的动物加入，AnimalSound函数就需要加新的逻辑。这是个很简单的例子。当你的app变得庞大和复杂时，你会发现每次加新动物的时候就会加一条if语句，随后你的app和AnimalSound函数都是if语句的身影。

那怎么修改AnimalSound函数呢？

	class Animal {
	        makeSound();
	        //...
	}
	class Lion extends Animal {
	    makeSound() {
	        return 'roar';
	    }
	}
	class Squirrel extends Animal {
	    makeSound() {
	        return 'squeak';
	    }
	}
	class Snake extends Animal {
	    makeSound() {
	        return 'hiss';
	    }
	}
	//...
	function AnimalSound(a: Array<Animal>) {
	    for(int i = 0; i <= a.length; i++) {
	        a[i].makeSound();
	    }
	}
	AnimalSound(animals);
	
现在Animal有个makeSound的私有方法。我们每一个animal继承了Animal类并且实现了私有方法makeSound。

每个animal实例都会在makeSound中添加自己的实现方式。AnimalSound方法遍历animal数组并调用其makeSound方法。

现在，如果我们添加了新动物，AnimalSound方法不需要改变。我们需要做的就是添加新动物到动物数组。

AnimalSound方法现在遵循了开闭原则。

另一个例子：

假设您有一个商店，并且您使用此类给您喜爱的客户打2折：

	class Discount {
	    giveDiscount() {
	        return this.price * 0.2
	    }
	}
	
当您决定为VIP客户提供双倍的20％折扣。 您可以像这样修改类：

	class Discount {
	    giveDiscount() {
	        if(this.customer == 'fav') {
	            return this.price * 0.2;
	        }
	        if(this.customer == 'vip') {
	            return this.price * 0.4;
	        }
	    }
	}
	
哈哈哈，这样不就背离开闭原则了么？如果我们又想加新的折扣，那又是一堆if语句。

为了遵循开闭原则，我们创建了继承Discount的新类。在这个新类中，我们将会实现新的行为：

	class VIPDiscount: Discount {
	    getDiscount() {
	        return super.getDiscount() * 2;
	    }
	}
	
如果你决定给VIP80%的折扣，就像这样：

	class SuperVIPDiscount: VIPDiscount {
	    getDiscount() {
	        return super.getDiscount() * 2;
	    }
	}
	
你看，这不就不用改了。

## 里氏替换

> A sub-class must be substitutable for its super-class

这个原则的目的是确定一个子类可以毫无错误地占据其超类的位置。如果代码会检查自己类的类型，它一定违反了这个原则。

继续Animal例子。

	//...
	function AnimalLegCount(a: Array<Animal>) {
	    for(int i = 0; i <= a.length; i++) {
	        if(typeof a[i] == Lion)
	            return LionLegCount(a[i]);
	        if(typeof a[i] == Mouse)
	            return MouseLegCount(a[i]);
	        if(typeof a[i] == Snake)
	            return SnakeLegCount(a[i]);
	    }
	}
	AnimalLegCount(animals);
	
这已经违反了里氏替换（也违反了OCP原则）。它必须知道每个Animal的类型并且调用leg-conunting相关（返回动物腿数）的方法。

如果要加入新的动物，这个方法必须经过修改才能加入。

	//...
	class Pigeon extends Animal {
	        
	}
	const animals[]: Array<Animal> = [
	    //...,
	    new Pigeon();
	]
	function AnimalLegCount(a: Array<Animal>) {
	    for(int i = 0; i <= a.length; i++) {
	        if(typeof a[i] == Lion)
	            return LionLegCount(a[i]);
	        if(typeof a[i] == Mouse)
	            return MouseLegCount(a[i]);
	         if(typeof a[i] == Snake)
	            return SnakeLegCount(a[i]);
	        if(typeof a[i] == Pigeon)
	            return PigeonLegCount(a[i]);
	    }
	}
	AnimalLegCount(animals);
	
来，我们依据里氏替换改造这个方法，我们按照Steve Fenton说的来：

* 如果超类（Animal）有一个接受超类类型（Animal）参数的方法。 它的子类（Pigeon）应该接受超类型（Animal类型）或子类类型（Pigeon类型）作为参数。
* 如果超类返回超类类型（Animal）。 它的子类应该返回一个超类型（Animal类型）或子类类型（Pigeon）。

现在，开始改造：

	function AnimalLegCount(a: Array<Animal>) {
	    for(let i = 0; i <= a.length; i++) {
	        a[i].LegCount();
	    }
	}
	AnimalLegCount(animals);
	
AnimalLegCount函数更少关注传递的Animal类型，它只调用LegCount方法。它就只知道这参数是Animal类型，或者是其子类。

Animal类现在必须实现/定义一个LegCount方法：
	
	class Animal {
	    //...
	    LegCount();
	}

然后它的子类就需要实现LegCount方法：

	//...
	class Lion extends Animal{
	    //...
	    LegCount() {
	        //...
	    }
	}
	//...
	
当它传递给AnimalLegCount方法时，他返回狮子的腿数。

你看，AnimalLegCount不需要知道Animal的类型来返回它的腿数，它只调用Animal类型的LegCount方法，Animal类的子类必须实现LegCount函数。

## 接口隔离原则

> 制定特定客户的细粒度接口
> 不应强迫客户端依赖它不需要的接口

该原则解决实现大接口的缺点。

让我们看下下面这段代码：

	interface Shape {
	    drawCircle();
	    drawSquare();
	    drawRectangle();
	}
	
这个接口定义了画正方形、圆形、矩形的方法。圆类、正方形类或者矩形类就必须实现 drawCircle()、 drawSquare()、drawRectangle().

	class Circle implements Shape {
	    drawCircle(){
	        //...
	    }
	    drawSquare(){
	        //...
	    }
	    drawRectangle(){
	        //...
	    }    
	}
	class Square implements Shape {
	    drawCircle(){
	        //...
	    }
	    drawSquare(){
	        //...
	    }
	    drawRectangle(){
	        //...
	    }    
	}
	class Rectangle implements Shape {
	    drawCircle(){
	        //...
	    }
	    drawSquare(){
	        //...
	    }
	    drawRectangle(){
	        //...
	    }    
	}
	
上面的代码看着很好笑。矩形类实现了它不需要的方法。其他类也同样的。

让我们再加一个接口。

	interface Shape {
	    drawCircle();
	    drawSquare();
	    drawRectangle();
	    drawTriangle();
	}

类必须实现新方法，否则将抛出错误。

我们看到不可能实现可以绘制圆形而不是矩形或正方形或三角形的形状。 我们可以实现方法来抛出一个错误，表明无法执行操作。

这个Shape接口的设计不符合接口隔离原则。（此处为Rectangle，Circle和Square）不应强制依赖于他们不需要或不使用的方法。

此外，接口隔离原则要求接口应该只执行一个动作（就像单一责任原则一样）任何额外的行为分组都应该被抽象到另一个接口。

这里，我们的Shape接口执行应由其他接口独立处理的动作。

为了使我们的Shape接口符合ISP原则，我们将操作分离到不同的接口：

	interface Shape {
	    draw();
	}
	interface ICircle {
	    drawCircle();
	}
	interface ISquare {
	    drawSquare();
	}
	interface IRectangle {
	    drawRectangle();
	}
	interface ITriangle {
	    drawTriangle();
	}
	class Circle implements ICircle {
	    drawCircle() {
	        //...
	    }
	}
	class Square implements ISquare {
	    drawSquare() {
	        //...
	    }
	}
	class Rectangle implements IRectangle {
	    drawRectangle() {
	        //...
	    }    
	}
	class Triangle implements ITriangle {
	    drawTriangle() {
	        //...
	    }
	}
	class CustomShape implements Shape {
	   draw(){
	      //...
	   }
	}
	
ICircle接口仅处理圆形绘画，Shape处理任何形状的绘图:)，ISquare处理仅正方形的绘制和IRectangle处理矩形绘制。

## 依赖反转

> 依赖应该是抽象而不是concretions
> 高级模块不应该依赖于低级模块。 两者都应该取决于抽象。
> 抽象不应该依赖于细节。 细节应取决于抽象。

在软件开发有一点，就是我们的app主要由模块组成。当发生这种情况时，我们必须通过使用依赖注入来清除问题。 高级组件取决于低级组件的功能。
	
	class XMLHttpService extends XMLHttpRequestService {}
	class Http {
	    constructor(private xmlhttpService: XMLHttpService) { }
	    get(url: string , options: any) {
	        this.xmlhttpService.request(url,'GET');
	    }
	    post() {
	        this.xmlhttpService.request(url,'POST');
	    }
	    //...
	}
	
这里，Http是高级组件，而HttpService是低级组件。此设计违反依赖反转第一条：高级模块不应该依赖于低级模块。 两者都应该取决于抽象。

Http类被迫依赖于XMLHttpService类。 如果我们要改变以改变Http连接服务，也许我们想通过Nodejs连接到互联网，甚至模拟http服务。我们将艰难地通过Http的所有实例来编辑代码，这违反了OCP（依赖反转）原则。

Http类应该更少关注正在使用的Http服务的类型。 我们创建一个Connection接口：

	interface Connection {
	    request(url: string, opts:any);
	}
	
Connection接口有一个请求方法。 有了这个，我们将一个Connection类型的参数传递给我们的Http类：

	class Http {
	    constructor(private httpConnection: Connection) { }
	    get(url: string , options: any) {
	        this.httpConnection.request(url,'GET');
	    }
	    post() {
	        this.httpConnection.request(url,'POST');
	    }
	    //...
	}
	
现在，Http类的无需知道它正在使用什么类型的服务。它都能正常工作。

我们现在可以重新写我们的XMLHttpService类来实现Connection接口：
	
	class XMLHttpService implements Connection {
	    const xhr = new XMLHttpRequest();
	    //...
	    request(url: string, opts:any) {
	        xhr.open();
	        xhr.send();
	    }
	}
	
我们可以创建很多各种用途的Http类并且不用担心出问题。
	
	class NodeHttpService implements Connection {
	    request(url: string, opts:any) {
	        //...
	    }
	}
	class MockHttpService implements Connection {
	    request(url: string, opts:any) {
	        //...
	    }    
	}
	
现在，我们可以看到高级模块和低级模块都依赖于抽象。Http类（高级模块）依赖Connection接口（抽象），Http服务（低级模块）实现Connection接口。

此外，依赖反转还强制我们不要违反里式替换：连接类型Node-XML-MockHttpService可替换其父类型Connection。

## 结论

我们涵盖了每个软件开发人员必须遵守的五项原则。 一开始可能难以遵守所有这些原则，但通过长期的坚持，它将成为我们的一部分，并将极大地影响我们的应用程序的维护。

如果您有任何疑问，请随时在下面发表评论，我很乐意谈谈！
