---
layout: post
title: "Currency Values in Java"
description: "How to manage currency values in Java"
author: tom
image: assets/images/1.jpg
tags: [java, java11, jshell, BigDecimal]
---

How to manage currency values in Java 11.

I am on a quest to improve my knowledge of Java and also because it is required for the class I am taking called Software Engineering where the final project is building an Android app. I used Java last year to learn Hadoop but still found it complicated in comparison with Python.

Quick note to self:

`String` is an object. Use lower case for primitive types like `byte`, `int`, `short`, `double`, `boolean`.

Using `jshell` to learn Java.

Following the online course `Java 11 Essential Training` in Linkedin Learning I learned that managing currency values in Java is not as easy as I thought.

To make life even more enjoyable I started looking into Ethereum. Maybe it's not too late for me. I was looking at the value of 1 USD to Ether `0.00037`

I was trying to calculate 3 USD times the value of Ether but it showed too many decimals. Should I round the value? Or use the print format to display the precision? Or is that the wrong way of managing currency?

    jshell> double dollarToEther = 0.00037;
    dollarToEther ==> 3.7E-4

    jshell> dollarToEther * 3;
    $18 ==> 0.0011099999999999999

The course Java11 Essential Training recommends to use `BigDecimal` to manage currencies like this:

1. Convert the value to String or pass the value as a String
2. Create a BigDecimal instance
3. Do calculations with this object
4. Convert the result to a primitive

## Convert the value to String

Convert the double value to String:

    > var etherString = Double.toString(dollarToEther);
    etherString ==> "3.7E-4"

## Create a BigDecimal instance

Convert the String to a BigDecimal object:

    > var etherBigDecimal = new BigDecimal(etherString);
    etherBigDecimal ==> 0.00037

## Calculations with BigDecimal

This is the part that I find the most annoying.

This doesn't work:

    > etherBigDecimal.multiply(3);
    |  Error:
    |  incompatible types: int cannot be converted to java.math.BigDecimal
    |  etherBigDecimal.multiply(3);
    | 

The method follows this syntax:

    BigDecimal.multiply(BigDecimal multiplicand)

The parameter in the method also must be `BigDecimal`.

Create the multiplicand. Let's say this is the USD value as a string.

    > var usdBigDecimal = new BigDecimal("3");
    usdBigDecimal ==> 3

    > var usdToEtherBigDecimal = etherBigDecimal.multiply(usdBigDecimal);
    usdToEtherBigDecimal ==> 0.00111

## Convert BigDecimal result to a primitive

Conver from BigDecimal to double:

    > var usdToEther = usdToEtherBigDecimal.doubleValue();


## Other BigDecimal methods

More about BigDecimal in the Oracle page [here](https://docs.oracle.com/en/java/javase/11/docs/api/java.base/java/math/BigDecimal.html)