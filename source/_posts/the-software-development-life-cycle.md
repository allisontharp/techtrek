---
title: The Software Development Life Cycle
url: 1214.html
id: 1214
categories:
  - Methodology
date: 2016-04-26 08:00:01
tags:
---

I've been writing code for about 5 years.  For many of those years, I only learned what I needed to know to get whatever thing I wanted to build running properly.  As such, the Software Development Life Cycle was not something that I ever really knew about or thought about.  However, since I am trying to make myself be a better programmer/developer/administrator/whatever, this topic is something that is beginning to intrigue me. The Software Development Life Cycle (SDLC) is a well-structured set of 11 steps that is used to help develop a piece of software.  The 11 steps are: communication, requirement gathering, feasibility study, system analysis, software design, coding, testing, integration, implementation, operations and maintenance, and finally, disposition.  Coming from an engineering background, these steps closely align with the principles I have encountered with product design.  They make a lot of sense and each step has its own purpose.

### Communication

In this step, the user/customer initiates the process.  He or she will contact the developer or the company and both will try to negotiate the terms.  The final output of this request should be formalized in writing.

### Requirement Gathering

If terms were agreed upon, from here on out the software team and/or its company will be working to fulfill the terms of the contract/request.  During the requirement gathering phase, the team will brainstorm together and/or separately to try to determine all of the different requirements for the project.  The team should think about user requirements, system requirements, and functional requirements.  Some steps that might help this process include:

*   Study the existing software, if it exists, even if (or especially if) it is obsolete
*   Interview the end users and the developers

### Feasibility Study

Now that the team has good idea about all of the different requirements, they can start working on a feasibility study.  The team should first strive to see if software can be developed to meet all of the requirements.  If not, they must ask themselves what compromises will need to be made and if those compromises are acceptable.  The project needs to be financially, practically, and technologically feasible for the organization to agree to it.  If any of these are not feasible, the requirements need to be reconsidered or the project abandoned.

### System Analysis

This step involves understanding the software limitations, learning system related issues, changes that might need to be done to the systems before they are compatible with the software, identifying the impact on the company, personnel, and systems, etc.  In this step, the team will also assess the scope of the project and determine the schedule and resources for successfully completing it.

### Software Design

In this step, the team will look at what inputs the software will need from its users.  This will typically come from the Requirement Gathering phase.  Additionally, the team will look at the outputs that will be required by the software.  This will result in both logic and physical design.  In this step, the team will work on things like dictionaries, logic diagrams, flow charts, and pseudo code.

### Coding

This is the step that most programmers and developers probably like the most.  Here, the team will sit down and write the actual software.  They will have determined a suitable language and will work on developing error-free programs.

### Testing

This might be a well-hated step for programmers and developers (though, to be honest, testing is one of my favorite parts of software design).  Some people estimate that as much as **50%** of this entire process will be eaten up by testing.  I think that value can likely be true (and it is true in other engineering disciplines as well).  This step is closely related to the coding step as the team will be testing and coding in a loop or even at the same time.  The team may have people dedicated just to testing the software at a code level, program level, product level, etc.  This step is critical to the long term success of the product.

### Integration

This step involves marrying the software with other libraries, databases, or outside programs (if necessary).

### Implementation

Here, the team will install the software on the user machines and test for compatibility and portability issues.

### Operation and Maintenance

In this phase, the team confirms that their solution is more efficient than the previous solution.  This step is also where the end users are trained (if applicable).  The software maintenance will also occur here, making this perhaps a very long lasting step.  If needed, updates will be made to the end user software.  Hidden bugs and unidentified problems may arise in this step.

### Disposition

Finally, the disposition step.  At some point, the software may no longer be needed.  This could happen for a number of reasons such as performance degradation or the software being obsolete.  Some of these issues could be mitigated through maintenance, but if the solution requires an intensive upgrade, or a major portion of the software needs to be eliminated, it might make since to phase out the product.  If that is the right move, the team will archive the data and the software components, close down teh system, and terminate the software at an appropriate timeline.

### SDLC Models

There are several different models or methodologies for following the above steps.  A couple basic ones worth mentioning are:

### Waterfall Model

This is the simplest model.  The idea behind it is that all of the above steps are linear and that when one phase finishes, the next begins.  It assumes that everything will happen and finish cleanly within each step, such that you never have to backtrack to any previous step.  This doesn't always work in the real world, as we discussed with the testing phase (it may be likely that you will code then test, code then test, etc).  This model works best when the team is designing something they have a lot of experience in or that they have done before.

### Iterative Model

In this model, the software is first developed on a very small scale.  Once all of the steps have been completed for this small scale product, the team goes through the steps again but adding more features.  With each iteration, the team adds more and more features and models and completely goes through each step of the process.  At the end of each cycle, the team has a complete piece of software which as more features than the previous version.  A big benefit here is that with each iteration, the team can look at risk management and prepare for the next iteration.  A con is that this model tends to take more resources. _Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._