---
layout: post
title: "Using Java Streams"
description: "How to use a Java stream"
author: tom
image: assets/images/1.jpg
tags: [java, streams]
comments: true
---

How to use a Java stream.

A Java stream reminds me of Spark SQL when using the dot notation. In IntelliJ, you can see the type of each nested method.

For example in this `Then` step from Cucumber

	@Then("A list of digits is extracted")
	public void a_list_of_digits_is_extracted(DataTable dataTable) {
		List<Integer> expectedDigits = dataTable.asLists()
			.get(0)
			.stream()
			.map(Integer::parseInt)
			.toList();

IntelliJ shows the type of the variable on the right side of each method

* `dataTable.asLists()`...`List<List<String>>`
* `.get(0)`...`List<String>`
* `.stream()`...`Stream<String>`
* `.map(Integer::parseInt)`...`Stream<Integer>`

Use a Java stream to filter or change/map the data.

	someArrayOfIntegerNumbers.stream()
		.filter(OddNumber::isOdd)
		.toList();

	someArrayOfStringNumbers.stream()
		.map(Integer::parseInt)
		.toList();
