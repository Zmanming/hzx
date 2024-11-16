# 实验五工作指南

**实验目的** - 开发一个基本的React应用程序，熟悉应用程序的生命周期和钩子（Hooks）。

**如果您在完成本实验室工作时遇到困难或没有足够的 JavaScript 知识，我们建议您观看[课程][iu5-javascript].**

## 实验计划

1. 介绍
2. React
- 2.1. Component
- 2.2. Element
- 2.3. Props
- 2.4. State
3. 创建项目
4. 路由（Routing）
5. 钩子（Hooks）
- 5.1. 钩子状态
- 5.2. 生命周期
- 5.3. 钩子效应
6. 实践练习 - iTunes
- 6.1. iTunes 相册页面
- 6.2. 将页面划分为组件 
- 6.3. 添加导航路径 Breadcrumbs 和详细页面
7. 根据选项细化React应用程序
- 7.1. 实现从模拟对象接收数据
- 7.2. 添加默认图像
- 7.3. 应用程序主菜单
- 7.4. 从Web服务中连接到自己的API
8. 重要事项. CORS（跨源资源共享）
9. 有用的链接


## 1. 介绍

<!-- TODO: 链接到虚拟机（VM）的安装 -->

<!-- TODO: 链接到集成开发环境（IDE）的设置 -->

### 如何使用 API

在之前的实验中，我们使用 Django REST Framework 创建了服务器并编写了一个 API。
现在让我们尝试编写一个可以使用我们的 API 的网站。

在之前，我们使用Django模板来向用户展示界面并显示数据，但现在这样做行不通了。当我们使用Django模板时，我们的服务器同时充当了后端和前端的角色，也就是说，它实现了处理数据的业务逻辑（后端）和生成HTML的模板引擎（前端）。

但现在，我们的服务器只充当后端的角色，它只是简单地提供数据，我们需要编写一个应用程序，这个应用程序能够获取这些数据并展示给用户。我们将有两个独立的应用程序。一个是在Django REST框架上的后端，另一个是在JavaScript上的前端
### 什么是TypeScript

TypeScript是一种带类型的JavaScript。在编写JavaScript代码时，我们经常会遇到动态类型化的问题。这意味着我们只能在程序运行时才能发现错误。TypeScript旨在解决这个问题，因为我们将在代码中编写类型，这样在应用程序构建阶段我们就能得到错误。关于TypeScript是什么以及其中包含的类型，可以阅读[这篇文章][habr-typescript]。

### 什么是JSX/TSX

JSX是JavaScript语法的扩展。这种语法看起来像模板语言，但拥有JavaScript的所有语言特性。编译JSX后，会产生简单的对象——“React元素”。关于JSX的更多信息，可以阅读[这篇文章][habr-react-jsx]. TSX是TypeScript语言中的JSX.

React DOM使用camelCase命名风格来代替HTML属性的常规名称。例如，在JSX中，属性'tabindex'变成了'tabIndex'。而属性'class'则写成'className'，因为'class'这个词在JavaScript中已经被保留了。

## 2. React

React开发的核心在于描述需要在页面上展示的内容（而不是为浏览器编写如何实现这些内容的指令）。这除了其他好处外，还意味着可以大幅减少模板代码的数量。

### 2.1. 组件 Component

React组件是可重复使用的代码片段，它们返回React元素以在页面上显示。最简单的React组件就是一个简单的JavaScript函数，它返回React元素。

```tsx
import { FC } from 'react'

const Welcome: FC = () => {
  return <h1>Привет, Мир</h1>;
}
```

组件也可以是ES6类。

```tsx
import React from 'react'

class Welcome extends React.Component {
  render() {
    return <h1>Привет, Мир</h1>;
  }
}
```

目前在开发中，更倾向于使用函数组件。关于它们的工作原理以及与类组件的区别，可以在[这篇文章][habr-react-diff-class-function-component]中详细了解. 在本实验中，我们只研究**功能组件**.

组件可以由其他组件构成，因此实际上整个页面可以被视为一个组件。

### 2.2. 元素 Element

React元素是React应用程序的基本构建块。它们可能与更广为人知的“组件”概念相混淆，但与组件不同，元素描述了你希望在屏幕上看到的内容。React元素是不可变的。

在标准的React应用程序中，状态是一个对象。React的协调过程（reconciliation process）确定是否需要重新渲染对象，因此需要跟踪这个对象的变化。

换句话说，如果React无法检测到对象的变化，它就不会更新虚拟DOM。

不可变性使得能够观察到这些变化。这反过来又允许React比较对象的旧状态和新状态，并基于这种比较重新绘制对象。

### 2.3. 属性 Props

`props` (属性) — 是传递给React组件的输入数据，从父组件传递到子组件。

示例简单的组件 `Text` ，带有属性.

```tsx
import { FC } from 'react'

interface Props {
  name: string
}

const Text: FC<Props> = (props) => {
    return <h1>Привет, {props.name}</h1>;
}
```

这个组件在首页上使用，我们向它传递文本的名称。

```tsx
import { FC } from 'react'

const MainPage: FC = () => {
  return <Text name='Мир'/>
}
```

属性是只读的。如果需要更改数据，则必须使用state (应用程序的状态).

在任何组件中都可以访问 `props.children`。这是位于组件打开标签和关闭标签之间的内容。例如：

```tsx
<React.StrictMode>
  <App />
</React.StrictMode>
```

对于 `React.StrictMode` ，其**children** 中包含的是 `App`组件.

### 2.4. 状态 State

当组件中的数据随时间变化时，组件需要使用 `state`。例如，一个 `Checkbox` 组件可能需要一个状态 `isChecked`. 。让我们来看一个这样的组件的例子。

