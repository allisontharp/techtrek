---
title: 'Building an Angular Web App: Part 3'
tags:
  - Angular
  - Pokemon
categories:
  - Personal Projects
  - PokeTracker
date: 2020-03-10 09:46:22
---

In the [previous posts](/Building-an-Angular-Web-App-Part-2), we looked at pulling, storing, and displaying the Pokemon data.  In this post, we'll look at pushing the web app to the cloud and making it a PWA.

This post will assume you have an AWS account and have at least some experience with it.

Create AWS S3 Bucket
--------------------
Head to the AWS console and go to Services -> S3.  Create a new S3 bucket by clicking Create Bucket.  Give the bucket a name and choose a region.

Under Configure Options, I left everything at their defaults.

Under Set Permissions, uncheck Block all public access.  Click next and finally Create Bucket.

After the bucket is created, we need to set its policy.  Click the bucket and go to Permissions -> Bucket Policy.  Paste the following in the editor:

```json
{
    "Version": "2008-10-17",
    "Statement": [
        {
            "Sid": "AllowPublicRead",
            "Effect": "Allow",
            "Principal": {
                "AWS": "*"
            },
            "Action": "s3:GetObject",
            "Resource": "arn:aws:s3:::<YourBucketNameHere>/*"
        }
    ]
}
```

Be sure to replace `<YourBucketNameHere>` with the bucket name you created.

You should now be able to see your static page at this address: http://<YourBucketName>.s3-website-<YourRegionName>.amazonaws.com.  You can also see your exact URl by going to Properties and clicking on Static Website Hosting.

Deploy the Angular Web App
--------------------------
Assuming you already have the aws CLI and your SSH keys stored, you can deploy your Angular web app by running:

```
aws s3 cp dist/<AngularProjectName> s3://<BucketName> —recursive
```

For me, this looked like:
```
aws s3 cp dist/Poketracker s3://poketracker —recursive
```

Hitting the URL should now display your web app!

Any time you make further updates that you want to deploy to the web app, you can simply run the command above to update it on AWS.

Making it a PWA
---------------
A PWA (or Progressive Web App) is an application that uses a hybrid of standard web browsers and pages as well as mobile applications.  Its essentially a website that behaves and looks like an app.  It allows developers to make one thing (a website) yet have their app look and feel like an Android or iOS app on mobile.  

In order to 'install' the app on their phone, the user simply goes to the page and adds a bookmark to their home screen.  My favorite PWA is (Nomie)[https://open.nomie.app/].

To make our Angular app a PWA, we need to install the necessary package:

```
ng add @angular/pwa --project PokeTracker
```

Rebuild (`ng bild`) and redeploy (`aws s3 cp dist/Poketracker s3://poketracker —recursive`).  Then, go to the site on your mobile device and add a bookmark.

iOS will not use the icon settings in the manifest file that the @angular/pwa package creates.  Instead, by default, the icon that iOS generates will be a picture of the site (and it looks awful!).  To fix this, add a .PNG image to the /assests/icons directory in your project.  Then, in the `<head>` section of `index.html`, add:

``` html
<link rel="apple-touch-icon" href="./assets/icons/pokemon.png">
```

Remember to rebuild and redeploy to get this to work.

_Have questions or suggestions? Please feel free to comment below or [contact me](/contact/)._