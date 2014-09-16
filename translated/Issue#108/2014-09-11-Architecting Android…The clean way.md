---
layout: post
title: Architecting Android…The clean way?
categories: [Android]
tags: [Android,MVP]
description:
fullview: true
---
<html>
<head>
<meta charset="utf-8">
<title>2014-09-11-Architecting Android…The clean way.md</title>
<link rel="stylesheet" href="https://stackedit.io/res-min/themes/base.css" />
<script type="text/javascript" src="https://stackedit.io/libs/MathJax/MathJax.js?config=TeX-AMS_HTML"></script>
</head>
<body><div class="container"><hr>

<blockquote>
  <p><strong><em>Edit by Fooyou Email : <a href="mailto:foyou23@qq.com">foyou23@qq.com</a> 2000-10-10 </em></strong> </p>
</blockquote>



<hr>



<h2 id="architecting-androidthe-clean-way"><strong>Architecting Android…The clean way?</strong></h2>

<p><a href="http://fernandocejas.com/2014/09/03/architecting-android-the-clean-way/">翻译原文</a></p>

<p>在过去的几个月，也就是，在<a href="http://corporate.tuenti.com/en/dev/blog"> Tuenti </a> 的时候，跟我2个同事 <a href="https://twitter.com/pedro_g_s">@pedro_g_s</a> 和 <a href="https://twitter.com/flipper83"> @flipper83</a> <em>（顺便提下，他们2个从事Android开发的坏家伙）</em>，进行了一次友好的讨论之后，我就决定，是时候写一篇，关于<strong>Android 应用框架</strong>的文章了。</p>

<p>这篇文章的目的，是想更多展示一下我这几个月所想的，以及，在摸索和实现（这个框架）过程中 ，学到的所有相关的内容。</p>



<h3 id="开始吧getting-started">开始吧（Getting Started）</h3>

<p><strong>我们都知道，要写一个优质的软件是挺艰难和复杂的</strong>：不仅要满足基本需求，还要尽量做到，健壮稳定，可维护性，易测试性，而且要足够灵活，来适配迭代更新和变化。这就是为什么 要提倡<strong>“一个简洁的架构（the clean architecture）”</strong>，当我们在开发任何软件应用的时候，能够很方便的使用它。</p>

<p>这个思路很简单：<strong>简洁架构</strong> 意味着产品系统中遵循一系列的习惯原则：</p>

<ul>
<li><strong>框架独立性</strong></li>
<li><strong>可测试</strong></li>
<li><strong>UI独立性</strong></li>
<li><strong>数据库独立性</strong></li>
<li><strong>任何外部代理模块的独立性</strong></li>
</ul>

<p><img src="http://fernandocejas.com/wp-content/uploads/2014/09/clean_architecture1.png" alt="enter image description here" title=""></p>

<p>也不是一定只用4圆圈（如上图所示），因为这只是个示例，但是，你得深入考虑 <strong>依赖原则（Dependency Rule）</strong>：源码依赖只能是由外向内的，还有，<strong>内环</strong>的里的任何东西，不需要知道<strong>外环</strong>任何实现方式。<em>（ 译者：简单说就是，<code>外环</code>会依赖于<code>内环</code>，反过来，<code>内环</code>的东西独立与<code>外环</code>的，不因<code>外环</code>内容改变而受到影响，而且，<code>内环</code>也不需要知道<code>外环</code>里的任何具体逻辑实现。）</em></p>

<blockquote>
  <p><em>译者：啊！受不了啦，这个翻译的太变扭了，为了尽可能的保持原滋原味的翻译，不愿意加入自己的语言组织，可是，这样表达的意思又不完整，不接地气，怎弄呀！</em></p>
</blockquote>

<p>下面是解释一些词汇术语，为了让你更熟悉，更容易理解这篇文章。</p>

<ul>
<li><strong>Entities：</strong> 这些是指应用的 <code>业务对象</code>。<em>（译者：就是模型类，实体类，简单Java对象）</em></li>
<li><strong>Use Cases：</strong> 这些是指<code>用例</code>，把数据和业务对象串起来的用例，也可以叫做约束器。<em>（译者：简单说就是数据关联用例，拦截器，约束，等等。）</em></li>
<li><strong>Interface Adapters：</strong> 这一组适配器，是负责以最合理的格式转换<code>用例</code>（<code>use cases</code>）和<code>实体</code>（<code>entities</code>）之间的数据，<strong>表现层</strong>（Presenters ）和<strong>控制层</strong>（Controllers ），就属于这一块的。</li>
<li><strong>Frameworks and Drivers:</strong> 这里是所有具体的实现了：比如：UI，工具类，基础框架，等等。</li>
</ul>

<p>想要更具体，更生动丰富的解释，可以参考<a href="http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html">这篇文章</a>或者<a href="http://vimeo.com/43612849">这个视频</a></p>

<blockquote>
  <p>上面视频，好像要翻墙，这个我帮不了你的 。XD</p>
</blockquote>



