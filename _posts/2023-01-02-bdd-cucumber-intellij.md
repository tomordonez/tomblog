---
layout: post
title: "BDD with Cucumber in IntelliJ"
description: "Setup and run Cucumber for BDD in IntelliJ"
author: tom
image: assets/images/1.jpg
tags: [BDD, testing, IntelliJ, Java]
comments: true
---

Setup Cucumber for BDD in IntelliJ.

## Testing Life Cycle

As seen in this LinkedIn Learning course about Cucumber [here](https://www.linkedin.com/learning/cucumber-essential-training)

1. Define acceptance tests
2. Define features - cover as many scenarios (write BDD tests)
3. Write TDD unit tests

## 3 key components of Cucumber

1. Feature files

Feature, Scenario, Given/When/Then, in business domain language (using Gherkin).

* `Given` a pre-condition
* `When` an action is done
* `Then` it produces a result

Example:

	Feature: Reading an ebook
	  Scenario: View Index Section
		Given: I am on any page of my ebook
		When: I access "Index" section
		Then: I should be navigated to the Index section

2. Step definition files.

In code, write `Given/When/Then` annotations with methods

3. Test runner

It glues feature with step definition files using JUnit.

## Setup Cucumber in IntelliJ

As seen in the Jetbrains doc [here](https://www.jetbrains.com/help/idea/enabling-cucumber-support-in-project.html)

In IntelliJ Ultimate the plugins are enabled by default.

- Settings/Plugins
- Scroll down to see Gherkin, then Cucumber for Java

In IntelliJ Community, go to Settings/Preferences/Plugins/Marketplace, and search/install the following plugins in order:

1. Gherkin
2. Cucumber for Java

## Setup JUnit in IntelliJ

* Use Maven
* Go to `pom.xml`, type `Alt+Insert` to add a dependency.
* Add `org.junit.jupiter:junit-jupiter`.
* Then reload the maven icon.

It should look like this, depending on the version:

	<dependency>
		<groupId>org.junit.jupiter</groupId>
		<artifactId>junit-jupiter</artifactId>
		<version>5.9.0</version>
		<scope>test</scope>
	</dependency>

## Add the Cucumber library to Maven

- Open `pom.xml`
- Do `Alt+Insert`
- Select `Add Dependency`
- Search for Cucumber
- Look for `Cucumber-JVM: Java io.cucumber:cucumber-java`
- Click Add
- Look for `Cucumber-JVM: JUnit io.cucumber:cucumber-junit`
- Click Add

It should look like this, depending on the version:

	<dependency>
		<groupId>io.cucumber</groupId>
		<artifactId>cucumber-java</artifactId>
		<version>7.10.1</version>
		<scope>test</scope>
	</dependency>
	<dependency>
		<groupId>io.cucumber</groupId>
		<artifactId>cucumber-junit</artifactId>
		<version>7.10.1</version>
		<scope>test</scope>
	</dependency>

## Reload Maven

Open the Maven tab (on the right). Click on the reload icon (first icon, hovering says `Reload all Maven projects`)

## Prepare folder structure in `test` directory

In the `test` directory, there should be a green `java` directory, marked as `Test Sources Root`. If it's not green then:

* Right-click the `test` directory.
* Select `Mark Directory As`
* Then `Test Sources Root`

In the `test` directory, create the directory `resources`:

* Right-click the `test` directory.
* A popup shows the option to add a `resources` subdirectory already marked as `Test Resources Root`.
* The icon is a directory with a small red/green diamond and a database-looking symbol.
* If no popup is shown, then:
  * After right-click `test/`
  * Create a directory called `resources`
  * Right-click this new directory
  * Select `Mark Directory As`
  * Then select `Test Resources Root`

## Example Mapping

Clarify/Confirm the acceptance criteria using example mapping. As seen in the Cucumber blog [here](https://cucumber.io/blog/bdd/example-mapping-introduction/) and [this](https://draft.io/example/example-mapping) other website.

For example, a program that creates a list of numbers based on a filter criteria such as odd, even, or prime numbers.

	Story: Create a list of odd numbers
	- Rule: A list of positive integers should keep only odd numbers
		- Example: Input [0, 1, 2, 3, 4, 5] Output [1, 3, 5]
		- Example: Input [5, 5, 2, 0, 3, 1] Output [5, 5, 3, 1]
	
	Story: Create a list of even numbers
	- Rule: A list of positive integers should keep only even numbers
		- Example: Input [0, 1, 2, 3, 4, 5] Output [0, 2, 4]
		- Example: Input [5, 5, 2, 0, 3, 1] Output [2, 0]
	
	Story: Create a list of prime numbers
	- Rule: A list of positive integers should keep only prime numbers
		- Example: Input [0, 1, 2, 3, 4, 5] Output [2, 3, 5]
		- Example: Input [5, 5, 2, 0, 3, 1] Output [5, 5, 2, 3]

## Create feature files

As seen in the Cucumber blog [here](https://cucumber.io/blog/bdd/solving-how-to-organise-feature-files/). Don't create a feature file for each user story. Instead, create feature files by functional areas, following a documentation hierarchy structure. For example:

	features/
		customers/
			registration.feature
			login.feature
		payment/
			pay_on_delivery.feature
			pay_on_collection.feature
		api/
			customer_api.feature
			payment_api.feature

Inside the `resources` directory:

* Create a directory `features`
* Inside `features` create directories for functional areas.
* Inside the functional area, create the `FeatureName.feature` files.
* The `.feature` file has the cucumber icon.
* Right-click/Run the `.feature` file to see Test Results

**Example of a feature file**

* In the corresponding `features` package in the `test/resources/` directory
* Create a features file `CreateNumbersList.feature`
* After adding the content, right-click this file to see the Test Results
* It should produce an output recommending to add `Given/When/Then` methods

Example:

	Feature: Create Numbers List

	  Scenario: Create a list of random positive odd numbers
	    Given I have a list of random positive numbers
	    When I filter by odd numbers
	    Then A list of positive odd numbers is created

## Create step definitions

As seen in the Jetbrains doc [here](https://www.jetbrains.com/help/idea/creating-step-definition.html), steps definitions should be located in a dedicated package.

* In `test/java/`, in the corresponding package created by JUnit (if created)
* Create a package `cucumber` (It should show as `test/java/some-package/cucumber/`)
* Go to the `feature` file and hover over a step (it should be underlined in yellow).
* In the popup menu select `Create step definition`
* Name the file `FeatureNameSteps`, select Java, and specify the location (the `cucumber` package created above).
* Copy the methods from Test Results that have the annotations `@Given`, `@When`, `@Then`.
* Import these classes.

**Example of a steps file**

* In the corresponding package in `test/java/some-package/cucumber/`
* Create the Java class `CreateNumbersListSteps`

Example:

	import com.tom.conditionarray.ConditionArrayList;
	import com.tom.conditionarray.RandomNumberList;
	import io.cucumber.java.en.Given;
	import io.cucumber.java.en.Then;
	import io.cucumber.java.en.When;
	
	import java.util.ArrayList;
	
	public class CreateNumbersListSteps {
	
		ConditionArrayList numberList;
		ArrayList<Integer> filteredNumberList;

		@Given("I have a list of positive numbers")
		public void iHaveAListOfPositiveNumbers() {
			numberList = new ConditionArrayList(RandomNumberList.createList(5, 10, 10));
		}
	
		@When("I filter by odd numbers")
		public void i_filter_by_odd_numbers() {
			filteredNumberList = numberList.filterByPredicate(listElement -> (listElement % 2 == 1));
		}

		@Then("A list of positive odd numbers is created")
		public void a_list_of_positive_odd_numbers_is_created() {
			System.out.println(filteredNumberList);
		}
	}

## Run Cucumber tests with JUnit

* Right-click the package with the step definitions `test/java/some-package/cucumber/`
* Select New, Java Class
* Name the class `FeatureNameTest`
* After adding the content. Right-click to Run

Add the following code to `FeatureNameTest`:

	import io.cucumber.junit.Cucumber;
	import io.cucumber.junit.CucumberOptions;
	import org.junit.runner.RunWith;
	
	@RunWith(Cucumber.class)
	@CucumberOptions(
		features = {"classpath:features/some-package/FeatureName.feature"},
		glue = {"some-package.cucumber"})
	public class RunCucumberTest {
	}

When running this file, if there is a compile error saying that the `features` or `glue` locations are not found, then check the corresponding `classpath` is correct.

## Write the implementation from the steps file

The corresponding implementation is not shown for brevity.

The steps file has this:

	ConditionArrayList numberList;
	ArrayList<Integer> filteredNumberList;

	@Given("I have a list of positive numbers")
	public void iHaveAListOfPositiveNumbers() {
		numberList = new ConditionArrayList(RandomNumberList.createList(5, 10, 10));
	}

The implementation should have:

* A `ConditionArrayList` class that extends `ArrayList<Integer>`
* The constructor should take a `RandomNumberList` with a static method `createList`
* This method should have a `seed`, a `bound` limit, and a `listSize`
* And return an `ArrayList<Integer>`

Then the step has:

	@When("I filter by odd numbers")
	public void i_filter_by_odd_numbers() {
	filteredNumberList = numberList.filterByPredicate(listElement -> (listElement % 2 == 1));
	}

The implementation should have:

* The `ConditionArrayList` class should have a method that takes a predicate argument to filter by odd numbers.

## Write JUNit tests

Write a unit test to verify the implementation produces a list of odd numbers.

    ConditionArrayList numberList;

    @BeforeEach
    void setUp() {
      numberList = new ConditionArrayList(RandomNumberList.createList(5, 10, 10));
    }

	@Test
	void filterByPredicate_OddEvenIntegerList_FilterByOddIntegerList() {
	  ArrayList<Integer> filteredNumberList = numberList.filterByPredicate(listElement -> (listElement % 2 == 1));
	  assertEquals(Arrays.asList(7, 5, 1, 1), filteredNumberList, "The ArrayList should only have odd numbers");
	}