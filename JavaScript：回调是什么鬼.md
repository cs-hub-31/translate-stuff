# JavaScript：回调是什么鬼

##### By Brandon Morelli | Jun 12, 2017

[原文](https://codeburst.io/javascript-what-the-heck-is-a-callback-aba4da2deced)

用六分钟通过简单的例子学习基础回调

## 前言

嗨，我是Brandon，我创建了[codeburst.io](https://codeburst.io/),在上面写了关于js的教程和文章来帮助初学者深入学习web开发。如果你有疑问，可以给我留言，或关注我的twitter [brandonmorelli](https://twitter.com/brandonmorelli).

## 什么是回调

简单地说：回调是一个函数执行完后紧接着才会被执行的函数——因此被称为函数。

详细地说：在js中，函数是Objects。正因为如此，函数能做为参数传给另一个函数，也能做为返回值被其他函数返回。接受函数做为参数或者返回值为函数的函数叫做高阶函数。做为参数传递的函数叫做回调函数。

## 为什么我们需要回调

一个非常重要的理由就是，js是事件驱动语言。这意味着，js不会等待响应，而是一直侦听其他事件一直执行下去。来看个例子：

	function first(){
	  console.log(1);
	}
	function second(){
	  console.log(2);
	}
	first();
	second();
	
和你想的一样，函数<code>first</code>先执行，然后是<code>second</code>函数。

	// 1
	// 2
	
到现在为止还好。

但是如果函数<code>first</code>有一段不能立即执行的代码呢？举个例子，我们有个异步请求（ajax之类的），需要等待响应的。我们用<code>setTimeout</code>来模拟下。我们延迟500毫秒来模仿这个异步请求。

	function first(){
	  // Simulate a code delay
	  setTimeout( function(){
	    console.log(1);
	  }, 500 );
	}
	function second(){
	  console.log(2);
	}
	first();
	second();
	
现在先不需要知道<code>setTimeout</code>的工作原理。重要的是我们知道在500毫秒后会打印1；所以当我们调用时会发生什么？

	first();
	second();
	// 2
	// 1
	
即使我们先调用了<code>first</code>函数，我们看到还是先打印了<code>second</code>函数里的内容。

这并不是因为js不按照我们想要的顺序执行，而是js没有等待<code>first</code>函数的响应，继续执行了<code> second </code>函数。

为什么要告诉你这些？因为你不能只是一个接着一个调用函数，并希望它们以正确的顺序执行。回调是一种确保某些代码在其他代码已经完成执行之前不会执行的方法。

## 创建回调

来动手写一个吧！

首先打开你的开发者工具Windows: Ctrl + Shift + J)(Mac: Cmd + Option + J控制台并在控制台输入以下代码：
	
	function doHomework(subject) {
	  alert(`Starting my ${subject} homework.`);
	}
	
上面的代码里，我们创建了一个接受一个参数的<code> doHomework </code>函数，并且把参数alert出来。

	doHomework('math');
	// Alerts: Starting my math homework.
	
现在加入我们的回调，代码如下：

	function doHomework(subject, callback) {
	  alert(`Starting my ${subject} homework.`);
	  callback();
	}
	
	doHomework('math', function() {
	  alert('Finished my homework');
	});

正如你所看到的，如果你在控制台中输入上述代码，你将收到两个alert：“starting homework”alert，然后是“finished homework”alert。

你也可以这样写：

	function doHomework(subject, callback) {
	  alert(`Starting my ${subject} homework.`);
	  callback();
	}
	function alertFinished(){
	  alert('Finished my homework');
	}
	doHomework('math', alertFinished);
	
此示例的结果与前一个示例完全相同，但setup略有不同。 如你所见，我们在doHomework（）函数调用期间将alertFinished函数定义作为参数传递！

## 真实例子

上周我发表了一篇关于如何用38行代码创建Twitter机器人的文章。 之所以能做到的唯一原因是因为Twitters API。当你向API发出请求时，你必须等待响应才能对该响应执行操作。这是现实中回调的一个很好的例子。 这是请求的样子：

	T.get('search/tweets', params, function(err, data, response) {
	  if(!err){
	    // This is where the magic will happen
	  } else {
	    console.log(err);
	  }
	})
	
* <code>T.get</code>是Twitter的请求方法
* 有三个参数，第一个是请求路径，第二个是我们的参数，第三个是对响应做点事情的回调函数。

回调在这里很重要，因为我们需要等待来自服务器的响应才能继续我们的后续操作。我们不知道我们的API请求是否会成功，所以在通过get请求将参数发送到search / tweets后，我们继续执行我们的同步任务，执行完后等待并执行异步任务。 一旦Twitter响应，就会调用我们的回调函数。 Twitter将向我们发送错误（错误）对象或响应对象。 在我们的回调函数中，我们可以使用if（）语句来确定我们的请求是否成功，然后相应地对新数据进行操作。

