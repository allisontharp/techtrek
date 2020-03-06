---
title: 'Building an Angular Web App: Part 3'
tags:
- Angular
- Pokemon
categories:
- Personal Projects
- PokeTracker
---
In the [previous posts](/Building-an-Angular-Web-App-Part-2), we looked at pulling, storing, and displaying the Pokemon data.  In this post, we'll look at pushing the web app to the cloud.

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

_Have questions or suggestions? Please feel free to comment below or [contact me](/contact/)._