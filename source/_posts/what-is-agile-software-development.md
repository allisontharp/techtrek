---
title: What is Agile Software Development?
url: 1206.html
id: 1206
categories:
  - Methodology
date: 2016-05-10 08:00:00
tags:
---

Agile software development is an idea that self-organizing and cross-functional teams can collaborate in a way that makes the software development process responsive to change.  True Agile does not define any specific methods to do this, but many people have their own ideas on how to achieve it.  In fact, the [official Agile Methodology website](http://agilemethodology.org/) is quick to point out that it is not a methodology at all!  According to that website, the Agile movement "seeks alternatives to traditional project management.  Agile approaches  help teams respond to unpredictability through incremental, iterative work cadences, known as sprints.  Agile methodologies are an alternative to waterfall, or traditional sequential development".

### What Is the Waterfall Model?

I discussed the Waterfall Model in the post titled ["The Software Development Life Cycle"](http://www.techtrek.io/the-software-development-life-cycle/).  As a review, the **Waterfall Model** is the simplest SDLC model.  The idea here is that each step in the SDLC (Communication, Requirement Gathering, Feasibility Study, System Analysis, Software Design, Coding, Testing, Integration, Implementation, Operations and Maintenance, and Disposition) is linear.  Additionally, it assumes that you will not move on to the next phase until your current phase is completely finished.  Similarly, you will not back track to a previous phase at any point.  As we discussed before, this model works best when the team is designing something that they have a lot of experience in or that they have done before.

### A Brief History Of Agile

A paper titled "Managing the Development of Large Software Systems" was presented back in 1970 by [Winston W. Royce](https://en.wikipedia.org/wiki/Winston_W._Royce) (a pioneer in the software development field).  Even by 1970, incremental software developments had been employed for about 13 years and people were beginning to think about the best ways to develop small and large software packages.  The software development methodologies at the time were very similar to manufacturing and construction methodologies (since that is what we were good at!).  Dr. Royce felt like these sequential methods were insufficient and his paper recommended against these sort of phase based approaches.  He saw the downfalls of the Waterfall Model such as the assumption that a team will know _all_ of the requirements before any design or coding happens -- this just isn't how the real world operates!

### How Does Agile Fix This?

The goal of Agile is to continually think about the direction of the project throughout its entire development.  This is achieved through **sprints** or **iterations**.  A sprint is a set amount of time during which specific work has to be finished and made ready for review.  Working in sprints results in an "inspect-and-adapt" approach which tends to greatly reduce the development costs and the time to market.  Another benefit is that the team can be working on the actual coding while still gathering gathering.

#### Sprints

A sprint will begin with a planning meeting.  During this meeting, whoever requested the work and whoever is going to do the work will agree what exactly is to be completed during the sprint.  The person doing the work should have the final say as far as how much work can realistically be completed in the set amount of time and the person requesting the work has the final say on what criteria has to be met in order for the work to be considered completed.

### What Is Scrum?

Scrum is the most popular way of introducing Agile.  According to the Agile Methodology website, it "emphasizes empirical feedback, team self management, and striving to build properly test product increments within short iterations".    Scrum uses feedback loops to encourage us to inspect and adapt our product and our processes.  Scrum uses the sprint idea - each sprint will include most of the SDLC steps: analysis, design, implementation, testing, future planning, and maybe even deployments.  Even from the very first sprint, the team will try to build a working and tested product, even if it is really small.  At the end of every sprint, they will show everyone what they have.  Sometimes, the customer may need to see the wrong product to really have an understanding of what they actually want, so these 'baby steps' are okay.  Because Scrum uses small iterations with everyone seeing the product after every sprint, a Scrum team will have more feedback more often and they will be more likely to discover and develop the correct product. Scrum is built on Roles, Artifacts, and Meetings:

#### Roles

*   Product Owner
    *   They request the work.
    *   They are responsible for the Return on Investment (ROI)
    *   They have final say in the requirements
    *   They are more focused on the _what_ and less focused on the _how_
*   Development Team
    *   Ideally this is a small team (4-9 people)
    *   A cross-functional team that needs to focus on self-organization
    *   They attempt to build a _potentially_ shippable product after every Sprint (this is hard to do in the beginning!)
    *   They collaborate effectively and are usually on the same team together full time
    *   No hierarchy in the team, leadership will come out naturally.
*   Scrum Master
    *   This role is often misunderstood.
    *   Unlike the name suggests, the Scrum Master has no management authority over the team.
    *   They are also not the project manager
    *   The Scrum Master protects the team from distractions
    *   They help improve engineering practices
    *   They help teach how to use Scrum

#### Artifacts

*   Product Backlog
    *   List of everything we might ever do (if it isn't in the backlog, it doesn't exist)
    *   Prioritized by the P_roduct Owner_
    *   Anyone can add items to the Backlog but the _Product Owner_ must prioritize it and the _Scrum Master_ must make it visible
    *   No tasks, just items.
*   Sprint Backlog
    *   List of everything the team agreed to have completed during the current Sprint
    *   Since it is linked to the current Sprint, it has an end date.
    *   It includes the **Commited Backlog Items** which is the "what" (what items need to be completed) as well as the **Sprint Tasks** which represent the "how"

#### Meetings

Scrum defines four types of meetings, but a lot of people have found that a fifth type of meeting is also very useful.  These meetings are:

*   Sprint Planning Meeting
*   Daily Scrum
*   Sprint Review Meeting
*   Sprint Retrospective Meeting
*   Backlog Refinement Meeting (not defined by Scrum and has no official name)

A Sprint is typically 2 weeks.  The first Monday morning of a Sprint, you may have a 4 hour _Sprint Planning Meeting_.  In this meeting, the Team and the Product Owner negotiate which items will be covered in the sprint.  The team will look at the Product Backlog and pull the top priority items from that list and put them into the Sprint Backlog.  The team will typically break each of these into smaller tasks.  Once this is complete, the team will discuss if it is the right amount of work and make sure everyone is on the same page about what needs to be done. After that, the team will have their _Daily Scrum Meetings_.  These are typically 15 minute "stand up" meetings.  This is where the team reports to each other (not the Scrum Master or the Product Owner) about what they completed yesterday, what their plan is for today, and what your current impedances are (if any). On the Wednesday before the Sprint is to be completed, you might have a 2 hour _Backlog Refinement Meeting_.  This meeting might also be called a _Backlog Grooming Meeting_.  Remember, this meeting isn't officially defined by Scrum but many teams have adapted it into their Scrum routine.  In this meeting, the team and the product owner will look, together, at the next few highest priority items in the Product Backlog.  These are the items which will likely be covered in the next couple of Sprints.  The team tries to clarify these items and break them into smaller Product Backlog Items, if possible.  The team will review their priorities, see what kind of dependencies each of them have, etc.  This is really a precursor to the Sprint Planning Meeting and, realistically, this work could be done during the Sprint Planning Meeting.  However, many teams have found that it is preferable to have this meeting prior to the Sprint Planning Meeting and will help that meeting run more smoothly. Finally, on the day the Sprint is due, you may have a 2 hour _Sprint Review Meeting_ in the morning where the team presents a possibly shippable product to whoever is interested (the product owner, stake holders, etc).  This is where the team will get feedback from the "higher ups".  If the team produced what they promised they would, the product owner or stake holders may decide that isn't exactly what they wanted and will give valuable feedback to the team. Every sprint will end with a _Sprint Retrospective Meeting_.  This is where the team will review how they worked together in the last iteration and adapt.  They'll talk about what went well, what didn't go so well (and how it can be improved), what they learned, what they found out they need to learn, etc.  The team should give feedback to each other as well.  This allows the team to take ownership in their own product and to learn to be held accountable to each other. Agile and Scrum both seem like really great ideas to help a software development team work more efficiently.  I have experience working at a large engineering company where we had many processes and procedures that, while quite different from Agile/Scrum, look to solve the same goal.  Sometimes those processes worked very smoothly and other times they seemed like a hindrance.  Scrum seems to be built, intentionally, to not be such a hindrance and to let the team try to figure out the best way to solve the problem in the given Sprint.  In particular, I like the idea of defined meetings with defined goals for those meetings.  While working for the large engineering company, I found myself having pre-meeting meetings and post-meeting meetings to discuss what we would discuss in the meeting and then what we actually ended up discussing in the meeting.  The end result of this was that a considerable amount of my time was spent in meetings where I wasn't able to work on doing the actual product.  I really like the principles defined here and I think if they are executed properly, it could make for a well-balanced team and product design process. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._