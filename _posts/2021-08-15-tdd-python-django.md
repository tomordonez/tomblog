---
layout: post
title: "TDD with Python and Django"
description: "Learning TDD with Python and Django"
author: tom
image: assets/images/1.jpg
tags: [tdd, python, django, unittest, selenium]
comments: true
published: false
---

Learning TDD with Python and Django.

This tutorial is based on the book *Test-Driven Development with Python by Harry Percival 2nd ed*. It is not a summary of the book but rather trying to replicate the application developed in the book with my current environment.

## Prerequisites

**Book Environment**

* Python 3.4
* Django 1.11
* Selenium 3
* geckodriver 0.17.0

**My Environment Sep 2021**

* Python 3.9.6
* Django 3.2.5
* Selenium 3.141.0
* geckodriver 0.29.1
* Linux Fedora 34 GNOME 40
* GCP

## Setup my environment

	$ conda create --name tdd
	$ conda activate tdd
	$ conda install django
	$ conda install selenium

Installing Django and output.

	The following packages will be downloaded:

	package                    |   build
	---------------------------|-----------------
	asgiref-3.4.1              |   pyhd3eb1b0_0    25 KB
	ca-certificates-2021.7.5   |     h06a4308_1   113 KB
	django-3.2.5               |   pyhd3eb1b0_0   3.1 MB
	krb5-1.19.2                |     hac12032_0   1.2 MB
	libpq-12.2                 |     h553bfba_1   2.1 MB
	pip-21.2.3                 | py36h06a4308_0   1.8 MB
	psycopg2-2.8.6             | py39h3c74f83_1   164 KB
	python-3.9.6               |     h12debd9_0  18.4 MB
	sqlparse-0.4.1             |           py_0    35 KB
	typing_extensions-3.10.0.0 |   pyh06a4308_0    27 KB
	tzdata-2021a               |     h5d7bf9c_0   111 KB
	wheel-0.37.0               |   pyhd3eb1b0_0    32 KB
	------------------------------------------------------------
	                               Total:        27.2 MB

Installing Selenium and output.

	The following packages will be downloaded:

	package              |            build
	---------------------|-----------------
	brotlipy-0.7.0       |py39h27cfd23_1003   324 KB
	cffi-1.14.6          |   py39h400218f_0   225 KB
	cryptography-3.4.7   |   py39hd23ed53_0   906 KB
	idna-3.2             |     pyhd3eb1b0_0    48 KB
	pysocks-1.7.1        |   py39h06a4308_0    31 KB
	selenium-3.141.0     |py39h27cfd23_1000   818 KB
	------------------------------------------------
	                           Total:         2.3 MB

## Install geckodriver

Install `geckodriver` or you will get this error when trying to run anything that has selenium webdriver

	No such file or directory: 'geckodriver'

