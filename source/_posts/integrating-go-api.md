title: Integrating Go API with Angular
tags:
  - Angular
  - Go
categories:
  - Personal Projects
  - Movie Tracker
date: 2021-02-25 08:00:33
---
In the [previous post](/getting-started-with-go) I built a simple go API that validate's a user's login.  In this post, I'll work on integrating that API with a simple Angular UI.

First, I created the angular template by running the following in the terminal within my root directory (and accepting the default parameters):
```
ng new login-ui
```
Next, test that the project and all packages installed correctly by CDing into the newly created directory, running `ng serve`, and finally navigating to `http://localhost:4200`.

## Cleaning up the project
- Delete the template within `src/app/app.component.html`
- Install boostrap (`npm install bootstrap`)
- Add the stylesheet within the `<head>` component of `index.html`:
```html
<link rel="stylesheet" href="../node_modules/bootstrap/dist/css/bootstrap.css">
 <!-- Bootstrap CSS -->
  <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootstrap/4.4.1/css/bootstrap.min.css" integrity="sha384-Vkoo8x4CGsO3+Hhxv8T/Q5PaXtkKtu6ug5TOeNV6gBiFeWPGFN9MuhOf23Q9Ifjh"
    crossorigin="anonymous">
  <link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.7.0/css/all.css" integrity="sha384-lZN37f5QGtY3VHgisS14W3ExzMWZxybE1SJSEsQp9S+oqd12jhcu+A56Ebc1zFSJ"
    crossorigin="anonymous">
  <link href="https://cdn.jsdelivr.net/npm/bootstrap@5.0.0-beta1/dist/css/bootstrap.min.css" rel="stylesheet" integrity="sha384-giJF6kkoqNQ00vy+HMDP7azOuL0xtbfIcaT9wjKHr8RbDVddVHyTfAAsrekwKmP1"
    crossorigin="anonymous">
```

## Creating the components
I know I will want a component for the actual login object as well as a component that I'll (perhaps incorrectly) call 'home', that will hold the login component.  Calling this component 'home' is maybe a poor choice because it will not necessarily be the actual home page of the website.

To create these components, you can run `ng g c <component name>` within your Angular project's directory.

## Adding the login form
I found a form that has the basic login structure on the [Bootstrap components page](https://getbootstrap.com/docs/4.4/components/forms/).  I copied the code from here into my `src/app/login/login.component.html` file:
```html
<form>
  <div class="form-group">
    <label for="exampleInputEmail1">Email address</label>
    <input type="email" class="form-control" id="exampleInputEmail1" aria-describedby="emailHelp">
    <small id="emailHelp" class="form-text text-muted">We'll never share your email with anyone else.</small>
  </div>
  <div class="form-group">
    <label for="exampleInputPassword1">Password</label>
    <input type="password" class="form-control" id="exampleInputPassword1">
  </div>
  <div class="form-group form-check">
    <input type="checkbox" class="form-check-input" id="exampleCheck1">
    <label class="form-check-label" for="exampleCheck1">Check me out</label>
  </div>
  <button type="submit" class="btn btn-primary">Submit</button>
</form>
```
Next, I added the reference to this component within the home component:
```html
<app-login></app-login>
```
If you are unsure about the name of the component reference, it is located within your <component>.component.ts under 'selector'.

Finally, I linked the home component to the default routing path (https://localhost:4000) by adding the following to the `src/app/app-routing.module.ts` file: 
```ts
const routes: Routes = [
    {path: '', component: HomeComponent}
];
```

## Update the format
At this point, I have an ugly webapp (seen below).
![](initiallogin.png)
The client has specified that the size of the login box needs to be 400x400.  There are several places we could accomplish this (for example, within the home.component.html, the login.component.html, or the login.component.css).  I chose to do this styling within the login.component.html file because I want it to always be the same size, regardless of which component calls it.
![](rightsize.png)
Now, I have a form that is the right size (as seen above).  The next step is to center the component per the client's request.  Because I might not /always/ want this to be centered, this definition will be inside the home component:
```html
<div style="min-height: 100vh; display: flex; justify-content: center; align-items: center;">
    <app-login></app-login>
</div>
```
The justify-content will center horizontally, while align-items aligns vertically.  In order to get the item to be centered vertically, I had to add the min-height:100vh.  `vh` stands for 'Viewport Height', in this case we want the minimum to be 100%.

Finally, I set the background color.  I am assuming that the entire app's background color will want to be affected, so I applied the color globally.  To do this, I added the following to my `src/app/app.component.css` file:
``` css
body{
    background-color: #ffffc8 !important;
}
```
and set the encapsulation to none in the app.component.ts file:
```ts
import {Component, ViewEncapsulation} from '@angular/core';
@Component({
    ....,
    encapsulation: ViewEncapsulation.None
})
```
The login page is starting to come together:
![](correctcolor.png)
However, the login box needs to be white.  To override the global background color, I added the following to the `login.component.css`:
```css
.login{
    background-color: white;
}
```
Then, I updated the `login.component.html` by wrapping the entire thing in a `<div class="login">`.  At this point, it is not great looking, but it meets the bare requirements that the client requested:
![](mvp.png)

## Mock the API call
At this point, I do not have the API ready.  I had success with my last progject ([laughtrack]()) by first mocking everything in the UI and then building the API.  Because the API will be written in a new-to-me language (golang), this seemed like a really great idea.

First, I created a new directory within `src/app` called `models`.  I anticpate only having one model for this project, but it's possible it will grow.  I'd like to have it in its final place in case this grows.

I started by trying to get the console to log the input when I hit submit.  I found that it kept coming back as `undefined`.  I then remembered that I needed to import the FormsModule within the `app.module.ts` file, which I forgot to do previously.  Runniing `ng build` followed by `ng serve` fixed this issue.

I created a service called `ApiService` (`ng g s ApiService`).  Currently, this has just one method, validateLogin, that just checks if the username = '2@2.com' and the password is 'qaz' (because that's easy to type when testing manually!).  The goal here is to be able to mock the backend to make sure the front end is working.  I added a red error that says 'invalid username or password!' when the password is not valid.  At this point, I believe the front end is mostly done.

## Integrating Local GO API 
Because we previously mocked up the API call, pointing it to the actual API is easy.  I added an apiUrl_Login variable within my `environment.ts` file that currently the value 'http://localhost:10000'.  The api service also is was updated to be:

```ts
validateLogin(login: Login): any {
  const url = `${this.apiUrl}/login`;
  return this.http.post(url, login, {responseType: 'json'});
}
```
This function was previously just checking against the hardcoded username and password and was passing a true or false.  As seen in the previous post, the actual API will send a status code back that tells if it is a good or bad request.  Therefore, the login submit button also needs a slight update:

```ts
submit() {
  this.login = {
    username: this.loginEmail,
    password: this.loginPass,
    token: this.loginToken
  }

  this.apiService.validateLogin(this.login)
  .subscribe(
    (r) => {
      window.location.href = environment.redirecturl;
    },
    error => {
      this.error = true;
    }
  )
}
```

## Next Steps
One of the next steps of this project would be to deploy the API to an environment (potentially via serverless).  If this were something I were working on for myself or a real client, I would also want to make the component a modal that pops up when requested and I would want to ensure the username and passwords are securely passed and stored.