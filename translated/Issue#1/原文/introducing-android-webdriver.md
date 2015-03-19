---
layout: post
title: "Introducing Android WebDriver"
date: 2014-08-07 13:30
comments: true
categories: Testing, Webdriver
---

#Introducing Android WebDriver

[This post is by Dounia Berrada, an engineer on the EngTools team. — Tim Bray]

Selenium WebDriver is a browser automation tool which provides a lightweight and elegant way for testing web apps. Selenium WebDriver is now available as an SDK extra in the Android SDK, and supports 2.3 (Gingerbread) and onwards!

Whether or not your site is optimized for mobile browsers, you can be sure that users will be accessing it from their phones and tablets. WebDriver makes it easy to write automated tests that ensure your site works correctly when viewed from the Android browser. We’ll walk you through some basics about WebDriver and look at it in action.

###WebDriver Basics

WebDriver tests are end-to-end tests that exercise the web application just like a real user would. WebDriver models user interactions with a web page such as finger flicks, finger scrolls and long presses. It can rotate the display and interact with HTML5 features such as local storage, session storage and the application cache. Those tests run as part of an [Android tests project](http://developer.android.com/guide/topics/testing/testing_android.html) and are based on Junit. They can be [launched from Eclipse](http://developer.android.com/resources/tutorials/testing/helloandroid_test.html) or the command line. WebDriver tests can be wired with a continuous integration system and can run on phone and tablet emulators or real devices. Once the test starts, WebDriver opens a WebView configured like the Android browser and runs the tests against it.

WebDriver is an Android SDK extra and can be installed following [these instructions](http://code.google.com/p/selenium/wiki/AndroidDriver?ts=1318649352&updated=AndroidDriver#Using_the_Android_Test_Framework). Once you’ve done that you’ll be ready to write tests! There is a comprehensive WebDriver [user guide](http://seleniumhq.org/docs/03_webdriver.html) on the Selenium site, but let’s start with a basic example using [www.google.com](http://www.google.com/) to give you a taste of what’s possible.

###Getting Started

First, create an Android project containing an empty activity with no layout.

	public class SimpleAppActivity extends Activity {
    	@Override
    	public void onCreate(Bundle savedInstanceState) {
        	super.onCreate(savedInstanceState);
    	}
	}
	
Then create the Android test project that will contain the tests. WebDriver will create the WebView and set the layout automatically in the main Activity.

Let’s write a test that opens the Google home page on Android and issues a query for “weather in San Francisco”. The test will verify that Google returns search results, and that the first result returned is giving the weather in San Francisco.
<pre><code>
public class SimpleGoogleTest extends ActivityInstrumentationTestCase2<SimpleAppActivity> {

    public void testGoogleShouldWork() {
      // Create a WebDriver instance with the activity in which we want the test to run
      WebDriver driver = new AndroidDriver(getActivity());
      // Let’s open a web page
      driver.get("http://www.google.com");

      // Lookup for the search box by its name
      WebElement searchBox = driver.findElement(By.name("q"));

      // Enter a search query and submit
      searchBox.sendKeys("weather in san francisco");
      searchBox.submit();

      // Making sure that Google shows 11 results
      WebElement resultSection = driver.findElement(By.id("ires"));
      List<WebElement> searchResults = resultSection.findElements(By.tagName("li"));
      assertEquals(11, searchResults.size());

      // Let’s ensure that the first result shown is the weather widget
      WebElement weatherWidget = searchResults.get(0);
      assertTrue(weatherWidget.getText().contains("Weather for San Francisco, CA"));
    }
}
</code></pre>

Now let’s see our test in action! WebDriver will create a WebView with the same configuration as the Android browser in the main UI thread, i.e. the activity thread. The activity will display the WebView on the screen, allowing you to see your web application as the test code is executing.

### Interaction Testing

We’ve mentioned that WebDriver supports creating advanced gestures to interact with the device. Let’s use WebDriver to throw an image across the screen by flicking horizontally, and ensure that the next image in the gallery is displayed.

<pre><code>
WebElement toFlick = driver.findElement(By.id("image"));
// 400 pixels left at normal speed
Action flick = getBuilder(driver).flick(toFlick, 0, -400, FlickAction.SPEED_NORMAL)
        .build();
flick.perform();
WebElement secondImage = driver.findElement(“secondImage”);
assertTrue(secondImage.isDisplayed());
</code></pre>

Now, let’s rotate the screen and ensure that the image displayed on screen is resized.

```
assertEquals(landscapeSize, secondImage.getSize())
((Rotatable) driver).rotate(ScreenOrientation.PORTRAIT);
assertEquals(portraitSize, secondImage.getSize());
```

What if your test reveals a bug? You can easily take a screenshot for help in future debugging:

	File tempFile = ((TakesScreenshot)driver).getScreenshotAs(OutputType.FILE);
	
### Find Out More

If this has whetted your appetite and you’d like to know more, go ahead and install the Android WebDriver, take a look at the documentation on the [Selenium project’s wiki](http://code.google.com/p/selenium/wiki/AndroidDriver), or just [browse the javadocs](http://selenium.googlecode.com/svn/trunk/docs/api/java/index.html). For questions and feedback not only of the Android WebDriver but also its desktop brethren, please join [webdriver@googlegroups.com](http://groups.google.com/group/webdriver/). For announcements keep an eye on [http://seleniumhq.wordpress.com/](http://seleniumhq.wordpress.com/).

---


原文地址：[http://android-developers.blogspot.com/2011/10/introducing-android-webdriver.html](http://android-developers.blogspot.com/2011/10/introducing-android-webdriver.html)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)