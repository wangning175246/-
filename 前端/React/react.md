#### 启动配置

##### 安装

```
npx create-react-app react-01 
```

可以是用npx create-react-app my-app 创建一个react项目。他会帮助我们安装react react-dom react-scripts

react 底层的API

react-dom 渲染页面

react-scripts 构建react应用的一些脚本文件，webpack的一些配置都在react中。

##### 启动

```shell
npm run start 
```

而后执行npm run start  实际他调用的是react-scripts start ，但是内部仍然调用的webpack

如果要查看react的给我们的默认的配置 可以执行 `npm run eject`, 他会把所有的配置都生成出来。一般不建议执行。

#### vscode快捷键

vscode 安装 ES7 React/Redux/GraphQL/React-Native snippets 这个插件

而后输入rcc,直接会出现自动创建一个类组件
输入rcf,创建一个函数组件。 

#### 目录结构规范

初始化完项目后，我们会新建一个components目录，里面存放的是所有的组件，compoents目录里面在新建一个index.js负责导出所有的组件，每个组件一个目录，这个目录里面放这个组件的所有东西，一般会在组件中新建一个index.js，用于存放组件的js代码

导入的这个组件的时候，我们可以不用写index.js，因为会自动找目录中的index.js文件，所有要导出组件名称一定要大写否则会导出不了。

```shell
|-components
|	|___index.js 负责导出所有的组件
|	|___组件1
|		|___index.js 组件1的组件。有可能组件下还包含其他小组件
|		|____item.js 组件1需要的小组件
|	|___组件2
|		|___index.js
|-Service
|   |___api.js  所有的后台接口的定义
|   |___index.js  定义请求后台的数据方法
|-APP.js 所有的组件的封装
|-index.js 引入APP组件,渲染到html页面
```

#### 介绍

react是构建用户界面的一个库。
mvc里面有mode control  view ，而react主要是view,没有mode，control。如果想做状态管理，路由，需要一些状态管理，路由类的东西，

##### 特定优势

​	虚拟dom
​	组件化
​	jsx

***开始下面测试前可以删除src中的所有文件***

#### 创建组件

react中的组件就是html标签，可以ReactDOM.render()函数把创建的组件渲染到页面中。默认渲染的页面是index.html。

reactDOM.render接收的参数

* 一个是 html元素或者存放html标签的变量，不用加引号，里面写的是jsx语法 ，如果html特别多的时候这个参数需要写很多东西，所有我们可以把它提取出去。只能有一个根元素，不能包含多个根元素。index04.js
* 第二个是container ,表示我们的把元素渲染到index.html中的那个地方。

写组件的时候，返回的标签，只能包含一个根元素

```react
import React from 'react'
import { render } from 'react-dom'

class   App extends React.Component{
    render (){
        return (
            <div >
                <h1>styled-conponents生成的标签</h1>
            </div>
        )
    }
}

render(
    <App/>,
    document.querySelector("#root")
)
```

 渲染到页面的时候会多加一个div，我们可以使用如下让页面渲染中不多加一个div,有2种方式

* 使用Fragment 

```react
import React, { Component,Fragment} from 'react'
export default class TodoHeader extends Component {
    render() {
        return (
            <Fragment>
                TodoHeader
            </Fragment>
        )
    }
}
```

* 使用空标签

```react
import React, { Component,Fragment} from 'react'
export default class TodoHeader extends Component {
    render() {
        return (
            <>
                TodoHeader
            </>
        )
    }
}
```

##### 创建组件

* 方式1

