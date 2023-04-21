---
layout: post
title: "Install Java on Fedora with OpenJDK"
redirect_from:
  - /install-java-fedora-openjdk-android.html
categories: [Linux]
image: assets/images/11.jpg
tags: [fedora, java, openjdk, jshell]
---

How to install Java on Fedora with OpenJDK.

## Installing OpenJDK 11 on Linux

Download OpenJDK 11 from [here](https://jdk.java.net/java-se-ri/11).

    $ cd /opt/
    $ sudo wget https://download.java.net/openjdk/jdk11/ri/openjdk-11+28_linux-x64_bin.tar.gz
    $ sudo tar -xvf openjdk-11+28_linux-x64_bin.tar.gz

A new folder is created:

    $ ls
    jdk-11

Remove the source file:

    $ sudo rm openjdk-11+28_linux-x64_bin.tar.gz

Check the Java version

    $ /opt/jdk-11/bin/java -version

Output:

    openjdk 11 2018-09-25
    OpenJDK Runtime Environment 18.9 (build 11+28)
    OpenJDK 64-Bit Server VM 18.9 (build 11+28, mixed mode)

Setup environment by editing your `.bashrc`. Add these to the end of the file.

    # openjdk version "13.0.1"
    export JAVA_HOME=/opt/jdk-11/
    export PATH=/opt/jdk-11/bin:$PATH

The source the file:

    $ source ~/.bashrc

Test the Java version:

    $ java -version
    openjdk 11 2018-09-25
    OpenJDK Runtime Environment 18.9 (build 11+28)
    OpenJDK 64-Bit Server VM 18.9 (build 11+28, mixed mode)

    $ which java
    /opt/jdk-11/bin/java


## Alternative: Installing Java on Linux with SDKMAN

Source: [SDKMAN website](https://sdkman.io/)

Just run this:

    $ curl -s "https://get.sdkman.io" | bash

List available packages:

    $ sdk list

Output:

    Java (11.0.10.hs-adpt) https://zulu.org/

    Java Platform, Standard Edition (or Java SE) is a widely used platform for
    development and deployment of portable code for desktop and server environments.
    Java SE uses the object-oriented Java programming language. It is part of the
    Java software-platform family. Java SE defines a wide range of general-purpose
    APIs – such as Java APIs for the Java Class Library – and also includes the Java
    Language Specification and the Java Virtual Machine Specification.

Install:

    $ sdk install java


## JShell on the Command Line

Open:

    $ jshell
    |  Welcome to JShell -- Version 13.0.1
    |  For an introduction type: /help intro

    jshell>

Exit with:

    jshell> /exit

The next few lines follow [this](http://cr.openjdk.java.net/~rfield/tutorial/JShellTutorial.html) tutorial.

Start `jshell` in verbose mode to provide feedback about what you typed.

    $ jshell -v

    jshell> int x = 45;
	x ==> 45
	|  created variable x : int

	jshell> String twice(String s) {
	   ...>     return s + s;
	   ...> }
	|  created method twice(String)

	jshell> twice("Ocean");
	$3 ==> "OceanOcean"
	|  created scratch variable $3 : String

	jshell> String x;
	x ==> null
	|  replaced variable x : String
	|    update overwrote variable x : int

Use `tab` to autocomplete or show available methods:

    jshell> System.c
	class  clearProperty(  console()  currentTimeMillis()

Use `tab` before entering a method's parameter:

    jshell> twice(
	$3       twice(   x        

	Signatures:
	String twice(String s)

	<press tab again to see documentation>

	String twice(String s)
	<no documentation found>

	<press tab again to see all possible completions;
	total possible completions: 542>

Use `vim` as an editor within `JShell`:

    jshell> /set editor vim
	|  Editor set to: vim

	jshell> /edit

It opens `vim`, which has everything I typed so far. I added a method, then saved, and closed the file:

    |  created method cube(double)

	jshell> cube(2);
	$6 ==> 8.0
	|  created scratch variable $6 : double