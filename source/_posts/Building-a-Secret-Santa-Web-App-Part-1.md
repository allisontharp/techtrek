---
title: Building a Secret Santa Web App Part 1
tags:
  - Angular
categories:
- Personal Projects
- Secret Santa
date: 2020-12-04 08:00:33
---

My DnD group is interested in doing a Secret Santa exchange this year.  With social distancing, pulling a name out of a hat is.. tricky.  I used to have a fun habit of building an app the night before and/or day of Halloween to track trick or treaters.  That didn't happen this year, so I thought this project could fill that void.  

## Goal
A quick web app that has the following functionality:
- Can handle multiple secret santa exchanges
- Displays each secret santa exchange's rules (dollar amount, whatever else is needed)
- Allows users to fill out some questions to help their secret santa find a gift
- Allows an admin to pair users (so no one gets someone in the same household)
- Emails each user their secret santa with their recipiant's responses

## Tools
I plan on making this substantially similar to the PokeTracker.  The tools will be:
- Angular - For front end
- AWS DynamoDb (on Demand Provisioning) - For storing emails and responses
- AWS Lambda (Python) - For the logic of determining the secret santas 
- AWS Simple Email Service - Sending the emails
- AWS Cloudfront (S3) - For the actual web app pages 


## Install Node 
### Check that you have node & npm
To confirm you have node:
`
node -v
`
To confirm you have npm:
`npm -v`

If you do not have them installed, you can download them [here](https://www.npmjs.com/get-npm).

## Set Up Angular CLI
Angular CLI is the official Angular command line tool and is used to initialize and work with angular projects.  Run the following in the command prompt to install:
`npm install -g @angular/cli@next`

## Create a New Angular Project 
Next, create a new Angular Project by running the following:
`ng new <project name>`
This will create a folder in whatever directory you are currently in with the name <project name> with several child directories and files.  It could take several minutes.

## Test the Project
Test that everything is working by going into the project's directory and running:
`ng serve`
This should allow you to use your browser and go to http://localhost:4200.  It should display a default page with information on how to proceed with Angular.

## Remove the template
Delete everything from the `src/app.component.html` file except the last like that has `<router-outlet></router-outlet>`.  Refresh your browser page and you should have an empty, white site.

## Generate a Component
In Angular, each aspect of the site is a component.  You can group multiple things together into one large component or have small (hopefully reusable) sections of the site be their own component.  I try to think of components as functions.  If there is something I'm going to want to reuse, it should be it's own small component.  The first component I'll make will be called form:
`ng g c form`
The `g` can be replaced with `generate` and `c` with `component` if you want to be verbose.

The command generated a folder in /src/app named form.  Within that folder are 4 files:
- form.component.css
	- Style sheet for the individual comonent.  Can be an empty file.
- form.component.html
	- HTML for the comopnent.  
- form.component.spec.ts
	- Used for unit testing, can be left alone.
- form.component.ts
	- TypeScript file that defines functions for the component.
	
In order to test the component, add it to the `src/app/app-routing.module.ts` file by adding these lines:
`import {HomeComponent} from './form/form.component'

const routes: Routes = [
  {path: 'form', component: FormComponent}
];
`
You can then view this component in your web browser by going to http://localhost:4000/form. The page should load and say 'form works!'.

## Using Bootstrap 4
Angular requires you to have at least a little understanding of HTML.  To make the HTML work a lot easier, I'm using [Bootstrap](https://getbootstrap.com/), which is an open source toolkit.

Make sure you are in your project's directory and install Bootstrap:
`npm install bootstrap`

Next, enable it by going to the `src/index.html` file and adding the stylesheet by adding the following inside of the `<head>` component:
`<link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.css">`

To use Bootstrap, you can search the [components page](https://getbootstrap.com/docs/4.4/components/) for the component you want and then just copy and paste the HTML into your `.component.html` file.  For this, I'm wanting to create a form that asks the users some questions.  I searched for form and copied the HTML into my html file.

From there, I can save and refresh my browser's page to see how it looks.  I add the questions I want:
{% codeblock %}
<form>
    <div class="form-group">
        <label for="participantName">Name</label>
        <input type="text" class="form-control" id="participantName" required>
    </div>

    <div class="form-group">
        <label for="participantEmail">Email address</label>
        <input type="email" class="form-control" id="participantEmail" aria-describedby="emailHelp" required>
    </div>

    <div class="form-group">
        <label for="participantColor">Favorite Color</label>
        <input type="text" class="form-control" id="participantColor">
    </div>

    <div class="form-group">
        <label for="participantFood">Favorite food/candy/snack</label>
        <input type="text" class="form-control" id="participantFood">
    </div>

    <div class="form-group">
        <label for="participantTeam">Favorite sports team(s) and/or fictional character</label>
        <input type="text" class="form-control" id="participantTeam">
    </div>

    <div class="form-group">
        <label for="participantScent">Favorite scent for the home</label>
        <input type="text" class="form-control" id="participantScent">
    </div>

    <div class="form-group">
        <label for="participantStore">Stores I love just about everything</label>
        <input type="text" class="form-control" id="participantStore">
    </div>

    <div class="form-group">
        <label for="participantGadget">Gadgets/tools I want</label>
        <input type="text" class="form-control" id="participantGadget">
    </div>

    <div class="form-group">
        <label for="participantEnough">Thanks, but I have enough</label>
        <input type="text" class="form-control" id="participantEnough">
    </div>

    <div class="form-group">
        <label for="participantEnjoy">I think I would really enjoy</label>
        <input type="text" class="form-control" id="participantEnjoy">
    </div>

    <div class="form-group">
        <label for="participantMisc">Anything Else:</label>
        <input type="text" class="form-control" id="participantMisc">
    </div>

    <button type="submit" class="btn btn-primary">Submit</button>
</form>
{% endcodeblock %}

## Mocking the Submit Button
Next, I want to make sure I can gather all of the information when the user hits submit.  For now, I just want to log to the console.  To do that, I create a function inside `form.component.ts`:
`submit(){
    console.log(this.participantName, this.participantEmail)
  }
`
and I update the button in the HTML file:
`<button type="submit" class="btn btn-primary" (click)="submit()">Submit</button>`

I also add variables in the `form.component.ts` file for each of the textboxes:
`
participantName: any;
participantEmail: any;
participantColor: any;
participantFood: any;
participantTeam: any;
participantScent: any;
participantStore: any;
participantGadget: any;
participantEnough: any;
participantEnjoy: any;
participantMisc: any;
`  

Finally, each input inside the html needs this added: 
`[(ngModel)]="<id>" name="<id>"`

At this point, the participant form is ready to be linked to AWS DynamoDb.  Before I do that, I want to set up the 

