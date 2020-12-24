## Redux思想

> 博客地址
> 	https://blog.csdn.net/special_programmer/article/details/81287631 

#### store

存储应用数据，所有的应用数据存储在一个store中，

#### reducer(dispatch)

Redux 中没有 Dispatcher 的概念 。它使用 reducer 来进行事件的处理，reducer 是一个纯函数，这个函数被表述为(previousState, action) => newState，它根据应用的状态和当前的 action 推导出新的 state。 

Redux 中有多个 reducer，每个 reducer 负责维护应用整体 state 树中的某一部分，多个 reducer 可以通过combineReducers方法合成一个根reducer，这个根reducer负责维护完整的 state，当一个 action 被发出，store 会调用 dispatch 方法向某个特定的 reducer 传递该 action，reducer 收到 action 之后执行对应的更新逻辑然后返回一个新的 state，state 的更新最终会传递到根reducer处，返回一个全新的完整的 state，然后传递给 view。 

每个reducer维护stote数据的一部分数据，更新数据的逻辑在reducer中实现,更新数据不是在原有的数据上更新，而后是把元素的数据复制一份传递到函数中而后更新后返回新的数据

数据的初始化也是在Reducer中初始化，

#### actions

定义动作的类型

git 13 自己实现Redux思想的代码

#### redux原理

```react
<!DOCTYPE html>
<html lang="en">
    <header>
        <meta charset="UTF-8">
        <meta name="viewport" content="width=device-width,initial-scale=1.0">
        <meta http-equiv="X-UA-Compatible" content="ie=edge">
        <title>Redux principle </title>
    </header>
    <body>
        <div >
           <button onclick="store.dispatch({type:'JIAN',n:2})">-</button>
           <span id="countDisplay">10</span>
           <button onclick="store.dispatch({type:'JIA',n:3})">+</button>
        </div>
    </body>
    <script>
        const  countDisplay=document.querySelector("#countDisplay")
        // 定义数据
        const countState ={
            count :5
        }
        const changeState=(state,action)=>{
            if (!state){
                return countState
            }
            switch(action.type){
                case "JIAN":
                    return {
                        ...state,
                        count:state.count-action.n
                    }
                case "JIA":
                    return{
                        ...state,
                        count:state.count+action.n
                    }
                default:
                    state
            }
        }
        // 定义createStros函数,里面
        const createStor=(changeState)=>{
            let state=null
            const getState=()=>state
            const listeners=[]
            const subscribe=(listen)=>{
                listeners.push(listen)
            }
            const dispatch=(action)=>{
                state=changeState(state,action)
                listeners.forEach(listen=>listen());
            }
            dispatch({})
            return {
                getState,
                dispatch,
                subscribe
            }
           
        }
        // 创建Stor
        const store = createStor(changeState)
        const readerCount=()=>{
            countDisplay.innerHTML=store.getState().count
        }
        readerCount()
        store.subscribe(readerCount)
       
    </script>
</html>
```

## 使用商品列表的功能实现

#### 安装

```shel
npm install redux -S
```

#### 使用Redux的目录结构 

分支 14

actions 存放actions类型
	定义动作类型, 添加数据点击+-的时候需要动作类型,
reducers 存放reducers
	多个reduces进行拆分,如何进行合并,在store.js中如何引用reducers,基于以上可以使用combineReducers进行合并多个reducers,

store.js 存放数据的

> 我们开发react在windows调试,需要把window.store=store,这样就可以在浏览器中进行调试了.

手动传递参数,一层一层的传递

自动传递参数,使用react-redux 提供的provider和connect.只用在最顶层传递一次即可，

不论是自动传递还是手动传递参数都需定义actions store reducers