```tsx
import { FC, useState } from 'react'

const Checkbox: FC = () => {
    const [isChecked, setIsChecked] = useState<boolean>(false)

    return <h1 onClick={() => setIsChecked((state) => !state)}>Состояние: ${isChecked}</h1>;
}
```

属性和状态之间的主要区别在于，状态用于管理组件，而属性用于获取信息。

如果需要改变属性，那么应该将它们提升到父组件的状态中，并且不仅要将这个对象传递到当前组件，还要传递一个能够改变它的函数。

在查看状态操作示例之前，让我们先了解应用程序的生命周期。

## 3. 创建项目

我们将使用 [Vite React TS][vite-template-project]模板进行开发 - 这是一个方便的环境，用于创建第一个简单的 **React**应用程序.

要创建第一个项目，请执行以下步骤。

```shell
npm create vite@latest my-app -- --template react-ts
cd my-app
npm install
```

执行完这些命令后，我们将拥有一个准备好的应用程序。 在我们应用程序的“后台”，使用了[Typescript]语言, [React]库 和 [Vite]构建工具.

为了方便开发，我们将对`vite.config.ts`进行一些修改, 以确保我们的本地服务器总是运行在 **3000 端口**上.

```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'

// https://vitejs.dev/config/
export default defineConfig({
  server: { port: 3000 },
  plugins: [react()],
})
```

为了与应用程序交互，我们有两主要的命令：

* `npm run dev` - 启动用于开发的本地服务器
* `npm run build` - 创建应用程序的优化构建版本

执行命令 `npm run dev`来启动用于开发的本地服务器。如果在代码中做出更改，页面将自动重新加载。你可以在控制台中看到构建错误和警告。

![Фото 1](./assets/1.png)

## 项目结构

在项目文件夹中，我们将有以下文件：

* `package.json` - 项目的主要文件，包含项目信息
* `package-lock.json` - 锁文件，包含依赖列表
* `vite.config.ts` - Vite 构建器的配置文件
* `tsconfig.json` - TypeScript 的配置文件
* `tsconfig.node.json` - 在 Node 环境下运行时的 TypeScript 配置文件
* `.eslintrc.cjs` - Eslint 的配置文件
* `index.html` - 我们应用程序的主要文件。当用户访问页面时，它将首先被加载
* `src/main.tsx` - 我们应用程序的主要 TS 文件。在这里，我们启动应用程序的渲染
* `src/App.tsx` - 应用程序的布局。包含 Vite 和 React 的标志

![Фото 2](./assets/2.png)

让我们更详细地了解我们应用程序的主要文件。

### index.html

```html
<!doctype html>
<html lang="en">
  <head>
    <meta charset="UTF-8" />
    <link rel="icon" type="image/svg+xml" href="/vite.svg" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0" />
    <title>Vite + React + TS</title>
  </head>
  <body>
    <div id="root"></div>
    <script type="module" src="/src/main.tsx"></script>
  </body>
</html>
```

文件 `index.html` 在应用程序启动时首先被加载。让我们来看看它的主要部分：

* `link` - 加载浏览器标签页的图标
* `title` - 浏览器标签页的标题
* `meta` -  设置技术信息
* `script` - 连接我们项目的主要 TS 文件。当它加载完成后，将开始执行并绘制 Vite 和 React 的标志
* `div id=root` - 技术性标签。React 将在这个标签内插入应用程序的代码

### src/main.tsx

```tsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import App from './App.tsx'
import './index.css'

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <App />
  </React.StrictMode>,
)
```

文件 `src/main.tsx` 从我们的 html 文件中加载，并在执行后开始渲染我们的应用。让我们来看看它的主要部分：

* `document.getElementById('root')` - 从 index.html 文件中获取技术性标签
* `React.StrictMode` - 用于发现应用中潜在问题的工具

### src/App.tsx

```tsx
import { useState } from 'react'
import reactLogo from './assets/react.svg'
import viteLogo from '/vite.svg'
import './App.css'

function App() {
  const [count, setCount] = useState(0)

  return (
    <>
      <div>
        <a href="https://vitejs.dev" target="_blank">
          <img src={viteLogo} className="logo" alt="Vite logo" />
        </a>
        <a href="https://react.dev" target="_blank">
          <img src={reactLogo} className="logo react" alt="React logo" />
        </a>
      </div>
      <h1>Vite + React</h1>
      <div className="card">
        <button onClick={() => setCount((count) => count + 1)}>
          count is {count}
        </button>
        <p>
          Edit <code>src/App.tsx</code> and save to test HMR
        </p>
      </div>
      <p className="read-the-docs">
        Click on the Vite and React logos to learn more
      </p>
    </>
  )
}

export default App
```

这是我们应用的主要组件，在这里绘制了 Vite 和 React 的标志。

## 4. 路由 Routing

无论我们选择的主题领域如何，我们的应用可能包含多个页面。为了方便在页面之间跳转，我们将使用路由。

为此，我们需要安装 `react-router-dom`和 `@types/react-router-dom`这两个库

```shell
npm i react-router-dom
npm i @types/react-router-dom -D
```

让我们在 `main.tsx`中设置多个页面。

```tsx
import React from 'react'
import ReactDOM from 'react-dom/client'

import { createBrowserRouter, RouterProvider } from 'react-router-dom'
import './index.css'

const router = createBrowserRouter([
  {
    path: '/',
    element: <h1>Это наша стартовая страница</h1>
  },
  {
    path: '/new',
    element: <h1>Это наша страница с чем-то новеньким</h1>
  }
])

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <RouterProvider router={router} />
  </React.StrictMode>,
)
```

### 首页

![Фото 3](./assets/3.png)

### 页面 `/new`

![Фото 4](./assets/4.png)

路由器允许我们在不重新加载的情况下在不同页面之间移动。为此，可以使用标准的 `a`标签.