```react
import React from 'react'
import ReactDOM from 'react-dom'
//  定义的变量必须使用才可以，不适用会报错

// 常见一个简单的react元素，react元素就是html的标签。jsx的语法所以可以 添加html代码不需要而不需要引号
let app = <h1>Welcome React !!!</h1>

const createApp = (x)=> {
    // {x} 是引用变量
    //  只要是在jsx中写js的代码只要加上{}即可
    return <h1>{x}</h1>
}

app=createApp("Welcome React !!!")

//  react中定义的组件的第一种方式，箭头函数
const createApp2 = (press)=> {
    return (
        // jsx中可以嵌套多个html标签。
        <div>
            {/* 如果想在jsx中引用js代码，只要加上{}即可，因为注释也是js的所以也加者{} */}
            <h1>Welcome {press.titile}</h1>
            <p>牛逼的react</p>
        </div>
    )
}

app=createApp2({
    titile:"react"
})

ReactDOM.render(

    // <h1>Welcome React !!!</h1> 
    app, 
    document.querySelector("#root")

)
```

* 第二种方式

  ```react
  import React from 'react'
  import ReactDOM from 'react-dom'
  const APP3=(press)=> {
      return (
          <div>
              <h1 title={press.titile}>Welcome {press.titile}</h1>
              <p>牛逼的react</p>
          </div>
      )
  }
  
  ReactDOM.render(
      <APP3 titile="react"/>, 
      // 这个找的是html中的div root
      document.querySelector("#root")
  )
  ```

* 第三种 类的方式  vscode 快捷键rcc

  ```react
  react
  import React from 'react'
  import { render } from 'react-dom'
  
  // 定义组件的第二种方式 ，使用类
  // 需要继承React.Component 是react最基础的组件类
  // 之所以使用类的方式，是因为我们可以在里面定义声明周期的检测的，属性的检查等一些东西
  class   App extends React.Component{
      render (){
          // 定义了一个组件的话 组件上传递的数据都是属于 props
          console.log(this.props)
          return (
              <div>
                  <h1>类组件</h1>
                  <p>{this.props.desc}</p>
              </div>
          )
      }
  }
  
  // 类的方式的 实现方式
  // const app=new App({
  //     desc:"类组件需要继承React.Component 是react的最基础的组件"
  // }).render()
  
  render(
      <App desc="类组件需要继承React.Component 是react的最基础的组件" />,
      // app,
      document.querySelector("#root")
  )
  ```

##### 组件之间可以嵌套

```react
import React from 'react'
import { render } from 'react-dom'

// 组件的可以嵌套

const Header = ()=><h1>类组件</h1>

class   App extends React.Component{
    render (){

        console.log(this.props)
        return (
            <div>
                {/* 嵌套组件 */}
                <Header />
                <p>{this.props.desc}</p>
            </div>
        )
    }
}


render(
    <App desc="类组件需要继承React.Component 是react的最基础的组件" />,
    // app,
    document.querySelector("#root")
)
```

#### jsx

jsx 在react中可以直接给变量赋值给html标签，而不需要加"" ，这就是jsx
如果想在jsx中引用js代码，只要加上{}即可，因为注释也是js的所以也要加上{}

index.js

```react
import React from 'react'
import ReactDOM from 'react-dom'

const APP3=(press)=> {
    return (
        <div>
        	{/* 如果想在jsx中引用js代码，只要加上{}即可，因为注释也是js的所以也加者{} */}
            <h1 title={press.titile}>Welcome {press.titile}</h1>
            <p>牛逼的react</p>
        </div>
    )
}

ReactDOM.render(
    // 传递参数的方式
    <APP3 titile="react"/>, 
    // 这个找的是html中的div root
    document.querySelector("#root")
)
```

react的是实现原理。他底层是主要是依赖React.createElement去创建dom的。

