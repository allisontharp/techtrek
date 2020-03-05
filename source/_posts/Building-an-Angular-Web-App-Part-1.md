---
title: 'Building an Angular Web App: Part 1'
tags:
  - Angular
date: 2020-03-05 09:50:15
---


I just recently finished my first Pokemon game (Sword).  I used an app to track which Pokemon I had and hadn't caught as well as to keep track of which version-specific Pokemon my boyfriend needed.  After finishing the game, I quickly started playing more Pokemon games.  I wanted to be able to use a single app to track my progress for any game but shockingly couldn't find such a thing.  This series will track the progress of making my own.

Set up Angular CLI
------------------
Assuming you already have Node installed, the first step is to install the Angular CLI.  This is the official Angular command line tool and is used to initialize and work with angular projects.  Run the following code in the command prompt to install:

```
npm install -g @angular/cli@next
```

Create a New Angular Project
----------------------------
To create a new Angular project, complete with the basic golders and files that you'll need, run:

```
ng new <project name>
```

It will ask you a few questions.  I said yes to Add Angular routing and chose CSS as the style sheet.  My project name was PokeTracker.

Test the Project
----------------
Test that everything is working so far by going into your project's directory in the command line (cd) and run:

```
ng serve
```

This should allow you to use your browser and go to http://localhost:4200.  

Generate a Component
--------------------
In Angular, each aspect of the site is a component.  You can group multiple things together into one large component or have a small (reusable) section of the site be its own component.  I think of components like functions.  If there is something I'm going to want to reuse, it should be its own component.  The first component I made was called ShowPokes:
```
ng g c ShowPokes
```
The `g` can be replaced with `generate` and `c` with `component` if you want to be verbose.

This command generated a folder in /src/app named show-pokes.  Within this folder are 4 files:
- show-pokes.component.css
 - Style sheet for the individual component. Can be an empty file.
- show-pokes.component.html
 - HTML for the component.  This is where the bulk of our work will be done.
- show-pokes.component.spec.ts
 - Generated spec sheet.  I have not touched this.
- show-pokes.component.ts
 - TypeScript file that defines functions for the component.

Overall, the process will be to define logic in the `.component.ts` file and then display it using HTML in the `.component.html` file.

In order to test the component, add it to the `app-routing.module.ts` file by adding these lines:
```ts
import {ShowPokesComponent} from './show-pokes/show-pokes.component';

const routes: Routes = [
  { path: 'ShowPokes/:id',      component: ShowPokesComponent }
];
```

You can view this component in your web browser by going to http://localhost:4000/ShowPokes/1

The `:id` portion at the end will help us be able to reuse the component later by passing a variable (in my case it will be region name).  This will allow us to generate multiple pages using the same code.

Using Bootstrap 4
-----------------
Angular requires you to have at least a little understanding of HTML.  To make the HTML work a lot easier, I'm using [Bootstrap](https://getbootstrap.com/), which is an open source toolkit.  

Make sure you are in your project's directory and install Bootstrap:
```
npm install boostrap
```

Then, enable it by going to the `index.html` file and adding the stylesheet by adding the following line inside the `<head>` component:
```html
<link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.css">
```

While you are in this file, it is a good time to update the name of the page by placing the name inside the `<title></title>` tags.

To use Bootstrap, you can search the [components page](https://getbootstrap.com/docs/4.4/components/alerts/) for the component you want and then just copy and paste the HTML into your `.component.html` file.  I want each Pokemon to show up as a [card](https://getbootstrap.com/docs/4.4/components/card/), so I pasted the code from the Bootstrap documentation to `/src/app/show-pokes/show-pokes.component.html`:

```html
<div class="card" style="width: 18rem;">
  <img src="..." class="card-img-top" alt="...">
  <div class="card-body">
    <h5 class="card-title">Card title</h5>
    <p class="card-text">Some quick example text to build on the card title and make up the bulk of the card's content.</p>
    <a href="#" class="btn btn-primary">Go somewhere</a>
  </div>
</div>
```

Refresh the page in the browser, and you should have a card on the page.

In the next posts, I'll discuss how I capture, store, and display data about the Pokemon.

_Have questions or suggestions? Please feel free to comment below or [contact me](/contact/)._