```tsx
import React from 'react'
import ReactDOM from 'react-dom/client'
import { createBrowserRouter, RouterProvider, Link } from 'react-router-dom'
import './index.css'

const router = createBrowserRouter([
  {
    path: '/',
    element: <h1>Это наша стартовая страница</h1>
  },
  {
    path: '/new',
    element: <h1>Это наша страница с чем-то новеньким</h1>
  }
])

ReactDOM.createRoot(document.getElementById('root')!).render(
  <React.StrictMode>
    <ul>
      <li>
        <a href="/">Старт</a>
      </li>
      <li>
        <a href="/new">Хочу на страницу с чем-то новеньким</a>
      </li>
    </ul>
    <hr />
    <RouterProvider router={router} />
  </React.StrictMode>,
)
```

### 最终结果

![Gif 1](./assets/1.gif)

现在你已经熟悉了应用程序中的路由。如果需要处理地址栏中的某些信息，或者使用某个 **特殊的路由器**, 那么可以更详细地了解路由器的类型和工作方式，可以在React的文档中查看，或者在 [React Router][react-router] 网站上查看带有示例的文档.

## 5. 钩子 Hooks

钩子是React 16.8引入的一项新功能，它允许我们在不编写类的情况下使用状态和其他React功能。

钩子是一种函数，它允许我们在应用程序的生命周期事件中添加自定义逻辑。 **它们不能在类组件中工作!**

### 5.1. 钩子状态

假设我们想在类中初始化状态 `count` 的值为 `0`. 为此，在其构造函数中将 `this.state` 赋值为对象 `{ count: 0 }`:

```tsx
import React from 'react'

class Example extends React.Component {
    constructor() {
        this.state = {
            count: 0
        };
    }
}
```

在函数组件中，我们无法访问 `this`, 因此我们不能通过 `this.state`来设置或读取状态。相反，我们直接在组件内部调用 `useState` 钩子.

```tsx
import { FC, useState } from 'react'

const Example: FC = () => {
    // Объявление новой переменной состояния «count»
    const [count, setCount] = useState(0);

    return <div onClick={()=>setCount(count=>count++)}>{count}</div>
}
```

## 5.2. 生命周期

让我们来看看每个生命周期阶段，包括与之相关的所有方法。在示例中，我们将讨论类组件的方法，因为在函数组件中使用的是钩子的概念，我们将在后面介绍。

### 挂载

类组件也是类，因此首先会调用 `constructor()`。在这里，我们执行组件状态的初始化。

接下来，组件会启动 `getDerivedStateFromProps()`, 然后是 `render()`方法, 返回 JSX. React 将组件«挂载» 到 DOM中.

然后，会启动 `componentDidMount()`方法, 在这里执行开发者定义的所有异步过程。至此，可以认为组件成功“诞生”。.

### 更新

这个阶段在状态或属性每次更改时启动。与挂载时一样，会调用 `getDerivedStateFromProps()`方法, 但这一次没有构造函数。然后是 `shouldComponentUpdate()`的启动.

在 `shouldComponentUpdate()`中 ，可以比较 **更新前** 和 **更新后**的状态, 以避免不必要的组件重新渲染。

之后，React将启动 `componentDidUpdate()`。与 `componentDidMount()`一样，可以用于异步调用或DOM管理。

### 卸载

当组件走完生命周期，就会进入卸载阶段——这是最后一个生命周期阶段。React会在组件从DOM中移除之前执行`componentWillUnmount()` 。此方法用于关闭所有打开的连接，如WebSocket连接或定时器。

### 5.3. 钩子效应

使用钩子 `useEffect` ，你可以从函数组件中执行副作用。它执行与React类组件中的 `componentDidMount`, `componentDidUpdate` 和 `componentWillUnmount` 相同的角色，将它们合并为一个统一的API：提供方法来处理函数组件的生命周期。你可以在[使用钩子效应][react-hooks]页面上找到`useEffect`和这些方法的比较.

```tsx
useEffect(() => {
  // Этот код выполнится на mount`е компонента
    
  return () => {
    // Этот код выполнится на unmount`е компонента
  }

  // Это список зависимостей хука, он будет вызван каждый раз, когда зависимости будут меняться
}, [])
```

钩子（Hooks）引入了两个额外的规则：
* 不要在循环、条件语句或嵌套函数中调用钩子。它们只能在**顶层**执行.
* 只有在React函数组件或自定义钩子中才能调用钩子。

自定义钩子是使用其他钩子的JavaScript函数。自定义钩子也遵循之前描述的钩子规则。

在 [这篇文章][habr-react-hooks] 中，你可以看到一些在开发中非常有用的自定义钩子的实现。

### 实践：useState和useEffect

我们将创建一个函数组件，通过它我们可以观察状态和应用程序生命周期的操作。

```tsx
import { FC, useEffect, useState } from 'react'

const data: string[] = [
    'Берик Дондаррион',
    'Леди Мелиссандра',
    'Полливер',
    'Уолдер Фрей',
    'Тайвин Ланнистер',
    'Сир Мерин Трэнт',
    'Король Джоффри',
    'Сир Илин Пейн',
    'Гора',
    'Пес',
    'Серсея Ланнистер',
]