```react
import React from 'react'
import { render } from 'react-dom'



//  react 使用jsx的方式
// class   App extends React.Component{
//     render (){

//         console.log(this.props)
//         return (
//             <div>
//                 <p>{this.props.desc}</p>
//             </div>
//         )
//     }
// }

// jsx 的实现原理

// 以下html用js 虚拟dome的表示方式
    //<div className="app" id="appRoute"> 
    //    <h1 className="title">类组件</h1>
    //    <p id="p">类组件需要继承React.Component 是react的最基础的组件</p>
    //</div> 
// const app={
//     tag:"div",
//     attrs:{
//         className:"app",
//         id:"appRoute"
//     },
//     children:[
//         {
//             tag:"h1",
//             attrs:{
//                 id:"title"
//             }
//         },
//         {
//             tag:"p",
//             attrs:{
//                 id:"p"
//             }
//         }
//     ]
// }

//  react会把react类的创建组件的方式编译成如下代码
class App1 extends React.Component{
    render(){
        return(
            React.createElement(
                "div",
                {
                    className:"app",
                    id:"appRoot"
                },
                React.createElement(
                        "h1",
                        {
                            id:"title"
                        },
                        "jsx 原理"
                ),
                React.createElement(
                        "p",
                        {
                            id:"p"
                        },
                        this.props.desc
                )
            )
        )
    }
}


render(
    <App1 desc="类组件需要继承React.Component 是react的最基础的组件" />,
    // app,
    document.querySelector("#root")
)
```

#### 组件的参数 props

传递给组件的所有参数都可以通过props获取，props改变之后组件会重新渲染。

```react
import React from 'react'
import {render} from 'react-dom'


class App extends React.Component {
    render(){
        return (
            <div>
                {this.props.des}
            </div>
        )
    }
}
// 参数
class App2 extends React.Component {
    render(){
        return (
            <div>
                {this.props.children}
            </div>
        )
    }
}

render(
    <div>
        {/* x是字符串，y是数字 */}
        <App des="App1" x="1" y={1}/>
        <App2>App2</App2>
    </div>,
    document.querySelector("#root")
)
```

快速传递多个参数

```react
import React, { Component } from 'react'
import Todoitem from './Todoitem'

export default class Todolist extends Component {
    render() {
        console.log(this.props.todos)
        return (
            <ul>
                {
                    this.props.todos.map(
                        todolist => {
                            return (
                                <Todoitem
                                    key={todolist.id}
                                    // 单个参数以此传递
                                    // id={todolist.id}
                                    // title={todolist.title}
                                    // isCompleted={todolist.isCompleted}
                                    //  使用这种方式传递比较方便，列表解析
                                    {...todolist}
                                />
                                )
                        }
                    )
                }
            </ul>
        )
    }
}
```

#### prop-typs 参数验证

需要安装prop-typs，可以对基本的类型进行验证，也可以对复杂的类型做验证，需要查看官网

有针对类组件和函数组件两种写法，

```react
import React, { Component} from 'react'
import PropTypes from 'prop-types'
// 我们想限制传递给我们的参数，可以使用prop-typs这个包。

export default class TodoHeader extends Component {
    //  这个是类组件的传递的参数校验
    static propTypes={
        des:PropTypes.string.isRequired
    }
	// 如果没有传递会设置一个默认值，如果外部传递了会覆盖这个
    static defaultProps={
        des:"今日事，今日毕"
    }
    render(props) {
        return (
            <>
            <h1>
                {/* {this.props.title} */}
                {this.props.children}
            </h1>
            <h3>
                {this.props.des}
            </h3>
            </>
        )
    }
}
//  对上面的props中数据的类型做限制,如果传递的类型不读会在前端页面报错,下面的这种校验一般用于函数组件的校验，也可以用于类的
TodoHeader.propTypes={
    m:PropTypes.number.isRequired, // 是number还是必须提供的
    z:PropTypes.number,
}

```

#### 引入css

如果创建标签的时候我们给创建的标签加上了className,或者id，则我们可以直接创建css文件给标签添加属性。
需要在index.js中引入css文件，

index.js 文件

```react
import React from 'react'
import { render } from 'react-dom'
import "./index.css"
// jsx中的标签添加样式


class   App extends React.Component{
    render (){
        // const style={cloer:'#F00'}
        return (
            //  jsx中class需要写成clasName
            <div className="">
                {/* 添加样式需要写两个{{}} ，第一个表示是写jsx代码，第二个是表示样式是个对象{}*/}
                <h1 style={{color:'#F00'}}>样式</h1>
                <p className="has-text-red">{this.props.desc}</p>
            </div>
        )
    }
}


render(
    <App desc="类组件需要继承React.Component 是react的最基础的组件" />,
    // app,
    document.querySelector("#root")
)
```

