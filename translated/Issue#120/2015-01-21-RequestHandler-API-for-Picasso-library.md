---
layout: post
title: "Picasso库中RequestHandler的API"
date: 2015-01-21 17：11
comments: true
categories: Picasso RequestHandler API
---

#Picasso库中RequestHandler的API

上周我发表了一篇使用Picasso库创建自定义下载的文章，发布这篇文章后，我被告知，下一个版本的Picasso（Picasso2.4）针对这个会有一个新的API。

这个新的API叫做RequestHandler API，这是[Lucas Rocha](http://lucasr.org/)的一个[PR](https://github.com/square/picasso/pull/512).

##问题

即使我们使用自定义下载能够处理大多数情况下的问题，新的API能解决引用这个库的一些限制，：

1. Dowloader应该仅仅被用来下载，如果我们处理本地图片，downloader就放错了地方。

2. 如果我们加载很多地方取得的位图，使用Dowloaders就显得过于复杂。

3. 只用一个Picasso实例来处理任何请求，比创建一个Piccasso 实例处理某种情况下的事情，和默认实例处理其余部分要好一些。

##解决方案
RequestHandler是一个Picasso的使用者可以通过被继承来扩展与自定义图像加载逻辑库中的公共类。

从现在开始，当Picasso实例化，Picasso将创建RequestHandler的list，其将迭代询问他们是否可以处理请求。

1. 在列表中的第一个位置我们发现了一个默认的Picasso 的RequestHandler，它用来处理Android本地资源（稍后我会解释为什么这个是列表中的第一个RequestHandler的） ：
[ResourceRequestHandler](https://github.com/square/picasso/blob/master/picasso/src/main/java/com/squareup/picasso/ResourceRequestHandler.java)

2. 在列表中的位置后，我们可以发现，自定义RequestHandler已经加入到我们的Picasso实例。注意，只有我们创建自定义的Picasso实例后，我们可以才添加自定义的RequestHandler。
```java
//Create Picasso instance with custom RequestHandler's
Picasso picassoInstance = = new Picasso.Builder(mContext.getApplicationContext())
        .addRequestHandler(new MyFirstRequestHandler())
        .addRequestHandler(new MySecondquestHandler())
        //..
        .addRequestHandler(new MyLastRequestHandler())
        .build();
```
3. 这个列表中剩余的部分将会促成其他的默认的Picasso RequestHandler

- [MediaStoreRequestHandler](https://github.com/square/picasso/blob/master/picasso/src/main/java/com/squareup/picasso/MediaStoreRequestHandler.java)
- [ContentStreamRequestHandler](https://github.com/square/picasso/blob/master/picasso/src/main/java/com/squareup/picasso/ContentStreamRequestHandler.java)
- [AssetRequestHandler](https://github.com/square/picasso/blob/master/picasso/src/main/java/com/squareup/picasso/AssetRequestHandler.java)
- [FileRequestHandler](https://github.com/square/picasso/blob/master/picasso/src/main/java/com/squareup/picasso/FileRequestHandler.java)
- [NetworkRequestHandler](https://github.com/square/picasso/blob/master/picasso/src/main/java/com/squareup/picasso/NetworkRequestHandler.java)

如果我们看下 [Picasso contructor](https://github.com/square/picasso/blob/master/picasso/src/main/java/com/squareup/picasso/Picasso.java#L144)我们会发现requestHandler 是如何构成的。
```java
final int extraCount = (extraRequestHandlers != null ? extraRequestHandlers.size() : 0);
final List<RequestHandler> allRequestHandlers = new ArrayList<RequestHandler>(7 + extraCount);
// ResourceRequestHandler needs to be the first in the list to avoid
// forcing other RequestHandlers to perform null checks on request.uri
// to cover the (request.resourceId != 0) case.
allRequestHandlers.add(new ResourceRequestHandler(context));
if (extraRequestHandlers != null) {
  allRequestHandlers.addAll(extraRequestHandlers);
}
allRequestHandlers.add(new ContactsPhotoRequestHandler(context));
allRequestHandlers.add(new MediaStoreRequestHandler(context));
allRequestHandlers.add(new ContentStreamRequestHandler(context));
allRequestHandlers.add(new AssetRequestHandler(context));
allRequestHandlers.add(new FileRequestHandler(context));
allRequestHandlers.add(new NetworkRequestHandler(dispatcher.downloader, stats));
requestHandlers = Collections.unmodifiableList(allRequestHandlers);
```

上面的代码中的注释说明为什么ResourceRequestHandler是列表中的第一个的原因：

ResourceRequestHandler需要首先在列表中，以避免迫使其他RequestHandlers对request.uri进行null检查，覆盖（ request.resourceId ！ = 0 ）的情况。
要理解这一点，我们需要知道如何创建一个自定义的RequestHandler。

每个自定义的RequestHandler继承自抽象类[RequestHandler.java](https://github.com/square/picasso/blob/master/picasso/src/main/java/com/squareup/picasso/RequestHandler.java)。同时，每个自定义的RequestHandler必须继承它的抽象方法：

- [public abstract boolean canHandleRequest(Request)](https://github.com/square/picasso/blob/master/picasso/src/main/java/com/squareup/picasso/RequestHandler.java#L94)

-- 如果ResquestHandler能处理这个请求这个方法返回true，否则返回false

- [public abstract Result load(Request)](https://github.com/square/picasso/blob/master/picasso/src/main/java/com/squareup/picasso/RequestHandler.java#L102)

-- 这个方法是我们写的自定义的调用Picasso加载图片的逻辑。该canHandleRequest被任何RequestHandler调用（当Picasso迭代他们），只要其中一个返回true ，。否则，抛出IllegalStateException的异常 。
```java 
static BitmapHunter forRequest(Picasso picasso, Dispatcher, dispatcher,Cache cache, Stats stats, Action action) {
    Request request = action.getRequest();
    List<RequestHandler> requestHandlers = picasso.getRequestHandlers();
    final int count = requestHandlers.size();
    for (int i = 0; i < count; i++) {
        RequestHandler requestHandler = requestHandlers.get(i);
        if (requestHandler.canHandleRequest(request)) {
            return new BitmapHunter(picasso, dispatcher, cache, stats, action, requestHandler);
        }
    }

    throw new IllegalStateException("Unrecognized type of request: " + request);
}
```

如果ResourcesRequestHandler （列表的第一项）不处理请求（返回false ） ，我们知道， request.uri不会为空。这就是为什么ResourcesRequestHandler是列表中的第一个。

自定义的RequestHandler很重要！

加载方法一将返回RequestHandler，结果实例同时包含和从（磁盘，内存，网络）加载的位图。

重点指出的是，我们仍然能继承自定义的Downloader，从而定义Picasso实例联网和下载图片的方式。但是我们自定义的Downloader，将会专门用于NetworkRequestHandler （列表的最后一个），这RequestHandler的只负责处理以下请求：

```java 
private static final String SCHEME_HTTP = "http";
private static final String SCHEME_HTTPS = "https";

//..

@Override public boolean canHandleRequest(Request data) {
String scheme = data.uri.getScheme();
    return (SCHEME_HTTP.equals(scheme) || SCHEME_HTTPS.equals(scheme));
}

//..
```
##代码

OK,现在，我才你们想知道...我如何创建自定义的RequestHandler。Well，那很好解释。

我将会使用和一样的DropBox的例子来保持解释的连贯性。

首先我们需要穿件一个继承自RequestHandler的类

```java 
public class DropBoxRequestHandler extends RequestHandler {

    private final DropBoxInteractor mDropBoxInteractor;

    public DropBoxRequestHandler(DropBoxInteractor dropBoxInteractor) {
        mDropBoxInteractor = dropBoxInteractor;
    }

    @Override
    public boolean canHandleRequest(Request data) {
        return false;
    }

    @Override
    public Result load(Request data) throws IOException {
        return null;
    }
}
```

然后我们需要增加在canHandleRequest(Request)方法中使用DropBoxRequestHandler的条件。就像，Picasso将会知道它是否应该使用handler或者保持轮询。

```java 
    @Override
    public boolean canHandleRequest(Request data) {
        String scheme = data.uri.getScheme();
        return (SCHEME_DROPBOX.equals(scheme));
    }
```

当Picasso询问DropBoxRequestHandler是否能处理dropbox的请求时候，这个条件为true，。所以当Picasso尝试显示bitmap时候，它将调用这个方法，。

```java 
 @Override
    public Result load(Request data) throws IOException {
        InputStream in = mDropBoxInteractor.getThumbnailStream(uri.toString().replace(SCHEME_DROPBOX+":",""));
        return new Result(BitmapFactory.decodeStream(in), NETWORK);
    }
```

最后一件我们需要做的事情是增加DropBoxRequestHandler给Picasso的实例。

```java 
 Picasso picassoInstance = new Picasso.Builder(mContext.getApplicationContext())
            .addRequestHandler(new DropBoxRequestHandler(dropBoxInteractor))
            .build();
When you have created you own picasso instance, make sure you don't use with(Context context). That's a static method that initializes the default Picasso instance. 
You just need to call picassoInstance.load() directly after you have built your instance, like in the following example:

picassoInstance.load(SCHEME_DROPBOX+":"+dropboxItem.getPath()).into(imageView);
```

好了！现在Picasso的实例能够像默认的Picasso实例一样处理相同的请求，包括dropbox的缩略图。


##结论

RequestHandler表明的事情：

我们不需要使用叫做downloader的东西去加载bitmap。

现在自定义的可以以模块化的方式加载bitmap，其中每个RequestHandler的有他自己的任务来完成。

自定义的Picasso实例可以用来做任何符合RequestHandler的条件的请求。

我们仍然能够自定义我们的HTTP和HTTPS URI下载方案。

对于这个这个新的实现我很高兴，我想说的是感谢你们为了提高这个库所做的工作。

我希望你们喜欢他，而且继续coding!

---


原文地址：[http://blog.jpardogo.com/requesthandler-api-for-picasso-library/](http://blog.jpardogo.com/requesthandler-api-for-picasso-library/)

译者：[jackuhan](https://github.com/jackuhan) 校对：[校对者ID](https://github.com/校对者ID)
