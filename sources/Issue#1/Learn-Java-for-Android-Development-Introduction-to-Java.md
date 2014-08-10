---
layout: post
title: "Introducing Android WebDriver"
date: 2014-08-10 19:10
comments: true
categories: Android Introduction
---

#Learn Java for Android Development: Introduction to Java
In this tutorial series, you’ll become familiar with Java, the programming language used to develop Android applications. Our goal is to prepare those already familiar with one programming language, such as PHP or Objective-C, to become comfortable working with the Java programming language and dive into Android app development. In this tutorial, you’ll get a brief introduction to Java fundamentals, including object oriented programming, inheritance and more. If you’re new to Java, or just looking to brush up on the details, then this is the tutorial series for you!
##Getting Started

As far as prerequisites go, we’re going to assume you understand how to program (perhaps in PHP, or Visual Basic or C++), but that you are unfamiliar with the specifics of programming in the Java language. We aren’t going to teach you to program; we’re going to provide you with clear examples of commonly used Java language constructs and principles, while pointing out some Android-specific tips and tricks.

##What You’ll Need

Technically, you don’t need any tools to complete this tutorial but you will certainly need them to develop Android applications.

To develop Android applications (or any Java applications, for that matter), you need a development environment to write and build applications. Eclipse is a very popular development environment (IDE) for Java and the preferred IDE for Android development. It’s freely available for Windows, Mac, and Linux operating systems.

