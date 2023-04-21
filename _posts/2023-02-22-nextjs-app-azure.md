---
layout: post
title: "Create a Next.js App and Deploy to Azure"
author: tom
tags: [azure, Next.js]
comments: true
---

Create a Next.js app with Azure Static Web Apps.

**Update: 4/20/23**: I moved this blog out of Next.js since having problems ranking in Google due to the way Next.js loads the content with JS.

**Sources**

* [Next.js doc to create an app](https://nextjs.org/learn/basics/create-nextjs-app)
* [Next.js doc to deploy app to Github](https://nextjs.org/learn/basics/deploying-nextjs-app/github)
* [Next.js app example](https://next-learn-starter.vercel.app/)
* [Deploy Next.js App to Azure](https://learn.microsoft.com/en-us/azure/static-web-apps/deploy-nextjs-hybrid)
* [Custom Domain in Azure Static Web Apps](https://learn.microsoft.com/en-us/azure/static-web-apps/custom-domain)
* [What is Azure Static Web Apps](https://learn.microsoft.com/en-us/azure/static-web-apps/overview)
* [Install Node with NVM](https://github.com/nvm-sh/nvm#installing-and-updating)

## Create GitHub repo

Created a repo called `azureblog` for all my Azure related tutorials and lessons learned.

* Don't add a `README`

## Install Node in Ubuntu

Using the default `sudo apt install nodejs npm` doesn't work with Next.js, since it requires `v.14` and above, and `apt` version is `v.12`.

Tried using Nodesource but running the commands returned an error that I couldn't figure out. (Wasted about 2 hrs here)

Install with NVM (took 5 minutes)

    cd ~/
    git clone https://github.com/nvm-sh/nvm.git .nvm
    cd ~/.nvm
    git checkout v0.39.3
    . ./nvm.sh

Add to `~/.bashrc`

    export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

List all available versions:

    nvm ls-remote

Install the latest LTS (`v18.14.1` as of Feb 2023):

    nvm install --lts

Verify that it was installed:

    $ node -v
    v18.14.1
    $ npm -v
    9.3.1

## Create a Next.js app

Use this command with the name of the github repo.

    npx create-next-app@latest azureblog --use-npm --example "https://github.com/vercel/next-learn/tree/master/basics/learn-starter"

Output:

    Creating a new Next.js app in /home/tom/azureblog.
    Downloading files from repo https://github.com/vercel/next-learn/tree/master/basics/learn-starter. This might take a moment.
    Installing packages. This might take a couple of minutes.
    added 18 packages, and audited 19 packages in 10s
    found 0 vulnerabilities
    Initialized a git repository.
    Success! Created azureblog at /home/tom/azureblog
    Inside that directory, you can run several commands:

    npm run dev
        Starts the development server.
    npm run build
        Builds the app for production.
    npm start
        Runs the built app in production mode.
    We suggest that you begin by typing:
    cd azureblog
    npm run dev

Open the directory

    cd azureblog

Add the git remote

    git remote add origin your-repo

Start the server

    npm run dev

Output:

    > dev
    > next dev

    ready - started server on 0.0.0.0:3000, url: http://localhost:3000
    event - compiled client and server successfully in 2.4s (149 modules)
    Attention: Next.js now collects completely anonymous telemetry regarding usage.
    This information is used to shape Next.js' roadmap and prioritize features.
    You can learn more, including how to opt-out if you'd not like to participate in this anonymous program, by visiting the following URL:
    https://nextjs.org/telemetry

Open localhost `http://localhost:3000`

## Initial push to Github

    git push -u origin main

## Create a static web app in Azure

Go to Azure portal:

* Create a resource
* Search `Static Web apps`
* Select `Create`

Basics:

* Select Subscription, Resource Group
* Enter a name
* Plan type: Free
* Region: Select your closest one
* Source: GitHub
* GitHub account: `Sign in with GitHub`

After sign in with Github:

* Choose (your Github account) Organization
* Select Repository
* Select Branch

Build Details:

* Build presets, select `Next.js`
* App location, leave default `/`

Review and create:

* Select `Review and Create`, then `Create`
* When completed `Go to resource`

## Check first deployment to Azure

Status of (actual) deployment:

* Azure shows a link for the deployed app. However, it said "waiting for the content"
* Go to the `Actions` workflow in the Github repo.
* It took about 2 minutes to complete
* Then check again the Azure link.
* It shows the default `Welcome to Next.js`

The Azure docs say that changes must be synced with local repo.

"When you created the app, Azure Static Web Apps created a GitHub Actions file in your repository. Synchronize with the server by pulling down the latest to your local repository"

Looking at GitHub, a new directory was created `.github/workflows` with a `yml` file. Some of the code:

        name: Azure Static Web Apps CI/CD

        on:
        push:
            branches:
            - main
        pull_request:
            types: [opened, synchronize, reopened, closed]
            branches:
            - main

Update local repo:

    $ git pull origin main

## Push/Deploy new changes

Looking at the `yml` script, it deploys once you push to the repo. Also the doc on `Azure Static Web Apps` says:

"...Azure interacts directly with GiHub or Azure DevOps, to monitor a branch...every time you push commits or accept pull requests into the watched branch, a build automatically runs and your app and API deploys to Azure"

Deploy to Azure pushing to GitHub:

    git push -u origin main

## Create a custom domain for the Azure generated URL

Go to Azure portal and get a `TXT` to validate that you own the domain:

* Azure Portal
* Go to the static web app resource
* Left menu/Settings/Custom domains
* Add
* `Custom domain on other DNS`
* Enter domain name
* Create `TXT` record
* Add settings to your DNS provider

With your DNS provider, create a `CNAME` record

* Type: `CNAME`
* Host: Your subdomain
* Value: Azure generated URL (without https://)

Wait a few hours to resolve.

## Build the Blog in Next.js

Run the server and keep it running.

    npm run dev

Following the official Next.js docs [here](https://nextjs.org/learn/basics/create-nextjs-app/editing-the-page)

**Edit a page**

Open `pages/index.js`. Replace `Welcome to` with `Learn`

**Create a new page**

* Under `pages` create the directory `posts`
* Create the file `first-post.js`

Add this content:

    export default function FirstPost() {
        return <h1>First Post</h1>;
    }

Then go to `http://localhost:3000/posts/first-post`

**Link Component**

In `pages/index.js` add:

    import Link from 'next/link';

Change the `h1` to:

    <h1 className="title">
        Read <Link href="/posts/first-post">this page</Link>
    </h1>

In `pages/posts/first-post.js` replace contents with:

    import Link from 'next/link';

    export default function FirstPost() {
        return (
            <>
                <h1>First Post</h1>
                <h2>
                    <Link href="/">Back to home</Link>
                </h2>
            </>
        );
    }

**Create a profile picture**

* Save a `profile.jpg` (400x400px) in `public/images`
* Remove the default `public/vercel.svg`
* Replace your favicon or leave the default in `public/favicon.ico`

**Modify the Metadata**

Add `Head` to `first-post.js`

    import Head from 'next/head';

Update the `FirstPost` component and include `Head`

    ...
    <>
        <Head>
            <title>First Post</title>
        </Head>
        <h1>First Post</h1>
    ...

**Create a Layout to share on all pages**

* Create a top level `components` directory
* Inside create a `layout.js`

Add this content:

    export default function Layout({ children }) {
        return <div>{children}</div>;
    }

In `pages/posts/first-post.js` add `Layout` import. Enclose everything in `return` with the `Layout` component, replacing the `<></>` with `<Layout></Layout`>.

    import Layout from '../../components/layout';

    ...
    return(
        <Layout>
        ...
        </Layout>
    );

Add CSS to `Layout`. Create a file `styles/Layout.module.css`:

    .container {
        max-width: 36rem;
        padding: 0 1rem;
        margin: 3rem auto 6rem;
    }

    .header {
        display: flex;
        flex-direction: column;
        align-items: center;
    }

    .backToHome {
        margin: 3rem 0 0;
    }

Modify `components/layout.js` with:

    import styles from '../styles/Layout.module.css';

    export default function Layout({ children }) {
        return <div className={styles.container}>{children}</div>;
    }

**Add Global Style**

Create the file `pages/_app.js`:

    export default function App({ Component, pageProps }) {
        return <Component {...pageProps} />;
    }

Restart the server.

Modify `styles/global.css` adding:

    body {
        line-height: 1.6;
        font-size: 18px;
    }

    a {
        color: #0070f3;
    }

    a:hover {
        text-decoration: underline;
    }

    img {
        max-width: 100%;
        display: block;
    }

Import the file  in `pages/_app.js`:

    import '../styles/globals.css';

Review results in `localhost:3000/posts/first-post`

## Add Google Analytics to Next.js app

* [Adding external JS in Next.js](https://nextjs.org/docs/basic-features/script)
* [Youtube tutorial Add Google Tag Manager](https://www.youtube.com/watch?v=UuE37-MM1ws)
* [Add Google Tag Manager to Nextjs](https://morganfeeney.com/how-to/integrate-google-tag-manager-with-next-js)

In Google Analytics `GA4`, in your account, create a property, and create a data stream.

**The default Google Tag JS code won't work**

After creating the data stream, it opens tag instructions with JS code to put after the head. I installed this in `_app.js` and the script showed correctly in localhost and then when it was deployed. However, the script wouldn't run. I checked in Google Analytics and data was being collected in the `Realtime` section.

    <!-- Google tag (gtag.js) -->
    <script async src="https://www.googletagmanager.com/gtag/js?id=G-YOURID"></script>
    <script>
    window.dataLayer = window.dataLayer || [];
    function gtag(){dataLayer.push(arguments);}
    gtag('js', new Date());

    gtag('config', 'G-YOURID');
    </script>

**Instead, use Google Tag Manager**

* Go to Google Analytics account
* At the top menu, go to your property dropdown
* The second icon (a diamond) next to Google Analytics is `Tag Manager`
* Follow the instructions to create a Tag Manager account as seen in [this](https://www.youtube.com/watch?v=UuE37-MM1ws) video from Google Analytics.
* Copy the JS code and save for later.

**Create a local environment variable**

Create the file `.env.local` in the root directory with this local environment variable:

    NEXT_PUBLIC_GOOGLE_ANALYTICS='G-YourCodeHere'

**Add .env.local to .gitignore**

Add this file to `.gitignore`. It might be already added:

    .env.local

**Add Google Tag Manager code to _app.js**

Go to `pages/_app.js`. Add the Google Analytics script using the local environment variable and a `strategy`.

If `strategy="afterInteractive"`, the script is added to the `body`. If `strategy="beforeInteractive"`, the script is added to the `head`.

{% raw %}

    import Script from 'next/script';

    export default function App({ Component, pageProps }) {
        return (
            <>
                <Script id="gtm-head" strategy="beforeInteractive">
                {`
                    (function(w,d,s,l,i){w[l]=w[l]||[];w[l].push({'gtm.start':
                    new Date().getTime(),event:'gtm.js'});var f=d.getElementsByTagName(s)[0],
                    j=d.createElement(s),dl=l!='dataLayer'?'&l='+l:'';j.async=true;j.src=
                    'https://www.googletagmanager.com/gtm.js?id='+i+dl;f.parentNode.insertBefore(j,f);
                    })(window,document,'script','dataLayer','${process.env.NEXT_PUBLIC_GOOGLE_ANALYTICS}');
                `}
                </Script>
                <Script id="gtm-body" strategy="afterInteractive">
                <noscript
                    dangerouslySetInnerHTML={{
                    __html: `<iframe src="https://www.googletagmanager.com/ns.html?id=${process.env.NEXT_PUBLIC_GOOGLE_ANALYTICS}"
                height="0" width="0" style="display:none;visibility:hidden"></iframe>`,
                    }}
                />
                </Script>
                <Component {...pageProps} />
            </>
        );
    }

{% endraw %}

Go to localhost to check that the scripts were loaded to the head and the body.

**Create an environment variable in Github Actions**

Since the deployment is done with GitHub and GitHub actions. Create an environment variable in GitHub. Actually, a repository variable, which is accessed with the `vars` context (see [GitHub docs](https://docs.github.com/en/actions/learn-github-actions/contexts#vars-context)). Alternatively, you could create an environment, and then an environment variable.

Create a repository variable:

* Go to the Next.js app repo
* Settings
* Security/Secrets and variables/Actions
* Go to the `Variables` tab
* On the green button click on `New repository variable`
* Enter name: `NEXT_PUBLIC_GOOGLE_ANALYTICS`
* Enter value: `G-YourCodeHere`

Modify your deployment `yml` file in `.github/workflows/`:

* Find the job `build_and_deploy_job`
* At the end of this job after `with`

As seen in this section:

    uses: Azure/static-web-apps-deploy@v1
    with:
        ...
        app_location: "/" # App source code path
        api_location: "" # Api source code path - optional
        output_location: "" # Built app content directory - optional
        ###### End of Repository/Build Configurations ######
    env:
        NEXT_PUBLIC_GOOGLE_ANALYTICS: ${{ vars.NEXT_PUBLIC_GOOGLE_ANALYTICS }}

Then deploy the app and verify online that the script was loaded.

**Adding an environment variable in Azure didn't work**

This didn't work for me. Before setting up the environment variable in GitHub, I did it in the Azure static web app dashboard like this:

* Go to the Azure static web app
* Settings/Configuration
* Application settings
* Environment: Production
* Click `Add`
* Enter name: `NEXT_PUBLIC_GOOGLE_ANALYTICS`
* Enter value: `G-YourCodeHere`

I spent hours trying to troubleshoot and nothing worked, then I remembered the diagram of [What is Azure Static Web Apps](https://learn.microsoft.com/en-us/azure/static-web-apps/overview) in the Microsoft website, it said `GitHub Actions` or `Azure DevOps`.

"When you create an Azure Static Web Apps resource, Azure interacts with GitHub or Azure DevOps, to monitor a branch of your choice"

I am assuming that since I chose `GitHub Actions` then all deployment configuration and environment variables must be set there and not in the Azure configuration feature.

**Verify the Google Tag Manager runs on the deployed website**

* Go to your deployed website
* Open developer tools
* In `Elements` (HTML structure), verify that the Tag Manager scripts were loaded to the head and body
* Go to `Network`. Check that there are two resources loaded. One starts with `gtm` and the one that confirms that Google Tag Manager is running and pinging Google Analytics starts with `collect`
* Go to your Google Analytics, `Realtime` reporting. And you should see a user visiting your deployed website.

## Create the blog structure of the Next.js app

**Polish the Layout**

As seen in the Next.js docs [here](https://nextjs.org/learn/basics/assets-metadata-css/polishing-layout)

Update these files with your content, or with content from the website above.

* Add a file `styles/utils.module.css`
* Update `components/layout.js`
* Update `pages/index.js`

**Create the blog structure**

As seen in the Next.js docs [here](https://nextjs.org/learn/basics/data-fetching/blog-data)

* Create the directory `posts` in project root
* Add markdown blog posts in this folder

Example `posts/pre-rendering.md`

    ---
    title: 'Two Forms of Pre-rendering'
    date: '2020-01-01'
    ---

    Next.js has two forms of pre-rendering

Install the library to parse the yaml metadata:

    npm install gray-matter

Create the directory `lib` in the project root. Then create a file `lib/posts.js`. Copy the code from the docs.

In `pages/index.js` add the code as seen [here](https://nextjs.org/learn/basics/data-fetching/implement-getstaticprops). This will add the list of blog posts to the home page.

**Create dynamic routes for the blog posts**

As seen in the docs [here](https://nextjs.org/learn/basics/dynamic-routes/page-path-external-data)

* In `page/posts/` create the file `[id].js`
* Remove the file `pages/posts/first-post.js`
* Add the code as seen in the docs.

## Wrapping up creating the app

Around this point following the Next.js docs, I realized this was way too much JS for me. It's a great learning experience if you wish to study and understand every single line of code. I completed all the files and added the code. Then deployed the final result to Azure.

## Creating blog posts

I created a Markdown template and included this file in `.gitignore`. I just copy/paste the template to `posts` and modify it to create a new blog post. Then git commit and `git push -u origin main` to deploy the blog to Azure.

## Add SEO to the app

**Create a sitemap.xml**

As seen in the docs [here](https://nextjs.org/learn/seo/crawling-and-indexing/xml-sitemaps). Create a `sitemap.xml.js` in the `pages` directory. Note the extension of the file. It's not just `.xml`. It is `.xml.js`.

Modify the sample code to include this:

    import { getSortedPostsData } from '../lib/posts';

Change the URL to your domain so it matches `https://your-domain/posts`:

    const EXTERNAL_DATA_URL = 'https://jsonplaceholder.typicode.com/posts';

Remove this section and add your domain here. Instead of this:

    <!--We manually set the two URLs we know already-->
     <url>
       <loc>https://jsonplaceholder.typicode.com</loc>
     </url>
     <url>
       <loc>https://jsonplaceholder.typicode.com/guide</loc>
     </url>

Replace with:

     <url>
       <loc>https://your-domain</loc>
     </url>

Making an API call as shown in the sample, didn't work for me, as I got an error that it expected JSON but it returned HTML. I removed this section:

    export async function getServerSideProps({ res }) {
        // We make an API call to gather the URLs for our site
        const request = await fetch(EXTERNAL_DATA_URL);
        const posts = await request.json();

Instead, I collected the blog posts from `getSortedPostsData`:

    export async function getServerSideProps({ res }) {
        const allPostsData = getSortedPostsData();
        const sitemap = generateSiteMap(allPostsData);

        res.setHeader('Content-Type', 'text/xml');
        // we send the XML to the browser
        res.write(sitemap);
        res.end();

        return {
            props: {},
        };
    }

**Create a robots.txt**

As seen in the Next.js docs [here](https://nextjs.org/learn/seo/crawling-and-indexing/robots-txt). In your root directory, create a `robots.txt`

    # Allow all crawlers
    User-agent: *
    Allow: /

Also add your sitemap like this:

    # Sitemap
    Sitemap: https://your-domain/sitemap.xml

**Submit Sitemap to Google Search Console**

After deploying the website.

* Go to `Google Search Console` or create an account
* Add your website
* Under Indexing/Sitemaps
* Add a new sitemap. Enter your sitemap and Submit.