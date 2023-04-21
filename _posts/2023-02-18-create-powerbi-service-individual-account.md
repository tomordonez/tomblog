---
layout: post
title: "Create a Power BI Service Individual Account"
author: tom
tags: [azure, power bi]
comments: true
---

What's the hack to creating a PBI service account with a personal email account?

I have used Premium Capacity in the past, where I could publish a dashboard, share reports, and control access to users. It was the easiest way to share reports because users logged in to the VPN or the internal network. They clicked on the email they received when a report was shared, and it opened the PBI service. Users didn't have to understand PBI licenses or figure out if they even had PBI. The report just opened on the web (internally). However, it cost the company ~$5,000/month.

Other licenses often got report consumers confused. They clicked on the shared report and it opened a page asking them to sign up for PowerBI Pro.

What if you are an individual and want to publish reports to the PowerBI service? Eventually, consumers (of the reports) need to get a paid license to see the shared reports.

The requirement to sign up for the PowerBI service is that you must have a corporate or education email account. I tried the Georgia Tech email account but it said "Your school doesn't allow you to buy PBI accounts" (or something similar to this). I wanted to sign up with a personal Gmail account. That's not possible. The website won't let you go through creating a PBI service account.

What's the hack to creating a PBI service account with a personal email account?

Follow [this](https://learn.microsoft.com/en-us/power-bi/enterprise/service-admin-signing-up-for-power-bi-with-a-new-office-365-trial) doc from Microsoft to sign up for a new Microsoft 365 trial. After the trial expires you can continue to sign in to the PBI service as a free user.

1. Sign up for a Microsoft 365 trial [here](https://www.microsoft.com/microsoft-365/business/compare-more-office-365-for-business-plans)

2. Select Office 365 E3 "Try for Free". Enter your personal email address. It will say "Looks like you need to create a new account". Click "Set up account"

3. Complete the "Tell us about yourself" form. For Company Name you can enter your FirstLastName. Company size: 1 person. Enter a phone number.

4. Send verification code.

5. The next screen says "How you'll sign in." Create your username and domain name (to complete the `onmicrosoft.com` email address). Continue. If there is an error. Go to [https://www.office.com/](https://www.office.com/) and try to log in with your user/password.

Follow [this](https://learn.microsoft.com/en-us/power-bi/fundamentals/service-self-service-signup-for-power-bi) doc from Microsoft to get an individual PBI license.

1. Select "Try free" from [https://powerbi.com/](https://powerbi.com/). It will try to single sign on and continue to a signup screen saying "You are signed in with..." (the `onmicrosoft.com` email address you created above). Click 'Continue'

2. Enter your name and click 'Get started'

3. It should open the PowerBI service website [https://app.powerbi.com/home](https://app.powerbi.com/home)

4. When the trial expires, you can continue to use the free Power BI service. But you can't share reports. Follow the prompts to sign up for PowerBI Pro. Although keep in mind, the end user (report consumer) also needs a paid license to view the report (unless you all have the same Premium Capacity account).