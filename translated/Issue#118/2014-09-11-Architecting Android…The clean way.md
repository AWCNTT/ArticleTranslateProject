---

layout: post
title: 
categories: [Android]
tags: [Android]
description:
fullview: true

---

----------------------------------------------------------

>  **_Edit by Fooyou Email : <foyou23@qq.com>  _** 
    
----------------------------------------------------------

## **Architecting Android…The clean way?**
[翻译原文](http://fernandocejas.com/2014/09/03/architecting-android-the-clean-way/)

  在过去的几个月，也就是，在[ Tuenti ][3] 的时候，跟我2个同事 [@pedro_g_s][0] 和 [ @flipper83][1] *（顺便提下，他们2个从事Android开发的坏家伙）*，进行了一次友好的讨论之后，我就决定，是时候写一篇，关于**Android 应用框架**的文章了。

这篇文章的目的，是想更多展示一下我这几个月所想的，以及，在摸索和实现（这个框架）过程中 ，学到的所有相关的内容。

###开始吧（Getting Started）

**我们都知道，要写一个优质的软件是挺艰难和复杂的**：不仅要满足基本需求，还要尽量做到，健壮稳定，可维护性，易测试性，而且要足够灵活，来适配迭代更新和变化。这就是为什么 要提倡**“一个简洁的架构（the clean architecture）”**，当我们在开发任何软件应用的时候，能够很方便的使用它。

这个思路很简单：**简洁架构** 意味着产品系统中遵循一系列的习惯原则：

 - **框架独立性**
 - **可测试**
 - **UI独立性**
 - **数据库独立性**
 - **任何外部代理模块的独立性**


![enter image description here](http://fernandocejas.com/wp-content/uploads/2014/09/clean_architecture1.png)

也不是一定只用4圆圈（如上图所示），因为这只是个示例，但是，你得深入考虑 **依赖原则（Dependency Rule）**：源码依赖只能是由外向内的，还有，**内环**的里的任何东西，不需要知道**外环**任何实现方式。*（ 译者：简单说就是，`外环`会依赖于`内环`，反过来，`内环`的东西独立与`外环`的，不因`外环`内容改变而受到影响，而且，`内环`也不需要知道`外环`里的任何具体逻辑实现。）*

> *译者：啊！受不了啦，这个翻译的太变扭了，为了尽可能的保持原滋原味的翻译，不愿意加入自己的语言组织，可是，这样表达的意思又不完整，不接地气，怎弄呀！*

下面是解释一些词汇术语，为了让你更熟悉，更容易理解这篇文章。

 - **Entities：** 这些是指应用的 `业务对象`。*（译者：就是模型类，实体类，简单Java对象）*
 - **Use Cases：** 这些是指`用例`，把数据和业务对象串起来的用例，也可以叫做约束器。*（译者：简单说就是数据关联用例，拦截器，约束，等等。）*
 - **Interface Adapters：** 这一组适配器，是负责以最合理的格式转换`用例`（`use cases`）和`实体`（`entities`）之间的数据，**表现层**（Presenters ）和**控制层**（Controllers ），就属于这一块的。
 - **Frameworks and Drivers:** 这里是所有具体的实现了：比如：UI，工具类，基础框架，等等。

想要更具体，更生动丰富的解释，可以参考[这篇文章][4]或者[这个视频][5]

> 上面视频，好像要翻墙，这个我帮不了你的 。XD

###我们的案例（ Our Scenario）

我会做一个简单的案例，来实践一下：简单创建一个应用，这个应用内容是显示一个好友列表，或者显示云端的用户反馈回复，当你点击这些任何一个条目的时候，会打开一个新的界面，展示这个用户更详细的内容。
我给出了一个视频，这样你能更直观的看到我所阐述的内容：
<iframe width="640" height="480" src="//www.youtube.com/embed/XSjV4sG3ni0" frameborder="0" allowfullscreen></iframe>

> 额，又是一个要翻墙的视频，Youtube地址。我帮不到你啦！

###Android架构 （Android Architecture）
这主要目的是**关联分离（separation of concerns ，解耦）** ，保证业务逻辑不涉及任何外环的相关内容，这样，他们就可以很方便的测试，不需要依赖任何外部元素。

要到达这一点呢，我的建议是把整个项目拆分成3个不同的层级，每一层都是各自功能，和其他层之间保持独立。
值得一提的是，每一层都使用他们自己的数据模型，这样才能做到依赖独立。（你将会发现在代码里面，需要做一个数据映射，才能更好的完成数据转换传输，这么做，是会付出一些代价的，如果你的模型的使用不是贯穿整个应用的话。）

>下图是一个大体结构，你可以直观的看清楚流程是怎么样的。

![enter image description here][6]

**说明：** 我没有使用任何其他的外部库（除了使用了`gson` 做json数据解析，还有使用，`junit`，`mockito`，robolectric ，`espresso` 来做测试），原因是因为，这样可以使项目更加简单，不管怎么样，不要犹豫使用，ORMs（对象关系映射）做数据存储，或者其他任何依赖注入的框架，或者其他一些功能类似的框架，这样，可以使生活变的更简单啦。**（记住，重复造轮子不是一个好习惯哦）**

###表现层（Presentation Layer）

在这里，就是做一些跟视图和动画相关的逻辑了，使用**Model View Presenter** 模式是最好不过的了（下面简称[MVP][7]），当然了，你可以使用其他类似的，比如 `MVC` 或者 `MVVM` 啦。我不具体阐述了，不过，在这里，只有**fragments** 和**activities** 才属于`视图`，在里面编写的，除了UI相关的逻辑之外，没有别的了，另外，渲染绘画相关的内容也是在这里。在这个层里，**Presenters**  结合用例 interactors (use cases)，另开新线程，在UI线程之外处理各种逻辑，然后，通过回调的方式把数据返回到视图模块。

![enter image description here](http://fernandocejas.com/wp-content/uploads/2014/09/clean_architecture_mvp.png)


如果你想要一个更酷的例子，可以看看[Effective Android UI][8]，也是使用了 `MVP` 和 `MVVM`，看看我朋友 Pedro Gómez 写的怎么样？

### 领域层 （Domain Layer）
**`业务逻辑`都在这：所以业务相关的内容都属于这一层。**就Android 项目来说的话，你会发现所有的用例interactors (use cases)实现也是在这样一层。
这一层是纯 `Java` 编写的，没有任何Android相关的依赖。所有外部组件都是通过接口的方式访问业务逻辑。

![enter image description here](http://fernandocejas.com/wp-content/uploads/2014/09/clean_architecture_domain.png)

###数据层（Data Layer）

所有应用数据的获取都是来自于这一层，以`用户仓库`（UserRepository ）的实现方式（接口是定义在`领域层`的），使用 仓库模式（[Repository Pattern](http://martinfowler.com/eaaCatalog/repository.html)），它的策略是采用工厂模式，传递不同的条件参数，获取不同的数据。
比如：通过ID查找用户，如果用户数据有做缓存的话，就会优先使用磁盘缓存数据，如果没有，就会重新请求数据，完成后，保存到磁盘。
这背后的思路就是，原始数据是对客户端透明的，客户端不需要关注，数据是来自于内存，还是磁盘，或者服务器云端，只要能数据传递出去，也能取到，就够了。


![enter image description here](http://fernandocejas.com/wp-content/uploads/2014/09/clean_architecture_data.png)

**说明：**在这次代码中，我实现的比较简单，使用了原生的文件系统存储，`Android` preferences配置文件方式来做磁盘缓存，只是为了达到学习目的，还是得提醒一下，**不要重复造轮子**，如果有现有的成熟的库来做这些工作的话。

###错误处理 （Error Handling）

这是一个经常讨论的话题了，如果你有很好的方案想要分享的话，那会更好。**我的建议是使用 回调**，这样，比如：如果在数据仓库发生错误的话，可以通过调用，回调接口的2个方法：**onResponse()** 和 **onError()**。后面一个方法，包含一个封装了各种异常数据的类 **“ErrorBundle”**，这样做会带来一些麻烦，因为会一连串的回调接口，一个接一个的在错误传递到表现层，代码可读性会降低，另一方面
我还实现了一套事件驱动系统，如果有错误发生，就会抛出事件，就好像使用[GOTO](http://www.drdobbs.com/jvm/programming-with-reason-why-is-goto-bad/228200966)一样，而且，在我看来，有时候你会混乱的，如果定义了很多个事件，而又没有合理的控制的话。



###测试 （Testing）

至于测试，我采用了好几个方案，这个取决于你的各个分层：

 - **Presentation Layer：** 使用Android自带的`instrumentation` 和`espresso`  做集成和功能测试。
 - **Domain Layer:** `JUnit` + `mockito`做单元测试。
 - **Data Layer:** `Robolectric` （这一层开始有Android相关的依赖）+`junit` +`mockito`  做单元和集成测试。

###代码展示（Show me the code）
**我知道你很想知道代码在哪呢？对不对？ ** 好把！[这里是github连接地址](https://github.com/android10/Android-CleanArchitecture) ，在上面你会找到我写的，至于目录结构，一些要说明的，就是，不同的层，使用不同的模块：

 - **presentation:** Android模块，放了presentation 表现层的内容。
 - **domain：**Java模块，不包含Android相关的东西。
 - **data:** Android模块，数据来源的地方。（ 数据都是从这获取）
 - **data-test:**  数据层测试，因为`Robolectric` 有一些限制，我把它分成几个java模块。

###总结（Conclusion）
正如 `Uncle Bob` 所说：**“Architecture is About Intent, not Frameworks”** ，我非常同意这个说法，当然了，有很多不同的方法做不同的事情（不同的实现方法），我很确定，你每天（像我一样）会面临很多挑战，但是遵循这些方法，确保的应用满足以下几点：

 - **易维护 Easy to maintain**
 - **易测试 Easy to test.**
 - **高聚合Very cohesive.**
 - **低耦合 Decoupled.**
最后，我强烈推荐你去尝试一下，实践，并且分享你的经验。也许你会找到更好的解决方案：我们都知道，**不断提升**一个件非常好的，非常积极的事情呀。我喜欢这篇文章对你有所帮助，欢迎各种反馈。

###连接资源（Links and Resources）

 1. Source code: [https://github.com/android10/Android-CleanArchitecture](https://github.com/android10/Android-CleanArchitecture)
 2. [The clean architecture by Uncle Bob](http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html)
 3. [Architecture is about Intent, not Frameworks](http://www.infoq.com/news/2013/07/architecture_intent_frameworks)
 4. [Model View Presenter](http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter)
 5. [Repository Pattern by Martin Fowler](http://martinfowler.com/eaaCatalog/repository.html)
 6. [Android Design Patterns Presentation](http://www.slideshare.net/PedroVicenteGmezSnch/)


Tagged as: android, androiddev, architecture, clean, clean architecture, developer, developers, development, model view presenter, mvp, pattern, patterns, programming, repository, repository pattern, strategy pattern



[0]: https://twitter.com/pedro_g_s
[1]: https://twitter.com/flipper83
[3]: http://corporate.tuenti.com/en/dev/blog
[4]: http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html
[5]: http://vimeo.com/43612849
[6]: http://fernandocejas.com/wp-content/uploads/2014/09/clean_architecture_android.png

[7]: http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter
[8]:https://github.com/pedrovgs/EffectiveAndroidUI/