index.css文件,直接给className是has-text-red 添加样式。

```css
.has-text-red{
    color: #ff0000;
}
```

#### 自动生成className

classnames ，这个是生成标签中class用的 ,在jsx中如果需要添加class标签，必须写成className
npm  install classnames

```react
import React from 'react'
import {render} from 'react-dom'
import classNames from 'classnames'

class App extends React.Component {
    render(){
        return (
            <div>
                <h1>xxxxx</h1>
                <p className={classNames('a',{'b':true,'c':true})}>classNames的应用</p>
            </div>
        )
    }
}
render(
    <App/>,
    document.querySelector("#root")
)
```

渲染到页面的结果，打开浏览器f12查看

```html
<p class="a b c">classNames的应用</p>
```

#### 样式独立

styled-components 
npm install styled-components

```react
import React from 'react'
import {render} from 'react-dom'
import styled from 'styled-components'

const Title = styled.h1`
    color:#F00
`

class App extends React.Component {
    render(){
        return (
            <div>
                <h1>xxxxx</h1>
                <Title>xxxxxx</Title>
            </div>
        )
    }
}

render(
    <App/>,
    document.querySelector("#root")
)
```

#### 组件的快速导入导出

* 方式1

  第一种导出方式,我们在导入的时候可以在做一些处理

```react
import TodoHeader from "./TodoHeader"
import TodoInput from "./TodoInput"
import TodoList from './TodoList'
export{
    TodoHeader,
    TodoInput,
    TodoList
}
```

* 方式2

```react
export {default as TodoHeader} from './TodoHeader'
export {default as TodoInput} from './TodoInput'
export {default as TodoList} from './TodoList'
```

#### 组件绑定事件

* 有名函数

```react
import React, { Component } from 'react'

export default class Liked extends Component {
    constructor(){
        super()
        this.state={
            isLiked:false
        }
    }
    handleLikedClick=()=>{
         this.setState({
             isLiked:!this.state.isLiked
         })
    }
    render() {
        return (
            <div>
                <span onClick={this.handleLikedClick}>
                    {
                        this.state.isLiked ?"取消 🧡":  "喜欢 🖤"
                    }
                </span>
            </div>
        )
    }
}

```

* 匿名函数

```react
<button onClick={()=>{
    console.log(this.state.inputValue)
}}>{this.props.add}</button>
```

* 函数传递参数

```react
<button onClick={this.handlerButtonClick.bind(this,123445)}>{this.props.add}</button>
```

* 示例

  react-01 git分支05 获取input中输入的内容

#### state

###### 定义和使用state

```react
import React, { Component } from 'react'
import { TodoHeader,TodoInput,TodoList } from './components'

export default class App extends Component {

    constructor(){
        super()
        this.state={
            des:"今日事，今日毕",
            title:"代办事项类别",
            // 列表的渲染
            todolist:[
                {
                    id:1,
                    title:"吃饭",
                    isCompleted:true
                },
                {
                    id:2,
                    title:"睡觉",
                    isCompleted:false
                }
            ]
        }
    }

    render() {
        return (
            <div>
                <TodoHeader  des={this.state.des} x="1" y="2" z={3} m={4}>
                    {/* 调用状态的定义 */}
                    {this.state.title}
                </TodoHeader>
                <TodoInput add="添加"/>
                {/* 将state中的的数据向下传递 */}
                <TodoList todos={this.state.todolist}/>
            </div>
        )
    }
}
```

###### 修改state的数据

state的数据不能直接修改需要，如果是直接修改可以修改成功，但是页面不会同步的渲染。需要使用this.setState(传入要修改的数据)
setState执行完成会立即把数据渲染到页面中进行显示
setState接受是异步执行的，他接受两个参数。
	第一个函数，必须是一个对象，或者一个箭头函数，这个函数也必须返回一个对象，
	第二函数是一个回调函数，当数据修改成功后会执行第二个函数，

