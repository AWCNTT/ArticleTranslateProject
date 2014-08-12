---
layout: post
title: "OAuth and REST in Android: Part 1"
date: 2014-08-10 10:41
comments: true
categories: OAuth REST Android
---
#OAuth and REST in Android: Part 1

I recently had the fun experience of learning a bit about both OAuth and making REST web service calls in Android and since I didn’t find much good material out there, I thought I would share it here.
![alt text](https://github.com/AWCNTT/ArticleTranslateProject/raw/master/sources/Issue%232/oauthpicture_thumb.png "oauthpicture_thumb.png")

Let’s talk about OAuth

The first thing you NEED to know about OAuth is that OAuth and OAuth 2.0 are not the same protocol.

From the [OAuth guide](http://hueniverse.com/2010/05/introducing-oauth-2-0/) on hueniverse:

	OAuth 2.0 is a completely new protocol and is not backwards 	compatible with previous versions. However, it retains the 	overall architecture and approach established by the previous 	versions, and the same introduction (from the Official Guide to 	OAuth 1.0) still very much applies.

But, before we get into that, let’s talk a little bit about what OAuth is for.

OAuth basically does two things for a web site providing some kind of service or API that another application might want to use.

<li>1.It prevents the consuming application from needing to store or have the user’s login and password.
<li>2.It allows for a scoping of access to the producer’s services.  (For example, a user might be able to login to a site and access all of the features, but an app using OAuth might only be granted permissions to do certain things.)
So now that we know why, let’s talk about how both OAuth and OAuth 2 work from a high level perspective.

So now that we know why, let’s talk about how both OAuth and OAuth 2 work from a high level perspective.

From the app developer perspective, the flow goes something like this:

<li>1.You register your application with the service you want to access and get some unique identifiers for your application.
<li>2.When you want to access something on the service, you make a request to the service using your unique identifier and telling it where to send the user after they authenticate.  (Usually you would launch a browser window here.)
<li>3.User will login to the service using the browser window you launched for that service and choose to grant your application certain privileges.
<li>4.The service will redirect the user to the callback url you provided and include a code you can use to get an access token.
<li>5.You call the service one more time passing in your unique identifier and the code you got back and the service grants you an access token which you can use to access services you have been granted permissions for.
<li>6.The next time you need to make a call you can just use that access token instead of going through this whole process again.  (At least till it expires.)

It really isn’t that complicated; the basic idea is that you tell the service who you are and ask the user to authenticate themselves and grant permissions to your application.  Then you prove that you got the response from the server and that it is still you and the service gives you a special pass to access the service.

The real difference between OAuth and OAuth 2 is the protocol itself, not the process.  So this is both good and bad.

Good, because we don’t have to change our process flow and understand something different.

Bad, because we can’t use the same libraries to access an OAuth 2 implementation as we do for an OAuth implementation and vice versa.  There is no backwards compatibility.

For the purpose of this post I am going to show how to use the [Leeloo library](https://bitbucket.org/smartproject/oauth-2.0/wiki/Home) to connect to an OAuth 2 service.

##Connecting to OAuth 2.0

###Download the library

The first thing you will want to do is to get the latest version of the Leeloo library.  It has moved to the Apache Amber project, so this link might change, but for now I found the [ready to download binaries here](https://bitbucket.org/smartproject/oauth-2.0/wiki/Home).


###Configure build path

Once you have the library downloaded you’ll want to add the following jars to your build path:

<li>jettison-1.2.jar
<li>oauth2-client.jar
<li>oauth2-common-0.1.jar
<li>slf4j-api.1.6.1.jar

###Make initial request for authorization

Make sure you already have a unique client id created with the service you are going to access.  Most services out there like Twitter have a page where you can request one for your application.

Once you have this, you will use this to create a url that you will redirect the user of your application to.

In the code snippet below, I am creating the url and then creating a new Intent in Android that will open a web browser at that url.

<pre><code>
OAuthClientRequest request = null;
request = OAuthClientRequest
       .authorizationLocation(authenticationUrl)
       .setClientId(&quot;&lt;your client id&gt;&quot;).setRedirectURI(&quot;&lt;your redirect url&gt;&quot;)
        .buildQueryMessage();
 
Intent intent = new Intent(Intent.ACTION_VIEW,
        Uri.parse(request.getLocationUri() + &quot;&amp;response_type=code&quot;));
startActivity(intent);
</code></pre>

A couple of things to note about this code snippet:
<li>You’ll need to replace client id with your client id and redirect URI with your redirect URI.
<li>The redirect URI for an Android application is going to be some protocol that doesn’t really exist, but you can tell your application to respond to.  (We’ll cover that in the next step, but you might have some URI like myapp://oauthresponse)
<li>I had to add a “&response_type=code” to the end of my URL because the authentication service required it.  You might have to look and see how the service you are trying to access expects requests to be formed.

###Configure your application to respond to your unique URI

In the previous step we had set the redirect to a unique URI like “myapp://oauthresponse”, now you will need to configure the activity you want to handle the response from the service to intercept that protocol.

You can do this by adding an intent filter to that activity like so:
<pre><code>
&lt;intent-filter&gt;
    &lt;action android:name=&quot;android.intent.action.VIEW&quot;/&gt;
    &lt;category android:name=&quot;android.intent.category.DEFAULT&quot; /&gt;
    &lt;category android:name=&quot;android.intent.category.BROWSABLE&quot;/&gt;
    &lt;data android:scheme=&quot;myapp&quot; android:host=&quot;oauthresponse&quot;/&gt;
&lt;/intent-filter&gt;
</code></pre>
With this intent filter we are telling our activity to respond to a URI request in the form of “myapp://oauthresponse”.
###Configure your activity to respond to handle the intent you registered to receive

Next we’ll want to set up an override in our activity to handle the new intent that will be called on our activity.  We can then pull the code the service gave us out and use that to request a real token.
<pre><code>
@Override
protected void onNewIntent(Intent intent)
{
    Uri uri = intent.getData();
 
   if (uri != null &amp;&amp; uri.toString()
           .startsWith(&quot;myapp://oauthresponse&quot;))
    {
        String code = uri.getQueryParameter(&quot;code&quot;);
        // ...
   }
}
</code></pre>
This code will allow us to respond to the intent that will be fired by the web browser when the service redirects the user to “myapp://oauthresponse.”

###Exchange the code for a token

Next we need to get a real token instead of a code.  When the user typed in their username and password and granted access to our application, the server called our callback URI with a code that we extracted in the last step.

Now we can pass that code back up to the server along with our client secret and client id and get an access token.

<pre><code>
OAuthClientRequest request = null;
 
request = OAuthClientRequest.tokenLocation(&quot;&lt;service request URL&gt;&quot;)
    .setGrantType(GrantType.AUTHORIZATION_CODE)
   .setClientId(&quot;&lt;your client id&gt;&quot;)
    .setClientSecret(&quot;&lt;your client secret&gt;&quot;)
    .setRedirectURI(&quot;myapp://oauthresponse&quot;)
    .setCode(code)
    .buildBodyMessage();
 
OAuthClient oAuthClient = new OAuthClient(new URLConnectionClient());
 
OAuthJSONAccessTokenResponse response = oAuthClient.accessToken(request);
String token = response.getAccessToken();
</code></pre>

In this bit of code we are sending back to the server all the information we have that identifies our application as well as the code that proves the user allowed us to access that service.

Then we get back an access token from the server.  I would recommend you save this access token so that you don’t have to do this reauthentication in the future.

###Wrapping it up

So what we have been able to do here is to redirect a user to the right page to authenticate our application, and then wire up our activity to respond to the redirect the service gives us in order to ultimately get an access token we can use to prove we are authorized to access that service on behalf of the user.

Be cautious though, because not everyone is implementing OAuth 2.0 exactly the same way, since the spec is not finalized.  So you might run into issues where things aren’t working exactly as laid out in this post.  If that is the case, you may have to modify a URL that is generated or something along those lines.

In my next post, I’ll show you what you can actually do with your authorization token.  We’ll go through calling a REST based API web service from Android.

Feel free to post any questions in comments.  I used this code to connect to the Dailymile.com API in my [PaceMaker application](https://market.android.com/details?id=com.pacemaker.android).


---


原文地址：[http://simpleprogrammer.com/2011/05/25/oauth-and-rest-in-android-part-1/](http://simpleprogrammer.com/2011/05/25/oauth-and-rest-in-android-part-1/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)