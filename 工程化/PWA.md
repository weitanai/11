# PWA

#### 渐进式 Web 应用会在桌面和移动设备上提供可安装的、仿应用的体验，可直接通过 Web 进行构建和交付。它们是快速、可靠的 Web 应用。最重要的是，它们是适用于任何浏览器的 Web 应用 

#### 快速 & 可靠

#### 安装

#### 手机&桌面



### 1 主要架构

#### 1 web应用清单 (manifest)

- 告诉浏览器你希望本应用在独立窗口中打开（ `display` ）。
- 定义首次启动本应用时要打开哪个页面（ `start_url` ）。
- 定义应用在 Dock 或应用启动器上应该是什么样子（ `short_name` ，`icons` ）。
- 创建一个闪屏（ `name` ， `icons` ， `colors` ）。
- 告诉浏览器以横向或纵向模式打开窗口（ `orientation` ）。
- 以及[很多其它配置](https://developer.mozilla.org/en-US/docs/Web/Manifest#Members) 。



#### 2 提供基本的离线体验  service worker

![swoker.png](https://cdn.nlark.com/yuque/0/2019/png/388749/1569769252874-d4ac1661-1528-4ce0-8f40-f4f79331aebb.png)





- 1 注册Service Worker
- 2 Precache 离线页面
- 3 清理旧的离线页面
- 4 处理失败的网络请求





### 2 Service Worker生命周期



![lifecycle.png](https://cdn.nlark.com/yuque/0/2019/png/388749/1569769476478-7d0fea40-2993-4be3-bcf5-c1094734834e.png)



#### `install` 事件

Service Worker 获得的第一个事件是 `install` 。它会在 Service Worker 执行时立即触发，并且每个 Service Worker 只会调用一次。**如果你更改了 Service Worker 脚本，浏览器就会将其视为另一个 Service Worker**，并且它将获得自己的 `install` 事件。



#### `activate` 事件

Service Worker 每次启动时都会收到 `activate` 事件。 `activate` 事件的主要目的是配置 Service Worker 的行为，清除以前运行中遗留的任何资源（例如旧缓存），并让 Service Worker 准备好处理网络请求（例如下面要讲的 `fetch` 事件）。



#### `fetch` 事件

fetch 事件允许 Service Worker 拦截并处理任何网络请求。它可以通过网络获取资源、可以从自己的缓存中提取资源、生成自定义响应，以及很多种不同的选择。查看[离线宝典](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook/)了解你可以使用的不同策略。



#### 更新 Service Worker

浏览器会检查每个页面加载时是否有新版本的 Service Worker 。如果找到新版本，则会下载这个新版本并在后台安装，但不会激活它。它会处于等待状态，直到不再打开任何使用旧 Service Worker 的页面。一旦关闭了使用旧 Service Worker 的所有窗口，新的 Service Worker 就会被激活并获得控制权。更多详细信息，请参阅 Service Worker 生命周期文档中的[更新 Service Worker](https://developers.google.com/web/fundamentals/primers/service-workers/lifecycle#updates)部分。







### 3 选择正确的缓存策略

#### 缓存静态资源

预先缓存资源与用户安装桌面或移动应用时的情况类似。应用运行所需的关键资源已安装或缓存在设备上，以后无论是否有网络连接都可以加载它们。

对于这个应用，我们将在安装 Service Worker 时预先缓存所有静态资源，以便把我们运行应用所需的一切都存储在用户的设备上。为了确保我们的应用快速加载，我们将使用[缓存优先](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook/#cache-falling-back-to-network)策略：不去网络获取资源，而是从本地缓存中取出；只有当缓存不可用时，我们才会尝试从网络中获取它。

#### ![static.png](https://cdn.nlark.com/yuque/0/2019/png/388749/1569769687799-a5a6336b-7dc5-4324-a87c-6f55252730e4.png)

#### 缓存应用数据

[stale-while-revalidate strategy](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook/#stale-while-revalidate) 对某些类型的数据是理想的，比如本应用。它会尽可能快地获取屏幕上要显示的数据，然后在网络返回最新数据后进行更新。 Stale-while-revalidate 意味着我们需要发起两个异步请求，一个到缓存，一个到网络。



![data.png](https://cdn.nlark.com/yuque/0/2019/png/388749/1569769739853-e9b921d8-141a-478b-b30e-97128ad8db0b.png)









### 4  service workers详解

1. service worker URL 通过 [`serviceWorkerContainer.register()`](https://developer.mozilla.org/zh-CN/docs/Web/API/ServiceWorkerContainer/register) 来获取和注册。
2. 如果注册成功，service worker 就在 [`ServiceWorkerGlobalScope`](https://developer.mozilla.org/zh-CN/docs/Web/API/ServiceWorkerGlobalScope) 环境中运行； 这是一个特殊类型的 woker 上下文运行环境，与主运行线程（执行脚本）相独立，同时也没有访问 DOM 的能力。
3. service worker 现在可以处理事件了。
4. 受 service worker 控制的页面打开后会尝试去安装 service worker。最先发送给 service worker 的事件是安装事件(在这个事件里可以开始进行填充 IndexDB和缓存站点资源)。这个流程同原生 APP 或者 Firefox OS APP 是一样的 — 让所有资源可离线访问。
5. 当 `oninstall `事件的处理程序执行完毕后，可以认为 service worker 安装完成了。
6. 下一步是激活。当 service worker 安装完成后，会接收到一个激活事件(activate event)。 `onactivate `主要用途是清理先前版本的service worker 脚本中使用的资源。
7. Service Worker 现在可以控制页面了，但仅是在 `register()`  成功后的打开的页面。也就是说，页面起始于有没有 service worker ，且在页面的接下来生命周期内维持这个状态。所以，页面不得不重新加载以让 service worker 获得完全的控制。

![img](https://cdn.nlark.com/yuque/0/2019/png/388749/1570366015992-156ac4e1-1428-4b83-a4b7-a5f4543d854d.png)

下图展示了 service worker 所有支持的事件：

![img](https://cdn.nlark.com/yuque/0/2019/png/388749/1570366018683-76df1e93-693e-4af5-b7a0-72ad0e30922a.png)