```react
import React, { Component } from 'react'

export default class Liked extends Component {
    constructor(){
        super()
        this.state={
            isLiked:false
        }
    }
    handleLikedClick=()=>{
        // 使用如下方式修改可以生效，但是页面不会重新渲染
        // this.state.isLiked = ! this.state.isLiked
        // 如果需要修改state中的数据使用如下方式,可以有两个参数
        // 第一参数有两种情况，第一种是一个对象如下
        // this.setState({
        //     isLiked:!this.state.isLiked
        // })
        //  第二种情况是一个方法,使用这种方法修改，因为pevState是上一次的isLiked的值，
        this.setState((prevState)=>{
            console.log(prevState) // false
            console.log("这个console后执行，内部",this.state.isLiked) false
            return{
                // isLiked:! this.state.isLiked
                isLiked:!prevState.isLiked
            }
        },
        // 由于setState是异步的，如果想要获取最新的state，应该在这个回调里面来获取，这个获取的是页面上最新的
        ()=>{console.log(this.state.isLiked)}
        )
        console.log("这个console先执行，外部",this.state.isLiked)

    }
    render() {
        return (
            <div>
                <span onClick={this.handleLikedClick}>
                    {
                        this.state.isLiked ?"取消 🧡":  "喜欢 🖤"
                    }
                </span>
            </div>
        )
    }
}
```

#### 生命周期

声明周期实际就是回调，在数据渲染到页面的过程中添加的回调函数。

* shouldComponentUpdate
  	默认行为是state每次发生变化组件都会重新渲染，可以根据他的返回值决定当此state的更改是否渲染到页面，
    	当props或者state发生变化时，shouldComponentUpdate会在渲染前调用

  ```react
  shouldComponentUpdate(nextProps,nextState){
      console.log(nextProps,nextState)
      // 这个this之前的数据，因为每次更新数据的时候先执行shouldComponentUpdate，而后才会执行更新数据的函数
      console.log(this.props)
      //  如下这样不是每次都会去执行 这个函数，只有值改变的时候才会去更新。
      return nextProps.isCompleted !== this.props.isCompleted 
  }
  ```

  	如果shouldComponentUpdate他的返回值是false,则不会调用componentDidUpdate()
  
* componentDidMount 
  	方法会在组件已经被渲染到DOM（浏览器）中后运行，一般页面的后台的数据请求都在这个里执行，收到数据可以执行setSate去

* React.PureComponent
  	默认的继承Component实现的类组件没有实现对数据变化的校验，会渲染所有数据，我们可以继承PureComponent，他实现了对数据的比较而后做渲染，只是渲染改变的数据。

* static getDerivedStateFromProps

  会在调用render方法之前调用，并且在初始化挂载及后续更新时都会被调用。他返回一个对象来更新state，如果为null，则表示不更新任何内容。
  如果存在的全局state和局部state，则如果全局state更新了则会在局部的getDerivedStateFromProps方法中跟新局部的state。

  ```react
  static getDerivedStateFromProps(props){
      return {
          completedText:props.isCompleted? "完成":"未完成"
      }
  }
  ```

* UNSAFE_componentWillReceiveProps 

  会在已挂载的组件接收新的props之前被调用 

  ```react
  // 每次proce或者state变化的时候会执行
  UNSAFE_componentWillReceiveProps(nextProps){
      this.setState({
          completedText:nextProps.isCompleted?"完成":"未完成"
      })
  }
  ```

#### 函数组件使用state

userState 
	类似于componentDidMount ，也就是说在函数组件中也可以使用State}
useEffect 类似于shouldComponentUpdate
	修改网页标题，动态更新
	也可以修改其他地方。