[This](https://stackoverflow.com/questions/40302006/no-such-file-or-directory-geckodriver-for-a-simple-selenium-application-in-py) stackoverflow post has more details about it.

Install using conda as shown in the Anaconda website [here](https://anaconda.org/conda-forge/geckodriver). It provides the HTTP API described by WebDriver to communicate with Gecko browsers like Firefox.

	$ conda install -c conda-forge geckodriver

Output:

	The following packages will be downloaded:

	package                    |     build
	---------------------------|-----------------
	ca-certificates-2021.5.30  |     ha878542_0  136 KB  conda-forge
	certifi-2021.5.30          | py39hf3d152e_0  141 KB  conda-forge
	geckodriver-0.29.1         |     h3146498_0  3.0 MB  conda-forge
	openssl-1.1.1k             |     h7f98852_0  2.1 MB  conda-forge
	python_abi-3.9             |         2_cp39    4 KB  conda-forge
	------------------------------------------------------------
	                              Total:         5.4 MB

## Test with a functional test

Source code from Page 5 of the book.

Create a file `functional_tests.py`

	from selenium import webdriver

	browser = webdriver.Firefox()
	browser.get('http://localhost:8000')

	assert 'Django' in browser.title

Then run:

	$ python functional_tests.py

It opens Firefox with `http://localhost:8000` and `unable to connect`, which is the right behavior. The terminal shows:

	Traceback (most recent call last):
	selenium.common.exceptions.WebDriverException: Message: 
	Reached error page: about:neterror?e=connectionFailure&u=
	http%3A//localhost%3A8000/&c=UTF-8&d=Firefox%20can%E2%80%99t%20
	establish%20a%20connection%20to%20the%20server%20at%20localhost%3A8000.

## Setup Django

Setup Django with this command:

	$ django-admin startproject superlists

Move the current test and initialize git

	$ mv functional_tests superlists
	$ cd superlists
	$ git init
	$ touch .gitignore

Run the server

	$ python manage.py runserver

In another shell run the test again

	$ python functional_tests.py

It opens Firefox on `localhost:8000` and it shows the Django default page `The install worked successfully! Congratulations!`.

However the browser title doesn't have Django. So the terminal shows this:

	Traceback (most recent call last):
		assert 'Django' in browser.title
	AssertionError

Change the `functional_tests.py` just to prove that an assertion error doesn't show up.

	from selenium import webdriver

	browser = webdriver.Firefox()
	browser.get('http://localhost:8000')

	assert 'The install worked successfully' in browser.title

Update `.gitignore`

	db.sqlite3
	geckodriver.log
	superlists/__pycache__/
	*.pyc

Remove this cache:

	$ git rm -r --cached superlists/__pycache__/
	$ git add .
	$ git commit -m "Setup Django"

## Add user stories and a unit test

Added user stories as comments and updated `functional_tests.py` with a unit test. Pages 15, 17.

	from selenium import webdrivers
	import unittest

	class NewVisitorTest(unittest.TestCase):

		def setUp(self):
			self.browser = webdriver.Firefox()
			self.browser.implicitly_wait(3)

		def tearDown(self):
			self.browser.quit()

		def test_start_a_list(self):
			# User goes to the home page
			self.browser.get('http://localhost:8000')

			# User sees the page title and a header mentioning a todo list
			self.assertIn('To Do', self.browser.title)
			self.fail('Finish the test')

			# User enters a task 'Document TDD blog' into a text box
			# When they hit enter, the page updates, and it shows one task
				# 1. Document TDD blog
			# They enter another task 'Review the first 2 chapters'
			# The page updates again and it shows two tasks
				# 1. Document TDD blog
				# 2. Review first 2 chapters
			# The user checks that a unique URL was created for their list
			# They visit that URL to check that it works
			# The user is done

	if __name__ == '__main__':
		unittest.main()

Run the server

	$ python manage.py runserver

In another shell run the test again

	$ python functional_tests.py

Firefox opens and closes in 3 seconds. The terminal shows this output:

	F
	======================================================================
	FAIL: test_start_a_list (__main__.TestTodoVisitor)
	----------------------------------------------------------------------
	Traceback (most recent call last):
	  File "/home/superlists/functional_test.py", line 18, in test_start_a_list
	    self.assertIn('To Do', self.browser.title)
	AssertionError: 'To Do' not found in 'The install worked successfully! Congratulations!'

	----------------------------------------------------------------------
	Ran 1 test in 3.831s

	FAILED (failures=1)

## Unit testing in Django

In the root directory `superlists` create a Django app.

	$ python manage.py startapp lists

Edit a unit test to check it works. Edit `lists/tests.py`. As seen on page 24 of the book.

	from django.tests import TestCase

	class SmokeTest(TestCase):

		def test_bad_maths(self):
			self.assertEqual(1 + 1, 3)

Run the test with:

	$ python manage.py test

Output is:

	Creating test database for alias 'default'...
	System check identified no issues (0 silenced).
	F
	===============================================
	FAIL: test_bad_maths (lists.tests.SmokeTest)
	Traceback (most recent call last):
	  File "superlists/lists/tests.py", line 8, in test_bad_maths
	    self.assertEqual(1 + 1, 3)
	AssertionError: 2 != 3

## Unit test that the root url resolves to the home page

Edit the test above.

	from django.urls import resolve
	from django.test import TestCase
	from lists.views import home_page

	class HomePageTest(TestCase):

		def test_root_url_resolves_to_home_page_view(self):
			found = resolve('/')
			self.assertEqual(found.func, home_page)

Running this test should fail.

	$ python manage.py test

There isn't a view called `home_page` in the file `lists/views.py`. Output is:

	System check identified no issues (0 silenced).
	E
	=================================================
	ERROR: lists.tests (unittest.loader._FailedTest)
	-------------------------------------------------
	ImportError: Failed to import test module: lists.tests
	Traceback (most recent call last):
	  File "superlists/lists/tests.py", line 3, in <module>
	    from lists.views import home_page
	ImportError: cannot import name 'home_page' from 'lists.views' (superlists/lists/views.py)

Correct this by first editing the routes file `superlists/urls.py` to map URLs to view functions.

This is the code in the book for `urls.py`:

	from django.conf.urls import url
	from django.contrib import admin
	
	urlpatterns = [
		url(r'^admin/', admin.site.urls),
	]

This is the default code in `urls.py`

	from django.contrib import admin
	from django.urls import path

	urlpatterns = [
		path('admin/', admin.site.urls),
	]

The `urls.py` file has commented out instructions on how to use the file:

	superlists URL Configuration

	The `urlpatterns` list routes URLs to views. For more information please see:
		https://docs.djangoproject.com/en/3.2/topics/http/urls/
	Examples:
	Function views
		1. Add an import:  from my_app import views
		2. Add a URL to urlpatterns:  path('', views.home, name='home')
	Class-based views
		1. Add an import:  from other_app.views import Home
		2. Add a URL to urlpatterns:  path('', Home.as_view(), name='home')
	Including another URLconf
		1. Import the include() function: from django.urls import include, path
		2. Add a URL to urlpatterns:  path('blog/', include('blog.urls'))

Modify the `urls.py`, following above instructions

	from django.urls import path
	from lists import views

	urlpatterns = [
		path('', views.home_page, name='home')
	]

Run the test to get an error. There isn't a view yet for `home_page`

	$ python manage.py test

Output:

	File "superlists/superlists/urls.py", line 20, in <module>
	    path('', views.home_page, name='home')
	AttributeError: module 'lists.views' has no attribute 'home_page'

Start creating the view for `home_page` in `lists/views.py`:

	from django.shortcuts import render

	def home_page():
		pass

Run the test

	$ python manage.py test

Output

	Creating test database for alias 'default'...
	System check identified no issues (0 silenced).
	.
	------------------------------------------------
	Ran 1 test in 0.001s

	OK
	Destroying test database for alias 'default'...

Commit all changes. Then create a test for the result of calling `home_page` in `lists/test.py`. Page 31 of the book.

	from django.urls import resolve
	from django.test import TestCase
	from django.http import HttpRequest

	from lists.view import home_page

	class HomePageTest(TestCase):

		def test_root_url_resolves_to_home_page_view(self):
			found = resolve('/')
			self.assertEqual(found.func, home_page)

		def test_home_page_returns_correct_html(self):
			request = HttpRequest()
			response = home_page(request)
			html = response.content.decode('utf8')
			self.assertTrue(html.startswith('<html>'))
			self.assertIn('<title>To Do List</title>', html)
			self.assertTrue(html.endswith('</html>'))

Running the test should fail.

	$ python manage.py test


***


Add images with:

	![Image Name]({{ site.baseurl }}/assets/images/add_image.jpg)

Add local URL with:

	[Local URL Title](../local-url/)

Add code output:

    <details>
        <summary>Output</summary>
        <pre>

        Long code output here
        </pre>

    </details>


***

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)