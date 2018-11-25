# js错误处理权威指北


##### By [Lukas Gisder-Dubé](https://levelup.gitconnected.com/@gisderdube1?source=post_header_lockup) | nov 14, 2018

[原文](https://levelup.gitconnected.com/the-definite-guide-to-handling-errors-gracefully-in-javascript-58424d9c60e6)

接着我上一篇文章，我想谈谈异常。我肯定你之前也听过——异常是个好东西。一开始，我们害怕异常，毕竟写bug容易被人喷。其实通过修bug，我们实际上学会了下次开发怎么避免这个bug并且可以做得更好。

在生活中，我们常说吃一堑长一智。但对于打代码来说，有些不一样。我们的编译器和一些工具现在都很智能，不但告诉我们哪里出错，还帮助我们优化代码【译者：eslint之类的】（有可能还会教我们如何修复bug）。

### js异常的一般处理方法

	throw new Error('something went wrong')
	
以上代码将会创建异常实例并停止执行你的脚本，除非你在错误回调里做一些处理。当你开始了js开发者的职业生涯，你自己很可能不会这样做，但是你会在其它的库里（或者运行时）看到类似‘ReferenceError: fs为定义’这样的错误。

#### 异常对象

异常对象有两个属性供我们使用。第一个是message，是你传递给异常的构造函数的参数，比如：

	new Error('This is the message')
	
你可以使用message属性来访问到该消息：
	
	const myError = new Error(‘please improve your code’)
	console.log(myError.message) // please improve your code
	
第二个参数是异常堆栈跟踪，非常重要。你可以使用stack属性来访问。异常堆栈为你提供历史记录（调用堆栈），从中可以查看到哪些文件导致了异常。堆栈顶部也包括了消息，然后是实际的堆栈，从距离异常最近的点开始，然后一直到最外层与异常有关的文件（译者：调用关系的追溯）：

	Error: please improve your code
	 at Object.<anonymous> (/Users/gisderdube/Documents/_projects/hacking.nosync/error-handling/src/general.js:1:79)
	 at Module._compile (internal/modules/cjs/loader.js:689:30)
	 at Object.Module._extensions..js (internal/modules/cjs/loader.js:700:10)
	 at Module.load (internal/modules/cjs/loader.js:599:32)
	 at tryModuleLoad (internal/modules/cjs/loader.js:538:12)
	 at Function.Module._load (internal/modules/cjs/loader.js:530:3)
	 at Function.Module.runMain (internal/modules/cjs/loader.js:742:12)
	 at startup (internal/bootstrap/node.js:266:19)
	 at bootstrapNodeJSCore (internal/bootstrap/node.js:596:3)
	 
### 抛出并处理异常

现在单个异常实例没有任何卵用。例如

	new Error('...')
	
以上代码并不会触发任何东西。当异常被抛出，事情就变得有趣了一些。然后，跟上文说的一样，js引擎停止执行你的脚本，除非你做了异常处理。记住，手动抛出异常，还是由库抛出异常，抑或是运行时抛出异常，都没关系。让我们看看在不同场景如何处理这些异常。

### try....catch

这是最简单的，但是经常被忘记的异常处理方法——多亏了async/await，越来越多人使用它了。它可以用来捕捉各种类型的非异步错误。例如：

	const a = 5
	
	try {
	    console.log(b) // b is not defined, so throws an error
	} catch (err) {
	    console.error(err) // will log the error with the error stack
	}
	
	console.log(a) // still gets executed
	
如果我们不将console.log（b）包装在try ... catch块中，脚本执行将停止。

### ...finally

有时候有不管有没有异常，都希望执行的代码。你可以使用finally。通常，它与try ... catch语句之后只有一行相同，但有时它可能很有用

	const a = 5
	
	try {
	    console.log(b) // b is not defined, so throws an error
	} catch (err) {
	    console.error(err) // will log the error with the error stack
	} finally {
	    console.log(a) // will always get executed
	}
	
### 异步——回调

异步，这是你在使用js时不得不去考虑的一个主题。当你有个异步方法，并且改方法内部发生异常时，你的脚本会继续执行，不会立即出现任何异常。当使用回调来处理异步方法的返回时（顺便提一下，不提倡使用回调），你通常会接收两个参数，例如：

	myAsyncFunc(someInput, (err, result) => {
	    if(err) return console.error(err) // we will see later what to do with the error object.
	    console.log(result)
	})
	
如果发生异常，err参数就是那个异常。如果没有，参数就是undefined或者时null。这样做很重要，不然如果你试图访问result.data时，乳沟发生异常，得到的结果就是undefined。

### 异步——Promises

处理异步的另一种方法时使用promises。除了代码更易读，异常处理也改进了。我们只需要在catch里处理异常就好了，不需要关心怎么捕捉异常。当链式调用promises时，catch会捕获自promise或最后一个catch块执行以来的所有错误。请注意，没有catch的promises不会终止脚本，但是会降低你的异常信息的可读性：

	(node:7741) UnhandledPromiseRejectionWarning: Unhandled promise rejection (rejection id: 1): Error: something went wrong
	(node:7741) DeprecationWarning: Unhandled promise rejections are deprecated. In the future, promise rejections that are not handled will terminate the Node.js process with a non-zero exit code. */
	
因此，记得为你的promises加上catch：

	Promise.resolve(1)
	    .then(res => {
	        console.log(res) // 1
	
	        throw new Error('something went wrong')
	
	        return Promise.resolve(2)
	    })
	    .then(res => {
	        console.log(res) // will not get executed
	    })
	    .catch(err => {
	        console.error(err) // we will see what to do with it later
	        return Promise.resolve(3)
	    })
	    .then(res => {
	        console.log(res) // 3
	    })
	    .catch(err => {
	        // in case in the previous block occurs another error
	        console.error(err)
	    })

### try … catch — again

随着js引入async / await，我们回到了处理异常的原始方式，使用try ... catch ... finally，这使得处理它们变得轻而易举：

	;(async function() {
	    try {
	        await someFuncThatThrowsAnError()
	    } catch (err) {
	        console.error(err) // we will make sense of that later
	    }
	
	    console.log('Easy!') // will get executed
	})()
	
由于这和我们处理“普通”同步异常方式一样，所以如果有需要，更容易使用更大作用域的catch语句。

## 服务器端异常产生与处理

现在我们有处理异常的工具了，让我们看看在实际情况中可以用这些工具做些什么。异常产生后能在后端正确处理是app的关键部分。这列有几种处理异常的方法。我将向你展示自定义error构造函数和错误代码的方法，我们可以轻松地将其传递给前端或任何API调用者。构建后端的细节不重要，基本思路不变。

我们用Express.js作为路由框架。让我们考虑一下我们希望获得最有效的异常处理的结构。我们想要：

1. 一般异常处理，如某种回退，基本上只是说：“有错误，请再试一次或联系我们”。这不是特别好，但至少通知了用户，app出错了——而不是无限加载或者白屏。
2. 特殊错误处理为用户提供详细信息，让用户了解有什么问题以及如何解决它们，例如，数据丢失，已存在条目等等。

### 构建一个自定义error构造函数

我们使用存在的erroe构造函数并且继承它。继承在js中是一件危险的事，但是在这里，我觉得非常有用。为什么我们需要它？我们仍然希望堆栈跟踪为我们提供良好的调试体验。拓展js自带的error构造函数就可以继续使用堆栈跟踪。我们唯一要做的就是添加代码和传递前端error.code

	class CustomError extends Error {
	    constructor(code = 'GENERIC', status = 500, ...params) {
	        super(...params)
	
	        if (Error.captureStackTrace) {
	            Error.captureStackTrace(this, CustomError)
	        }
	
	        this.code = code
	        this.status = status
	    }
	}
	
	module.exports = CustomError
	
### 如何处理路由

完成error的自定义之后，我们需要设置路由结构。正如我所指出的，我们需要一个单点truth来进行异常处理，这意味着对于每个路由，我们都希望具有相同的异常处理行为。express默认是不支持的，因为路由封装好了。

为了解决这个问题，我们可以实现一个路由处理程序，并把实际的路由逻辑定义为普通函数。这样，如果路由功能（或任何内部函数）抛出异常，他将返回到路由处理程序，然后可以返回给前端。当后端发生错误时，我们用以下格式传递给前端——比如一个JSON API：

	{
	    error: 'SOME_ERROR_CODE',
	    description: 'Something bad happened. Please try again or     contact support.'
	}
	
准备好大吃一惊吧，当我说下面这段话时，我的学生总是生气：

> 如果你咋看之下不太理解，不用担心。只要使用一段时间，你就会发现为什么要那样。

顺便说一下，这叫自上而下学习，我非常喜欢。

路由处理程序像这样子：

	const express = require('express')
	const router = express.Router()
	const CustomError = require('../CustomError')
	
	router.use(async (req, res) => {
	    try {
	        const route = require(`.${req.path}`)[req.method]
	
	        try {
	            const result = route(req) // We pass the request to the route function
	            res.send(result) // We just send to the client what we get returned from the route function
	        } catch (err) {
	            /*
	            This will be entered, if an error occurs inside the route function.
	            */
	            if (err instanceof CustomError) {
	                /* 
	                In case the error has already been handled, we just transform the error 
	                to our return object.
	                */
	
	                return res.status(err.status).send({
	                    error: err.code,
	                    description: err.message,
	                })
	            } else {
	                console.error(err) // For debugging reasons
	
	                // It would be an unhandled error, here we can just return our generic error object.
	                return res.status(500).send({
	                    error: 'GENERIC',
	                    description: 'Something went wrong. Please try again or contact support.',
	                })
	            }
	        }
	    } catch (err) {
	        /* 
	        This will be entered, if the require fails, meaning there is either 
	        no file with the name of the request path or no exported function 
	        with the given request method.
	        */
	        res.status(404).send({
	            error: 'NOT_FOUND',
	            description: 'The resource you tried to access does not exist.',
	        })
	    }
	})
	
	module.exports = router

我希望你看下代码的注释，我想这比我在这解释有意义。现在，让我们看下实际的路由文件长什么样子：

	const CustomError = require('../CustomError')
	
	const GET = req => {
	    // example for success
	    return { name: 'Rio de Janeiro' }
	}
	
	const POST = req => {
	    // example for unhandled error
	    throw new Error('Some unexpected error, may also be thrown by a library or the runtime.')
	}
	
	const DELETE = req => {
	    // example for handled error
	    throw new CustomError('CITY_NOT_FOUND', 404, 'The city you are trying to delete could not be found.')
	}
	
	const PATCH = req => {
	    // example for catching errors and using a CustomError
	    try {
	        // something bad happens here
	        throw new Error('Some internal error')
	    } catch (err) {
	        console.error(err) // decide what you want to do here
	
	        throw new CustomError(
	            'CITY_NOT_EDITABLE',
	            400,
	            'The city you are trying to edit is not editable.'
	        )
	    }
	}
	
	module.exports = {
	    GET,
	    POST,
	    DELETE,
	    PATCH,
	}

在这些例子中，我没有对实际请求做任何事情，我只是假装不同的异常场景。 因此，例如，GET / city将在第3行结束，POST / city将在第8行结束，依此类推。 这也适用于查询参数，例如 GET / city？startsWith = R. 从本质上讲，您将有一个未处理的异常，前端将收到：

	{
	    error: 'GENERIC',
	    description: 'Something went wrong. Please try again or contact support.'
	}
	
或者你手动抛出CustomError，例如：

	throw new CustomError('MY_CODE', 400, 'Error description')
	
上述代码会变成：

	{
	    error: 'GENERIC',
	    description: 'Something went wrong. Please try again or contact support.'
	}
	
现在我们有了这个漂亮的后端设置，我们不再有错误日志泄漏到前端，并将始终返回有关出错的可用信息。

## 向用户显示异常

下一步也是最后一步是管理前端的异常。 在这里，您希望使用第一部分中描述的工具处理前端逻辑本身产生的异常。但是，也必须显示来自后端的异常。 我们先来看看我们如何显示异常。 如前所述，我们将在演练中使用React。

### 把异常保存在react state中

接下来我们要澄清的是具有匹配视觉表示的不同类型的异常。就像在后端一样，有三种类型：

1. 全局异常，例如，其中一个常见的异常是来自后台，用户没有登录等。
2. 来自后台的具体异常，例如，用户向后台发送登录凭证。后台答复密码错误
3. 前端导致的异常，例如，电子邮箱格式错误。

2和3虽然源头不一样，但是非常类似并且可以在同样的state处理。我们来看看在代码中如何实现。

我们使用react原声state实现，但是，你可以使用类似MobX或Redux这样的状态管理系统。

### 全局异常

通常，我将这些异常保存在最外层的有状态组件中并呈现静态UI元素，这可能是屏幕顶部的红色横幅，模态或其他任何内容，设计实现你自己决定。

来看下代码：

	import React, { Component } from 'react'
	
	import GlobalError from './GlobalError'
	
	class Application extends Component {
	    constructor(props) {
	        super(props)
	
	        this.state = {
	            error: '',
	        }
	
	        this._resetError = this._resetError.bind(this)
	        this._setError = this._setError.bind(this)
	    }
	
	    render() {
	        return (
	            <div className="container">
	                <GlobalError error={this.state.error} resetError={this._resetError} />
	                <h1>Handling Errors</h1>
	            </div>
	        )
	    }
	
	    _resetError() {
	        this.setState({ error: '' })
	    }
	
	    _setError(newError) {
	        this.setState({ error: newError })
	    }
	}
	
	export default Application
	
正如你所看到的一样，Application.js中的状态存在异常。我们也有方法重置和更改异常值。 我们将值和重置方法传递给`GlobalError`组件，在点击‘x’时，该组件会显示异常并重置。让我们来看看 我们将值和reset方法向下传递给`GlobalError`组件：

	import React, { Component } from 'react'
	
	class GlobalError extends Component {
	    render() {
	        if (!this.props.error) return null
	
	        return (
	            <div
	                style={{
	                    position: 'fixed',
	                    top: 0,
	                    left: '50%',
	                    transform: 'translateX(-50%)',
	                    padding: 10,
	                    backgroundColor: '#ffcccc',
	                    boxShadow: '0 3px 25px -10px rgba(0,0,0,0.5)',
	                    display: 'flex',
	                    alignItems: 'center',
	                }}
	            >
	                {this.props.error}
	                &nbsp;
	                <i
	                    className="material-icons"
	                    style={{ cursor: 'pointer' }}
	                    onClick={this.props.resetError}
	                >
	                    close
	                </i>
	            </div>
	        )
	    }
	}
	
	export default GlobalError
	
你可以在第五行看到，如果没有异常，我们不会渲染任何内容。这可以防止我们始终在页面上显示空的红色框。当然，你可以更改此组件的外观和行为。例如，你可以使用Timeout替换'x'，以便在几秒钟后重置异常状态。

现在，你已准备好在任何地方使用此全局异常状态，只需从Application.js传递_setError，然后就可以设置全局异常，例如 当来自后端的请求返回时出现字段error：'GENERIC'。例如：

	import React, { Component } from 'react'
	import axios from 'axios'
	
	class GenericErrorReq extends Component {
	    constructor(props) {
	        super(props)
	
	        this._callBackend = this._callBackend.bind(this)
	    }
	
	    render() {
	        return (
	            <div>
	                <button onClick={this._callBackend}>Click me to call the backend</button>
	            </div>
	        )
	    }
	
	    _callBackend() {
	        axios
	            .post('/api/city')
	            .then(result => {
	                // do something with it, if the request is successful
	            })
	            .catch(err => {
	                if (err.response.data.error === 'GENERIC') {
	                    this.props.setError(err.response.data.description)
	                }
	            })
	    }
	}
	
	export default GenericErrorReq
	
如果你很懒，你可以在这里停下来。即使你有具体异常，也可以随时更改全局异常状态并在页面顶部显示错误框。但是，本文展示如何处理和显示特定的异常。为什么？首先，这是处理异常的指南，所以我不能就此止步。其次，如果你把所有异常都作为全局状态来显示，那么UX人员会感到很难受。

### 处理具体的请求异常

与全局异常类似，我们也可以在其他组件中包含局部异常状态。 程序是一样的：

	import React, { Component } from 'react'
	import axios from 'axios'
	
	import InlineError from './InlineError'
	
	class SpecificErrorRequest extends Component {
	    constructor(props) {
	        super(props)
	
	        this.state = {
	            error: '',
	        }
	
	        this._callBackend = this._callBackend.bind(this)
	    }
	
	    render() {
	        return (
	            <div>
	                <button onClick={this._callBackend}>Delete your city</button>
	                <InlineError error={this.state.error} />
	            </div>
	        )
	    }
	
	    _callBackend() {
	        this.setState({
	            error: '',
	        })
	
	        axios
	            .delete('/api/city')
	            .then(result => {
	                // do something with it, if the request is successful
	            })
	            .catch(err => {
	                if (err.response.data.error === 'GENERIC') {
	                    this.props.setError(err.response.data.description)
	                } else {
	                    this.setState({
	                        error: err.response.data.description,
	                    })
	                }
	            })
	    }
	}
	
	export default SpecificErrorRequest
	
这里要记住的一件事，清除异常通常会有不同的触发器。 使用'x'删除异常是没有意义的。在这里，在发出新请求时清除异常会更有意义。你还可以在用户进行更改时清除异常，例如，当输入值改变时。

### 前端的异常

如前所述，这些异常可以与来自后端的特定异常以相同的方式（状态）处理。 我这次使用带有输入字段的示例，只允许用户删除城市，当他实际提供输入时：
	
	import React, { Component } from 'react'
	import axios from 'axios'
	
	import InlineError from './InlineError'
	
	class SpecificErrorRequest extends Component {
	    constructor(props) {
	        super(props)
	
	        this.state = {
	            error: '',
	            city: '',
	        }
	
	        this._callBackend = this._callBackend.bind(this)
	        this._changeCity = this._changeCity.bind(this)
	    }
	
	    render() {
	        return (
	            <div>
	                <input
	                    type="text"
	                    value={this.state.city}
	                    style={{ marginRight: 15 }}
	                    onChange={this._changeCity}
	                />
	                <button onClick={this._callBackend}>Delete your city</button>
	                <InlineError error={this.state.error} />
	            </div>
	        )
	    }
	
	    _changeCity(e) {
	        this.setState({
	            error: '',
	            city: e.target.value,
	        })
	    }
	
	    _validate() {
	        if (!this.state.city.length) throw new Error('Please provide a city name.')
	    }
	
	    _callBackend() {
	        this.setState({
	            error: '',
	        })
	
	        try {
	            this._validate()
	        } catch (err) {
	            return this.setState({ error: err.message })
	        }
	
	        axios
	            .delete('/api/city')
	            .then(result => {
	                // do something with it, if the request is successful
	            })
	            .catch(err => {
	                if (err.response.data.error === 'GENERIC') {
	                    this.props.setError(err.response.data.description)
	                } else {
	                    this.setState({
	                        error: err.response.data.description,
	                    })
	                }
	            })
	    }
	}
	
	export default SpecificErrorRequest
	
我希望你对如何处理异常有所了解。忘记console.error（错误），它是过去的事情了。 可以使用它进行调试，但它不应该在生产版本中。 为了防止这种情况，我建议你使用一个日志库，我过去一直在使用loglevel，我很满意。