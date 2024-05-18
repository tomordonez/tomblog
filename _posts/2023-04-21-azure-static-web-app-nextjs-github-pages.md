---
layout: post
title: "From Next.js and GitHub Pages to Azure Static Web App"
author: tom
tags: [azure, azure static web app, nextjs]
comments: true
---

I consolidated my two blogs into one with Jekyll in Azure using Azure Static Web Apps.

**Problems**

* My main blog was in GitHub pages but it became very slow to build.
* My second blog about Azure using Next.js in Azure. The website wouldn't rank on Google after many fixes and waiting about 2 months. After a lot of research I learned that this was an issue with the way Next.js loaded the website using JS.

After I joined the two blogs moving out of Next.js into Jekyll and submitting my sitemap to Google, I started ranking within a few days.

## Summary

Publish Jekyll with Azure Static Web App: [Docs](https://learn.microsoft.com/en-us/azure/static-web-apps/publish-jekyll)

* Create a local jekyll app
* Create a Github repo and add remote to local app
* In Azure create a Static Web App and link to Github repo. This creates a deploy YML for Github actions.
* Push local jekyll app to Github. If pushed or merged to main, it will trigger the action, and deploy to Azure.

## Install Jekyll

	jekyll new app-name
	cd app-name
	git init
	git branch -m master main
	git add -A
	git commit -m "First commit"

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

**Static Web App Quotas**

As seen in the MS docs [here](https://learn.microsoft.com/en-us/azure/static-web-apps/quotas). There are limits to the `Storage` in the `Free` and `Standard` plans:

* Storage Free Plan: 250MB max per app
* Storage Standard Plan: 500MB max per app

I recently reached the Free plan limit. I got an error from GitHub CI/CD pipeline saying:

	The content server has rejected the request with: BadRequest
	Reason: The size of the app content was too large. The limit for this Static Web App is 262144000 bytes. For a higher app size limit, consider upgrading to the Standard plan.

Changing the plan:

* Go to the Static Web App in Azure
* Under `Settings/Hosting Plan`
* Change from `Free` to `Standard`
* It will increase the price to `9.00 USD/per app/month`

**Tentative Strategy to move back to Free**

The Free plan has a limit for `Custom domains` to `2 per app`.

* Create another app to store posts older than 2 years.
* Manually do a cutoff of 2y or older posts and move them.
* Or write a program that automatically moves older posts to the archive blog app.

## Check GitHub Status

Go to the GitHub repo

* Actions
* See the status of the workflows
* Go back to Azure when workflows are completed
* Go to the deployed resource
* Open the URL of the deployment

## Migrated content

Magical skills using Linux to move my content to the new website.

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