For complete instructions on how to install Eclipse (including which versions are supported) and the Android SDK, see the [Android developer website](http://developer.android.com/sdk/index.html).

##What is Java?

Android applications are developed using the Java language. As of now, that’s really your only option for native applications. Java is a very popular programming language developed by Sun Microsystems (now owned by Oracle). Developed long after C and C++, Java incorporates many of the powerful features of those powerful languages while addressing some of their drawbacks. Still, programming languages are only as powerful as their libraries. These libraries exist to help developers build applications.

Some of the Java’s important core features are:

<li>It’s easy to learn and understand
<li>It’s designed to be platform-independent and secure, using
virtual machines
<li>It’s object-oriented

Android relies heavily on these Java fundamentals. The Android SDK includes many standard Java libraries (data structure libraries, math libraries, graphics libraries, networking libraries and everything else you could want) as well as special Android libraries that will help you develop awesome Android applications.

##Why is Java Easy to Learn?

Java is easy to learn for a variety of reasons. There’s certainly no shortage of Java resources out there to help you learn the language, including websites, tutorials, books, and classes. Java is one of the most widely discussed, taught, and used programming languages on the planet. It’s used for many different types of programming projects, no matter their scale, from web applications to desktop applications to mobile applications.

If you’re coming from a traditional programming background like C or C++, you’ll find Java syntax quite similar. If you’re not, then take comfort in knowing that you’ve chosen one of the easiest languages to learn. You’ll be up and running in no time at all.

Finally, Java is one of the most human-readable languages out there, by which we mean that a person who knows nothing about programming can often look at some Java code and have at least an inkling what it’s doing. Consider the following example:
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

##Why is Platform Independence Important?

With many programming languages, you need to use a compiler to reduce your code down into machine language that the device can understand. While this is well and good, different devices use different machine languages. This means that you might need to compile your applications for each different device or machine language—in other words, your code isn’t very portable. This is not the case with Java. The Java compilers convert your code from human readable Java source files to something called “bytecode” in the Java world. These are interpreted by a Java Virtual Machine, which operates much like a physical CPU might operate on machine code, to actually execute the compiled code. Although it might seem like this is inefficient, much effort has been put into making this process very fast and efficient. These efforts have paid off in that Java performance in generally second only to C/C++ in common language performance comparisons.

Android applications run in a special virtual machine called the Dalvik VM. While the details of this VM are unimportant to the average developer, it can be helpful to think of the Dalvik VM as a bubble in which your Android application runs, allowing you to not have to worry about whether the device is a Motorola Droid, an HTC Evo, or the latest toaster running Android. You don’t care so long as the device is Dalvik VM friendly—and that’s the device manufacturer’s job to implement, not yours.

##Why is Java Secure?

Let’s take this bubble idea a bit further. Because Java applications run within the bubble that is a virtual machine, they are isolated from the underlying device hardware. Therefore, a virtual machine can encapsulate, contain, and manage code execution in a safe manner compared to languages that operate in machine code directly. The Android platform takes things a step further. Each Android application runs on the (Linux-based) operating system using a different user account and in its own instance of the Dalvik VM. Android applications are closely monitored by the operating system and shut down if they don’t play nice (e.g. use too much processing power, become unresponsive, waste resources, etc.). Therefore, it’s important to develop applications that are stable and responsive. Applications can communicate with one another using well-defined protocols.

##Compiling Your Code

Like many languages, Java is still a compiled language even though it doesn’t compile all the way down to machine code. This means you, the developer, need to compile your Android projects and package them up to deploy onto devices. The Eclipse development environment (used with the Android Development plug-in) makes this pretty painless. In Eclipse, automatic compilation is often turned on by default. This means that every time you save a project file, Eclipse recompiles the changes for your application package. You immediately see compile errors. Eclipse also interprets Java as you type, providing handy code coloring and formatting as well as showing many types of errors as you go. Often, you can click on the error and have Eclipse automatically fix a typo, or add an import statement, or provide a method stub for you, saving lots of typing.

You can still manually compile your code if you so desire. Within Eclipse, you’ll find the Build settings under the project menu. If you have “Build Automatically” turned on, you can still choose the “Clean…” option that will allow you to do full rebuild of all files. If “Build Automatically” is turned off, “Build All” and “Build Project” menu options are enabled. "Build All" means to build all of the projects in the workspace. You can have many projects in an Eclipse workspace.

The build process, for regular Java projects, results in a file with the extension of JAR – Java ARchive. Android applications take JAR files and package them for deployment on devices as Android PacKage files with an extension .apk. These formats not only include your compiled Java code, but also any other resources, such as strings, images, or sound files, that your application requires to run as well as the Application Manifest file, AndroidManifest.xml. The Android Manifest file is a file required by all Android applications, which you use to define configuration details about your app.

##What is an Object Oriented Programming Language?

Okay. Time for a very brief and 20,000 foot view of object oriented programming (OOP). OOP is a programming style or technique that relies upon the definition of data structures called objects. For those new to OOP, an object can be thought of much like a custom data type. For example, you might have a Dog object, which represents the blueprint for a generic dog, with a name, breed, and gender. You could then create different instances of the Dog object to represent specific dogs. Each Dog object must be created by calling its constructor (a method that has the same name as the object itself, and may or may not have parameters for setting initial values). For example, the following Dog objects use a constructor with three parameters (name, breed, gender):

	Dog dog1 = new Dog("Lassie", collie, female);
	Dog dog2 = new Dog("Fifi", poodle, female);
	Dog dog3 = new Dog("Asta", foxterrier, male);
	
So where is this Dog object defined? Well, here we need to begin defining some of the fundamental building blocks of the Java programming language. A class provides a definition for an object. Therefore, there is a Dog class somewhere—either defined by you or in some library somewhere. Generally speaking, a class will be defined in its own file, with the filename matching the class name (e.g. Dog.java). There are exceptions to this rule, such as classes defined within other classes (when a class is declared within a class, it is generally defined for use within the parent class only as a helper class, and referred to as an inner class).

When you want to reference an object from within another class, you need to include an import statement in the top of your class file, much like you would use a #include statement in a compiled language like C.

A class typically describes the data and behavior of an object. The behavior is defined using class methods. Method is the common term for a subroutine in an OOP language. Many common object classes are defined in shared class libraries like software development kits (SDKs), whereas others are defined by you, the developer, for your own purposes. Software is then built up by using and manipulating object instances in different ways.

Please realize this is a very generalized definition of OOP. There are entire books written on this subject. If you’d like to know more about OOP, here are a few resources you might want to check out:

<li>Wikipedia has a [nice overview of OOP](http://en.wikipedia.org/wiki/Object-oriented_programming)
<li>[Sun Java Tutorials on Java](http://www.oracle.com/technetwork/java/index.html)
<li>[The Java Tutorials at Oracle](http://docs.oracle.com/javase/tutorial/java/concepts/)

Note: We use a lot of different terminology in this tutorial. There are multiple ways to refer to a given concept (e.g. superclass vs. parent class), which is confusing to those new to object oriented programming. Different developers use different terms, and so we have tried to mention synonyms where appropriate. Deciding which terms you will use is a personal choice.

##Understanding Inheritance

Here is another important Java concept you’ll run into a lot: inheritance. Simply put, inheritance means that Java classes (and therefore objects) can be organized into hierarchies with lower, more specific, classes in the hierarchy inheriting behavior and traits from higher, more generic, classes.

This concept is best illustrated by example. Let’s pretend we are developing a Java application to simulate an aquarium. This aquarium has some fish in it. Therefore, we might define a class to represent a fish. This class, called Fish, could include some data fields (also called attributes, or class member variables) to describe a fish object: species, color and size; as well as some of its behavior in the form of methods (also called subroutines, or functions in procedural languages), like `eat()`, `sleep()`, and `makeBabyFish()`.

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
Classes can be organized into hierarchies, where a derived class (or subclass) includes all the features of its parent class (or superclass), but refines and adds to them to define a more specific object using the extends keyword. This is called inheritance.

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
##Organizing Object Behavior with Interfaces

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
##Organizing Classes and Interfaces with Packages

Class hierarchies, such as our fish classes, can then be organized into packages. A package is simply a set of classes and interfaces, bundled together. Developers use namespaces to uniquely name packages. For example, we could use com.mamlambo.aquarium or com.ourclient.project.subproject as our package name to keep track of our fish-related classes.

##Wrapping Up

Wow! You’ve just embarked on a crash-course in Java for Android development. We’ve covered a pretty intense amount of material here, so let things settle for a bit before moving on to the next lesson of this tutorial series. In Lesson 2, we switch our focus to the nitty-gritty details of Java syntax.

You’ve only scratched the surface of Java development for Android development. Check out all the other great tutorials on Mobiletuts+ to dive deeper into Java and Android development. Good luck!

About the Authors
Mobile developers Lauren Darcey and Shane Conder have coauthored several books on Android development: an in-depth programming book entitled Android Wireless Application Development and Sams TeachYourself Android Application Development in 24 Hours. When not writing, they spend their time developing mobile software at their company and providing consulting services. They can be reached at via email to androidwirelessdev+mt@gmail.com, via their blog at androidbook.blogspot.com, and on Twitter @androidwireless.

---


原文地址：[http://code.tutsplus.com/tutorials/java-tutorial--mobile-2604](http://code.tutsplus.com/tutorials/java-tutorial--mobile-2604)

译者：[译者ID](https://github.com/译者ID) 校对：[校对者ID](https://github.com/校对者ID)

本文由[AWCNTT](https://github.com/AWCNTT) 原创翻译，[AndroidWeekly中国](http://www.androidweekly.cn/) 荣誉推出

版权声明：原创译文欢迎自由转载-非商用-非衍生-保持署名 | [Creative Commons BY-NC-ND 3.0](http://creativecommons.org/licenses/by-nc-nd/3.0/deed.zh)