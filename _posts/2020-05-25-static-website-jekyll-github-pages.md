---
layout: post
title: "Static Website with Jekyll and Github Pages"
description: "Build a free website with Jekyll and Github Pages with custom domain."
categories: [Code]
image: assets/images/5.jpg
tags: [jekyll, github pages]
---

This is a step by step tutorial to build a Github Pages site with Jekyll. Github Pages are freely hosted on Github and you can use Jekyll, a static website generator to customize your Github Pages site.

## Summary

* Install Ruby
* Jekyll and Github Pages
* Customizing the theme
* Using SEO
* Creating blog posts
* Github Pages custom domain
* Change your DNS name server
* Deploying custom plugins
* Google Analytics
* Pagination
* Jekyll without Plugins
* Reading Time
* Search Box
* Text Expand/Collapse
* Open external site in new window

## Install Ruby

Install RVM as seen [here](http://rvm.io/)

	$ gpg2 --recv-keys 409B6B1796C275462A1703113804BB82D39DC0E3 7D2BAF1CF37B13E2069D6956105BD0E739499BDB
	$ \curl -sSL https://get.rvm.io | bash -s stable

If you are on Linux/Gnome. As seen [here](https://rvm.io/integration/gnome-terminal). Go to the Terminal preferences and find a checkbox that says `Run command as a login shell`. This must be checked.

Close the terminal and open it again.

    $ source ~/.rvm/scripts/rvm
    $ type rvm | head -n 1

This should say `rvm is a function`

Restart the shell and Install Ruby. Check the current stable version in the official Ruby docs. Below version is outdated, but leaving it as an example.

	$ rvm install 2.7.1
	$ rvm use 2.7.1 --default

Create a gemset for the blog

	$ rvm use 2.7.1@blog --create

List gemsets with:

    $ rvm gemset list

Switch gemsets with:

    $ rvm gemset use name-of-gemset


## Install Jekyll

Setup Jekyll:

	$ gem install bundler jekyll

Go to the root directory where you want to install. Then create a new jekyll blog.

	$ jekyll new blog
	$ cd blog

This creates the following:

	404.html
	about.markdown
	_config.yml
	Gemfile
	Gemfile.lock
	index.markdown
	_posts/


Run the blog:

	$ bundle exec jekyll serve

Open `http://localhost:4000` and Ctrl+C to stop

## Jekyll and Markdown

Jekyll uses Kramdown. Github Pages uses a customized `CommonMark` markdown.

* [Jekyll Kramdown markdown](https://jekyllrb.com/docs/configuration/markdown/)
* [Kramdown syntax](https://kramdown.gettalong.org/syntax.html)
* [CommonMark doc](https://commonmark.org/)
* [CommonMark Github Pages version](https://github.com/github/jekyll-commonmark-ghpages)


## Jekyll and Github Pages

Look at Github pages dependency [versions](https://pages.github.com/versions/)

Modify the Gemfile to use dependency versions supported.

	$ bundle update
	$ bundle install

Output:

	Bundle complete! 6 Gemfile dependencies, 85 gems now installed.
	Use `bundle info [gemname]` to see where a bundled gem is installed.

Test again:

	$ bundle exec jekyll serve

## More about Github Pages

Official docs [here](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/getting-started-with-github-pages).

A few important details:

* There are three types of sites: project, user, and organization.
* The default publishing source for project sites is the root of the `gh-pages` branch. For user/org sites the default source might be `master`. Review below a section on setting up a user site with custom plugins with the gh-pages branch.
* The site cannot be larger than 1GB.
* Bandwidth limit of 100GB per month.


## Troubleshooting dependency errors

When testing the site. For `jekyll-3.8.5` it says `warning: Using the last argument as keyword parameters is deprecated`.

* Also for `pathutil-0.16.2` it says the same.
* More [here](https://github.com/jekyll/jekyll/issues/7947)
* And [here](https://github.com/jekyll/jekyll/pull/7948)

Edit the `Gemfile` and comment this line again `gem "github-pages", group: :jekyll_plugins`. Then add this one:

	gem 'jekyll', github: 'jekyll/jekyll'

If you have `plugins` update them to this:

	group :jekyll_plugins do
	    gem 'jekyll-feed', github: 'jekyll/jekyll-feed'
	    gem 'jekyll-sitemap', github: 'jekyll/jekyll-sitemap'
	    gem 'jekyll-paginate', github: 'jekyll/jekyll-paginate'
	    gem 'jekyll-seo-tag', github: 'jekyll/jekyll-seo-tag'
	    gem 'jekyll-redirect-from', github: 'jekyll/jekyll-redirect-from'
	end

My `Gemfile` currently looks like this:

	source "https://rubygems.org"

	gem 'jekyll', github: 'jekyll/jekyll'

	gem "minima", "~> 2.5"

	group :jekyll_plugins do
	    gem 'jekyll-feed', github: 'jekyll/jekyll-feed'
	    gem 'jekyll-sitemap', github: 'jekyll/jekyll-sitemap'
	    gem 'jekyll-paginate', github: 'jekyll/jekyll-paginate'
	    gem 'jekyll-seo-tag', github: 'jekyll/jekyll-seo-tag'
	    gem 'jekyll-redirect-from', github: 'jekyll/jekyll-redirect-from'
	end

Then run `bundle install`


## Using a theme and edit `_config.yml`

The default theme is `minima`. The [docs](https://github.com/jekyll/minima) have good details on how to set it up.

It has instructions on how to set your `_config.yml`

<details>
	<summary>Here is a template:</summary>
	<pre>
	title: Name and Title of My Blog
	email: 
	description: >-
	  Some awesome description here
	baseurl: ""
	url: "https://www.mywebsite.com"
	twitter_username: mytwitter
	github_username:  mygithub
	permalink: /:title/

	# Build settings
	theme: minima
	minima:
	  skin: solarized

	header_pages:
	  - about.md

	disqus:
	    shortname: mydisqus_shortname

	author:
	  name: My Name

	show_excerpts: true

	minima:
	  social_links:
	    twitter: mytwitter
	    github: mygithub
	    linkedin: mylinkedin_shortname

	google_analytics: myGAcode

	plugins:
	  - jekyll-feed
	  - jekyll-feed
	  - jekyll-sitemap
	  - jekyll-paginate
	  - jekyll-seo-tag
	  - jekyll-redirect-from

	exclude:
	  - .sass-cache/
	  - .jekyll-cache/
	  - gemfiles/
	  - Gemfile
	</pre>
</details>

## Customizing the theme 'minima'

The default theme is installed as a gem and you won't see the source files in your blog directory. To find the source files run this:

	$ bundle info minima


My output was this:

	* minima (2.5.1)
	Summary: A beautiful, minimal theme for Jekyll.
	Homepage: https://github.com/jekyll/minima
	Path: /home/tom/.rvm/gems/ruby-2.7.1@blog/gems/minima-2.5.1


If you open this path, the README file shows where files are located:

* The `_layouts` directory define the markup for your theme.
* The `_includes` directory has snippets of code that can be inserted in layouts.
* The `_sass` directory define the theme's styles.
* The `assets` directory contains the `main.scss`.

The `main.scss` imports sass files from the `_sass` directory. It gets processed into the theme's main stylesheet `main.css` called by `_layouts/default.html` via `_includes/head.html`.

To override the default structure and style, create the specific directory at the root of the blog, copy the file to that directory, and then edit the file.

For example:

* To override the `_includes/head.html`.
* Create an `_includes` directory in the root of your blog.
* Copy `_includes/head.html` from minima gem folder to this directory.
* Edit that file.


## Updating the default CSS

* Go to the gem path.
* Copy the `assets/` folder to your blog root.
* Edit the `/assets/main.scss` file.

## Using SEO

The `minima` theme comes with the plugin `jekyll-seo-tag` which is approved by Github Pages.

Follow the [usage docs](https://github.com/jekyll/jekyll-seo-tag/blob/master/docs/usage.md) and [advanced usage](https://github.com/jekyll/jekyll-seo-tag/blob/master/docs/advanced-usage.md).

You can use the following in your post/page YAML header:

* `title`
* `description`

## Using the sitemap

This plugin `jekyll-sitemap` generates a sitemap as `domain.com/sitemap.xml`. You can submit this `sitemap.xml` in your Google Search Console.


## Using a different theme

I tried a theme that looked like [Medium](https://wowthemesnet.github.io/mundana-theme-jekyll/index.html)

Instead of installing Jekyll as shown above, do the following:

	$ git clone https://github.com/wowthemesnet/mundana-theme-jekyll.git blog
	$ cd blog
	$ bundle
	$ bundle exec jekyll serve

I liked it for a while but it had some weird bugs. I spent many hours trying to fix them but then I gave up and switched back to the default `minima` theme.


## Creating an `about` page

In your root blog create edit the default `about` and modify it as `about.md`:

	---
	layout: page
	title: "About"
	permalink: "/about.html"
	comments: false
	---

## Creating blog posts

These go in the `_posts` folder. I got a template that I follow for each new blog post:

	---
	layout: post
	title: "Title in Double Quotes"
	author: tom
	categories: [A category]
	tags: [some tags here]
	---

You can define other variables in the header of pages/posts. This header is called `YAML front matter block`. It must be on the top of the file and in between triple dash lines.

As seen on [Front Matter docs](https://jekyllrb.com/docs/front-matter/) you can set other variables like these:

* `published`: Set to `false` if you want to hide a post
* `date`: To override the date from the name of the post

## Disqus comments

Assuming that you already have a Disqus account and added your site there. Copy the `Shortname` for your site. More about shortnames in the disqus official docs [here](https://help.disqus.com/en/articles/1717111-what-s-a-shortname)

Go to your site's `_config.yml` and add your shortname in this section:

	disqus:
	    shortname: your_shortname_here

When creating a blog post add the variable `comments` and set to `true`. Previous example should show this:

	---
	layout: post
	title: "Title in Double Quotes"
	author: tom
	categories: [A category]
	tags: [some tags here]
	comments: true
	---

Comments can be disabled by not including this variable. More details in the Disqus docs [here](https://disqus.com/admin/install/platforms/jekyll/)

## Creating redirects

Add this to the `Gemfile` in the `plugins` block:

	gem 'jekyll-redirect-from', github: 'jekyll/jekyll-redirect-from'

Then run `bundle install`.

As seen in the docs [here](https://github.com/jekyll/jekyll-redirect-from)

Add it to the `_config.yml` under `plugins`

	- jekyll-redirect-from

My problem was that for my previous website, the blog posts had this format:

	blog_post_name.html

This new website removes the `.html` and a lot of blog posts crawled by google are being sent to a `404 File not found page`.

I want to redirect `blog_post_name.html` to `blog_post_name/`

In the blog post the header should show something like this:

	title: "Blog Post Name"
	redirect_from:
	  - /blog_post_name.html


## 404 page

More about 404 pages [here](https://help.github.com/en/github/working-with-github-pages/creating-a-custom-404-page-for-your-github-pages-site)

You can create a `404.md` file if you add this:

	---
	layout: page
	title: "Not Found"
	permalink: "/404.html"
	comments: false
	redirect_from:
	  - /index2.html
	---

Use the `redirect_from:` to redirect bad URLs from Google search results. Or use the same approach for blog posts. You can also try to fix them in your Google Search Console.


## Add a Favicon

Based on your theme, it should allow you to add a Favicon on the `_config.yml`. Otherwise you would need to add it directly to the `head.html`.

For the `minima` theme, the docs say that you can add an `_includes/custom-head.html` to your root folder and add your code for the favicon files. However, this didn't work for me as shown on [2.5.1 can't include custom-head.html](https://github.com/jekyll/minima/issues/472).

I added the favicon code directly to `head.html`.


## Setup Github

Go to Github:

* Create a new repo with the format `username.github.io`

Setup the repo:

	$ git init
	$ git remote add origin link-to-repo


## CNAME, robots.txt

If you have a custom domain, create a `CNAME` file, add a line with your website, and save it to your local blog root directory:

	www.yoursite.com

Create a `robots.txt` and add this line to the file:

    User-agent: *

You can also use `Disallow` for bad URLs.

	User-agent: *
	Disallow: /bad.html
	Allow: /


## Deploy to Github

If you want to cache your credentials

	$ git config --global credential.helper 'cache --timeout=3600'


There are two way to build your blog, development and production.

Build in development and test in localhost:

	$ bundle exec jekyll serve

Build in development uses `_config.yml`:

	$ jekyll build

Build to production using an environment variable:

	$ JEKYLL_ENV=production jekyll build

If you prefer to use a separate deploy configuration you can add a `_config-deploy.yml` to your blog root.

	$ cp _config.yml _config-deploy.yml

Add this line to `_config-deploy.yml`:

	environment: production

Build the blog like this:

	$ jekyll build --config _config-deploy.yml

Then deploy:

	$ git add .
	$ git commit -m "Awesome commit message here"
	$ git push -u origin master

You can also build to production using a gem called `jgd` as explained below in `Deploying custom plugins`.

## Change your DNS name server

Setup an account with Cloudflare if you don't have one. Find the DNS name server

In your DNS provider point it to Cloudflare.


## Cloudflare settings

Create these records in Cloudflare:

    Type    Name               Content
    ALIAS   yoursite.com       youruser.github.io
    CNAME   www.yoursite.com   youruser.github.io
    TXT     yoursite.com       youruser.github.io


Add `A` records as seen on [Setting up an Apex domain](https://help.github.com/articles/setting-up-an-apex-domain/)

Add `TXT` record to verify Google webmaster tools:

* Add property
* Add TXT google verification code


Setup these Page rules. As seen [here](https://www.jonathan-petitcolas.com/2017/01/13/using-https-with-custom-domain-name-on-github-pages.html)

    https://www.yoursite.com/*
    Cache Level: Cache Everything

    https://yoursite.com/*
    Forwarding URL: (Status Code: 301 - Permanent Redirect, URl: https://www.yoursite.com$1)

    http://www.yoursite.com/*
    Always Use HTTPS

In your Overview dashboard set these (if you are on the free plan)

* Security level: medium
* SSL: Full
* Caching level: Standard


## Migrating content

Here is where I was challenged.

With the Pelican Python static website generator the files were named as `title.md`. With Jekyll they need to be in the format `YYYY-MM-DD-title.md`.

For Pelican the header looks like this:

	Title: Powerful things you can do with the Markdown editor"
	Date: 2020-02-09 20:00
	Category: Jekyll, tutorial
	Tags: featured
	Slug: powerful-things
	Author: Tom Ordonez
	Status: published
	Summary: A blog post about Markdown editor.

For Jekyll it needs to look like this:

	---
	layout: post
	title:  "Powerful things you can do with the Markdown editor"
	author: tom
	categories: [ Jekyll, tutorial ]
	image: assets/images/11.jpg
	tags: [featured]
	---

How to make this change to about 100 blog posts?

I needed to extract the `Date` from the header and use it to rename the file. Then extract other content like the `Title`, `Category`, and `Tags`. Then replace this header with the new header.

Also the blog posts used this syntax to insert images in the content `{static}/images/` while Jekyll uses:

	{% raw %}
	{ {site.baseurl} }/assets/images/
	{% endraw %}

Read more in [Python, Files, and OS Module](../python-files-os-module)

## Deploying custom plugins

As shown on the [Github Pages docs](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/about-github-pages-and-jekyll#plugins). Github Pages cannot build sites using unsupported plugins.

Here is the list of approved plugins. Go to [dependency versions](https://pages.github.com/versions/).

As shown in this blog post [Deploy Jekyll to Github Pages](https://www.yegor256.com/2014/06/24/jekyll-github-deploy.html). You can use a gem to setup deployment of Jekyll when you are using custom plugins.

Update your `Gemfile`:

	gem 'jgd'

Run `bundle`
	
	$ bundle

Output:

	Fetching trollop 2.9.9
	Installing trollop 2.9.9
	Fetching jgd 1.12
	Installing jgd 1.12

	Post-install message from trollop:
	!    The 'trollop' gem has been deprecated and has been replaced by 'optimist'.
	!    See: https://rubygems.org/gems/optimist
	!    And: https://github.com/ManageIQ/optimist

More about `trollop` in the [official doc](https://www.manageiq.org/optimist/) and this tutorial: [writing a Ruby CLI using Trollop](https://kundeveloper.com/blog/trollop/).

To deploy use this:

	$ JEKYLL_ENV=production jgd

### Change the source of your Github Pages

* Go to your Github repo
* Settings
* Options
* Scroll down to Github Pages
* Source
* Change your branch from `master` to `gh-pages`.
* Save

Then deploy again:

	$ JEKYLL_ENV=production jgd

<details>
	<summary>Some of the output:</summary>
	<pre>
	[DEPRECATION] This gem has been renamed to optimist 
	and will no longer be supported. Please switch to optimist 
	as soon as possible.
	+ set -e
	+ set -o pipefail
	+ URL=https://github.com/...git
	+ BRANCH=gh-pages
	+ BRANCH_FROM=master
	+ DEPLOY_CONFIG=_config-deploy.yml
	+ BUNDLE=
	+ DRAFTS=
	++ pwd
	+ SRC=/home/.../blog
	++ mktemp -d -t jgd-XXX
	+ TEMP=/tmp/jgd-NAn
	+ trap 'rm -rf /tmp/jgd-NAn' EXIT
	+ CLONE=/tmp/jgd-NAn/clone
	+ COPY=/tmp/jgd-NAn/copy
	+ echo -e 'Cloning Github repository:'
	
	Cloning Github repository:
	+ git clone -b master https://github.com/... /tmp/jgd-NAn/clone
	
	Cloning into '/tmp/jgd-NAn/clone'...
	remote: Enumerating objects: 3025, done.   
	remote: Counting objects: 100% (3025/3025), done.                                     
	remote: Compressing objects: 100% (1920/1920), done.                                  
	remote: Total 3025 (delta 1272), 
	  reused 2692 (delta 954), pack-reused 0               
	Receiving objects: 100% (3025/3025), 31.22 MiB 
	  | 13.20 MiB/s, done.
	Resolving deltas: 100% (1272/1272), done.
	+ cp -R /tmp/jgd-NAn/clone /tmp/jgd-NAn/copy
	+ cd /tmp/jgd-NAn/clone
	+ echo -e '\nBuilding Jekyll site:'
	
	Building Jekyll site:
	+ rm -rf _site
	+ '[' -r _config-deploy.yml ']'
	+ jekyll build
	Configuration file: /tmp/jgd-NAn/clone/_config.yml
	            Source: /tmp/jgd-NAn/clone
	       Destination: /tmp/jgd-NAn/clone/_site
	 Incremental build: disabled. Enable with --incremental
	      Generating... 
	       Jekyll Feed: Generating feed for posts
	                    done in 1.67 seconds.
	 Auto-regeneration: disabled. Use --watch to enable.
	+ '[' '!' -e _site ']'
	+ cp -R _site /tmp/jgd-NAn
	+ cd /tmp/jgd-NAn
	+ rm -rf /tmp/jgd-NAn/clone
	+ mv /tmp/jgd-NAn/copy /tmp/jgd-NAn/clone
	+ cd /tmp/jgd-NAn/clone
	+ echo -e '\nPreparing gh-pages branch:'

	Preparing gh-pages branch:
	++ git branch -a
	++ grep origin/gh-pages
	+ '[' -z '' ']'
	+ git checkout --orphan gh-pages
	
	Switched to a new branch 'gh-pages'
	+ echo -e '\nDeploying into gh-pages branch:'

	Deploying into gh-pages branch:
	+ rm -rf 404.md about.md assets CNAME _config.yml Gemfile 
	  Gemfile.lock _includes index.markdown _layouts _posts
	+ cp -R /tmp/jgd-NAn/_site/404.html /tmp/jgd-NAn/_site/about.html
	+ rm -f README.md
	+ git add .
	++ date
	+ git commit -am 'new version Thu 08 Oct 2020 
	  12:10:05 AM EDT' --allow-empty
	[gh-pages (root-commit) a36ecd2] new version 
	  Thu 08 Oct 2020 12:10:05 AM EDT
	 819 files changed, 61268 insertions(+)
	 create mode 100644 .github/FUNDING.yml
	 create mode 100644 .gitignore
	 create mode 100644 .jekyll-cache/Jekyll/Cache/Jekyll--Cache/b7
	 ...

    + git push origin gh-pages
	+ sed 's|https://github.com/...|[skipped]|g'
	remote: 
	remote: Create a pull request for 'gh-pages' on GitHub by visiting:        
	remote:      https://github.com/.../pull/new/gh-pages         
	remote: 
	To [skipped]
	 * [new branch]      gh-pages -> gh-pages
	+ echo -e '\nCleaning up:'

	Cleaning up:
	+ rm -rf /tmp/jgd-NAn/clone
	+ rm -rf ''
	+ rm -rf /tmp/jgd-NAn
	</pre>
</details>

## Related Posts - Jekyll Plugin

This is a custom Jekyll plugin. Documentation [here](https://github.com/toshimaru/jekyll-tagging-related_posts).

Update your `Gemfile`:

	gem 'jekyll-tagging-related_posts'

Run `bundle`
	
	$ bundle

Output:

	Fetching nuggets 1.6.0
	Installing nuggets 1.6.0
	Fetching jekyll-tagging 1.1.0
	Installing jekyll-tagging 1.1.0
	Fetching jekyll-tagging-related_posts 1.1.0 
	Installing jekyll-tagging-related_posts 1.1.0

	Post-install message from nuggets:

	nuggets-1.6.0 [2018-07-12]:

	* Added <tt>JSON.*_{multi,canonical}</tt>.

	Post-install message from jekyll-tagging:

	jekyll-tagging-1.1.0 [2017-03-07]:

	* Added ability to append extra data to all tag pages. (tfe)
	* Provides compatibility to the current jekyll (3.4.1).
	* A few fixes. (felipe)
	* Some documentation improvements. (wsmoak, jonathanpberger)
	* Prooves who is the worst open source maintainer. (pattex ^__^)

Update `_config.yml` and `_config-deploy.yml`:

	plugins:
	  - jekyll/tagging
	  - jekyll-tagging-related_posts

Create a `_layouts` directory in blog root:

	$ mkdir _layouts

Copy the `post.html` layout from the `minima` theme Gem to this new directory.

	$ cp /home/tom/.rvm/gems/ruby-2.7.1@blog/gems/minima-2.5.1/_layouts/post.html _layouts/

Add this code to `post.html` after the blog post content and before disqus code.

{% raw %}

	{% if site.related_posts.size >= 1 %}
	<div>
	  <h3>Related Posts</h3>
	  <ul>
	  {% for related_post in site.related_posts limit: 5 %}
	    <li><a href="{{ related_post.url }}">{{ related_post.title }}</a></li>
	  {% endfor %}
	  </ul>
	</div>
	{% endif %}

{% endraw %}

## Google Analytics

The default theme `minima` comes with this line in the `_config.yml` file:

	google_analytics: UA-XXXXXX

Enter your Google Analytics code there.

For reference, this is inserted into `_includes/head.html`.

{% raw %}

	{%- if jekyll.environment == 'production' and site.google_analytics -%}
		{%- include google-analytics.html -%}
	{%- endif -%}

{% endraw %}

Therefore the blog needs to be deployed either using the `production` environment variable or a different deploy config file as previously shown.

Using the environment variable:

	$ JEKYLL_ENV=production jekyll build

If you are using the gem `jgd` then deploy like this

	$ JEKYLL_ENV=production jgd

## Pagination

Jekyll comes with a default plugin `jekyll-paginate` that you can set by adding the line `paginate: 5` to `_config.yml`. This is the Jekyll [doc](https://jekyllrb.com/docs/pagination/).

Go to `_config.yml` and make sure this line is under `plugins`:

	plugins:
	  - jekyll-paginate

Then add another line (outside of plugins):

	paginate: 5

If your theme's index is `index.markdown`, change it to `index.html` or you will get this error:

	Pagination: Pagination is enabled, but I couldn't find an index.html
	page to use as the pagination template. Skipping pagination.

Update your `_layouts/home.html`. Change this line, from this:

	for post in site.posts

To this:

	for post in paginator.posts

For pagination links, add the code as shown on [Jekyll's Render the paginated posts](https://jekyllrb.com/docs/pagination/#render-the-paginated-posts). Before the closing `endif` of `if site.posts.size > 0`.

You can center the CSS of pagination in `assets/main.scss`:

	.pagination {
		display: flex;
	}
	.previous, a.previous {
		flex: 1;
	}
	.page_number {
		flex: 1;
	}
	next, a.next {
		flex: 1;
	}

## Jekyll without Plugins

An alternative option to using custom plugins restricted by Jekyll is by not using plugins at all.

Here is a list of features to use [Jekyll without plugins](https://jekyllcodex.org/without-plugins/).

### Reading Time

Similar to Medium you can show the time it will take to read the blog post.

Here is the documentation to [reading time](https://jekyllcodex.org/without-plugin/reading-time-indicator/#)

Create the file `reading-time.html` in your `_includes` and add the code shown on the doc.

Go to your `_layouts/post.html` and include the `reading-time.html` line within the `header` tag, before the closing paragraph. You can test to see if it shows correctly on your blog post such as `Aug 29, 2020 • tom • 3 min read`.

Deploy the source to `master`:

	$ jekyll build
	$ git add .
	$ git commit -m "Added reading time"
	$ git push -u origin master

If you installed the `jgd` gem, then deploy static pages to the `gh-pages` branch:

	$ JEKYLL_ENV=production jgd

### Search Box

Here is the documentation to [Search with Lunr.js](https://jekyllcodex.org/without-plugin/search-lunr/#)

Save the file `search-lunr.html` in `_includes`. In this file, you can exclude the types of documents to search. For example:

	if page.url contains '.xml' or page.url contains 'assets' or page.url contains '.json' or page.url contains 'about.html'

Download the file `lunr.js` into your `js` folder, then make sure that `search-lunr.html` indicates the correct location of the file. For example:

	src="/assets/js/lunr.js"

I copied the `default.html` layout file from my Gem location to the `_layouts` directory:

	cp /home/tom/.rvm/gems/ruby-2.7.1@blog/gems/minima-2.5.1/_layouts/default.html _layouts/

Inside the `default.html` layout page, include the `search-lunr.html` as indicated in the docs inside curly percentage brackets. Add this in the `main` class, before the `content` tag.

	include search-lunr.html

Customize the CSS for the search box. At the bottom of `search-lunr.html` there is code with the form. You can wrap this in a class:

	<div class="search">
	    <form onSubmit="return lunr_search(document.getElementById('lunrsearch').value);">
	        <p><input type="text" class="form-control" id="lunrsearch" name="q" maxlength="255" value="" placeholder="Search" /></p>
	    </form>
	</div>

Then in `assets/main.scss` you can try something like this:

	.search input {
	    height: 30px;
	    width: 60%;
	    padding-left: 10px;
	    border: 1px solid #D9D9D9;
	    border-radius: 10px;
	    font-size: 16px;
	}


## Text Expand/Collapse or Collapbsible Markdown

I often add whole output to every command I use. This can take a large space in a blog post and might disrupt reading focus.

There are three options for hiding/display text that can be expanded, also known by these keywords: text expand, expand/collapse, collapsible markdown, details element.

Use whatever works best.

### Text Expand

This is a JS that might need some tweaking. When you click on `read more` it expands the section but it scrolls back to the top.

This is the doc for [Jekyll Text Expand](https://jekyllcodex.org/without-plugin/text-expand/).

Download the file `text-expand.html` into the `_includes` directory. Then edit the `_layouts/default.html` and add this before the closing `body` tag:

{% raw %}

	{% include text-expand.html %}

{% endraw %}

Then you can use the `expand` tag in a blog post by adding only one line for each of the open/closing tag such as:

	[expand]
	Long content here
	and here
	...
	[/expand]

### Collapbsible Markdown with Details element

This uses the details disclosure element: `details`. More details in the [Mozilla details element doc](https://developer.mozilla.org/en-US/docs/Web/HTML/Element/details).

You have to wrap your content like this:

	<details>
		<summary>Click to expand</summary>
		Long content here
		and here
	</details>

To add a code block, you need to have a previous empty line, enclose the code block with three tildes `~~~`, optionally you can add the language at the end of the first enclosing tildes:

	<details>
		<summary>Click to expand</summary>
		
		~~~ python
		Code here
		~~~
	</details>

However, you can encounter this issue [Details is not formatted correctly in Jekyll/Github pages](https://gist.github.com/ericclemmons/b146fe5da72ca1f706b2ef72a20ac39d#gistcomment-2710296). You can enclose the content with the `<pre>` tag.

I am using this for my long content output:

	<details>
		<summary>Click to expand</summary>
		<pre>
			
		Long content here
		</pre>

	</details>

Here is an example:

<details>
	<summary>Click to expand</summary>
	<pre>
	Long content here
	</pre>
</details>

I also customized the CSS:

	details {
		padding-bottom: 20px;
		color: grey;
	}

### The text in details not processed correctly

I haven't tested this. This blog post shows [adding support for HTML5 details element to Jekyll](http://movb.de/jekyll-details-support.html). It uses a custom plugin.

Add the `ruby` code into `_plugins/details_tag.rb`.

Then use like this:

{% raw %}

	{% details Click to expand %}

		~~~ python
		Code here
		~~~
	{% enddetails %}

{% endraw %}

More troubleshooting in [using details in Github](https://gist.github.com/ericclemmons/b146fe5da72ca1f706b2ef72a20ac39d) and [collapsible markdown](https://gist.github.com/joyrexus/16041f2426450e73f5df9391f7f7ae5f).

## Open external site in new window

By default linking to external sites open in the same window using this syntax:

	[External Title](link to external site)

Jekyll uses `kramdown` and you can link like this:

	[External Title](link to external site){:target="_blank"}

What I find annoying about this, is that it adds a weird highlighted row in SublimeText. I also think it's a weird syntax to remember.

An alternative option is this JS called [new window fix](https://jekyllcodex.org/without-plugin/new-window-fix)

Download the code into `_includes/new-window-fix.html` and remove the `PDF` section if you don't need it.

Add this to your `_layouts/default.html` before the closing `body` tag.

{% raw %}

	{% include new-window-fix.html %}

{% endraw %}

## Add external site to menu

There isn't a clear way to add an external link to the menu as discussed on [navigation external links](https://github.com/jekyll/minima/issues/207).

This [comment](https://github.com/jekyll/minima/issues/207#issuecomment-377095111) shows a quick fix to add the external link to `_includes/header.html`. In the past I had my Linkedin profile on the top menu using this:

{% raw %}

	<div class="trigger">
	  {%- for path in page_paths -%}
	    {%- assign my_page = site.pages | where: "path", path | first -%}
	    {%- if my_page.title -%}
	    <a class="page-link" href="{{ my_page.url | relative_url }}">{{ my_page.title | escape }}</a>
	    <a class="page-link" href="https://www.linkedin.com/in/tomordonez/">Linkedin</a>
	    {%- endif -%}
	  {%- endfor -%}
	</div>

{% endraw %}

The `header_pages` has to be enabled in `_config.yml` for the menu to show. I have an about page here:

	header_pages:
	  - about.md

However after a future migration, this stopped working, I ended up removing the liquid for loop, and added the links inside the div tag.

{% raw %}

    <div class="trigger">
            <a class="page-link" href="/about.html">About</a>
            <a class="page-link" href="/work.html">Projects</a>
            <a class="page-link" href="{{ my_page.url | relative_url }}">{{ my_page.title | escape }}</a>
            <a class="page-link" href="https://github.com/tomordonez/learning">Learning</a>
            <a class="page-link" href="https://github.com/tomordonez/reading">Reading</a>
    </div>

{% endraw %}