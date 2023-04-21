---
layout: post
title: "How to open a file in Java"
description: "Open a file in Java with InputStream"
author: tom
image: assets/images/1.jpg
tags: [java, InputStream]
comments: true
---

Open a file in Java with InputStream.

Given a filename such as this, in the `src/main/resources` directory:

	String filename = "avengers.txt"

The steps to open this file are:

* Create an `InputStream` to get this file from the `resources` directory
* Create an `InputStreamReader` to read the stream
* Create a `BufferedReader` to read the file
* Then you can process the data like adding each line to an `ArrayList`

Note:

* Use the `InputStream` inside a try/catch with `IOException`
* Assert that the InputStream instance is not null.

In this example, `DigitFileReader` is the current class name:

	List<String> textFileData = new ArrayList<>();

	try (InputStream inputStream = DigitFileReader.class.getClassLoader().getResourceAsStream(filename)) {
		assert inputStream != null;
		InputStreamReader streamReader = new InputStreamReader(inputStream);
		BufferedReader reader = new BufferedReader(streamReader);
	
		String line;
		while ((line = reader.readLine()) != null) {
			textFileData.add(line);
		}
	} catch (IOException e) {
		throw new RuntimeException(e);
	}