---
layout: post
title: "Cucumber DataTable"
description: "How to use Cucumber DataTable to create mock tests"
author: tom
image: assets/images/1.jpg
tags: [cucumber, datatable, mock test, java]
comments: true
---

How to use Cucumber DataTable to create mock tests.

Previously I wrote about setting up [Cucumber in IntelliJ](../bdd-cucumber-intellij/).

In a feature file you can create a mock test for an input and assert an output.

For example, an application that reads lines from a text, and extracts specific content, for instance, extract all the numbers from the text.

The `.feature` file could be the following:

	Feature: Extract Digits From File
		Scenario: Extract digits from a text file
			Given I have a text file
				| Mash at 152F for 60 mins |
				| Using Pilsner malt at 82% and 7% Vienna malt. |
				| With an ABV of 7% |
			When I extract the digits from the file
			Then A list of digits is extracted
				| 152 | 60 | 82 | 7 | 7 |

In the steps class file, `Given` has:

	List<List<String>> textFileData;

	@Given("I have a text file")
	public void i_have_a_text_file(DataTable dataTable) {
		textFileData = new ArrayList<>(dataTable.asLists());
	}

If you look at the source code for `DataTable`, it creates an object of type `List<List<String>>`. You can convert this object into an `ArrayList`. If you print `textFileData` in the above example, it will print:

	[ [Mash at 152F for 60 mins], [Using Pilsner malt at 82% and 7% Vienna malt.], [With an ABV of 7%] ]

In the steps class file. `Then` has:

	@Then("A list of digits is extracted")
	public void a_list_of_digits_is_extracted(DataTable dataTable) {
		List<Integer> expectedDigits = dataTable.asLists()
			.get(0)
			.stream()
			.map(Integer::parseInt)
			.toList();

The method `asLists()` converts `dataTable` to an object of type `List<List<String>>`. You can do the comparison of objects by printing this:

	System.out.println(dataTable.getClass());
	System.out.println(dataTable.asLists().getClass());

If you print `dataTable.asLists()` in the example above, you will get a list of lists with one element:

	[ [152, 60 , 82 , 7 , 7 ] ]

These aren't of type `int` or `Integer` though. You can extract the first element in this array with `.get(0)`, convert it to a stream `.stream()`, map each string number to an Integer `.map(Integer::parseInt)`, and convert it back to a list `.toList()`.

Printing `expectedDigits` will result in a `List<Integer>`:

	[152, 60 , 82 , 7 , 7 ]
	