<h3 id="我们的案例-our-scenario">我们的案例（ Our Scenario）</h3>

<p>我会做一个简单的案例，来实践一下：简单创建一个应用，这个应用内容是显示一个好友列表，或者显示云端的用户反馈回复，当你点击这些任何一个条目的时候，会打开一个新的界面，展示这个用户更详细的内容。 <br>
我给出了一个视频，这样你能更直观的看到我所阐述的内容：</p>

<iframe width="640" height="480" src="//www.youtube.com/embed/XSjV4sG3ni0" allowfullscreen=""></iframe>

<blockquote>
  <p>额，又是一个要翻墙的视频，Youtube地址。我帮不到你啦！</p>
</blockquote>



<h3 id="android架构-android-architecture">Android架构 （Android Architecture）</h3>

<p>这主要目的是<strong>关联分离（separation of concerns ，解耦）</strong> ，保证业务逻辑不涉及任何外环的相关内容，这样，他们就可以很方便的测试，不需要依赖任何外部元素。</p>

<p>要到达这一点呢，我的建议是把整个项目拆分成3个不同的层级，每一层都是各自功能，和其他层之间保持独立。 <br>
值得一提的是，每一层都使用他们自己的数据模型，这样才能做到依赖独立。（你将会发现在代码里面，需要做一个数据映射，才能更好的完成数据转换传输，这么做，是会付出一些代价的，如果你的模型的使用不是贯穿整个应用的话。）</p>

<blockquote>
  <p>下图是一个大体结构，你可以直观的看清楚流程是怎么样的。</p>
</blockquote>

<p><img src="http://fernandocejas.com/wp-content/uploads/2014/09/clean_architecture_android.png" alt="enter image description here" title=""></p>

<p><strong>说明：</strong> 我没有使用任何其他的外部库（除了使用了<code>gson</code> 做json数据解析，还有使用，<code>junit</code>，<code>mockito</code>，robolectric ，<code>espresso</code> 来做测试），原因是因为，这样可以使项目更加简单，不管怎么样，不要犹豫使用，ORMs（对象关系映射）做数据存储，或者其他任何依赖注入的框架，或者其他一些功能类似的框架，这样，可以使生活变的更简单啦。<strong>（记住，重复造轮子不是一个好习惯哦）</strong></p>



<h3 id="表现层presentation-layer">表现层（Presentation Layer）</h3>

<p>在这里，就是做一些跟视图和动画相关的逻辑了，使用<strong>Model View Presenter</strong> 模式是最好不过的了（下面简称<a href="http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter">MVP</a>），当然了，你可以使用其他类似的，比如 <code>MVC</code> 或者 <code>MVVM</code> 啦。我不具体阐述了，不过，在这里，只有<strong>fragments</strong> 和<strong>activities</strong> 才属于<code>视图</code>，在里面编写的，除了UI相关的逻辑之外，没有别的了，另外，渲染绘画相关的内容也是在这里。在这个层里，<strong>Presenters</strong>  结合用例 interactors (use cases)，另开新线程，在UI线程之外处理各种逻辑，然后，通过回调的方式把数据返回到视图模块。</p>

<p><img src="http://fernandocejas.com/wp-content/uploads/2014/09/clean_architecture_mvp.png" alt="enter image description here" title=""></p>

<p>如果你想要一个更酷的例子，可以看看<a href="https://github.com/pedrovgs/EffectiveAndroidUI/">Effective Android UI</a>，也是使用了 <code>MVP</code> 和 <code>MVVM</code>，看看我朋友 Pedro Gómez 写的怎么样？</p>



<h3 id="领域层-domain-layer">领域层 （Domain Layer）</h3>

<p><strong><code>业务逻辑</code>都在这：所以业务相关的内容都属于这一层。</strong>就Android 项目来说的话，你会发现所有的用例interactors (use cases)实现也是在这样一层。 <br>
这一层是纯 <code>Java</code> 编写的，没有任何Android相关的依赖。所有外部组件都是通过接口的方式访问业务逻辑。</p>

<p><img src="http://fernandocejas.com/wp-content/uploads/2014/09/clean_architecture_domain.png" alt="enter image description here" title=""></p>



<h3 id="数据层data-layer">数据层（Data Layer）</h3>

<p>所有应用数据的获取都是来自于这一层，以<code>用户仓库</code>（UserRepository ）的实现方式（接口是定义在<code>领域层</code>的），使用 仓库模式（<a href="http://martinfowler.com/eaaCatalog/repository.html">Repository Pattern</a>），它的策略是采用工厂模式，传递不同的条件参数，获取不同的数据。 <br>
比如：通过ID查找用户，如果用户数据有做缓存的话，就会优先使用磁盘缓存数据，如果没有，就会重新请求数据，完成后，保存到磁盘。 <br>
这背后的思路就是，原始数据是对客户端透明的，客户端不需要关注，数据是来自于内存，还是磁盘，或者服务器云端，只要能数据传递出去，也能取到，就够了。</p>

