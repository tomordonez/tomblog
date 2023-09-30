---
layout: post
title: "ChatGPT Replacing Google"
description: "I replaced Google with ChatGPT for most technical questions"
author: tom
image: assets/images/1.jpg
tags: [chatgpt, ai]
comments: true
---

I replaced Google with ChatGPT for most technical questions.

I tried ChatGPT4 for Unity, and it failed to understand complex problems that required systems integrations. Unity is a monster of integrated systems. It is as if you were to combine Photoshop, Illustrator, AutoCAD, Solidworks, and ProTools in one software. GPT-4 and GPT-3.5 can guide you in implementing and integrating something from scratch. However, since the AI cannot see your whole codebase, it can't tell the entire context of your problem. You can give it pieces (even long ones) of your code and it does a good job explaining every single line of code. But it often fails at providing a solution when it doesn't know what your other dozens of files have.

ChatGPT wouldn't replace (some) humans in the future. Let's say that a future version of ChatGPT can plug into your whole codebase. It might be able to fix bugs, refactor code, and even write new code. However, it cannot interface with non-technical humans to understand the context and intention of a problem.

If non-technical stakeholders requested a report to ChatGPT to integrate data and get insights for a business segment, it would just output instructions and the code to build it. However, this is where ChatGPT would fail:

* It might provide complex code unless they request "easy to understand" code.
* They wouldn't know what to do with the code. The AI is not smart enough to log in to all the required systems to implement the solution.
* Let's say they give the code to a (human) engineer who tries to implement the code but finds no clear association among the underlying database tables.
* The engineer goes back to ChatGPT to explain the problem. Not sure if the AI can provide output to the following: You give it the table schemas and sample data, and you expect it will tell you how the tables are associated.
* You would give ChatGPT the DDL codes to tell you what you already know. The code has constraints and associations with other tables. However, in this particular case, even the DDL doesn't show there is an association between table A and table B, even though you can see that the UI shows that there is a relationship among data from both columns.
* Can you give the AI a screenshot of the UI? They said the next version of ChatGPT4.X can generate images. But can it understand screenshots of anything? When you paste an image containing text in a Word (or was it OneNote?) document, it recognizes the text if you search for it.

## ChatGPT as an assistant

I use Google to search for answers, and navigate through the pages to see if I can find the answer. Most of them are often from StackOverflow. I wonder why I don't go to StackOverflow directly. Sometimes, results are blog posts loaded with ads. Rarely do I end up in someone's blog where they care more about the content than with annoying ads. I also use Youtube to find short tutorials. However, I must watch unnecessary content to get to the actual answer.

How is ChatGPT replacing this experience?

It provides context based on what you previously searched for.

Here are prompts for a conversation with ChatGPT. I was having problems animating a door in Unity. I had an idea what a Prefab was but wanted more details.

Me: What is a Prefab
GPT: ...answers in detail

Me: I have a Prefab door that is closed. When the character gets near, the door opens. When the character crosses the door, the door closes. How would you use Mecanim to do this?
GPT: ...answers in detail, step by step (with steps in bolded sections), with sample code

Me: This code doesn't work
GPT: ...answers in detail

This conversation went on and on back and forth. However, after each prompt I didn't have to provide the whole context. It understood the conversation from the previous prompts. You obviously cannot do this with Google. Every search in Google is independent of each other. I also tried Bard. It pretty much sucks. You would think Google having vaults full of gold that you could swim in, that Bard would compete with small tiny OpenAI. I even tricked Bard by asking why Google is slow at implementing technology. I phrased my question in a way that required an answer, and Bard replied that Google is slow because of bureaucracy, too big of a company, and slow at adapting to change that deviates from its primary source of income.

I decided not to use the ChatGPT code in the above example because it used C# syntax I have never seen before. But it gave me an idea of how to unstuck myself.

## ChatGPT doesn't work with evolving software

I tried asking ChatGPT: "How do you do this in VScode?" It answered, but when I looked for that setting. I could not find it. Then I complained to it, and the answer was: "I am sorry, you are right, that setting is not there." The conversation went on back and forth until I gave up on it. The most likely cause is that if ChatGPT only has data up to a year ago, and the software changed since then, it wouldn't know about such change.

ChatGPT works best for topics that don't evolve as fast. Ask anything about a programming language, even put short/long lines of code, and it can understand and explain in detail what each line is for.

## ChatGPT helps you get unstuck

Why is this SQL code not working? The error from the IDE is not helping.

I put the code in ChatGPT, and it explained in regular words (not abstract IDE errors) what was wrong with it.

"Try putting backticks on the alias instead of single quotes." That was one of the answers. I tried that, and it worked. I might have mixed single quotes and double quotes.

I use ChatGPT as a tool to replace Google. It helps me get unstuck. Once in a while, I ask random things like "Is Grogu related to Yoda?" or "Why is the sky blue?" But I don't use it as a replacement for writing. I am sure this blog post might have grammar mistakes. In the past, I used Grammarly, and sometimes I use it for a school project, but I don't see the need for ChatGPT to correct my writing. I don't like it when Grammarly recommends that I change words to ones I never use. "It is of utmost importance to utilize a plethora of vocabulary."

I asked ChatGPT what jobs it would replace. It said data entry jobs. In a data entry job, a human might need to interact with another human, look at a screen to recognize images and text and transcribe information to another system. Unless the AI is smart enough to do this. 

This could be an interesting AI project: upload 100 images and tell the AI to describe what it sees in them, then save these descriptions as image metadata. It would probably take the AI a few minutes to do this. It might take a human hours, days or weeks. However, the AI might fail to reason with humans and correct itself. Let's say the user asks some descriptions to include more emotion based on how we assume the people in the images felt in that moment. How would the AI answer compared to a human?