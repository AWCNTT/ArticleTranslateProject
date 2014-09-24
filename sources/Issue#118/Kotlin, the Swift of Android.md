---
layout: post
title: "Kotlin, the Swift of Android"
date: 2014-09-12 23:50
comments: true
categories: Objective-C Swift Kotlin
---

Well, not quite but let's start from the beginning.

Now that Apple replaced Objective-C with Swift for iOS, the lack of a less archaic language for Android development has become more apparent.

For the desperate and adventurous there are the JVM alternatives like Scala and Groovy, but using them with Android is expensive: importing a language means importing the whole runtime, which is a nightmare for the package size and method count. Fine for small applications, but they are not what you are trying to solve with a better language.

### Kotlin

Introducing Kotlin -- JVM-based language made by JetBrains (folks behind IntelliJ IDEA and, by extension, Android Studio) and named after an [island near Saint Petersburg](http://en.wikipedia.org/wiki/Kotlin_Island), which is where the development office behind the project is located. Introduced in 2011, it's been around for a few years now and the Android support came in the second milestone release (M2).

Expecting the standard reaction: yes, another JVM alternative, but this one is specifically designed to be light by excluding all the domain-specific heft and only keeping the core features missing from Java. If that sounds good, read on.

### Features

What are those features, you ask? They are aplenty but I will focus on the ones that I have been missing from Java the most.

#### Named and optional arguments

Named arguments are a simple language feature that makes the code more readable, especially with longer method signatures.

Let's look at an example method:

```
void circle(int x, int y, int rad, int stroke) {  
    ...
}
```

Calling it in Java looks something like this:

```
circle(15, 40, 20, 1);  
```

It takes multiple glances at the method signature to digest what it actually does. In Kotlin, the definition looks similar:

```
fun circle(x: Int, y: Int, rad: Int, stroke: Int) {  
    ...
}
```

But the call is a lot better:

```
circle(15, 40, rad = 20, stroke = 1);  
```

Suppose we now want to make the stroke argument optional. In Java, you would overload a second method with one less argument and call the first one from it. Here is what you can do in Kotlin:

```
fun circle(x: Int, y: Int, rad: Int, stroke: Int = 1) {  
    ...
}
```

Not mind-blowing by any measure but still missing from Java.

### Lambdas

Functional programming is all the rage these days and Kotlin supports lambdas too.

Let's start with a simple one. Suppose you have a list of integers and you want to remove all the odd elements:

```
list.filter {it % 2 == 1}  
```

The function here takes the element type (in this case integer), and outputs a boolean. If we break out the filter predicate into an explicit variable, it might be easier to understand:

```
val predicate: (Int) -> Boolean = { it ->  
    it % 2 == 1
}
list.filter(predicate)  
```

The syntax is similar to most other languages with lambda support so let's leave it there.

### Null and type safety

Always having to check for nulls is another significant annoyance in Java, which is partially solved in Kotlin.

If you have an ordinary variable definition, the compiler does not allow it to be null.

```
var text1: String = "something" // All good  
var text2: String = null // Does not compile!  
```

For those cases, where you allow nulls, you have to define an optional data type with a question mark following the type.

```
var text2: String? = null // All good  
```

Calling methods on an optional-typed variable then requires optional calls.

```
text2?.replace(" ", "_")  
```

This means that if text2 is null, the replace() method call is ignored and no NullPointerException is thrown.

If you're handed an optional-typed variable that you are absolutely sure is not null (happens a lot in Android API's, where you can safely assume that a method never returns a null but an optional is used for consistency), then you can enforce the call.

```
text2!!.replace(" ", "_")  
```

This does produce a NullPointerException if text2 happens to be null, so be careful!

Another point of safety is testing for types. Let's say you have an instance of Context and you want to test whether it's an Activity (which, as you know, extends Context) and if so, do something that can only be done to an activity.

```
val context = getContext()  
if (context is Activity) {  
    context.finish()
}
```

Notice how after the type check you can just start using the context as an activity without having to cast it. Also, like instanceof in Java, is is null-safe so even if getContext() returns a null, the above code will not crash.

### Data objects

When writing data objects, the things you always have to do manually are: toString(), hashCode() and equals(). Even though most IDE's can make this task less tedious, that doesn't help with updating the methods when a new field is added.

In Kotlin, there is no need to bother with any of that! All you do is add the prefix "data" before the class definition and all three methods will be implicitly generated.