<p><img src="http://fernandocejas.com/wp-content/uploads/2014/09/clean_architecture_data.png" alt="enter image description here" title=""></p>

<p><strong>说明：</strong>在这次代码中，我实现的比较简单，使用了原生的文件系统存储，<code>Android</code> preferences配置文件方式来做磁盘缓存，只是为了达到学习目的，还是得提醒一下，<strong>不要重复造轮子</strong>，如果有现有的成熟的库来做这些工作的话。</p>



<h3 id="错误处理-error-handling">错误处理 （Error Handling）</h3>

<p>这是一个经常讨论的话题了，如果你有很好的方案想要分享的话，那会更好。<strong>我的建议是使用 回调</strong>，这样，比如：如果在数据仓库发生错误的话，可以通过调用，回调接口的2个方法：<strong>onResponse()</strong> 和 <strong>onError()</strong>。后面一个方法，包含一个封装了各种异常数据的类 <strong>“ErrorBundle”</strong>，这样做会带来一些麻烦，因为会一连串的回调接口，一个接一个的在错误传递到表现层，代码可读性会降低，另一方面 <br>
我还实现了一套事件驱动系统，如果有错误发生，就会抛出事件，就好像使用<a href="http://www.drdobbs.com/jvm/programming-with-reason-why-is-goto-bad/228200966">GOTO</a>一样，而且，在我看来，有时候你会混乱的，如果定义了很多个事件，而又没有合理的控制的话。</p>



<h3 id="测试-testing">测试 （Testing）</h3>

<p>至于测试，我采用了好几个方案，这个取决于你的各个分层：</p>

<ul>
<li><strong>Presentation Layer：</strong> 使用Android自带的<code>instrumentation</code> 和<code>espresso</code>  做集成和功能测试。</li>
<li><strong>Domain Layer:</strong> <code>JUnit</code> + <code>mockito</code>做单元测试。</li>
<li><strong>Data Layer:</strong> <code>Robolectric</code> （这一层开始有Android相关的依赖）+<code>junit</code> +<code>mockito</code>  做单元和集成测试。</li>
</ul>

<h3 id="代码展示show-me-the-code">代码展示（Show me the code）</h3>

<p><strong>我知道你很想知道代码在哪呢？对不对？ </strong> 好把！<a href="https://github.com/android10/Android-CleanArchitecture">这里是github连接地址</a> ，在上面你会找到我写的，至于目录结构，一些要说明的，就是，不同的层，使用不同的模块：</p>

<ul>
<li><strong>presentation:</strong> Android模块，放了presentation 表现层的内容。</li>
<li><strong> domain：</strong> Java模块，不包含Android相关的东西。</li>
<li><strong>data:</strong> Android模块，数据来源的地方。（ 数据都是从这获取）</li>
<li><strong>data-test:</strong>  数据层测试，因为<code>Robolectric</code> 有一些限制，我把它分成几个java模块。</li>
</ul>



<h3 id="总结conclusion">总结（Conclusion）</h3>

<p>正如 <code>Uncle Bob</code> 所说：<strong>“Architecture is About Intent, not Frameworks”</strong> ，我非常同意这个说法，当然了，有很多不同的方法做不同的事情（不同的实现方法），我很确定，你每天（像我一样）会面临很多挑战，但是遵循这些方法，确保的应用满足以下几点：</p>

<ul>
<li><strong>易维护 Easy to maintain</strong></li>
<li><strong>易测试 Easy to test.</strong></li>
<li><strong>高聚合Very cohesive.</strong></li>
<li><strong>低耦合 Decoupled.</strong> <br>
最后，我强烈推荐你去尝试一下，实践，并且分享你的经验。也许你会找到更好的解决方案：我们都知道，<strong>不断提升</strong>一个件非常好的，非常积极的事情呀。我希望这篇文章对你有所帮助，欢迎各种反馈。</li>
</ul>



<h3 id="连接资源links-and-resources">连接资源（Links and Resources）</h3>

<ol>
<li>Source code: <a href="https://github.com/android10/Android-CleanArchitecture">https://github.com/android10/Android-CleanArchitecture</a></li>
<li><a href="http://blog.8thlight.com/uncle-bob/2012/08/13/the-clean-architecture.html">The clean architecture by Uncle Bob</a></li>
<li><a href="http://www.infoq.com/news/2013/07/architecture_intent_frameworks">Architecture is about Intent, not Frameworks</a></li>
<li><a href="http://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93presenter">Model View Presenter</a></li>
<li><a href="http://martinfowler.com/eaaCatalog/repository.html">Repository Pattern by Martin Fowler</a></li>
<li><a href="http://www.slideshare.net/PedroVicenteGmezSnch/">Android Design Patterns Presentation</a></li>
</ol>

<p>Tagged as: android, androiddev, architecture, clean, clean architecture, developer, developers, development, model view presenter, mvp, pattern, patterns, programming, repository, repository pattern, strategy pattern</p></div></body>
</html>