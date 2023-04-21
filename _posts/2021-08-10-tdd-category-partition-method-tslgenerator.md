---
layout: post
title: "TDD, Category-Partition Method, and TSLgenerator"
description: "Creating tests from feature specs using TDD, Category-Partition Method, and the TSLgenerator."
author: tom
image: assets/images/1.jpg
tags: [tdd, unittest, functional tests, tslgenerator]
comments: true
---

Creating tests from feature specs using TDD, Category-Partition Method, and the TSLgenerator.

Special thanks to Georgia Tech for teaching me how to use the category-partition method. Professor page [here](https://www.cc.gatech.edu/~orso/)

The paper *The category-partition method for specifying and generating functional tests* [here](https://www.cc.gatech.edu/~harrold/6340/cs6340_fall2009/Readings/ostrandCategoryPartition88.pdf) is a great resource for understanding how to create tests from feature specifications.

Here are some lessons learned from this paper.

## Strategy for creating test cases

1. Partition the input domain of a feature/function being tested. Then select test data from each class of the partition.

2. Identify the functional units. Then the `parameters` (inputs to the functional units). Then the `environment conditions` (characteristics of the system's state when executing a functional unit).

3. Find `categories` that represent each parameter and environment condition by reading the specification, and marking phrases that describe how the functional unit behaves.
	* Categories are based on the info on the specs

4. Partition each category into `choices` including different kinds of values possible for each category.
	* Choices are based on: specs, tester's past experience selecting test cases, knowledge of likely errors
	* Choices can be annotated with constraints to indicate relations with other choices

5. Categories and choices are written into a `test specification` for each functional unit.

6. The test specification is used to build `test frames` which are then used to build test cases.
	* A test frame has a set of choices with each category contributing zero or one choice
	* The total number of test frames is the cross product of the choices in each category.
	* Test frames are produced with a `generator tool`

7. A `test case` is built from a test frame by specifying an element from each choice in the frame


## Example: find

As seen on the same paper, page 679 [here](https://www.cc.gatech.edu/~harrold/6340/cs6340_fall2009/Readings/ostrandCategoryPartition88.pdf).

### Specs

**Command**

	find

**Syntax**

	find <pattern> <file>

**Function**

Use to locate or or more instances of a pattern in the file. Line in the fail that contain the pattern are printed to stdout. A line containing the pattern is written only once, regardless of the number the times the pattern exists in the line.

The pattern is any sequence of characters with length not greater to max length of a line in the file. If the pattern has a blank, the entire pattern must be enclosed in quotes. If the pattern has embedded quotes, escape it with a quote. Such as `"homer "" simpson"`

## Test Specification Language (TSL)

The TSL is the way of implementing the category-partition strategy.

This is the TSL generator tool for the category partition method. Source code [here](https://github.com/alexorso/tslgenerator)

The documentation [here](https://github.com/alexorso/tslgenerator/blob/master/Docs/TSLgenerator-manual.txt) has the following:

**Comments**

The `#` character creates a comment until the next line. You can add at the beginning of the line or inline and it will comment from that point forward.

**Categories**

Use the syntax ending the string with a `:` colon to define a category.

The `Parameters:` and `Environments:` categories are ignored because they are not followed by choices.

**Choices**

Use the syntax ending the string with a `.` period to define a choice.

**Constraints**

Use square brackets `[constraint name]` to define a constraint inline after a choice.

	a choice [single]

There are three types of constraints:

* property lists
* error/single markings
* selector expressions

**Property lists**

* Use the syntax `[property <name1>, <name2>, ...]`
* A choice can have more than one property list
* Not more than 10
* It sets the properties to true

Example

	a choice [if something][property A, B, C]
			 [else][property X, Y]

**Error/Single markings**

* Use the syntax `[error]`, `[single]`
* Only one test frame is generated

**Selector expressions**

Use this syntax, where `error/single` and `property` are optionals.

	[if something][error][property A, B, C]
	[else][property X, Y]

If there is an `[error]` or `[single]`. Then the `[property]` is ignored. In this case `[property A, B, C]` is ignored. And `[property X, Y]` sets to true `X` and `Y` when the expression meets the `[else]`.

**Correct order of syntax**

This creates a test case that can be combined with others

	a choice [property awesome]

This ignores everything after `[single]` and creates one test frame

	a choice [single][if something][property X, Y]

Don't put a `[property]` before a selector or it will be ignored. This only sets `Y` to true. Ignoring `X`.

	a choice [property X][if something][property Y]

Don't put a `[property]` before a single/error or it will be ignored. This ignores `[property]` and creates a single test frame.

	a choice [property X][single]

Using a selector and a single/error. Always put the selector in front.

	a choice [if something][single]

**Logical Operator**

* Order of evaluation is `not > and > or`

Examples:

	[if A]
	[if !A]
	[if A || B]
	[if A && B]

You can also use parenthesis:

	[if (A || B) && C]

## Using TSLgenerator

For a step by step video tutorial from Georgia Tech, go [here](https://www.youtube.com/watch?v=zico2p-Ws2Y)

Clone the tool from Github. Source code [here](https://github.com/alexorso/tslgenerator.git)

Go to the `Binaries` directory and find the one for your environment.

For Linux:

	cd Binaries
	chmod +x TSLgenerator-linux

Then run like this

	./TSLgenerator-linux -c test_spec.txt


## Test Specification with TSL syntax

From the specifications of the `find` example, these are the parameter and environment categories.

**Parameter Categories**

* Pattern size
* Quoting
* Embedded blanks
* Embedded quotes
* File name

**Environment Categories**

* Number of times of pattern in file
* Number of times of pattern on a line

## Building the test specification to generate the test cases

This is the `Test Specification` with `Categories` and `Choices` using the TSL syntax. Save this in a file called `test_spec.txt`

	Parameters:
		Pattern size:
			empty.
			single character.
			many characters.
			longer than any line in the file.

		Quoting:
			pattern is quoted.
			pattern is not quoted.
			pattern is improperly quoted.

		Embedded blanks:
			none.
			one.
			many.

		Embedded quotes:
			none.
			one.
			many.

		File name:
			found.
			not found.
			not entered.

	Environments:
		Number of times of pattern in file:
			none.
			one.
			more than one.

		Number of times of pattern on a line:
		# assume line contains the pattern
			one.
			more than one.

Run `TSLgenerator` to see how many test frames are created:

	./TSLgenerator-linux -c test_spec.txt

Output shows this:

	1944 test frames generated

	Write test frames to test_spec.txt.tsl (y/n)?

Without constraints the generated test frames can be incorrect such as the following example. The pattern is empty, it contains quotes, embedded blanks, found once on a line, but not found in the file. This would be illogical:

	Pattern size: empty
	Quoting: pattern is quoted
	Embedded blanks: many
	Embedded quotes: none
	File name: found
	Number of times of pattern in file: none
	Number of times of pattern on a line: one

## Test specs with constraints (property and selector expressions)

As shown in the TSLgenerator. There are three types of constraints:

* property lists
* error/single markings
* selector expressions

As seen on the same paper, page 681 [here](https://www.cc.gatech.edu/~harrold/6340/cs6340_fall2009/Readings/ostrandCategoryPartition88.pdf).

Define the test specs with properties and select expressions.

	Parameters:
		Pattern size:
			empty. [property Empty]
			single character. [property NotEmpty]
			many characters. [property NotEmpty]
			longer than any line in the file. [property NotEmpty]

		Quoting:
			pattern is quoted. [property Quoted]
			pattern is not quoted. [if NotEmpty]
			pattern is improperly quoted. [if NotEmpty]

		Embedded blanks:
			none. [if NotEmpty]
			one. [if NotEmpty && Quoted]
			many. [if NotEmpty && Quoted]

		Embedded quotes:
			none. [if NotEmpty]
			one. [if NotEmpty]
			many. [if NotEmpty]

		File name:
			found.
			not found.
			not entered.

	Environments:
		Number of times of pattern in file:
			none. [if NotEmpty]
			one. [if NotEmpty][property Match]
			more than one. [if NotEmpty][property Match]

		Number of times of pattern on a line:
		# assume line contains the pattern
			one. [if Match]
			more than one. [if Match]

Run `TSLgenerator` to see how many test frames are created:

	./TSLgenerator-linux -c test_spec.txt

Output shows this:

	678 test frames generated

	Write test frames to test_spec.txt.tsl (y/n)?

## Test specs with constraints (single/error)

These choices should produce an error:

* Pattern size is longer than any line in the file
* Pattern is improperly quoted
* File name not found
* File name not entered

These choices could produce a single test frame, when combining the choices with others, produce reduntant test frames:

* Several embedded quotes
* Number of times of pattern in file is none
* Number of times of pattern in target line is more than one

Modify the test specification with single and error constraints:

	Parameters:
		Pattern size:
			empty. [property Empty]
			single character. [property NotEmpty]
			many characters. [property NotEmpty]
			longer than any line in the file. [error]

		Quoting:
			pattern is quoted. [property Quoted]
			pattern is not quoted. [if NotEmpty]
			pattern is improperly quoted. [error]

		Embedded blanks:
			none. [if NotEmpty]
			one. [if NotEmpty && Quoted]
			many. [if NotEmpty && Quoted]

		Embedded quotes:
			none. [if NotEmpty]
			one. [if NotEmpty]
			many. [if NotEmpty][single]

		File name:
			found.
			not found. [error]
			not entered. [error]

	Environments:
		Number of times of pattern in file:
			none. [if NotEmpty][single]
			one. [if NotEmpty][property Match]
			more than one. [if NotEmpty][property Match]

		Number of times of pattern on a line:
		# assume line contains the pattern
			one. [if Match]
			more than one. [if Match][single]

Run `TSLgenerator` to see how many test frames are created:

	./TSLgenerator-linux -c test_spec.txt

Output shows this:

	40 test frames generated

	Write test frames to test_spec.txt.tsl (y/n)? y

	40 test frames written to test_spec.txt.tsl

## Test frames output

The file `test_spec.txt.tsl` contains 40 test frames. This is a sample of the first 8 test frames:

	Test Case 1  		<error>
	   Pattern size :  longer than any line in the file


	Test Case 2  		<error>
	   Quoting :  pattern is improperly quoted


	Test Case 3  		<single>  (follows [if])
	   Embedded quotes :  many


	Test Case 4  		<error>
	   File name :  not found


	Test Case 5  		<error>
	   File name :  not entered


	Test Case 6  		<single>  (follows [if])
	   Number of times of pattern in file :  none


	Test Case 7  		<single>  (follows [if])
	   Number of times of pattern on a line :  more than one


	Test Case 8  		(Key = 1.1.0.0.1.0.0.)
	   Pattern size                         :  empty
	   Quoting                              :  pattern is quoted
	   Embedded blanks                      :  <n/a>
	   Embedded quotes                      :  <n/a>
	   File name                            :  found
	   Number of times of pattern in file   :  <n/a>
	   Number of times of pattern on a line :  <n/a>

## TDD with Test Frames

The next step is using the test frames to write unit tests.

Using Java:

* Import `junit` packages
* Create a test class
* Setup the before `setUp()` and after `tearDown()` functions.

Create a unit test for each test case.

Let's say we want to create a unit test for this test case

	Test Case 5  		<error>
		   File name :  not entered


With an empty `Main` class

	// Main.java

	import ...
	...

	public class Main {
		public static void main(String[] args) throws IOException {
		}
	}

Given `errStream` is configured. Here is part of the code:

	// Test.java

	import org.junit..
	...

	public class Test {

		private ByteArrayOutputStream errStream;

		@Before
    	public void setUp() throws Exception {
	        errStream = new ByteArrayOutputStream();
	        PrintStream err = new PrintStream(errStream);
	        errOrig = System.err;
	        System.setErr(err);
    	}

    	@After
    	public void tearDown() throws Exception {
	        System.setErr(errOrig);
    	}

	}

The test case should produce an error because there is nothing implemented in `Main`.

	// Test.java

	// Test Case 5
	@Test
	public void findpatternTest5() throws Exception {
		String args[] = {"the pattern"};
		Main.main(args);
		assertEquals("Usage: find <pattern> <file>", errStream.toString());
	}

Then write the code in `Main` to pass the test case when a file name is not given.


[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)