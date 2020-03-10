---
title: 'Building an Angular Web App: Part 2'
tags:
  - Angular
  - Pokemon
categories:
  - Personal Projects
  - PokeTracker
date: 2020-03-09 22:21:28
---


In the [previous post](/Building-an-Angular-Web-App-Part-1), we looked at getting an Angular web app with a single component up and running.  In this post, I will walk through the general steps I took to get an MVP Pokemon tracking web app (as well as what the final MVP looks like).

Getting Pokemon Data
--------------------
For my project, I wanted to be able to get all Pokemon available in a specific game and store two items: caught status and favorite status.  In the end, this actually means storing four things (pokemon name, region/game name, caught status, favorite status).  It also poses two interesting problems: getting and storing the data.

Luckily, there is an open source PokemonAPI that we can leverage called [PokeAPI](https://pokeapi.co/).  Even better, there is an open source browser side wrapper called [pokeapi-js-wrapper](https://github.com/PokeAPI/pokeapi-js-wrapper) that auto-caches results (meaning we don't have to hit the PokeAPI servers with every call).  

To install the wrapper, run the following command in the Angular project:
```
npm install pokeapi-js-wrapper --save
```

I then created an angular service called api by running:
```
ng g service api
```
This created three files in the /src directory (`api.service.spec.ts` and `api.service.ts`).  I've since learned that creating an api service like this isn't really right (for one, I'm not writing an api), but mistakes like this are part of learning!

I used the `api.service.ts` file as sort of a wrapper for the PokeAPI wrapper.  I created my own functions that call the wrapper and get the data I needed in the format I needed it in.  To use the wrapper, you need to add the following lines to the top of your file:

```typescript
const Pokedex = require('pokeapi-js-wrapper');
const P = new Pokedex.Pokedex();
```

Within the ApiService class, I defined my functions.  The first function I needed was one that would get all Pokemon for a specific game.  That function looks like this:

```typescript
public async setAllPokemon(region: any){
    const dex = await P.getPokedexByName(region);
    var db = localforage.createInstance({name: region});
    dex.pokemon_entries.forEach(async function (value) {
        var pokemonName = value.pokemon_species.name;
        var p = {
          name: pokemonName,
          region: region,
          caught: false,
          favorite: false
        }
        db.setItem(pokemonName, p).then(function (value) {
          }).catch(function(err) {
            console.error(`Error: ${err}`);
          });
    });
  }
```

I get all of the Pokemon by calling out to the wrapper with `P.getPokedexByName` and passing in the region name (for instance, Kanto for Pokemon Lets Go).  Then, for each Pokemon in the returned object, I get the species name and set the caught and favorited values to false.  This data is stored in a database that is named after the region name.

Storing the Data
----------------
I ultimately want this web app to be a PWA (Progressive Web App).  As part of that goal, I would like the data to stay in the browser.  I had never done anything like this before and wasn't sure if it would be possible.  After some research, I found that the PokeAPI wrapper was actually storing the Pokemon in a browser database named [localForage](https://localforage.github.io/localForage/).  This is an open soure JavaScript library for offline databases and is really simple to use.

To use localForage, we must first insall it:
```
npm install localforage
```
Then, we need to call it in the `api.service.ts` file:
```typescript
import * as localforage from "localforage";
```
The local database is an offline key value pair system.  If you try to write to a database row that already exists, it will overwrite that item.

Line 2 in the setAllPokemon function above creates a database with the name of the provided region if it doesn't exist.  If a database with that name already exists, it will grab the data in the database.  To add an item to the database, we simply need to call `db.setItem`.

Reading all rows from a database is similarly easy:

```typescript
public async getAllPokemon(region: any){
    console.log(`Region: ${region}`);
    var db = localforage.createInstance({name: region});
    var pokemon = new Array;
    var setDb = true
    await db.iterate(function(value, key, iterationNumber) {
        pokemon.push(value);
        // this.pokemon.push(value);
    }, function() {

    });

    if(pokemon.length == 0 && setDb){
      await this.setAllPokemon(region);
      await this.getAllPokemon(region);
      setDb = false;
    }

    return pokemon;
  }
  ```

  This function attempts to get an existing database for a provided region.  It then iterates through every row in the database and adds the data to an array named pokemon.  If the array is empty, it means the database has not been created and does not have any data.  In this case, it creates the database and populates it with all of the Pokemon for that region.

  Putting it Together
  -------------------
  Those two functions, as well as the setup work done in the previous post, are the majority of the legwork for the MVP.  The final step is to call the getAllPokemon function from HTML when a page loads.  To do this, we simply need to add a line to the ngOnInit() method within `show-pokes.component.ts`:

```typescript
this.pokemon = await this.apiService.getAllPokemon(this.id);
```

At the top of the file, we should import in the ApiService and ActivatedRoute:
```typescript
import { ApiService } from "../api.service";
import { ActivatedRoute } from '@angular/router';
```
ActivatedRoute lets us pull the parameter from the URL (remember we set the route in app-routing.module to be /ShowPokes/:id).  Importing this module in will allow us to grab the `:id` value by adding this to ngOnInit:

```typescript
this.sub = this.route.params.subscribe(params => {
      this.id = params['id']; 
    });
```

In the end, the `show-pokes.component.ts` file looks like this:

```typescript
import { Component, OnInit } from '@angular/core';
const Pokedex = require('pokeapi-js-wrapper');
const P = new Pokedex.Pokedex();
import { ApiService } from "../api.service";
import { ActivatedRoute } from '@angular/router';

@Component({
  selector: 'app-show-pokes',
  templateUrl: './show-pokes.component.html',
  styleUrls: ['./show-pokes.component.css']
})
export class ShowPokesComponent implements OnInit {
  pokemon;
  searchText;
  s;
  f;

  id: string;
  private sub: any;

  constructor(
    private apiService: ApiService,
    private route: ActivatedRoute
  ) { }

  async ngOnInit(): Promise<void> {
    this.sub = this.route.params.subscribe(params => {
      this.id = params['id']; 
    });
    // this.apiService.deleteDatabase();
    this.pokemon = await this.apiService.getAllPokemon(this.id);
  }
```

When this component loads, it will set the URL parameter to `this.id` and get all of the pokmeon for that region, saving the data to the `this.pokemon` variable.  This is really cool because it allows us to write one component and reuse it effortlessly.  The same component will be used to display each region and all we have to do is pass a different region name to the URL.

In the `show-pokes.component.html` page, we can iterate over every pokemon in `this.pokemon` and display the Bootstrap card:

```html
<div class="card-deck" style="margin-left: 5px; margin-top: 25px;">
    <div *ngFor="let name of pokemon | filter:{name: searchText, caught: s, favorite: f}">
        <div class="card" style="width: 18rem;">
            <div class="card-body">

                <div class="d-inline-block">
                    <h5 class="card-title" >{{name.name}}</h5>
                </div>
                
                <div class="d-inline-block" >
                    <div *ngIf="name.favorite===true;else inactiveFavorite">
                        <button type="button" class="btn active" data-toggle="button" aria-pressed="true" (click)="setSinglePokemon(name.name, name.region, name.caught, false)">
                            <i class="fas fa-star" ></i>
                        </button>
                    </div>
                    <ng-template #inactiveFavorite>
                        <button type="button" class="btn" data-toggle="button" aria-pressed="false" (click)="setSinglePokemon(name.name, name.region, name.caught, true)">
                            <i class="far fa-star"></i>
                        </button>
                    </ng-template>
                </div>

                <div *ngIf="name.caught===true;else inactiveCaught">
                    <div class="btn-group btn-group-toggle" data-toggle="buttons">
                        <label class="btn btn-secondary active" (click)="setSinglePokemon(name.name, name.region, true, name.favorite)">
                            <input type="radio" name="options" id="Caught" > Caught
                        </label>
                        <label class="btn btn-secondary" (click)="setSinglePokemon(name.name, name.region, false, name.favorite)">
                            <input type="radio" name="options" id="Caught" > Not Caught
                        </label>
                    </div>
                </div>

                <ng-template #inactiveCaught>
                    <div class="btn-group btn-group-toggle" data-toggle="buttons">
                        <label class="btn btn-secondary" (click)="setSinglePokemon(name.name, name.region, true, name.favorite)">
                            <input type="radio" name="options" id="Caught" > Caught
                        </label>
                        <label class="btn btn-secondary active" (click)="setSinglePokemon(name.name, name.region, false, name.favorite)">
                            <input type="radio" name="options" id="Caught" > Not Caught
                        </label>
                    </div>
                </ng-template>

            </div>
        </div>
    </div>
</div>
```

Current State
-------------
PokeTracker currently looks like this:
![](CurrentState.png)

It has the following features:
- Compatible with Pokemon Lets Go and Pokemon Omega Ruby/Alpha Sapphire
- Track Caught/Not Caught
- Track Favorite
- Search by Name
- Filter by Caught Status
- Filter by Favorite Status (and any combination of the above)
- It is a PWA!
- It is being hosted as a static site on AWS!

It has the following issues:
- Nothing refreshes on its own.  This means: 
 - It will be blank when you load the page for the first time and you have to reload to populate it
 - If you mark a Pokemon as caught and immediately try to filter on caught status (without reloading), the setting won't be saved
 - On mobile, clicking the Favorite icon doesn't change until you refresh
- It's kind of.. ugly
 - I'd like to change the color scheme to be closer to Pokemon (red maybe?)
 - Things aren't lined up (favorite star, the search box)

In addition to fixing the issues, I want to add the following enhancements (in no particular order):
- Add iOS icon support for PWA
- Add ability to sort by PokeNumber
- Show count of caught and uncaught
- Add a home page with easy links to the different pages
- Add more information about the Pokemon:
 - Click a Pokemon card to see its evolution
 - Click a Pokemon card to see where that Pokemon can be found
 - Store the Pokemon types on the card
- See which Pokemon are available in a given location (search location and see pokemon)

Overall, I'm really happy with how this has turned out so far.  It was a great first Angular project because it had the components of a big project (front end, data collection, data storage, etc) but was small enough in scope to get done in just a couple of evenings.  It's definitely useable but also has lots of areas for improvement (which means more things to learn!).

The next post will discuss how I made this into a PWA as well as how I got it into the cloud.  

The current version of PokeTracker is on my Github [here](https://github.com/allisontharp/PokeTracker).

_Have questions or suggestions?  Please feel free to comment below or [contact me](/contact/)._