```
data class Island(val name: String? = null)  
```

So now if you were to instantiate the above class, it would produce a human-readable toString() automatically.

```
val island = Island(“Kotlin”)  
print(island.toString()) // Output: Island(name=Kotlin)  
```

Similarly, if we create another instance with the same name, it will equal to the original object and their hashCode() will match.

```
val island2 = Island("Kotlin")  
assertTrue(island.equals(island2))  
assertTrue(island.hashCode() == island2.hashCode())  
```

Obviously, if you need those methods customised, you are still free to implement them manually the way you would in Java.

### Singletons

Singletons are used often enough for a simpler way of creating them to exist. Instead of the usual static instance, getInstance() method and a private constructor, Kotlin uses the object notation.

```
object ApiConfig {  
    val baseUrl: String = "https://github.com"
}
```

For consistency, object notation is also used to define static methods.

```
open class MyFragment() : Fragment() {  
    class object {
        fun newInstance(): MyFragment {
            return MyFragment()
        }
    }
}
```

The above static method can be invoked by calling `MyFragment.newInstance()`, as any static method in Java.

### Traits

Although Kotlin doesn't support multiple inheritance, it comes closer by supporting traits, which are essentially interfaces with default implementation.

```
trait SessionCloseable {  
    fun closeSession() {
        Log.d(TAG, "Closing...")
    }
}
```

The above trait defines an object that can close a session, so here is how we implement it in an activity.

```
open class MyActivity : Activity, SessionCloseable {  
    override fun onStop() {
        closeSession()
    }
}
```

The notable thing to remember about the syntax is that there is no difference between extending and implementing in Kotlin, so everything goes in the list after the colon.

### Extension functions

Finally, let's look at a case, where you want to add functionality to an existing class from another API.

For example, if you wanted to create a method that takes a string and replaces all the spaces with underscores. In Java you would have to create a utility method that takes the original string.

```
public class StringUtils {  
    public static String encodeString(String str) {
        return str.replaceAll(" ", "_")
    }
}
```

In Kotlin, you can just create an extension method (even if the original class is final):

```
fun String.encodeSpaces(): String {  
    return this.replaceAll(" ", "_")
}
```

For those familiar with Objective-C, this is similar to [categories](https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/ProgrammingWithObjectiveC/CustomizingExistingClasses/CustomizingExistingClasses.html).

### How-to

To set your Android project up to use Kotlin, you need the following three things:

1. Import the plugin
1. Import the runtime
1. Create and link source directories

Plugin is imported in the `buildscript` clause (use the latest version available):

```
buildscript {  
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath "org.jetbrains.kotlin:kotlin-gradle-plugin:$kotlin_version"
    }
}
apply plugin: 'kotlin-android'  
```

Runtime is imported similarly in the `dependencies` clause:

```
dependencies {  
    compile "org.jetbrains.kotlin:kotlin-stdlib:$kotlin_version"
}
```

Finally, the directory structure would look something like this:

```
- app
  - src
    - debug
      - java
      - kotlin
    - main
      - java
      - kotlin
    - release
      - java
      - kotlin
```

For such structure, the android clause would need the following additions:

```
android {  
    sourceSets {
        main.java.srcDirs += 'src/main/kotlin'
        debug.java.srcDirs += 'src/debug/kotlin'
        release.java.srcDirs += 'src/release/kotlin'
    }
}
```

That's it! Now you can just create packages and *.kt files in the "kotlin" source directories and they will appear in your classpath.

For IDE support, you will need to install the Kotlin plugin in your Android Studio or IntelliJ IDEA (available from the repository) and setup Kotlin annotations for a project when prompted.

For more details, check out my sample application in the Source section, which demonstrates most of the language features.

### Cost

There are two parts to the cost that Android developers worry about: size and method count, both of which can be evaluated for a simple test application.

The last section deals with the trade-offs that you will be faced with when switching to Kotlin.

### Test application

There is no test application that will be indicative of the costs for any other application, largely because the number of underlying Kotlin methods that you touch is the deciding factor, which will vary wildly depending on the number of Android API's and language features that you use.

Having said that, the cost will only vary if you are using ProGuard to clean up the unused methods, otherwise you are stuck with the worst case, as the data below will show.

So here is the approach: one blank activity as generated by Android Studio (sample layout and menu, nothing else), first in its original Java form, then re-written in Kotlin. The goal is establishing the smallest running application as the best case with ProGuard enabled, and the worst case with it disabled.

