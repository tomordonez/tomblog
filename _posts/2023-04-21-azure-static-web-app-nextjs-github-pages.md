---
layout: post
title: "From Next.js and GitHub Pages to Azure Static Web App"
author: tom
tags: [azure, azure static web app, nextjs]
comments: true
---

I moved out of Next.JS because it would not load the Google Analytics script. This blog is back to Jekyll built through Github Actions to an Azure Static Web App.

## Summary

Publish Jekyll with Azure Static Web App: [Docs](https://learn.microsoft.com/en-us/azure/static-web-apps/publish-jekyll)

* Install Ruby on Windows
* Create a local jekyll app
* Create a Github repo and add remote to local app
* In Azure create a Static Web App and link to Github repo. This creates a deploy YML for Github actions.
* Push local jekyll app to Github. If pushed or merged to main, it will trigger the action, and deploy to Azure.

## Install Ruby on Windows

Jekyll on Windows: [Docs](https://jekyllrb.com/docs/installation/windows/)

Install Ruby on Windows: [Docs](https://rubyinstaller.org/downloads/)

After install and checking `ruby -v` in the command prompt. Add an environment variable in your Windows setting. Otherwise, VScode might not recognize Ruby.

* In a command prompt `where ruby` and copy the path
* In Windows search for Environment Variables
* It opens System Properties. Click on Environment Variables
* Under System Variables, find `Path` and edit
* Add a new variable and paste the ruby path
* Close/reopen VScode

## Install Jekyll

Install Jekyll

    gem install jekyll bundler

Check that it installed

    jekyll -v

### If you need to create a new app

    gem install jekyll bundler
	jekyll new app-name
	cd app-name
	git init
	git branch -m master main
	git add -A
	git commit -m "First commit"

### If you cloned a repo and need to rebuild the app

    cd cloned-app
    gem install jekyll bundler
    bundle install

### Preview the app in your local machine

    bundle exec jekyll serve

## Create Github repo

Create a blank GitHub repo without a readme.

Add the repo to local app

	git remote add origin https://...repo-name
	git push --set-upstream origin main

## Create Azure Static Web App

Go to Azure

* Search `Static web apps`
* Create
* Enter the basics details
* Plan type `Free`
* Select sign in with GitHub
* Build details select Custom
* App location `./`
* API location empty
* Output location `_site`
* Review and Create

Go to local app

* If using main branch and it's not blocked for push, then push to main
* If using a feature branch, then PR and merge to main

Push to main or PR/merged to main will trigger the Github action.

## Add Google Analytics

In local Jekyll app.

Edit `_config.yml` and add:

    google_analytics: G-...your-analytics-code

In the `_includes` folder, create the file `analytics.html`, and add your JS tracking code.

In `_includes/head.html` add the environment logic, as shown in the Jekyll docs [here](https://jekyllrb.com/docs/configuration/environments/) enclosing the code with curly percentage braces

    if jekyll.environment == 'production' and site.google_analytic
    include analytics.html
    endif

In `.github\workflows` inside the deployment YML file, add the environment

    - name: Build And Deploy
        id: builddeploy
        ...
        with:
        ...
        env:
            JEKYLL_ENV: production

## Static Web App Quotas

As seen in the MS docs [here](https://learn.microsoft.com/en-us/azure/static-web-apps/quotas). There are limits to the `Storage` in the `Free` and `Standard` plans:

* Storage Free Plan: 250MB max per app
* Storage Standard Plan: 500MB max per app

If you reach the Free plan limit. You will get an error from GitHub CI/CD pipeline saying:

	The content server has rejected the request with: BadRequest
	Reason: The size of the app content was too large. The limit for this Static Web App is 262144000 bytes. For a higher app size limit, consider upgrading to the Standard plan.

**You can't see the size of the app in Azure**

Using the Azure Static Web App service, you cannot see the Storage account or container anywhere in Azure. The only way to see the size is to build the website locally and seeing the size of the `_site` directory.

    bundle exec jekyll serve

### Changing the plan

* Go to the Static Web App in Azure
* Under `Settings/Hosting Plan`
* Change from `Free` to `Standard`
* It will increase the price to `9.00 USD/per app/month`

## Check GitHub Status

Go to the GitHub repo

* Actions
* See the status of the workflows
* Go back to Azure when workflows are completed
* Go to the deployed resource
* Open the URL of the deployment

## Create content workflow

* Create new posts
* Then `git add -A`, `git commit -m "Create new post"`, `git push -u origin main`
* If you get a git push rejection try `git pull --rebase` then push again.

## Custom domain

It is preferred to use an Azure DNS as seen in the docs [here](https://learn.microsoft.com/en-us/azure/static-web-apps/azure-dns-zone).

* In Azure search for `DNS Zones`
* Create
* Use the name `yourdomain.com`
* Create and wait to deploy
* Copy the name servers
* Go to your DNS registrar and update the name servers
* Go to Azure DNS Zone
	* Click on `Record Set` to add a CNAME record with name `www`, alias record set `No`, keep default TTL, Alias set to the static web app URL.
* Go to Static Web App
	* Custom domain, add `Add custom domain on Azure DNS`, enter your domain as `www.yourdomain.com`, select the DNS zone created before.
* Set the apex record
	* Go to Static web app
	* Go to Custom domain, add `Custom domain on Azure DNS`, enter apex domain as `yourdomain.com`
	* It says `We will automatically create the CNAME or TXT and Alias records, validate and configure the custom domain in your Azure DNS zone`
	* Close and go to the Custom domain list
	* If status says `Validating` click on `view details`
	* Create the TXT record in the DNS provider
* Set the default domain
	* Go to Static web app
	* Go to custom domain
	* Select `yourdomain.com` and click `Set default`

[![Ask me anything on Linkedin]({{ site.baseurl }}/assets/images/ama-linkedin-tomordonez.png)](https://www.linkedin.com/in/tomordonez/)