const StartPage: FC = () => {
    // В функциональных компонентах для работы с состоянием можно использовать хук useState()
    // Он возвращает кортеж из двух элементов:
    // 1 элемент - объект состояния
    // 2 элемент - метод который позволит нам обновить состояние
    const [randomName, setRandomName] = useState('')

    // Кстати, это хороший пример деструктуризации массива в JavaScript
    const [names, setNames] = useState(data)

    const [showNames, setShowNames] = useState(false)

    // В данном хендлере мы изменяем состояние на какое-то конкретное
    const handleShowNames = () => {
        setShowNames(true)
    }

    // В данном хендлере мы изменяем состояние на какое-то конкретное
    const handleHideNames = () => {
        setShowNames(false)
    }

    useEffect(()=>{
        console.log('Этот код выполняется только на первом рендере компонента')
        // В данном примере можно наблюдать Spread syntax (Троеточие перед массивом)
        setNames((names) => [...names, 'Бедный студент'])

        return () => {
            console.log('Этот код выполняется, когда компонент будет размонтирован')
        }
    },[])

    useEffect(()=>{
        console.log('Этот код выполняется каждый раз, когда изменится состояние showNames')
        setRandomName(names[Math.floor(Math.random()*names.length)])
    },[showNames])

    return (
        <div>
            <h3>Случайное имя из списка: {randomName}</h3>
            {/*Кнопка для того, чтобы показать имена*/}
            <button onClick={handleShowNames}>Хочу увидеть список имен</button>
            {/*Кнопка для того, чтобы скрыть имена*/}
            <button onClick={handleHideNames}>Хочу скрыть список имен</button>

            {/*React отрисует список только если showNames будет равен true, boolean значения игнорируются при отрисовке*/}
            {showNames && (
                <ul>
                    {names.map((name, index)=> (
                        <li key={index}>
                            <span>{name}</span>
                        </li>
                    ))}
                </ul>
            )}
        </div>
    )
}

export default StartPage
```

![Gif 2](./assets/2.gif)

## 6. 实际操作 - - iTunes

假设我们已经有了一个工作的API（示例中使用的是iTunes API）。我们可以获取实体列表，对其进行过滤，并以用户友好的方式显示。

### 6.1. iTunes 专辑页面

为此，我们需要安装 `react-bootstrap` 和 `bootstrap`库.

```shell
npm i react-bootstrap bootstrap
```

需要在 `main.tsx`文件中导入`bootstrap`

```tsx
import 'bootstrap/dist/css/bootstrap.min.css'
```

创建一个页面来绘制iTunes中的曲目。

### ITunesPage

#### ITunesPage.css

```css
.container {
    display: flex;
    flex-direction: column;
    padding: 20px 40px;
    min-width: 800px;
}
.containerLoading {
    filter: blur(8px);
}

@media (max-width: 600px) {
    .container{
        min-width: auto;
    }
}
```

#### ITunesPage.tsx

```tsx
import { FC, useState} from 'react'
import { Card, Col, Row, Button, Spinner } from 'react-bootstrap'
import './ITunesPage.css'

interface ITunesMusic {
    wrapperType: string
    artworkUrl100: string
    artistName: string
    collectionCensoredName: string
    trackViewUrl: string
}

interface ITunesResult {
    resultCount: number
    results: ITunesMusic[]
}

const getMusicByName = async (name = ''): Promise<ITunesResult> =>{
    return fetch(`https://itunes.apple.com/search?term=${name}`)
        .then((response) => response.json())
        .catch(()=> ({ resultCount:0, results:[] }))
}

