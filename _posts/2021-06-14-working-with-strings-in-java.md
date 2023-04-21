---
layout: post
title: "Working with strings in Java"
description: "Working with strings in Java using StringBuilder, regex, Pattern, and Matcher."
author: tom
image: assets/images/1.jpg
tags: [java, stringbuilder, jshell, android, regex]
---

Working with strings in Java using StringBuilder, regex, Pattern, and Matcher.

Open `jshell` and try this:

    jshell> var name = "Homer";
    jshell> name.charAt(0);
    ==> 'H'

Try to replace the letter `H` with the number `1`

    jshell> name.charAt(0) = 1;
    |  Error:
    |  unexpected type
    |    required: variable
    |    found:    value
    |  name.charAt(0) = 1;
    |  ^------------^

It cannot be done because the variable `name` is a string and strings are immutable.

## Using StringBuilder in Java

Now try using the `StringBuilder` class which creates a String object that can be changed.

    jshell> StringBuilder nameSB = new StringBuilder();
    nameSB ==>

    jshell> nameSB.append(name);
    ==> Homer

Now replace the letter `H` with the number `1`. The method wants the start point (inclusive), end point (exclusive), and string to replace.

    jshell> nameSB.replace(0, 1, "1");
    ==> 1omer

    jshell> nameSB
    nameSB ==> 1omer

The variable `nameSB` is a StringBuilder object. Then convert to string:

    jshell> var name = nameSB.toString();
    name ==> "1omer"

## Using StringBuilder in Android

Check if an email address has a valid format.

Let's say that the email address is captured from the android app:

    textEmail = (EditText) findViewById(R.id.emailID);

Then the string is assigned to a variable:

    String email = textEmail.getText().toString();

I know that there are complex regular expressions to match any email but I have seen most emails having this syntax

    [letter/number/dash/underscore/period]@[letter/number].[3 letters]

The regular expression can be of this form:

    [a-z0-9\\-_\\.]{1,}@[a-z0-9]{1,}\\.[a-z]{3}

Assign the regex to a variable:

    var regex = "[a-z0-9\\-_\\.]{1,}@[a-z0-9]{1,}\\.[a-z]{3}";

Then check a few email addresses:

    var email1 = "homer.simpson_1@gmail.com";
    var email2 = "bart@gmailcom";
    var email3 = "Lisa@gmail.com";

We can convert the strings to lowercase and then match the regex:

    email1.toLowerCase().matches(regex);
    ==> true

    email2.toLowerCase().matches(regex);
    ==> false

    email3.toLowerCase().matches(regex);
    ==> true

This one returns false `email2` which had this format `bart@gmailcom` with the period missing.

How can we correct the email address and insert the period before the `com`?

For this exercise let's assume that all email addresses end with three letters like `com`, `org`, `edu` and they have to be preceded by a period. We can iterate through each character of the string until the position where the `.` period is expected. Capture and append that substring into a StringBuilder object. Append the period. Then append the top level domain.

    StringBuilder correctEmailSB = new StringBuilder();

    for (var i = 0; i < email2.length() - 3; i++) {
        correctEmailSB.append(email2.charAt(i));
    }

Check the variable:

    jshell> correctEmailSB;
    correctEmailSB ==> bart@gmail

Append the rest of the string:

    correctEmailSB.append(".com");

## Regular expressions with classes Pattern and Matcher

You can use regular expressions with classes `Pattern` and `Matcher`:

    jshell> var email = "homer.simpson_1@gmail.com";
    jshell> StringBuilder emailSB = new StringBuilder();

Modify the regular expression by adding groups in parentheses:

    jshell> var regex2 = "([a-z0-9\\-_\\.]{1,})(@)([a-z0-9]{1,})(\\.)([a-z]{3})";

Use `Pattern` like this:

    Pattern pattern = Pattern.compile(regex2);

Use `Matcher` on a string:

    Matcher matcher = pattern.matcher(email);

These are available methods for the `matcher` object.

    appendReplacement(
    appendTail(
    end(
    equals(
    find(
    getClass()
    group(
    groupCount()
    hasAnchoringBounds()
    hasTransparentBounds()
    hashCode()
    hitEnd()
    lookingAt()
    matches()
    notify()
    notifyAll()
    pattern()
    region(
    regionEnd()
    regionStart()
    replaceAll(
    replaceFirst(
    requireEnd()
    reset(
    results()
    start(
    toMatchResult()
    toString()
    useAnchoringBounds(
    usePattern(
    useTransparentBounds(
    wait(

Let's use `groupCount()`

    jshell> matcher.groupCount();
    $39 ==> 5

See what the groups are, in `jshell` you can autocomplete and read the help pages by pressing tab as you type:

    jshell> matcher.group(

    Signatures:
    String Matcher.group()
    String Matcher.group(int group)
    String Matcher.group(String name)

    <press tab again to see documentation>

The parameter is to enter a group integer but this won't work:

    jshell> matcher.group(1);

    |  Exception java.lang.IllegalStateException: No match found
    |        at Matcher.group (Matcher.java:645)
    |        at (#43:1)

First you have to find the matches with `matcher.find()`.

You can iterate to find all groups as seen on stackoverflow [here](https://stackoverflow.com/a/17969620/1434022) or assign the matching groups to variables.

First try the method:

    jshell> matcher.find()
    == true

Then get the groups:

    while (matcher.find()) {
        System.out.println(matcher.group(1));
        System.out.println(matcher.group(2));
        System.out.println(matcher.group(3));
        System.out.println(matcher.group(4));
        System.out.println(matcher.group(5));
    }

Output:

    homer.simpson_1
    @
    gmail
    .
    com

This will be different for a string that doesn't match the regex.

    jshell> var email = "bart@gmailcom";
    jshell> Matcher matcher = pattern.matcher(email);
    
    jshell> matcher.groupCount();
    $6 ==> 5

Let's see the groups:

    while (matcher.find()) {
        System.out.println(matcher.group(1));
        System.out.println(matcher.group(2));
        System.out.println(matcher.group(3));
        System.out.println(matcher.group(4));
        System.out.println(matcher.group(5));
    }

No output. Let's try the method:

    jshell> matcher.find();
    ==> false

Maybe the regular expression can be simplified to capture everthing up to `@` and everything after that. Then the check if the substring with the domain and TLD has a period.

    jshell> var regex = "(.*)(@)(.*)"
    jshell> Pattern pattern = Pattern.compile(regex);
    jshell> Matcher matcher = pattern.matcher(email);
    jshell> matcher.find();
    ==> true

    jshell> matcher.groupCount();
    ==> 3

    jshell> matcher.group(1);
    ==> "bart"

    jshell> matcher.group(2);
    ==> "@"

    jshell> matcher.group(3);
    ==> "gmailcom"

The third substring `group(3)` can be validated to add the period. Then all the substrings can be added to the StringBuilder object.