**MVC，MVP和MVVM的区别和联系**

1. MVC（Model-View-Controller）是最常见的软件架构之一，业界有着广泛应用。它本身很容易理解，但是要讲清楚，它与衍生的 MVP 和 MVVM 架构的区别就不容易了。

MVC模式的意思是，软件可以分成三个部分。

	视图（View）：用户界面。
	控制器（Controller）：业务逻辑
	模型（Model）：数据保存

通信方式如下： <br>

<img src="https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/mvc-mvp-mvvm/mvc01.png?raw=true" style="zoom:50%" />

View 传送指令到 Controller <br>
Controller 完成业务逻辑后，要求 Model 改变状态 <br>
Model 将新的数据发送到 View，用户得到反馈 <br>

互动模式:

1,接受用户指令时，MVC 可以分成两种方式。一种是通过 View 接受指令，传递给 Controller。<br>

<img src="https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/mvc-mvp-mvvm/mvc02.png?raw=true" style="zoom:50%" />

2,另一种是直接通过controller接受指令。<br>

<img src="https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/mvc-mvp-mvvm/mvc03.png?raw=true" style="zoom:50%" />

3,实际项目往往采用更灵活的方式 <br>

<img src="https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/mvc-mvp-mvvm/mvc04.png?raw=true" style="zoom:50%" />

	1. 用户可以向 View 发送指令（DOM 事件），再由 View 直接要求 Model 改变状态。
	2. 用户也可以直接向 Controller 发送指令（改变 URL 触发 hashChange 事件），再由 Controller 发送给 View。
	3. Controller 非常薄，只起到路由的作用，而 View 非常厚，业务逻辑都部署在 View。所以，Backbone 索性取消了 Controller，只保留一个 Router（路由器） 。

2. MVP 模式将 Controller 改名为 Presenter，同时改变了通信方向。<br>

<img src="https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/mvc-mvp-mvvm/mvp.png?raw=true" style="zoom:50%" />

1. 各部分之间的通信，都是双向的。
2. View 与 Model 不发生联系，都通过 Presenter 传递。
3. View 非常薄，不部署任何业务逻辑，称为"被动视图"（Passive View），即没有任何主动性，而 Presenter非常厚，所有逻辑都部署在那里。

3. MVVM模式将 Presenter 改名为 ViewModel，基本上与 MVP 模式完全一致。<br>

<img src="https://github.com/wangyuansxya/wangyuansxya.github.io/blob/master/assets/mvc-mvp-mvvm/mvvm.png?raw=true" style="zoom:50%" />

唯一的区别是，它采用双向绑定（data-binding）：View的变动，自动反映在 ViewModel，反之亦然。[Angular](https://angularjs.org/) 和 [Ember](https://emberjs.com/) 都采用这种模式。