const ITunesPage: FC = () => {
    const [searchValue, setSearchValue] = useState('')

    const [loading, setLoading] = useState(false)

    const [music, setMusic] = useState<ITunesMusic[]>([])

    const handleSearch = async () =>{
        await setLoading(true)
        const { results } = await getMusicByName(searchValue)
        await setMusic(results.filter(item => item.wrapperType === "track"))
        await setLoading(false)
    }

    return (
        <div className={`container ${loading && 'containerLoading'}`}>
            {loading && <div className="loadingBg"><Spinner animation="border"/></div>}

            <div className="inputField">
                <input value={searchValue} onChange={(event => setSearchValue(event.target.value))}/>
                <Button disabled={loading} onClick={handleSearch}>Искать</Button>
            </div>

            {!music.length && <div>
                <h1>К сожалению, пока ничего не найдено :(</h1>
            </div>}

            <Row xs={4} md={4} className="g-4">
                {music.map((item, index)=> (
                    <Col >
                        <Card key={index} className="card">
                            <Card.Img className="cardImage" variant="top" src={item.artworkUrl100} height={100} width={100}  />
                            <Card.Body>
                        
                            <div className="textStyle">
                                <Card.Title>{item.artistName}</Card.Title>
                            </div>

                            <div className="textStyle">
                                <Card.Text>
                                    {item.collectionCensoredName}
                                </Card.Text>
                            </div>

                            <Button className="cardButton" href={item.trackViewUrl} target="_blank" variant="primary">Открыть в ITunes</Button>
                            </Card.Body>
                        </Card>
                    </Col>
                ))}
            </Row>
        </div>
    )
}

export default ITunesPage
```

![Gif 3](assets/3.gif)

### 6.2.  将页面分解为组件

为了将来更容易开发，最好将页面分解为组件，并在不同的文件中分离逻辑。

目前，我们所有的代码都在一个文件中。我们将卡片和输入字段移到 `components` 目录下。将网络操作的工作移到 `modules`目录下.

### components/InputField

#### InputField.css

```css
.inputField {
    display: flex;
    padding: 12px 0;
}
.inputField > input {
    width: 200px;
    margin-right: 12px;
    border: 2px solid black;
    border-radius: 4px;
    outline: none;
}
```

#### InputField.tsx

```tsx
import { FC } from 'react'
import { Button } from 'react-bootstrap'
import './InputField.css'

interface Props {
    value: string
    setValue: (value: string) => void
    onSubmit: () => void
    loading?: boolean
    placeholder?: string
    buttonTitle?: string
}

const InputField: FC<Props> = ({ value, setValue, onSubmit, loading, placeholder, buttonTitle = 'Искать' }) => (
    <div className="inputField">
        <input value={value} placeholder={placeholder} onChange={(event => setValue(event.target.value))}/>
        <Button disabled={loading} onClick={onSubmit}>{buttonTitle}</Button>
    </div>
)

export default InputField
```

### components/MusicCard

#### MusicCard.css

```css
.card {
    padding: 8px;
}
.loadingBg {
    z-index: 999;
    position: fixed;
    height: 100%;
    width: 100%;
    display: flex;
    justify-content: center;
    align-items: center;
}
.textStyle {
    height:48px;
    overflow:hidden;
    text-overflow:ellipsis;
    margin-bottom:12px;
}
.cardImage {
    object-fit: contain;
}
.cardButton {
    width: 100%;
}
```

#### MusicCard.tsx

```tsx
import { FC } from 'react'
import { Button, Card } from 'react-bootstrap'
import './MusicCard.css'

interface Props {
    artworkUrl100: string
    artistName: string
    collectionCensoredName: string
    trackViewUrl: string
}

const MusicCard: FC<Props> = ({ artworkUrl100, artistName, collectionCensoredName, trackViewUrl }) => (
    <Card className="card">
        <Card.Img className="cardImage" variant="top" src={artworkUrl100} height={100} width={100}  />
        <Card.Body>            
            <div className="textStyle">
                <Card.Title>{artistName}</Card.Title>
            </div>
            <div className="textStyle">
                <Card.Text>
                    {collectionCensoredName}
                </Card.Text>
            </div>
            <Button className="cardButton" href={trackViewUrl} target="_blank" variant="primary">Открыть в ITunes</Button>
        </Card.Body>
    </Card>
)

export default MusicCard;
```

### modules/itunesApi.ts

```ts
export interface ITunesMusic {
    wrapperType: string
    artworkUrl100: string
    artistName: string
    collectionCensoredName: string
    trackViewUrl: string
}

export interface ITunesResult {
    resultCount: number
    results: ITunesMusic[]
}

export const getMusicByName = async (name = ''): Promise<ITunesResult> =>{
    return fetch(`https://itunes.apple.com/search?term=${name}`)
        .then((response) => response.json())
        .catch(()=> ({ resultCount:0, results:[] }))
}
```

### ITunesPage.tsx

```tsx
import { FC, useState} from 'react'
import { Col, Row, Spinner } from 'react-bootstrap'
import { ITunesMusic, getMusicByName } from './modules/itunesApi'
import { InputField } from './components/InputField'
import { MusicCard } from './components/MusicCard'
import './ITunesPage.css'

const ITunesPage: FC = () => {
    const [searchValue, setSearchValue] = useState('')
    const [loading, setLoading] = useState(false)
    const [music, setMusic] = useState<ITunesMusic[]>([])

    const handleSearch = async () =>{
        setLoading(true)
        const { results } = await getMusicByName(searchValue)
        setMusic(results.filter(item => item.wrapperType === "track"))
        setLoading(false)
    }

    return (
        <div className={`container ${loading && 'containerLoading'}`}>
            {loading && <div className="loadingBg"><Spinner animation="border"/></div>}

            <InputField
                value={searchValue}
                setValue={(value) => setSearchValue(value)}
                loading={loading}
                onSubmit={handleSearch}
            />

            {!music.length && <div>
                <h1>К сожалению, пока ничего не найдено :(</h1>
            </div>}

            <Row xs={4} md={4} className="g-4">
                {music.map((item, index)=> (
                    <Col key={index}>
                        <MusicCard {...item} />
                    </Col>
                ))}
            </Row>
        </div>
    )
}

export default ITunesPage
```

最终我们得到了一个用于在iTunes中搜索音乐的应用程序。在这个应用程序中，我们使用了函数组件、生命周期钩子和应用程序状态。



### 6.3. 添加导航链Breadcrumbs和“详细信息”页面

添加“详细信息”页面以查看您的产品/服务的数据。

为了方便导航，我们将添加导航链 `Breadcrumbs`.
我们将创建一个BreadCrumbs组件，该组件将根据传递的参数显示到当前页面的路径。活动页面（最后一片“面包屑”）将以不同的颜色突出显示。采用这种方法，需要指定所有“面包屑”，除了“首页”页面 - 它始终显示。

为了方便起见，我们将创建与应用程序页面及其名称相对应的ROUTES和ROUTE_LABELS对象:

#### Routes.tsx
```ts
export const ROUTES = {
  HOME: "/",
  ALBUMS: "/albums",
}
export type RouteKeyType = keyof typeof ROUTES;
export const ROUTE_LABELS: {[key in RouteKeyType]: string} = {
  HOME: "Главная",
  ALBUMS: "Альбомы",
};
```
我们将对应用程序进行以下修改：添加首页，修改iTunesPage页面，添加专辑页面，使卡片可点击，并在itunesApi中添加按ID查询专辑的请求。
#### HomePage
```tsx
import { FC } from "react";
import { Link } from "react-router-dom";
import { ROUTES } from "../../Routes";
import { Button, Col, Container, Row } from "react-bootstrap";

export const HomePage: FC = () => {
  return (
    <Container>
      <Row>
        <Col md={6}>
          <h1>Itunes Music</h1>
          <p>
            Добро пожаловать в Itunes Music! Здесь вы можете найти музыку на
            любой вкус.
          </p>
          <Link to={ROUTES.ALBUMS}>
            <Button variant="primary">Просмотреть музыку</Button>
          </Link>
        </Col>
      </Row>
    </Container>
  );
};
```

#### modules/itunesApi.ts
```ts
export interface ITunesMusic {
  wrapperType: string;
  artworkUrl100: string;
  artistName: string;
  collectionCensoredName: string;
  trackViewUrl: string;
  collectionId: number;
}
export interface ITunesResult {
  resultCount: number;
  results: ITunesMusic[];
}

export const getMusicByName = async (name = ""): Promise<ITunesResult> => {
  return fetch(`https://itunes.apple.com/search?term=${name}`).then(
    (response) => response.json()
  );
};

export const getAlbumById = async (
  id: number | string
): Promise<ITunesResult> => {
  return fetch(`https://itunes.apple.com/lookup?id=${id}`).then(
    (response) => response.json()
  );
};
```

#### components/MusicCard
```tsx
import { FC } from "react";
import { Button, Card } from "react-bootstrap";
import "./MusicCard.css";

interface ICardProps {
  artworkUrl100: string;
  artistName: string;
  collectionCensoredName: string;
  trackViewUrl: string;
  imageClickHandler: () => void;
}

export const MusicCard: FC<ICardProps> = ({
  artworkUrl100,
  artistName,
  collectionCensoredName,
  trackViewUrl,
  imageClickHandler,
}) => {

  return (
    <Card className="card">
      <Card.Img
        className="cardImage"
        variant="top"
        src={artworkUrl100}
        height={100}
        width={100}
        onClick={imageClickHandler}
      />
      <Card.Body>
        <div className="textStyle">
          <Card.Title>{collectionCensoredName}</Card.Title>
        </div>
        <div className="textStyle">
          <Card.Text>{artistName}</Card.Text>
        </div>
        <Button
          className="cardButton"
          href={trackViewUrl}
          target="_blank"
          variant="primary"
        >
          Открыть в ITunes
        </Button>
      </Card.Body>
    </Card>
  );
};
```


#### BreadCrumbs.tsx
```tsx
import "./BreadCrumbs.css";
import React from "react";
import { Link } from "react-router-dom";
import { FC } from "react";
import { ROUTES } from "../../Routes";

interface ICrumb {
  label: string;
  path?: string;
}

interface BreadCrumbsProps {
  crumbs: ICrumb[];
}

export const BreadCrumbs: FC<BreadCrumbsProps> = (props) => {
  const { crumbs } = props;

  return (
    <ul className="breadcrumbs">
      <li>
        <Link to={ROUTES.HOME}>Главная</Link>
      </li>
      {!!crumbs.length &&
        crumbs.map((crumb, index) => (
          <React.Fragment key={index}>
            <li className="slash">/</li>
            {index === crumbs.length - 1 ? (
              <li>{crumb.label}</li>
            ) : (
              <li>
                <Link to={crumb.path || ""}>{crumb.label}</Link>
              </li>
            )}
          </React.Fragment>
        ))}
    </ul>
  );
};
```

#### BreadCrumbs.css
```css
:root {
  --active_color: black;
  --additional_color: gray;
}

.breadcrumbs {
  list-style: none;
  display: flex;
  gap: 10px;
  padding: 20px;
}

.breadcrumbs * {
  color: var(--additional_color);
  transition: 0.5s;
}

.breadcrumbs *:not(.slash):hover {
  color: var(--active_color);
}

.breadcrumbs li {
  position: relative;
  cursor: pointer;
}

.breadcrumbs li:last-child {
  color: var(--active_color);
}
```

示例：在专辑页面（ITunesPage）上使用BreadCrumbs的示例：
#### ITunesPage
```tsx
import "./ITunesPage.css";
import { FC, useState } from "react";
import { Col, Row, Spinner } from "react-bootstrap";
import { ITunesMusic, getMusicByName } from "../../modules/itunesApi";
import { InputField } from "../../components/InputField";
import { BreadCrumbs } from "../../components/BreadCrumbs";
import { ROUTES, ROUTE_LABELS } from "../../Routes";
import { MusicCard } from "../../components/MusicCard";
import { useNavigate } from "react-router-dom";

const ITunesPage: FC = () => {
  const [searchValue, setSearchValue] = useState("");
  const [loading, setLoading] = useState(false);
  const [music, setMusic] = useState<ITunesMusic[]>([]);

  const navigate = useNavigate();

  const handleSearch = () => {
    setLoading(true);
    getMusicByName(searchValue)
      .then((response) => {
        setMusic(
          response.results.filter((item) => item.wrapperType === "track")
        );
        setLoading(false);
      });
  };
  const handleCardClick = (id: number) => {
    // клик на карточку, переход на страницу альбома
    navigate(`${ROUTES.ALBUMS}/${id}`);
  };

  return (
    <div className="container">
      <BreadCrumbs crumbs={[{ label: ROUTE_LABELS.ALBUMS }]} />
      
      <InputField
        value={searchValue}
        setValue={(value) => setSearchValue(value)}
        loading={loading}
        onSubmit={handleSearch}
      />

      {loading && ( // здесь можно было использовать тернарный оператор, но это усложняет читаемость
        <div className="loadingBg">
          <Spinner animation="border" />
        </div>
      )}
      {!loading &&
        (!music.length /* Проверка на существование данных */ ? (
          <div>
            <h1>К сожалению, пока ничего не найдено :(</h1>
          </div>
        ) : (
          <Row xs={4} md={4} className="g-4">
            {music.map((item, index) => (
              <Col key={index}>
                <MusicCard
                  imageClickHandler={() => handleCardClick(item.collectionId)}
                  {...item}
                />
              </Col>
            ))}
          </Row>
        ))}
    </div>
  );
};

export default ITunesPage;
```
在crumbs中只指定label，因为路径对我们来说并不重要，最后一片面包屑是非激活状态。

示例：在专辑页面上使用BreadCrumbs（专辑名称从请求中获取，并作为最终目的地传递给BreadCrumbs）:

#### AlbumPage
```tsx
import "./AlbumPage.css";
import { FC, useEffect, useState } from "react";
import { BreadCrumbs } from "../../components/BreadCrumbs";
import { ROUTES, ROUTE_LABELS } from "../../Routes";
import { useParams } from "react-router-dom";
import { ITunesMusic, getAlbumById } from "../../modules/itunesApi";
import { Col, Row, Spinner, Image } from "react-bootstrap";
import { ALBUMS_MOCK } from "../../modules/mock";
import defaultImage from "/DefaultImage.jpg";

export const AlbumPage: FC = () => {
  const [pageData, setPageDdata] = useState<ITunesMusic>();

  const { id } = useParams(); // ид страницы, пример: "/albums/12"

  useEffect(() => {
    if (!id) return;
    getAlbumById(id)
      .then((response) => setPageDdata(response.results[0]));
  }, [id]);

  return (
    <div>
      <BreadCrumbs
        crumbs={[
          { label: ROUTE_LABELS.ALBUMS, path: ROUTES.ALBUMS },
          { label: pageData?.collectionCensoredName || "Альбом" },
        ]}
      />
      {pageData ? ( // проверка на наличие данных, иначе загрузка
        <div className="container">
          <Row>
            <Col md={6}>
              <p>
                Альбом: <strong>{pageData.collectionCensoredName}</strong>
              </p>
              <p>
                Исполнитель: <strong>{pageData.artistName}</strong>
              </p>
            </Col>
            <Col md={6}>
              <Image
                src={pageData.artworkUrl100 || defaultImage} // дефолтное изображение, если нет artworkUrl100
                alt="Картинка"
                width={100}
              />
            </Col>
          </Row>
        </div>
      ) : (
        <div className="album_page_loader_block">{/* загрузка */}
          <Spinner animation="border" />
        </div>
      )}
    </div>
  );
};
```

#### AlbumPage.css
```css
.album_page_loader_block {
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}
```

新的路由:
```tsx
import { BrowserRouter, Route, Routes } from "react-router-dom";
import { AlbumPage, AlbumsPage } from "./pages";
import ITunesPage from "./pages/ItunesPage";
import { ROUTES } from "./Routes";

function App() {
  return (
    <BrowserRouter>
      <Routes>
        <Route path={ROUTES.HOME} index element={<ITunesPage />} />
        <Route path={ROUTES.ALBUMS} element={<AlbumsPage />} />
        <Route path={`${ROUTES.ALBUMS}/:id`} element={<AlbumPage />} />
      </Routes>
    </BrowserRouter>
  );
}

export default App;
```

![Gif 4](./assets/4.gif)

## 7. 根据选项完善React应用程序

### 7.1. 实现从模拟对象中获取数据

根据你的选项完善应用程序的页面。通过模拟对象来填充数据。

为此，我们将创建一个mock.ts文件:

#### modules/mock.ts
```ts
import { ITunesResult } from "./getMusicByName";

export const SONGS_MOCK: ITunesResult = {
  resultCount: 3, 
  results: [
    {
      wrapperType: "track",
      artistName: "Pink Floyd",
      collectionCensoredName: "The Wall",
      trackViewUrl: "",
      artworkUrl100: "",
    },
    {
      wrapperType: "track",
      artistName: "Queen",
      collectionCensoredName: "A Night At The Opera",
      trackViewUrl: "",
      artworkUrl100: "",
    },
    {
      wrapperType: "track",
      artistName: "AC/DC",
      collectionCensoredName: "Made in Heaven",
      trackViewUrl: "",
      artworkUrl100: "",
    },
  ],
};

```
示例用法：

#### ITunesPage
```tsx
import "./ITunesPage.css";
import { FC, useState } from "react";
import { Col, Row, Spinner } from "react-bootstrap";
import { ITunesMusic, getMusicByName } from "../../modules/itunesApi";
import { InputField } from "../../components/InputField";
import { BreadCrumbs } from "../../components/BreadCrumbs";
import { ROUTES, ROUTE_LABELS } from "../../Routes";
import { MusicCard } from "../../components/MusicCard";
import { useNavigate } from "react-router-dom";
import { ALBUMS_MOCK } from "../../modules/mock";

const ITunesPage: FC = () => {
  const [searchValue, setSearchValue] = useState("");
  const [loading, setLoading] = useState(false);
  const [music, setMusic] = useState<ITunesMusic[]>([]);

  const navigate = useNavigate();

  const handleSearch = () => {
    setLoading(true);
    getMusicByName(searchValue)
      .then((response) => {
        setMusic(
          response.results.filter((item) => item.wrapperType === "track")
        );
        setLoading(false);
      })
      .catch(() => { // В случае ошибки используем mock данные, фильтруем по имени
        setMusic(
          ALBUMS_MOCK.results.filter((item) =>
            item.collectionCensoredName
              .toLocaleLowerCase()
              .startsWith(searchValue.toLocaleLowerCase())
          )
        );
        setLoading(false);
      });
  };

  // тот же код
};

export default ITunesPage;
```

### 7.2. 添加默认图片
在MusicCard中添加并使用默认图片:
```jsx
import { FC } from 'react'
import { Button, Card } from 'react-bootstrap'
import "./MusicCard.css"
import image from "/DefaultImage.jpg";

interface Props {
    artworkUrl100: string
    artistName: string
    collectionCensoredName: string
    trackViewUrl: string
}

export const MusicCard: FC<Props> = ({ artworkUrl100, artistName, collectionCensoredName, trackViewUrl }) => (
    <Card className="card">{/*                          // изображение по умолчанию   */}
        <Card.Img className="cardImage" variant="top" src={artworkUrl100 || image} height={100} width={100}  />
        <Card.Body>                
            <div className="textStyle">
                <Card.Title>{artistName}</Card.Title>
            </div>
            <div className="textStyle">
                <Card.Text>
                    {collectionCensoredName}
                </Card.Text>
            </div>
            <Button className="cardButton" href={trackViewUrl} target="_blank" variant="primary">Открыть в ITunes</Button>
        </Card.Body>
    </Card>
)
```
现在，如果图片没有加载，将显示默认图片。

#### AlbumPage
```tsx
import "./AlbumPage.css";
import { FC, useEffect, useState } from "react";
import { BreadCrumbs } from "../../components/BreadCrumbs";
import { ROUTES, ROUTE_LABELS } from "../../Routes";
import { useParams } from "react-router-dom";
import { ITunesMusic, getAlbumById } from "../../modules/itunesApi";
import { Col, Row, Spinner, Image } from "react-bootstrap";
import { ALBUMS_MOCK } from "../../modules/mock";
import defaultImage from "/DefaultImage.jpg";

export const AlbumPage: FC = () => {
  const [pageData, setPageDdata] = useState<ITunesMusic>();

  const { id } = useParams(); // ид страницы, пример: "/albums/12"

  useEffect(() => {
    if (!id) return;
    getAlbumById(id)
      .then((response) => setPageDdata(response.results[0]))
      .catch(
        () =>
          setPageDdata(
            ALBUMS_MOCK.results.find(
              (album) => String(album.collectionId) == id
            )
          ) /* В случае ошибки используем мок данные, фильтруем по ид */
      );
  }, [id]);

 // ....
};
```

![Gif 5](./assets/5.gif)

## 7.3. 应用程序的主菜单

我们将使用из `react-bootstrap`中的 `Navbar` 来创建应用程序的主菜单。

[了解更多](https://react-bootstrap.github.io/components/navbar/)

```jsx
import Container from 'react-bootstrap/Container';
import Nav from 'react-bootstrap/Nav';
import Navbar from 'react-bootstrap/Navbar';
import NavDropdown from 'react-bootstrap/NavDropdown';

function BasicExample() {
  return (
    <Navbar bg="light" expand="lg">
      <Container>
        <Navbar.Brand href="#home">React-Bootstrap</Navbar.Brand>
        <Navbar.Toggle aria-controls="basic-navbar-nav" />
        <Navbar.Collapse id="basic-navbar-nav">
          <Nav className="me-auto">
            <Nav.Link href="#home">Home</Nav.Link>
            <Nav.Link href="#link">Link</Nav.Link>
            <NavDropdown title="Dropdown" id="basic-nav-dropdown">
              <NavDropdown.Item href="#action/3.1">Action</NavDropdown.Item>
              <NavDropdown.Item href="#action/3.2">
                Another action
              </NavDropdown.Item>
              <NavDropdown.Item href="#action/3.3">Something</NavDropdown.Item>
              <NavDropdown.Divider />
              <NavDropdown.Item href="#action/3.4">
                Separated link
              </NavDropdown.Item>
            </NavDropdown>
          </Nav>
        </Navbar.Collapse>
      </Container>
    </Navbar>
  );
}

export default BasicExample;
```

### 7.4. 连接到自己的Web服务API

回到我们的iTunes示例。现在我们需要将我们的 `fetch` 请求到iTunes服务替换为对我们的 `Django` 或 `Golang`服务的调用。

## 8. 重要事项. CORS  

当你将React应用程序连接到你的Web服务时，你可能会遇到与JSON相关的显示问题。

如何判断空白屏幕是否与此错误有关？

在页面上的任何位置点击鼠标右键，然后点击 **查看页面源代码**。之后转到 **Console**标签，在那里查找与CORS策略相关的错误。如果存在此类错误，那么你可以简单地禁用 `CORS`.

我们提供了3种解决此问题的方法。

### CORS. 前端代理

我们可以通过我们的开发服务器来代理请求。代理的方案是：前端 -> 代理 -> 后端。这样我们就不会遇到 `CORS` 请求的问题.

为了设置，你需要在 `vite.config.ts` 文件中指定 [代理][vite-proxy]字段. 例如，如果我们对`/api/someroute`进行请求，那么我们的开发服务器将代理到 `http://localhost:8080/someroute`.

```ts
export default defineConfig({
  plugins: [react()],
  server: {
    proxy: {
      "/api": {
        target: "http://localhost:8080",
        changeOrigin: true,
        rewrite: (path) => path.replace(/^\/api/, "/"),
      },
    },
  },
});
```

### CORS. 后端设置

需要在后端设置 `CORS` 头部的返回。如何做到这一点，可以阅读 [这里][habr-cors1] 或 [这里][habr-cors2].

### CORS. 前端禁用

我们可以使用特殊的扩展程序在前端禁用 `CORS` 策略的工作。例如，这是一个有用的扩展程序用于 [Google Chrome][chrome-cors-unblock]。但是，浏览器扩展并不是解决这个问题的最佳方案，它在第三方网站上使用时是不安全的。因此，它只能用于开发阶段的调试，而不是最终解决方案。

## 9. 有用的链接

* [React]
* [React Router][react-router]
* [Typescript]
* [Vite]

[iu5-javascript]: https://github.com/iu5git/JavaScript
[react]: https://react.dev
[react-hooks]: https://react.dev/reference/react
[react-router]: https://reactrouter.com
[vite]: https://vitejs.dev
[vite-proxy]: https://vitejs.dev/config/server-options.html#server-proxy
[vite-template-project]: https://vitejs.dev/guide/#scaffolding-your-first-vite-project
[typescript]: https://www.typescriptlang.org/
[habr-react-diff-class-function-component]: https://habr.com/ru/company/ruvds/blog/444348
[habr-react-hooks]: https://habr.com/ru/company/ruvds/blog/554280
[habr-react-jsx]: https://habr.com/ru/articles/319270
[habr-typescript]: https://habr.com/ru/articles/663964
[habr-cors1]: https://habr.com/ru/companies/macloud/articles/553826
[habr-cors2]: https://habr.com/ru/articles/514684
[chrome-cors-unblock]: https://chrome.google.com/webstore/detail/cors-unblock/lfhmikememgdcahcdlaciloancbhjino