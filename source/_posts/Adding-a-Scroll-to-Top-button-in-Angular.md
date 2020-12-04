---
title: Adding a Scroll to Top button in Angular
tags:
  - Angular
  - Pokemon
categories:
  - Personal Projects
  - PokeTracker
date: 2020-03-13 08:52:57
---


I have been creating a Pokemon tracking PWA using Angular (you can follow the project [here](categories/Personal-Projects/PokeTracker/)).  I'm at the point where the MVP is working and I'm adding quality of life features.  One such feature – a scroll to top button – came together so quickly and was such high value that I wanted to share.

The idea of a scroll to top button, generally, is that an arrow appears (generally in the bottom right of the screen) that, when clicked, will send you to the top of the page.  There are hundreds of types of Pokemon in a single game, so lots of scrolling around.

Add a New Component
----------------------------
First, I added a new component to my Angular project:
```
ng g c scrollToTop
```
This command created a new directory in my `/src/app` folder named scroll-to-top.  Within that folder, I added the following to the `.css` file:

```css
.scroll-to-top {
    position: fixed;
    bottom: 15px;
    right: 15px;
    opacity: 0;
    transition: all .2s ease-in-out;
   }
   .show-scrollTop {
    opacity: 1;
    transition: all .2s ease-in-out;
   }
```

This is what positions the arrow in the bottom right of the screen.  

Next, I added this to the `.html` file:
```html
<div class="scroll-to-top" [ngClass]="{'show-scrollTop': windowScrolled}">
    <button type="button" class="btn active" data-toggle="button" aria-pressed="true" (click)="scrollToTop()">
        <i class="fas fa-chevron-up" ></i>
    </button>
  </div>
```
There are two important things happening here.  First, I created a button that has the `fas fa-chevron-up` icon on it.  I already had FontAwesome installed in my app, so all I needed to do was search the site to find [the icon I wanted to use](https://fontawesome.com/v4.7.0/icon/chevron-up).  This is how I knew to use the words "fas fa-chevron-up".  

In order to use FontAwesome, you'll need to have the following in the head of your `index.html` file:

```html
    <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.7.0/css/all.css" integrity="sha384-lZN37f5QGtY3VHgisS14W3ExzMWZxybE1SJSEsQp9S+oqd12jhcu+A56Ebc1zFSJ" crossorigin="anonymous">
```

The next important thing happening here is what happens when you click the button – `(click)="scrollToTop()"`.  To define that function, add the following to the `.ts` file (not the `.spec.ts` file):

```ts
import { Component, OnInit, Inject, HostListener } from '@angular/core';
import { DOCUMENT } from '@angular/common';

@Component({
  selector: 'app-scroll-to-top',
  templateUrl: './scroll-to-top.component.html',
  styleUrls: ['./scroll-to-top.component.css']
})
export class ScrollToTopComponent implements OnInit {
  windowScrolled: boolean;
  constructor(@Inject(DOCUMENT) private document: Document) {}
  @HostListener("window:scroll", [])
  onWindowScroll() {
      if (window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop > 100) {
          this.windowScrolled = true;
      } 
     else if (this.windowScrolled && window.pageYOffset || document.documentElement.scrollTop || document.body.scrollTop < 10) {
          this.windowScrolled = false;
      }
  }
  scrollToTop() {
      (function smoothscroll() {
          var currentScroll = document.documentElement.scrollTop || document.body.scrollTop;
          if (currentScroll > 0) {
              window.requestAnimationFrame(smoothscroll);
              window.scrollTo(0, currentScroll - (currentScroll / 8));
          }
      })();
  }
  ngOnInit() {}
}
```

This function will scroll the window until it reaches the top.

Finally, we need to implement the component.  I want this available on every page, so I added it to my `app.component.html` file.  However, if you only want this functionality on a specific page or set of pages, you could add it to a different file.  To implement, simply add the following to the html file:

```html
<app-scroll-to-top></app-scroll-to-top>
```

If you named your component in the same way I did, your tag names will be the same.  However, you can always check what the tag names need to be by looking at the the selector portion of the @Component within your `.ts` file.  As you can see above, my selector is `app-scroll-to-top`.  This is the reason why my tags are named `app-scroll-to-top`.

_Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._

