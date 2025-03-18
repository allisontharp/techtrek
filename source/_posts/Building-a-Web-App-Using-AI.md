---
title: Building a Web App Using AI
tags:
  - AI
  - AI/Cursor
  - NextJS
categories:
  - Personal Projects
date: 2025-03-17 00:00:00
---

AI is quickly taking over a lot of the world right now and some believe it threatens software engineering jobs.  I haven't been using it much yet and believe if I want to be the best engineer I can, that I should add it as a tool in my toolbox.

This blog is a testament that the way I have historically learned new tech stacks is by doing.  So to that end, I wanted to create a new website using just AI.

# The Problem 
I have a 1985 Chevy El Camino named [Sven](https://sven.cruises).  There is a website that tracks car shows in Indiana ([OldCarsOnly](https://www.oldcarsonly.com)), but it looks like a website from the early 2000s (which, honestly, is part of its charm).

I want to create a car show tracking website similar to OldCarsOnly, but with a modern tech stack.

# The Requirements 
There are two categories of requirements.  The first is for the actual website, and the second is more for my personal growth.

## Website
- Look good on both desktop and mobile 
- Calendar view with ability to filter by name, distance, category, etc 
- Ability to submit a car show
- Admin capabilities:
	- Edit any existing car show 
    - Approve/deny submitted car shows 
    
## Growth
- Use AI as much as possible.  The goal isn't necessarily to build a website but rather to learn AI. 
- Pick a tech stack that I am unfamiliar with

# Overview 
I first used ClaudeAI to help me pick the TechStack and basic outline of components.  I originally did not know about [Projects](https://www.anthropic.com/news/projects), so I was working with distinct contexts.  My original prompt was this: 

```prompt
The web app I'm wanting to build is for car shows in Indiana.  I'm open to brainstorming ideas to get started, but in general these are the features: 

    List car shows by date. Each car show listing should include things like date, location, fees, description, contact info, add to calendar, share (to facebook, twitter, etc), category, etc
    search car shows (by name, location, etc)
    ability to submit a new car show, and have an admin be able to approve and edit any car shows

what other features do you think it should have?
```

It listed a lot of cool features, which I do want to implement.  I then asked it for a tech stack and it suggested NextJS, Tailwind CSS, Prisma, and NextAuth.  It helped me initialize the project and built out all of the initial basic features.

It went both really easy and very difficult at the same time.  When it works, it *works*, but when it doesn't, it is very frustrating.  AI is confident in every response, even in responses when it is wrong.  If I truly did not understand software engineering, I think this project would have been a major failure. 

When it came time to deploy, it was a nightmare.  The way it was set up, it was going to be expensive and difficult to deploy to AWS.  I spent a lot of time trying to get it to make the NextJS API deployable, and ultimately ended up switching to [Cursor](https://www.cursor.com), and having it rewrite the endpoints to Lambdas (see [Lessons Learned > Picking The Right Tool For The Job](#picking-the-right-tool-for-the-job) below for more information on the AI switch).

Once I got things working both locally and deployed, it did feel like I was on some god mode coding wise.  It got to a point where it felt like it was just reading my mind and I could tab complete.  I think a measure of a good engineer will quickly become how disciplined they are with AI code completions -- it can be very tempting to just trust it once it starts working correctly.

# Lessons Learned
## Picking The Right Tool For The Job
I should have done more research on the different AI models and applications before purchasing a year of [ClaudeAI Professional](https://claude.ai). Claude has a few ways to interact with it, including a web interface (Professional) and a separate plan to use the API.  You can pay for credits for the API and plug that into [CoPilot](https://copilot.microsoft.com/) and use it directly in VSCode.  I wasn't really aware of that and also wasn't really keen on the credit model, so I originally went with Professional as it is more of a pay once type of plan.

The issue is that it doesnt have access to your code base and it gets slower and slower as you paste more and more files in.  It is cumbersome to try to give it the context it needs and ultimately extremely frustrating.  

## Prompt Quality Is Probably Related To Output
Garbage In, Garbage Out is still a thing in the AI world.  As I used AI more and more, I think I got better at writing prompts.  A major help is ending prompts with `Ask clarifying questions before writing any code` and/or `If you still have more questions, ask them`.  Otherwise, it will NOT ask questions and will just go full steam ahead with 100% confidence.

## I Still Needed Professional Help
Deploying and troubleshooting is a weak spot of mine (I've been blessed to work with amazing SREs, so I often only have to worry about my own code and not how or where it is deployed).  When issues came up, it was hard to troubleshoot with AI.  I think this is similar to how if a non developer were to use AI to build a web app would feel.  It would continue to make a suggestion, I would get an error, it would make some other suggestion, I would get the same error, it would make the initial suggestion.  Even when I told it specifically not to.

One example of this is that I wanted my user to have a role set to `'ADMIN'`.  This is a custom attribute in my User Pool, and I wanted it as infrastructure as code.  I spent days trying to get it to write a correct sst config, and ultimately I had to call on my husband (also an amazing SRE who is fluent in all things AWS).  It's hard to know what you don't know and AI doesn't know what you don't know, and it is hard to bridge that gap.

# Conclusion
I *really* like NextJS, it was a blast to use. I really liked the API coding with it and was bummed to switch away to lambda.  

Coding with AI is a dichotomy.  I simultaneously feel like software engineers will be replaced imminently and never.  I feel like it's extremely amazing and extremely bad.  I feel like it makes me go faster and it makes me slower.  It's wild and I think with time, I'll begin to use it more and more effeciently. 

You can view the final website [here](https://ddu3qllj7eijh.cloudfront.net/).  I hope to add more features, both with and without the use of AI, in the coming weeks.  

_Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._