###Size

```
          Original   ProGuard
Java      55 KB      54 KB  
Kotlin    396 KB     57 KB  
```

Size difference is quite small -- at most 341 KB, no more than a medium-sized library.

### Method count

```
          Original   ProGuard
Java      24         11  
Kotlin    6,063      53  
```

Depending on how much of Kotlin your application uses, the overhead is at most 6,063 methods. Not insignificant but, put in perspective, [Guava](https://code.google.com/p/guava-libraries/) (18.0-rc1) is 14,835 and [Google Play Services](https://developer.android.com/google/play-services/index.html) (5.0.77) is 20,298, so could be worse.

For the curious, here is a dump of the methods by the package (as given by [dex-method-counts](https://github.com/mihaip/dex-method-counts)):

```
Read in 6063 method IDs.  
<root>: 6063  
    : 5
    android: 3
        app: 2
        view: 1
    java: 441
        io: 68
        lang: 204
            annotation: 1
            ref: 2
            reflect: 14
        math: 11
        net: 1
        nio: 3
            charset: 3
        text: 4
        util: 150
            concurrent: 14
                locks: 10
            regex: 7
    javax: 14
        xml: 14
            parsers: 7
            transform: 7
                dom: 1
                stream: 1
    jet: 3
        runtime: 3
            typeinfo: 3
    kotlin: 5519
        browser: 10
        concurrent: 120
        dom: 309
        internal: 13
        io: 210
        jvm: 154
            internal: 153
        math: 23
        modules: 44
        platform: 1
        properties: 129
        reflect: 319
            jvm: 222
                internal: 193
                    pcollections: 54
        support: 20
        template: 57
        test: 69
        util: 5
    net: 17
        gouline: 17
            kotlindemo: 23
    org: 61
        jetbrains: 2
            annotations: 2
        w3c: 59
            dom: 59
                events: 19
```

### Trade-offs

Finally, let's touch on some of the trade-offs. Here is a major one: although most Java libraries work just fine with Kotlin, annotation processors, such as [Butter Knife](http://jakewharton.github.io/butterknife/), are not supported, at least for now (see [KT-5715](http://youtrack.jetbrains.com/issue/KT-5714) and [KT-5720](http://youtrack.jetbrains.com/issue/KT-5720)).

Having said that, if you can't live without your Butter Knife, one workaround is performing the injection in a Java base class, then extending it in Kotlin and doing the rest there (see [MainActivity](https://github.com/mgouline/android-samples/blob/master/kotlin-demo/app/src/main/kotlin/net/gouline/kotlindemo/app/MainActivity.kt) in the sample project). Whether or not the benefit outweighs the cost is for you to decide but hey, it works.

### Conclusion

To be fair to Java, most of the features listed [above](http://blog.gouline.net/2014/08/31/kotlin-the-swift-of-android/#features) have been added to Java 8, however there is no telling when that becomes available for Android (in fact, this was asked at the[ Android fireside chat](https://www.google.com/events/io/schedule/session/85311b0e-70ca-e311-b297-00155d5066d7) at Google I/O 2014 and according to [Xavier Ducrohet](https://plus.google.com/+XavierDucrohet), there were no immediate plans), while Kotlin is here now.

Besides, the language is quite mature and definitely not one to be taken as a novelty. The only difficulty that I found so far is the documentation -- often lacking and outdated, however since the syntax is so similar to its JVM siblings, knowing how things work in Scala usually produces accurate enough guesses, so it's a minor hiccup.

In conclusion, Kotlin is what I intend to use for most of my Android projects from now on. Obviously, given an existing codebase it may not be wise to convert everything straightaway, however since Kotlin can co-operate with Java classes, the barriers to entry are minimal and it's definitely worth a try.

### Updates

* 2014-09-03: Instrumentation tests are working, see comments for [KT-5714](http://youtrack.jetbrains.com/issue/KT-5714#comment=27-793936).

### Source

* [Kotlin Demo](https://github.com/mgouline/android-samples/tree/master/kotlin-demo) (GitHub)

### Resources

* [Kotlin Reference](http://kotlinlang.org/docs/reference/) (source: Project Kotlin)

---


原文地址：[http://blog.gouline.net/2014/08/31/kotlin-the-swift-of-android/](http://blog.gouline.net/2014/08/31/kotlin-the-swift-of-android/)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)