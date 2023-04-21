---
layout: post
title: "IntelliJ Cheat Sheet"
description: "A list of IntelliJ tips and tricks"
author: tom
image: assets/images/1.jpg
tags: [intellij, java]
comments: true
---

A list of IntelliJ tips and tricks.

## Installing IntelliJ on Ubuntu

Use snap.

    sudo snap install intellij-idea-ultimate --classic

Pycharm can be installed in the same way.

    sudo snap install pycharm-community --classic

## Install Java versions

In IntelliJ, go to:

1. File
2. New Projects Setup
3. Structure
4. Platform Settings > SDK
5. Use the plus sign

Or install using the command line (for Ubuntu):

    sudo apt install openjdk-17-jdk openjdk-17-jre

## IntelliJ and markdown

I can't remember if this plugin was installed by default. [Markdown plugin](https://plugins.jetbrains.com/plugin/7793-markdown).

Otherwise, install:

1. In IntelliJ go to marketplace
2. `Ctrl + Alt + S` to open Settings
3. Go to Plugins
4. Search `Markdown` and check it's the same as the Markdown plugin above.
5. Install

**Customize Markdown**

1. Open Settings `Ctrl + Alt + S`
2. Search for `markdown` and it should open in `Editor/Color Scheme/Markdown`

These are my settings:

* Bold: Go to `Style/Bold Text`, on the right check `Bold` and `Foreground`. Change Foreground to `F07178`.
* Italic: Go to `Style/Italic Text`, same color for Foreground.
* Header: Go to `Header/1st level header`, set to `Bold`, also 2nd level header. Set `Header marker` same color as level headers.
* Horizontal rule: Set `Background` to an orange color.

## Soft Wrap Text

Use the shortcut `Shift + Shift` to open search. Type `Soft wrap` and select the result to have wrap text in the current opened file.

Alternatively open settings `Ctrl + Alt + S`, go to `Editor/General`, select `Soft wrap these files` and check that `.md` is included.

## Template shortcuts

Type `sout` and `TAB` to autocomplete `System.out.println()`

Other shortcuts: `main`, `foreach`, `ifn` (if null statement), `inst` (is object instance of), `iter` (iterate iterable or array).

Use `Ctrl + J` to see other Java shortcuts. They are also under `Settings` then
`Editor` then `Live Templates`.

## Create a new project. HelloWorld example
 
* File > New Project
* Enter a name without spaces
* Uncheck to add sample code
* Right click on `src` > New Class
* Follow the standard `com.domain.package.Class` such as `com.example.helloworld.HelloWorld`
* Inside the created class, type `main` and autocomplete the rest.
* Inside `main` brackets use `Shift + Enter` to create a new line
* Type `sout` and tab to autocomplete.
* Inside the `println` type `"Hello world"`
* Run to build/compile (`javac` compiles to JVM bytecode to `out` directory) then JVM runs the bytecode.

### Package the application in JAR
 