```react
import React,{useState,useEffect} from 'react'
import {render} from 'react-dom'

const Counter=()=>{
    //  userState是一个函数，可以有一个参数，返回一个数组，第一个元素是传递的参数，第二个是处理参数的方法
    // 解构处理数组的两个值
    const [count,setCount] = useState(0)
    useEffect(()=>{
        document.title=`当前数量未${count}`
    })
    return(
        <div>
            {/* setcount就是返回的函数，和SetState不一样的地方这个参数就是一个新值即可 */}
            <button onClick={()=>setCount(count-1)}>-</button>
            {/* 这个就是useState创建的值 */}
            <span>{count}</span>
            <button onClick={()=>setCount(count+1)}>+</button>
        </div>
    )
}

render(
    <Counter/>,
    document.querySelector("#root")
)
```

#### 请求后台数据

请求后台数据时通过axios这个模块进行请求数据的，
请求后台的数据的时候，如果后台数据请求比较慢，这时候我们需要在页面中显示正在加载，可以看`代码.md`

service/api.js

```js
export default {
    baseUrl:'http://jsonplaceholder.typicode.com',
    todos:"/todos"
}
```

service/index.js

```js
import axios from 'axios'
import api from './api'
const ajax=axios.create({
    baseURL:api.baseUrl
})

export const getTodos=()=>{
    return ajax.get(api.todos)
}
```

APP.js中使用

```js
getTodos().then(
            resp=>{
                // 判断返回状态码
                if (resp.status === 200){
                   setTimeout(
                       ()=>{  
                           this.setState({
                                todolist:resp.data,
                                isLoading:false
                            })
                            
                        },
                        10000
                   )
                  
                }
            }
    	// 如果过出现异常的时候执行
        ).catch(
            err=>{
                console.log(err)
            }
        )
```

#### ref

操作input标签，通过ref给input设置焦点

```react
// react里面我们想通过ref获取组件或者操作dom元素，要使用ref之前必须先调用React.CreateRef方法。来创建一个ref
import React,{Component,createRef} from 'react'
import PropTypes from 'prop-types'

export default class TodoInput  extends Component {
    constructor(){
        super()
        this.state = {
            inputValue:""
        }
        //  在构造函数中创建ref
        this.inputDome=createRef()
    }
    handleInputChange=(e)=>{
        this.setState({
            inputValue:e.currentTarget.value
        })

    }

    handlerButtonClick=()=>{
        // 这个this.inputDome就是我们的input标签。
        // console.log(this.inputDome,"xxxxxxxxxx")
        console.log(this.props)
        // 做简单的验证，为空的则不添加
        if (this.state.inputValue === ""){
            return
        }
        this.props.option(this.state.inputValue)
        this.setState(
            {
                inputValue:""
            }
            // 添加这个之后点击添加完成后会自动聚焦到intput框中
        ,()=>this.inputDome.current.focus()
        )
    }
    handlerKeyUp=(e)=>{
        // code 13 回车
        if(e.keyCode === 13){
            this.handlerButtonClick()
        }
    }
    render(){
        return (
            <div>
                <input
                type="text" 
                value={this.state.inputValue}
                onChange={this.handleInputChange}
                // 回车也可以添加的到列表
                onKeyUp={this.handlerKeyUp}
                //使用ref
                ref={this.inputDome}
                />
                <button onClick={this.handlerButtonClick}>{this.props.add}</button>
            </div>
        )
    }
}
```

#### context 跨组件通信

createConext这个方法的结果是一个对象，里面有Provider和Consumer
	provide 用于提供状态数据，最终体现为组件形式
	Consumer用于接收状态 最终体现为组件形式

