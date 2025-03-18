title: Building a Web App Using AI
tags:
  - AI
  - AI/Cursor
  - NextJS
categories:
  - Personal Projects
date: 2025-03-17 00:00:00
---
AI is quickly taking over a lot of the world right now and some believe it threatens software engineering jobs.  I haven't been using it yet and believe if I want to be the best engineer I can, that I should add it as a tool in my toolbox.

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

# Lessons Learned
## Picking The Right Tool For The Job
I should have done more research on the different AI models and applications before purchasing a year of [ClaudeAI Professional](https://claude.ai). Claude has a few ways to interact with it, including a web interface (Professional) and a separate plan to use the API.  You can pay for credits for the API and plug that into [CoPilot](https://copilot.microsoft.com/) and use it directly in VSCode.  I wasn't really aware of that and also wasn't really keen on the credit model, so I originally went with Professional as it is more of a pay once type of plan.

The issue is that it doesnt have access to your code base and it gets slower and slower as you paste more and more files in.  It is cumbersome to try to give it the context it needs and ultimately extremely frustrating.  