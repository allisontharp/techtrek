---
title: Movie Tracker Web App
tags:
  - Angular
categories:
  - Personal Projects
  - Movie Tracker
date: 2020-12-10 08:00:33
---


Many years ago, I made a bucket list (and then promptly forgot about it).  One of the items was to watch every movie on the American Film Institute's [100 Years...100 Laughs](https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Laughs) list.  I watched the first (last?) few but never got around to finishing the list.  I was originally tracking when I watched the movies in a Google Sheet.  I've since tried to de-google my life, so tracking in the same sheet isn't something I'm keen to do.  I've made a couple of web apps recently and thought this would be a good opportunity for another.

# Project Goals
In the end, I would like to have a web app that:
- Stores list of movies
  - Including at least: title, rank, year released, professional rating (IMDB/Rotten Tomatatoes?), my rating, Troy's rating, when we watched, notes
- List of movies should be sortable (by name, rank, year, my rating, Troy's rating)
- Add a meeting to calendars (both my own and Troy's) to watch the movie (not really necessary but could be cool)
- Ability to rate the movie (both of us) along with notes (could be just one note field for both of us, doesn't need to be separate)
- Would be cool to check if it is a movie in our library and if not, add it 
- Would be cool to add the movie and my rating to [Nomie](https://www.nomie.app)

# Tools
Much like my [Secret Santa](/Building-a-Secret-Santa-Web-App-Part-1) and [PokeTracker](/categories/Personal-Projects/PokeTracker/) projects, this will use:
- Angular
- AWS DynamoDB
- AWS Lambda (Python)
- AWS S3
However, for something a little different, instead of using Bootstrap for the front end components, I will try to use [Material](https://material.angular.io/components/categories).  I have no real reason for this, other than to just learn a little more about it.

# Process
Instead of blogging the individual details of everything I'm doing, since I've now done that twice for Angular, I wanted this post to just talk about my thought process.  My first step was to determine my goals, which I documented here.  Then, I needed to decide on the tools (also documented here).  Both of these should be pretty fluid -- if I find that a tool isn't cutting it or a goal is too difficult or a new goal idea comes, I'm open to freely switching gears.

Once I had these thoughts documented, it was time for the most important thing -- coming up with a name.  Troy came up with Laughtrack, which is perfect.  From here, I created my angular project (`ng new laughtrack`).  The first component for me is always the hardest.  I'm never sure where to start.  For this, I created a home component (`ng g c home`).  I wouldn't be too surprised if I end up getting rid of it, though.  I then added that component to my `src/app/app-routing.module.ts` so I can easily view my changes.

# Adding Angular Material 
First, I added material to my project:
```
ng add @angular/material
```
This threw many errors that said unable to resolve dependency tree.  To resolve this, I updated npm (`npm install npm@latest -g`) then inside my root directory for the project ran `npm install`.  This updated my packages and everything seemed fine afterwards.

# Material Table
The first element I think I want to add is a table that can (later) display the movies in a sortable way.  Material has a [Sort header](https://material.angular.io/components/sort/overview) component that looks promising.  At first, I just copied the example from the Material page right into my home.component.html file.  Additionally, I copied the typescript from the Material page into my home.component.ts file.

The sort module that is required threw this error:
```
Error: The target entry-point "@angular/material/sort" has missing dependencies:
 - @angular/cdk/coercion
 - @angular/cdk/a11y
```

Running `npm install --save @angular/material @angular/cdk` seemed to fix the issue.

At this point, the page compiled but gave this error in the browser:
`
Argument of type 'Event' is not assignable to parameter of type 'Sort'.
`
It turned out that my Angular Material was v9 and they are up to v11.  I uninstalled it (`npm remove @angular/material @angular/cdk`) and then reinstalled (`npm add @angular/material @angular/cdk`).  Then, I stopped my angular server (ctrl c) and restarted it (`ng serve`).  I still had the same issue, so in the end, I decided to drop Material.

I usually leave these types of details out of my blog.  However, this is the reality of development.  A large percentage of your time, especially at the beginning (of learning, of new projects, etc), will be trying and failing.  Even though I've been a professional developer for several years, I still find that my failure rate is really high.  In the end, I could choose to continue to chase this down, use something I'm more familiar with (Bootstrap), or try something new ([Foundation](https://get.foundation/sites/docs-v5/index.html), [PrimeNG](https://www.primefaces.org/primeng/), something else?).

Ultimately, I tried PrimeNG but ran into similar issues.  I want to get this project off the ground, so I'm going back to my comfort zone: Bootstrap.

# Uninstall Material
I uninstalled material using the same method as above, `npm remove @angular/material @angular/cdk`.  I also removed the scripts from my `index.html` page that I added while troubleshooting the issue.

# Add A Theme
To make this project a little different from PokeTracker, I found a cool website called [bootswatch](https://bootswatch.com) which has free bootstrap themes.  Just download the css file of the theme you like and paste the contents into your `src/styles.css` file.  I chose solar because I like dark themes.

# Build the UI (Mocking Data)
I've traditionally been a data-first person.  Between UI design being something I really struggle with and pulling data being something I really enjoy, I tend to always start with the data pulls.  This has very often lead me to pull tons of data, thinking I knew what I wanted, only to find out later I wanted another piece of data and needing to pull the data again.  I tried a new approach with this project -- build a model of the data I want for each movie and then build the UI.  I also wanted to try to spend more time on the UI design to make it look more professional.  I am really proud of the end result and will definitely be using this methodology in the future.

To start, I created a new directory to hold my models: `src/app/models` and added a file named `movie.model.ts`.  The idea is that this will be the things I think I want to store and display about a movie.  It ended up looking like this (though changed along the way):

```ts
import { Rating } from './rating.model';

export interface Movie {
    title: string,
    year: number,
    description: string,
    image?: string,
    ratings?: Rating[],
    watched: string,
    dateWatched?: Date[],
    liked?: boolean,
    id: number,
    director?: string,
    writer?: string,
    stars?: string,
    runtime?: string,
    releaseDate?: string,
    genres?: string,
    budget?: string,
    openingWeekendUSA?: string,
    tags?: string[],
    comments?: string[]
  }
```

Next, I created a directory to hold my services: `src/app/services`.  I cded into that directory and ran `ng g s database`.  This creates 2 files (`database.service.ts` and `database.service.spec.ts` -- the latter of which is used for unit testing).  Within the .service.ts file, I mocked up what I wanted my actual database service to respond with:

```ts
import { Injectable } from '@angular/core';
import { Movie } from 'src/app/models/movie';

@Injectable({
  providedIn: 'root'
})
export class DatabaseService {
  movies: Movie[] = [];
  constructor() { }

  getMovies(){
    this.movies = [{
      title: 'Good Morning, Vietnam', year: 1987, description: `In 1965, an unorthodox and irreverent DJ named Adrian Cronauer begins to shake up things
    when he is assigned to the U.S. Armed Services radio station in Vietnam.`
      , image: '../../assets/afi100.jpg'
      , ratings: [{ source: 'IMDB', rating: 7.3, numberVotes: 125005 }, { source: 'Allison', rating:7, numberVotes: 1 }, { source: 'Troy', rating: 7, numberVotes: 1 }]
      , watched: true
      , id: 1
    },
    {
      title: 'The Nutty Professor', year: 1963, description: `A timid, nearsighted chemistry teacher discovers a magical potion that can transform him into a suave and handsome Romeo. 
    The Jekyll and Hyde game works well enough until the concoction starts to wear off at the most embarrassing times. `
      , image: '../../assets/afi99.jpg'
      , ratings: [{ source: 'IMDB', rating: 6.7, numberVotes: 15332 }, { source: 'Allison', rating: 4, numberVotes: 1 }]
      , watched: false
      , id: 2
    },
    {
      title: 'The Court Jester', year: 1956, description: `A hapless carnival performer masquerades as the court jester as part of a plot against an evil ruler who has overthrown the rightful King. `
      , image: '../../assets/afi98.jpg'
      , ratings: [{ source: 'IMDB', rating: 7.9, numberVotes: 11526 }, { source: 'Allison', rating: 6, numberVotes: 1 }, { source: 'Troy', rating: 7, numberVotes: 1 }]
      , watched: false
      , id: 3
    }]


    return this.movies;
  }

  getMovie(id: number){
    let movies = this.getMovies();
    console.log(movies)
    let movie = movies.filter(function (movie: Movie) {
      return movie.id == id;
    })[0];

    return movie;
  }
}
```

The awesome part about this is that I can interact with getMovies within the UI and hopefully **not have to change anything with the ui** after I make it be an actual API.  It allows you to be more flexible with what data you think you might want, without having to build the API (which could change).

Next, I tried to build the UI on my own.  The result was.. ugly.  Below is a bit of the history of the changes:
![Capture](/wp-content/uploads/2020/12/laughtrackhome01.png)
![Capture](/wp-content/uploads/2020/12/laughtrackhome02.png)
![Capture](/wp-content/uploads/2020/12/laughtrackhome03.png)
It was at this time that I realized I should look at other websites and apps that do similar things and try to incoroprate their design ideas.  The jump in quality between these two revisions was really exciting for me.
![Capture](/wp-content/uploads/2020/12/laughtrackhome04.png)
![Capture](/wp-content/uploads/2020/12/laughtrackhome05.png)
At the time of this writing, the homepage landed here:
![Capture](/wp-content/uploads/2020/12/laughtrackhome06.png)

The movie page went through similar iterations and at the time of this writing, landed here:
![Capture](/wp-content/uploads/2020/12/laughtrackmovie01.png)

# Conclusion
In the end, I was able to add most of the features I had originally hoped.  This feels like a project I can continuously work on -- adding movies, games, even board games if we wanted?  There are still a few features I definitely want to work on soon that didn't make the MVP cut:
- Sort by things other than name, AFI 100 rank
- Add a meeting to our calendars
- Hold multiple ratings (each time we watch, add a rating)
- Check if the movie is in our library or not and add it if needed
- Add Nomie integration
- Make tags searchable
- Dedicated tags page (maybe on reports page?)
- Let things like Genres on the movies page be links for searching

It seems like a lot is missing from the original list.  However, I added some functionality that was unexpected:
- Can track multiple watches
- Can add comments and tags