```react
import {render} from 'react-dom'
import React, { Component,createContext} from 'react'
// createContext 主要是用来跨组件进行通信的方法


// createConext这个方法的结果是一个对象，里面有Provider和Consumer
// provide用于提供状态数据
// Consumer用于接收状态
const{
    Provider, //数据的提供者组件
    Consumer:CounterConsumer //数据的消费者组件
}=createContext()

// 数据的生产组件,可以进行数据的修改，对Provider的封装，因为直接使用Provider，不方便管理
class CounteProvider extends Component {
    constructor(){
        super()
        //  这里的状态是共享的，任何CounterProvider的后代组件都可以通过CounterConsumer接收组件
        this.state={
            count:100
        }
    }
    // 这里的方法也可以通过Provider共享下去，
    incrementCount =()=>{
        this.setState({
            count:this.state.count+1
        })
    }
    decrementCount =()=>{
        this.setState({
            count:this.state.count-1
        })
    }
    render(){
        return(
            // 使用provider这个组件,他必须有一个value,可以传递任何数据,一般传递一个对象比较合理
            <Provider value={{
            count:this.state.count,
            incrementCount:this.incrementCount,
            decrementCount:this.decrementCount
            }}>
                {this.props.children}
            </Provider>
        )
    }
}

// 自己定义的一个组件
class CountBtn extends Component {
    render() {
        return (
            // 使用CounterConsumer接收数据count这个值
            // 注意Consumer的children必须是一个函数，这个函数有一个参数，这个参数就是Provider的value
            <CounterConsumer>
                {
                    {/*实际是整个value传递了进行，但是这里使用了字典解析，所有只是接受他们两个参数*/}
                    ({incrementCount,decrementCount})=>{
                        let handler = this.props.type==="decrement"?decrementCount:incrementCount
                        return  <button onClick={handler}>{this.props.children}</button> 
                    }
                }
            </CounterConsumer>
        )
    }
}

// 自定的组件
class Counter extends Component {
    render() {
        return (
            <CounterConsumer>
                {/* 他里面的内容必须是一个function */}
                {
                    (arg)=>{
                        // 可以获取CounteProvider中提供的值
                        console.log(arg)
                        return (
                            <span>{arg.count}</span>
                        )
                    }
                }
            </CounterConsumer>
        )
    }
}

// APP组件
class App extends Component {
    render() {
        return (
            <>
                <CountBtn type="decrement">-</CountBtn>
                <Counter />
                <CountBtn type="increment">+</CountBtn>
            </>
        )
    }
}

// 需要使用CounteProvider把所有要提供的数据包含进来。
render(
    <CounteProvider>
    <App/>
    </CounteProvider>,
    document.querySelector("#root")
)
```

#### 高阶组件

高阶组件实现了类似于装饰器的功能，可以不用改变原始组件的前提下增强原始组件的功能,

他的实现了高级函数，返回值是一个组件，可以进行拦截组件，可以增强组件的功能。

hig.js

```react
import React,{Component} from 'react'
const hig =(Youcomponent)=>{
    return class Hig extends Component{
        render() {
            return(
                <div>
                    <Youcomponent {...this.props}/>
                    装饰器
                </div>
            )
        } 
    }
}

export default hig

```

Another.js 被装饰的组件

```react
import React, { Component } from 'react'
import hig from './hig'

class Another extends Component {
    render() {
        return (
            <div>
                Another {this.props.text}
            </div>
        )
    }
}
export default hig(Another)
```

配置使用@hig的方式

* 安装

  ```shell
  npm install react-app-rewired --save-dev
  npm install @babel/plugin-proposal-decorators
  npm install customize-cra --save-dev
  ```

* 配置

  修改package.json里面把scripts里面的react-scripts替换成react-app-rewired

  在项目根目录下创建config-overrides.js而后配置

  ```js
  module.exports=(config)=>{
      // 而后在这按照官网进行配置
      return config
  }
  ```

  直接配置比较麻烦，可以使用customize-cra进行配置

  ```js
  module.exports=(config)=>{
      // 而后在这按照官网进行配置
      return config
  }
  const {override,addDecoratorsLegacy} = require("customize-cra")
  module.exports = override(
      addDecoratorsLegacy(),
  )
  ```

* 配置完成可以直接在程序中使用@装饰器语法了

  ```react
  import React, { Component } from 'react'
  import hig from './hig'
  
  @hig
  class Another extends Component {
      render() {
          return (
              <div>
                  Another {this.props.text}
              </div>
          )
      }
  }
  export default Another
  ```

