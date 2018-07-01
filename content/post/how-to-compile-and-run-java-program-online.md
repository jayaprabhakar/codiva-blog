---
title: "How to Compile and Run Java Program Online"
pageTitle: "How to Compile and Run Java Program Online in 2 minutes | Codiva Online Compiler Blog"

date: 2017-12-10T18:43:58-08:00
draft: false
tags: []
---

If you are learning Java, or practicing for coding interviews, you need a fast way to run many short programs. 


Codiva Online IDE is best suited for your usecase. No installation needed. Works on Chromebooks and even on mobile.
<!--more-->

If you are learning Java, there are 3 options.

1. [Install JDK and use text editor]({{<ref "#install-jdk-and-use-text-editor" >}}) 
1. [Install an IDE like Eclipse, IntelliJ]({{<ref "#install-an-ide-like-eclipse-intellij">}})
1. [Use Online Compilers like Codiva.]({{<ref "#use-online-compilers-like-codiva">}})

Each of these have different pros and cons.

## Use Online Compilers like Codiva.
This is the easiest option if you are getting started.

1. Open <https://www.codiva.io> on your browser.
1. Create a new project, by giving a name "my first project", and click 'Create' button
1. Define your new Java file, by giving a name, say 'HelloWorld' on the new file form.
1. Codiva will autocreate a basic file, to get started.
	```java
	class HelloWorld {
	  public static void main(String[] args) {    
	    System.out.println("Hello Codiva");
	  }
	}
	```

1. Start editing. 
  * Compilation happens in the background. 
  * You can see the compilation errors in the editor itself
1. Once done, and no compilation errors, click 'Run'


#### Advantages:

1. You can start coding in minutes. No Downloads. No installation. No path configuration.
1. No context switching time between text editor and the console. Everything in one place.
1. Very simple development environment. Just suited for the writing simple programs for learning.
1. Works on any browser on any device. Even on low end laptops, Chromebooks or even mobile phones.
1. Very easy to share code with friends or stack overflow to ask questions.
1. Easy to embed in your website or blog.

#### Disadvantages:

1. Codiva needs internet connection to work properly. Don't worry, if you can download a facebook profile picture, codiva will work for you.
1. Limited set of features. Codiva achieves its simplicity by cutting down some features. So, once you want to use advanced programs like Swing or Servlets or Android, you might start using another option.


## Install JDK and use text editor

This is the basic approach. 

1. [Download Java 9](https://docs.oracle.com/javase/9/install/overview-jdk-9-and-jre-9-installation.htm#JSJIG-GUID-8677A77F-231A-40F7-98B9-1FD0B48C346A) and install. You may have to follow specific instruction depending on the Operation System.
1. Use any text editor to create a file, say `HelloWorld.java`
1. On the terminal, go to that directory, and run

    ```bash
    javac HelloWorld.java
    ```
1. If there are no compilation errors, this will create a file `HelloWorld.class` (or whatever class that was in the file)
1. Then run, 
    ```bash
    java HelloWorld
    ```


#### Advantages:

1. Very basic tools.
1. Complete flexibility, and pretty much any kind of development can be done with just the JDK and a text editor.

#### Disadvantages:

1. Slow development because of context switching. If you are a beginner, most of the code you write is going to have compilation errors. Now, you have to switch between console and text editor frequently. You have to remember the line number and the error text, for each error and go to the editor to fix the code, and repeat the process.
1. Reading through the installation steps is a turn down. 

## Install an IDE like Eclipse, IntelliJ

When you have a decent powered laptop or a desktop, installing an IDE is not a bad option. If you don't have any preference, just use Eclipse.

1. Install latest version of [eclipse](http://www.eclipse.org/downloads/packages/eclipse-ide-java-developers/oxygen2)
1. Once installed, click on the icon to start Eclipse.
1. Create a new workspace
1. Create a new Java project
1. Create a new Java class, name it say 'HelloWorld'. This will create a HelloWorld.java with a simple boilerplate. Add necessary code.
1. You will see that, the compilation happens as you type. When you see compilation errors, there will be a red x on the left side.
1. If there are no errors, just click 'Run'


#### Advantages:

1. Very powerful. You can develop very fast.
1. No context switching time between text editor and the console. Everything in one place.
1. Very versatile development environment. Only minor changes will be required when you start developing any complex applications.

#### Disadvantages:

1. System requirements are huge. If you have a low end Windows laptop, it won't work well.
1. No way to work on Chromebooks or mobile phones.
1. You should have installation permissions on your device. Many companies disallow installation on the work computers.
1. Each time to start and stop the IDE itself will take a couple of minutes.



Happy Coding!