As seen on IntelliJ docs [here])(https://www.jetbrains.com/help/idea/creating-and-running-your-first-java-application.html#package). A JAR (Java archive) `artifact` is created like this:

**Create an artifact configuration**
* File > Project Structure > Artifacts > + > JAR > From modules with dependencies
* Main Class > open folder > select HelloWorld > OK > OK

**Build the JAR artifact**
* Build > Build artifacts > HelloWorld.jar > Build

**Run the JAR artifact**
* Ctrl+Shift+A > Edit configurations > Run/Debug configurations > + > JAR application
* Name it: HelloWorldJar
* In Path to JAR > click the folder > find the JAR file in out/artifacts/
* In Before launch > + > Build Artifacts > select HelloWorld:jar

**Execute run configuration**
* In the toolbar (top right), the dropdown changed from 'Current File' to 'HelloWorldJar'
* Click on Run

It should output similar to:

    /usr/lib/jvm/java-1.17.0-openjdk-amd64/bin/java -Dfile.encoding=UTF-8 -jar 
    /home/IdeaProjects/HelloWorld/out/artifacts/HelloWorld_jar/HelloWorld.jar
    
    Hello World
    
    Process finished with exit code 0

## Add Java SDK API docs to project

* File > Project Structure > Project > SDK > Edit > Documentation path
* Click the `+` sign with the earth globe to add URL
* Leave the default if applicable to the corresponding SDK version
* OK
* Browse over a word for example `System` in `System.out.println`
* A popup shows the documentation for that word.

## Move Code Around

Use `Ctrl + Shift + arrow up/down`

## Generate toString()

As seen in IntelliJ docs
[here](https://www.jetbrains.com/help/idea/generating-code.html#generate-tostring).
The `toString()` method of the Java superclass `java.lang.Object` returns the
string representation of the object.

* By default it returns the name of the class and the object hash code
* To override it and return the values of the object fields. Use the menu
    `Code`, then `Generate`, then `toString()`.

Example Project:

    src
      com.example.helloworld
        model
          Person
        HelloWorld

Example `HelloWorld.java`:

    package com.example.helloworld;

    import com.example.helloworld.model.Person;

    public class HelloWorld {
        public static void main(String[] args) {
            Person person = new Person("Homer", "Simpson", 50);
            System.out.println(person);
        }
    }

It outputs:

    com.example.helloworld.model.Person@5acf9800

Example `Person.java` with override `toString()`:

    ...
    public Person(String firstName, String lastName, int age) {
        this.firstName = firstName;
        this.lastName = lastName;
        this.age = age;
    }

    @Override
    public String toString() {
        return "Person{" +
                "firstName='" + firstName + '\'' +
                ", lastName='" + lastName + '\'' +
                ", age=" + age +
                '}';
    }

Running `HelloWorld.java` again outputs `person` with the override:

    Person{firstName='Homer', lastName='Simpson', age=50}


## Extract Method

From the `public class HelloWorld` select the lines inside `main`.

* Menu > Refactor > Refactor This > Extract Method
* Rename the method and press Enter

It changed from:

    public static void main(String[] args) {
        Person myPerson = new Person("Homer", "Simpson", 50);
        System.out.println(myPerson);
    }

To:

    public static void main(String[] args) {
        System.out.println("Hello World");

        myPerson();
    }

    private static void myPerson() {
        Person myPerson = new Person("Homer", "Simpson", 50);
        System.out.println(myPerson);
    }

## Debugging with break points

Expanding on my post on [Debug with IntelliJ](../debug-with-intellij/).

Using this example:

	private static void myPerson() {
        Person myPerson = new Person("Homer", "Simpson", 50);
        System.out.println(myPerson);

        for (int i = 0; i < 100; i++) {
            System.out.println("The number i is: " + i);
        }
    }

In IntelliJ, click on the left margin from the statement `Person myPerson`, to show a red dot.

Click on the bug icon to Debug.

**Step over**
On the Debugger window, click on `step over` to move to the line
`System.out.println(myPerson)`. Click on `step over` again to move to the `for`
loop.

Stop the debugger and debug again.

**Step into**
Click on `step into`. It moves from `Person myPerson` to the `Person.java` class in the constructor
definition. Use `step over` to move through the statements in the constructor.
Then it moves back to statement creating the object `Person myPerson`.

Click on `step over` moves again to the next statement
`System.out.println(myPerson)`.

**Step over (for loop)**
If you debug the for loop, there is no difference between `step over` and `step
into` as it runs over each loop and it displays the result of each iteration on
the right side of the statement.

    for (int i = 0; i < 100; i++) {  i: 2
        people.add(new Person("Homer " + i, "Simpson", 50)); i: 2
        }

You can also add a condition to the break point.

* Right click break point
* In the Contition field enter `i == 9`
* Then `Resume Program`
* The for loop will go through 9 loops
* Double click on `people` will show a popup `ArrayList size = 9` with a `+`
    sign that will show the list of all 9 created objects.
















































