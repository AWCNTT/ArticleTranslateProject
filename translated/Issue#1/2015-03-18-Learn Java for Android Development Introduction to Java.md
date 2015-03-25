---
layout: post
title: "Introducing Android WebDriver"
date: 2014-08-10 19:10
comments: true
categories: Android Introduction
---

#Learn Java for Android Development: Introduction to Java
[翻译原文](http://code.tutsplus.com/tutorials/java-tutorial--mobile-2604)

In this tutorial series, you’ll become familiar with Java, the programming language used to develop Android applications. Our goal is to prepare those already familiar with one programming language, such as PHP or Objective-C, to become comfortable working with the Java programming language and dive into Android app development. In this tutorial, you’ll get a brief introduction to Java fundamentals, including object oriented programming, inheritance and more. If you’re new to Java, or just looking to brush up on the details, then this is the tutorial series for you!

通过这一系列教程，你将熟悉用于开发Android应用程序的编程语言--Java。我们的目标人群是那些已经掌握了一种编程语言（PHP或者Objective-C）的人，使他们方便的使用Java语言并投入到Android应用程序的开发中。在本教程中，你将会学习到一个简明的Java基本原理介绍，包括面向对象编程，继承以及其他。如果你刚刚接触Java，或者想要复习Java的细节，那么这就是你要找的教程！
##开始（Getting Started）

As far as prerequisites go, we’re going to assume you understand how to program (perhaps in PHP, or Visual Basic or C++), but that you are unfamiliar with the specifics of programming in the Java language. We aren’t going to teach you to program; we’re going to provide you with clear examples of commonly used Java language constructs and principles, while pointing out some Android-specific tips and tricks.

至于预备知识，我们假定你理解如何编程（假定为PHP，Visual Basic或者C++），但是并不熟悉Java语言的相关细节。我们并不会教您编程，我们将向您提供清晰的Java语言常用的结构和原理的例子，指出了一些Android的特定的技巧和窍门。

##必备技能（What You’ll Need）

Technically, you don’t need any tools to complete this tutorial but you will certainly need them to develop Android applications.

从技术上讲，你不需要任何工具来完成本教程，但你肯定会需要它们来开发Android应用程序。

To develop Android applications (or any Java applications, for that matter), you need a development environment to write and build applications. Eclipse is a very popular development environment (IDE) for Java and the preferred IDE for Android development. It’s freely available for Windows, Mac, and Linux operating systems.

为了开发Android应用程序（或者任何Java应用程序，对于这个问题而言），你需要一个开发环境来编写、运行应用程序。Eclipse是一个非常流行的Java开发环境（IDE）并且扮演着Android的开发环境。它免费支持Windows，Mac和Linux操作系统。

For complete instructions on how to install Eclipse (including which versions are supported) and the Android SDK, see the [Android developer website](http://developer.android.com/sdk/index.html).

关于如何安装Eclipse（包括支持哪个版本）和Android SDK的完整介绍，请参见[Android developer website](http://developer.android.com/sdk/index.html)。

##Java初见（What is Java?）

Android applications are developed using the Java language. As of now, that’s really your only option for native applications. Java is a very popular programming language developed by Sun Microsystems (now owned by Oracle). Developed long after C and C++, Java incorporates many of the powerful features of those powerful languages while addressing some of their drawbacks. Still, programming languages are only as powerful as their libraries. These libraries exist to help developers build applications.

Android应用程序使用Java语言开发。截止目前，这是你开发原生应用程序的唯一选择。Java是一种非常流行的编程语言，它由Sun Microsystems (now owned by Oracle)公司开发。在C和C++之后开发出来，Java的集成了很多语言中的强大功能，同时解决他们的一些弊端。尽管如此，编程语言和它的的库一样强大。这些库的存在帮助开发者开发应用程序。

Some of the Java’s important core features are:

<li>It’s easy to learn and understand
<li>It’s designed to be platform-independent and secure, using
virtual machines
<li>It’s object-oriented

Java中一些重要的核心特性：

<li>便于理解，便于学习
<li>平台无关性和安全性，使用虚拟机
<li>面向对象

Android relies heavily on these Java fundamentals. The Android SDK includes many standard Java libraries (data structure libraries, math libraries, graphics libraries, networking libraries and everything else you could want) as well as special Android libraries that will help you develop awesome Android applications.

Android对这些Java基本原理依赖性很强。Android SDK包含很多标准的Java库文件（数据结构库，数学库，图形库，网络库和任何你想要使用的库）和一些特殊的Android库，以便于帮助你开发出漂亮的应用程序。

##为什么Java易学（Why is Java Easy to Learn?）

Java is easy to learn for a variety of reasons. There’s certainly no shortage of Java resources out there to help you learn the language, including websites, tutorials, books, and classes. Java is one of the most widely discussed, taught, and used programming languages on the planet. It’s used for many different types of programming projects, no matter their scale, from web applications to desktop applications to mobile applications.

Java易学是由很多原因的。这些Java资源在帮助你学习这门语言中是不可或缺的，包括网站、教程、书籍和课程。Java是世界上讨论、教授和使用最多的编程语言。Java被用于很多不同类型的编程项目，无论这些项目的范围，从web应用程序到桌面应用程序，再到移动应用程序。

If you’re coming from a traditional programming background like C or C++, you’ll find Java syntax quite similar. If you’re not, then take comfort in knowing that you’ve chosen one of the easiest languages to learn. You’ll be up and running in no time at all.

如果你有传统的编程语言的经验，例如C和C++，你会发现Java的语法与其很相似。如果你没有相关经验，那么放松下来，要知道你选择的Java是最简单的编程语言之一。你会学会并且很快进入到状态中。

Finally, Java is one of the most human-readable languages out there, by which we mean that a person who knows nothing about programming can often look at some Java code and have at least an inkling what it’s doing. Consider the following example:

最后，Java是最易于人阅读的编程语言之一，言下之意就是没有任何编程经验的人也可以看懂Java代码并且也能大概理解这些代码的作用。考虑下面的例子：

<pre><code>
char character = 'a';
if(character=='a')
{
    doSomething();
} else {
    doSomethingElse();
}
</code></pre>
If you simply read the code aloud, you can pretty much tell that this snippet of code is doing. There’s a single letter variable called character. If the character variable equals the letter a, then we do something (call the `doSomething()` method), otherwise we do something else (by calling the `doSomethingElse()` method).

如果你简单阅读一下这段代码，你几乎可以理解这段代码的作用。这里有个叫做character的单字母变量。如果这个character变量等同于字母a，那么我们执行一些语句（调用doSomething（）方法），否则，我们执行一些其他的语句（通过调用doSomethingElse（）方法）。

##为什么平台无关性很重要（Why is Platform Independence Important?）

With many programming languages, you need to use a compiler to reduce your code down into machine language that the device can understand. While this is well and good, different devices use different machine languages. This means that you might need to compile your applications for each different device or machine language—in other words, your code isn’t very portable. This is not the case with Java. The Java compilers convert your code from human readable Java source files to something called “bytecode” in the Java world. These are interpreted by a Java Virtual Machine, which operates much like a physical CPU might operate on machine code, to actually execute the compiled code. Although it might seem like this is inefficient, much effort has been put into making this process very fast and efficient. These efforts have paid off in that Java performance in generally second only to C/C++ in common language performance comparisons.

与很多编程语言一样，你需要一个编译器来将你的代码翻译成设备可以理解的机器语言。虽然这是一个好主意，但是不同的机器支持不同的机器语言。这就意味着你需要将你的应用程序编译成不同的设备和不同的机器语言---换言之，你的代码是不可移植的。这对于Java来说不是问题。Java编译器将你的代码从人可读的Java源代码文件转变为Java中被称为“字节码"文件。这些由一个Java虚拟机，其操作很像一个物理的可以运行机器代码的CPU，以实际执行的编译代码。虽然这看起来是低效的，但是为了使这个过程变得快速和有效率，还是做了很多努力的。这些努力在Java性能得到了回报在一般共同语言的性能比较中仅次于C / C++。

Android applications run in a special virtual machine called the Dalvik VM. While the details of this VM are unimportant to the average developer, it can be helpful to think of the Dalvik VM as a bubble in which your Android application runs, allowing you to not have to worry about whether the device is a Motorola Droid, an HTC Evo, or the latest toaster running Android. You don’t care so long as the device is Dalvik VM friendly—and that’s the device manufacturer’s job to implement, not yours.

Android应用程序运行在一个被称作Dalvik VM的特殊的虚拟机中。对于普通开发者来说，这个虚拟机的细节是不重要的，当你的Android应用程序运行的时候把这个虚拟机想象成一个泡泡对你来说是有帮助的，这些使得你不用担心你的设备是Motorola Droid，还是HTC Evo，亦或者是最新的运行Android系统的设备。你不用关心这些，只要该设备运行的是Dalvik虚拟机，那就是设备制造商的工作来实现，不是你的。

##为什Java是安全的（Why is Java Secure?）

Let’s take this bubble idea a bit further. Because Java applications run within the bubble that is a virtual machine, they are isolated from the underlying device hardware. Therefore, a virtual machine can encapsulate, contain, and manage code execution in a safe manner compared to languages that operate in machine code directly. The Android platform takes things a step further. Each Android application runs on the (Linux-based) operating system using a different user account and in its own instance of the Dalvik VM. Android applications are closely monitored by the operating system and shut down if they don’t play nice (e.g. use too much processing power, become unresponsive, waste resources, etc.). Therefore, it’s important to develop applications that are stable and responsive. Applications can communicate with one another using well-defined protocols.

让我们让这个气泡想法深入一点。因为Java应用程序运行在虚拟机中，独立于底层硬件。因此，虚拟机可以封装，包含，和管理代码执行，与直接操作机器代码的语言相比更加安全。每个Android应用使用不同的用户账户运行在基于Linux的操作系统中并且在它本身的虚拟机实例中。Android应用程序是由操作系统密切监视和关闭，如果他们运行出现问题（例如，使用太多处理能力，无响应，浪费资源等等）。因此，开发应用程序中重要的是稳定性和可响应性。应用程序可以使用定义良好的协议相互通信。

##编译你的代码（Compiling Your Code）

Like many languages, Java is still a compiled language even though it doesn’t compile all the way down to machine code. This means you, the developer, need to compile your Android projects and package them up to deploy onto devices. The Eclipse development environment (used with the Android Development plug-in) makes this pretty painless. In Eclipse, automatic compilation is often turned on by default. This means that every time you save a project file, Eclipse recompiles the changes for your application package. You immediately see compile errors. Eclipse also interprets Java as you type, providing handy code coloring and formatting as well as showing many types of errors as you go. Often, you can click on the error and have Eclipse automatically fix a typo, or add an import statement, or provide a method stub for you, saving lots of typing.

与很多编程语言一样，Java仍旧是一种编译型语言，尽管Java并不是全程编译成机器语言。这就意味着开发者需要编译你的Android项目并且打包项目配置到设备上。Eclipse开发环境（有Android开发插件）让这一切变得容易。在Eclipse中，自动编译默认是自动运行的。这意味着你每次保存项目时，Eclipse会重新编译你的项目。编译错误会及时看到。当你输入Java代码时Eclipse会同时解释你的程序，在你键入代码时提供代码高亮和格式化代码，以及各种类型的错误。通常，你可以点击错误使Eclipse自动为你修改代码，或者添加引用声明，或者为你提供跟方法，这样节省了很多输入工作。

You can still manually compile your code if you so desire. Within Eclipse, you’ll find the Build settings under the project menu. If you have “Build Automatically” turned on, you can still choose the “Clean…” option that will allow you to do full rebuild of all files. If “Build Automatically” is turned off, “Build All” and “Build Project” menu options are enabled. "Build All" means to build all of the projects in the workspace. You can have many projects in an Eclipse workspace.

如果你喜欢你依旧可以手动编译你的代码。在Eclipse中你会发现在项目按钮下的编译设置（Build setting）。如果你打开了“Build Automatically”，你依旧可以选择“Clean...”选项来重新编译你的项目。如果关闭了“Build Automatically”，“Build All”和“Build Project”菜单选项就可以选择的。“Build All”选项是编译当前workspace的所有项目。你可以在Eclipse的workspace中创建多个项目。

The build process, for regular Java projects, results in a file with the extension of JAR – Java ARchive. Android applications take JAR files and package them for deployment on devices as Android PacKage files with an extension .apk. These formats not only include your compiled Java code, but also any other resources, such as strings, images, or sound files, that your application requires to run as well as the Application Manifest file, AndroidManifest.xml. The Android Manifest file is a file required by all Android applications, which you use to define configuration details about your app.

编译进程，对于一般的Java项目，最终形成JAR--Java ARchive扩展文件。Android应用程序使用JAR文件和包文件来开发，形成Android Package files的为.apk的扩展文件。这些格式化的并不只包括已经编译好的Java代码，还包括一些其他资源，例如字符串，图片和声音文件，你的应用程序需要同时运行在Application Manifest file, AndroidManifest.xml。Android清单文件是所有的Android应用程序所要求的，你用它来定义你的应用程序配置的详细信息所需的文件。

##什么是面向对象的编程语言（What is an Object Oriented Programming Language?）

Okay. Time for a very brief and 20,000 foot view of object oriented programming (OOP). OOP is a programming style or technique that relies upon the definition of data structures called objects. For those new to OOP, an object can be thought of much like a custom data type. For example, you might have a Dog object, which represents the blueprint for a generic dog, with a name, breed, and gender. You could then create different instances of the Dog object to represent specific dogs. Each Dog object must be created by calling its constructor (a method that has the same name as the object itself, and may or may not have parameters for setting initial values). For example, the following Dog objects use a constructor with three parameters (name, breed, gender):

	Dog dog1 = new Dog("Lassie", collie, female);
	Dog dog2 = new Dog("Fifi", poodle, female);
	Dog dog3 = new Dog("Asta", foxterrier, male);

好了。现在是时间来简明的看一下什么是面向对象了。面向对象变成是一种编程风格也是一种技术，其依赖于一种被称为对象的数据结构的定义。对于刚刚接触面向对象编程的人，你可以认为对象为一种自定义的数据类型。例如，你有一个称为Dog的对象，它代表一类普通的有名字、繁殖和性别的狗类模型。你可以创建Dog对象不同的实例以表示特定类型的狗狗。每个Dog对象必须有他的构造方法（与对象同名的方法，可能有参数来设定初始值）创建。例如，下面的Dog对象必须使用一个有三个参数（name，breed，gender）的构造方法来创建。

	Dog dog1 = new Dog("Lassie", collie, female);
	Dog dog2 = new Dog("Fifi", poodle, female);
	Dog dog3 = new Dog("Asta", foxterrier, male);

	
So where is this Dog object defined? Well, here we need to begin defining some of the fundamental building blocks of the Java programming language. A class provides a definition for an object. Therefore, there is a Dog class somewhere—either defined by you or in some library somewhere. Generally speaking, a class will be defined in its own file, with the filename matching the class name (e.g. Dog.java). There are exceptions to this rule, such as classes defined within other classes (when a class is declared within a class, it is generally defined for use within the parent class only as a helper class, and referred to as an inner class).

所以这个Dog对象是如何定义的呢？我们需要开始定义一些Java编程语言的基本的编译模块。类为对象提供定义。因此，Dog类既不是由用户定义的，也不是由一些类库定义的。一般来说，类是由他自身的文件名和类名相同的文件定义的。但这之中还是有特例的，比如在其他类中定义的类（当一个类在另一个类中声明，它通常只是为父类定义并作为一个帮助类来使用，或者指向一个内部类）。

When you want to reference an object from within another class, you need to include an import statement in the top of your class file, much like you would use a #include statement in a compiled language like C.

当你想在其他类中引用一个对象时，你需要在你的类文件的开头出包含一个引用声明，与编译型语言C中的#include声明很类似。

A class typically describes the data and behavior of an object. The behavior is defined using class methods. Method is the common term for a subroutine in an OOP language. Many common object classes are defined in shared class libraries like software development kits (SDKs), whereas others are defined by you, the developer, for your own purposes. Software is then built up by using and manipulating object instances in different ways.

类通常是用来描述对象的数据和行为的。行为用来定义类的方法。方法是面向对象编程语言中最常见的子程序。很多对象类被定义在一个共享的类中，像软件开发包（SDKs），其他的都是由开发人员根据具体的需求来自行定义的。软件是通过不同的方式使用和操作对象实例来编写的。

Please realize this is a very generalized definition of OOP. There are entire books written on this subject. If you’d like to know more about OOP, here are a few resources you might want to check out:

<li>Wikipedia has a [nice overview of OOP](http://en.wikipedia.org/wiki/Object-oriented_programming)
<li>[Sun Java Tutorials on Java](http://www.oracle.com/technetwork/java/index.html)
<li>[The Java Tutorials at Oracle](http://docs.oracle.com/javase/tutorial/java/concepts/)

请认识到这是一种普遍的面向对象编程的定义。还有一些有关这个方面的比较全面的书籍。如果你想深入了解面向对象编程，这里有一些你可以浏览的资源：

<li>Wikipedia has a [nice overview of OOP](http://en.wikipedia.org/wiki/Object-oriented_programming)
<li>[Sun Java Tutorials on Java](http://www.oracle.com/technetwork/java/index.html)
<li>[The Java Tutorials at Oracle](http://docs.oracle.com/javase/tutorial/java/concepts/)

Note: We use a lot of different terminology in this tutorial. There are multiple ways to refer to a given concept (e.g. superclass vs. parent class), which is confusing to those new to object oriented programming. Different developers use different terms, and so we have tried to mention synonyms where appropriate. Deciding which terms you will use is a personal choice.

注意：本教程中我们使用了很多术语。对于刚刚接触面向对象编程的人来说有很多方法来了解这些术语（例如：superclass vs. parent class）。不同的开发者使用不同的术语，因此我们尝试着使用合适的同义词。使用什么术语由你个人的情况而定。

##理解继承（Understanding Inheritance）

Here is another important Java concept you’ll run into a lot: inheritance. Simply put, inheritance means that Java classes (and therefore objects) can be organized into hierarchies with lower, more specific, classes in the hierarchy inheriting behavior and traits from higher, more generic, classes.

还有一个非常重要今后会经常使用的Java内容：继承。简单来说，继承的含义是Java的类（也可以是对象）可以被使用在一个低层级的更加有效的类中，该类的行为方法继承自更加高级的的类。（这段翻译的不好，请见谅啊。）

This concept is best illustrated by example. Let’s pretend we are developing a Java application to simulate an aquarium. This aquarium has some fish in it. Therefore, we might define a class to represent a fish. This class, called Fish, could include some data fields (also called attributes, or class member variables) to describe a fish object: species, color and size; as well as some of its behavior in the form of methods (also called subroutines, or functions in procedural languages), like `eat()`, `sleep()`, and `makeBabyFish()`.

下述例子很好的阐明了这些内容。我们假设正在开发一个Java应用程序用来模拟一个水池。这个水池中有一些鱼。因此，我们可以定义一个类来代表鱼。该类被命名为Fish，包含一些数据字段（也被成为属性，或者类的成员变量）来表述一个鱼的对象：种类，颜色和大小；和用方法的格式来表述鱼的一些行为（也被称为子程序，或者程序语言上的功能），如 `eat()`, `sleep()`, and `makeBabyFish()`.

A special type of method, called a constructor, is used to create and initialize an object; constructors are named the same as their class and may include parameters. The following Fish class has two constructors: one for creating a generic Fish object and another for constructing a specific Fish object with some initial data. You’ll also see that the Fish class has two eat() methods: one for eating something random, and another for eating another fish, which would be represented by another instance of the Fish class:
<pre><code>
public class Fish {

    private String mSpecies;
    private String mColor;
    private int mSize;

    Fish() {
        // generic fish
        mSpecies = "unknown";
        mColor = "unknown";
        mSize = 0;
    }

    Fish(String species, String color, int size) {
        mSpecies = species;
        mColor = color;
        mSize = size;
    }
    public void eat() {
        // eat some algae
    };
        
    public void eat(Fish fishToEat) {
        // eat another fish!
    };
        
    public void sleep() {
        // sleep
    };
        
    public void makeBabyFish(Fish fishSpouse, int numBabies) {
        // Make numBabies worth of baby fish with Fish spouse
    };
}
</code></pre>

构造方法是一种特殊类型的方法，用于创建和初始化一个对象；构造方法的名字与所在的类一致并且还包含一些参数。下面的Fish类中包含两个构造方法：一个用来创建一个普通的Fish对象，另一个用来构造一个具有一些初始化数据特定的Fish对象。你可以看到Fish类中有两个eat（）方法：一个作用是随机吃东西，另一个是吃其他的鱼，其可以被其他的Fish类实例所代表。
<pre><code>
public class Fish {

    private String mSpecies;
    private String mColor;
    private int mSize;

    Fish() {
        // generic fish
        mSpecies = "unknown";
        mColor = "unknown";
        mSize = 0;
    }

    Fish(String species, String color, int size) {
        mSpecies = species;
        mColor = color;
        mSize = size;
    }
    public void eat() {
        // eat some algae
    };
        
    public void eat(Fish fishToEat) {
        // eat another fish!
    };
        
    public void sleep() {
        // sleep
    };
        
    public void makeBabyFish(Fish fishSpouse, int numBabies) {
        // Make numBabies worth of baby fish with Fish spouse
    };
}
</code></pre>

Classes can be organized into hierarchies, where a derived class (or subclass) includes all the features of its parent class (or superclass), but refines and adds to them to define a more specific object using the extends keyword. This is called inheritance.

类可以被组织进不同的层级，衍生类（或者子类）包含父类（或者超类）的所有特性，但是定义一个更加高效的对象通过使用改善和添加扩展字来实现。这就是继承。

For example, the Fish class might have two subclasses: FreshwaterFish and SaltwaterFish. These subclasses would have all the features of the Fish class, but could further customize the objects through new attributes and behaviors or modified behaviors from the parent class Fish. For example, the FreshwaterFish class might include information about the type of freshwater environment lived in (e.g. river, lake, pond, or puddle). Similarly, the SaltwaterFish class might customize the `makeBabyFish()` method such that the fish eats its mate after breeding (as defined in the super class) by using the override mechanism, like this:

<pre><code>
public class SaltwaterFish extends Fish
{
    @Override
    public void makeBabyFish(Fish fishSpouse, int numBabies) {
        // call parent method
        super.makeBabyFish(fishSpouse, numBabies);
        // eat mate
        eat(fishSpouse);
    }
}
</code></pre>

比如说，Fish类有两个子类：FreshwaterFish和SaltwaterFish。这两个子类有Fish类的所有特性，但是可以通过添加新的属性和行为或者重新定义Fish父类的行为来自定义对象。例如，FreshwaterFish类中包含淡水环境的信息（例如，河流，湖泊，池塘或者水潭）。与此相似的是，SaltwaterFish类自定义makeBabyFish（）方法，即鱼儿在繁殖后吃了同伴，通过使用override机制，如下述代码：
<pre><code>
public class SaltwaterFish extends Fish
{
    @Override
    public void makeBabyFish(Fish fishSpouse, int numBabies) {
        // call parent method
        super.makeBabyFish(fishSpouse, numBabies);
        // eat mate
        eat(fishSpouse);
    }
}
</code></pre>

##通过接口组织对象的行为（Organizing Object Behavior with Interfaces）

In Java, you can organize object behaviors in what are called interfaces. While a class defines an object, an interface defines some behavior that can be applied to an object. For example, we could define a Swimmer interface that provides a set of methods that are common across all objects that can swim, whether they are fish, otters, or submergible androids. The Swimmer interface might specify four methods: `startSwimming()`, `stopSwimming()`, `dive()`, and `surface()`.
<pre><code>
public interface Swimmer
{
    void startSwimming();
    void stopSwimming();
    void dive(); 
    void surface(); 
}
</code></pre>
A class like Fish could then implement the Swimmer interface (using the implements keyword) and provide implementations of the swimming behavior:
<pre><code>
public class Fish implements Swimmer {
    // Provide implementations of the four methods within the Swimmer interface 
}
</code></pre>

在Java中，你可以通过使用接口来组织对象行为。虽然类定义了一个对象，但是接口定义的一些行为也可以应用于对象。例如，我们可以定义一个名为Swimmer的接口，这个接口提供了一系列的方法来实现所有对象都具有游泳的行为，无论是鱼，水獭还是安卓潜艇。Swimmer接口指定了四个方法：`startSwimming()`, `stopSwimming()`, `dive()`, and `surface()`。
<pre><code>
public interface Swimmer
{
    void startSwimming();
    void stopSwimming();
    void dive(); 
    void surface(); 
}
</code></pre>

像Fish这样的类可以实现Swimmer接口并且提供游泳行为的实现：
<pre><code>
public class Fish implements Swimmer {
    // Provide implementations of the four methods within the Swimmer interface 
}
</code></pre>


##通过包组织类和接口（Organizing Classes and Interfaces with Packages）

Class hierarchies, such as our fish classes, can then be organized into packages. A package is simply a set of classes and interfaces, bundled together. Developers use namespaces to uniquely name packages. For example, we could use com.mamlambo.aquarium or com.ourclient.project.subproject as our package name to keep track of our fish-related classes.

类的层级，比如我们的fish类们，可以使用packages来组织起来。包就是一系列类和接口简单的集合在一起。开发者使用命名空间（namespaces）来命名不同的包。例如，我们可以使用com.mamlambo.aquarium或者com.ourclient.project.subproject来作为包名来追踪我们的与鱼相关的类。

##Wrapping Up

Wow! You’ve just embarked on a crash-course in Java for Android development. We’ve covered a pretty intense amount of material here, so let things settle for a bit before moving on to the next lesson of this tutorial series. In Lesson 2, we switch our focus to the nitty-gritty details of Java syntax.

哇!你刚刚完成了Java for Android development的课程。我们讨论了很多重要的知识，因此在进入下一节课程之前先把本课的内容吸收一下。在第二节中，我们将注意力转移到Java语法上来。

You’ve only scratched the surface of Java development for Android development. Check out all the other great tutorials on Mobiletuts+ to dive deeper into Java and Android development. Good luck!

你仅仅是接触了Android开发中需要的Java知识的皮毛。查看Mobiletuts+上其他好的教程来深入学习Java和Android开发。祝你好运！

About the Authors
Mobile developers Lauren Darcey and Shane Conder have coauthored several books on Android development: an in-depth programming book entitled Android Wireless Application Development and Sams TeachYourself Android Application Development in 24 Hours. When not writing, they spend their time developing mobile software at their company and providing consulting services. They can be reached at via email to androidwirelessdev+mt@gmail.com, via their blog at androidbook.blogspot.com, and on Twitter @androidwireless.

关于作者
Mobile developers Lauren Darcey 和 Shane Conder合著了一些有关Android开发的书籍：《深入学习Android无线程序开发》和《Sams24小时自学Android开发》。在写书之前，他们在他们公司做移动软件开发。你可以联系他们通过邮件：androidwirelessdev+mt@gmail.com，通过他们的博客androidbook.blogspot.com,或者通过Twitter @androidwireless.

---


原文地址：[http://code.tutsplus.com/tutorials/java-tutorial--mobile-2604](http://code.tutsplus.com/tutorials/java-tutorial--mobile-2604)

译者：[chiahaolu](https://github.com/chiahaolu) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)