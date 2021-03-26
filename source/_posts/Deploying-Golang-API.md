---
title: Deploying Golang API
tags:
  - Go
categories:
  - Personal Projects
date: 2021-02-28 21:52:40
---

The next step in my process of learning Go is to get familiar with deploying an API into a production-like environment.  For this practice example, I am going to deploy my login API written in Go (more info on that API [here](/getting-started-with-go) post.

## Serverless template
According to the serverless docs [here](https://www.serverless.com/blog/framework-example-golang-lambda-support), there are two serverless templates for AWS:

- `aws-go` for a basic service with two functions
- `aws-go-dep` for a basic service using the dep dependency management tool

Their example used `aws-go-dep` but I do not have [dep](https://github.com/golang/dep) installed, so I will use the basic service:

```
serverless create -t aws-go -p loginservice
```

Running this command within your go directory will create a new folder named `loginservice` with two nested folders (`hello` and `world`), as well as the serverless.yml file and a few others.  When I try to compile the function (by running `make` within the `loginservice` directory), I get two errors:
```
no required module provides package github.com/aws/aws-lambda-go/events; to add it:
    go get github.com/aws/aws-lambda-go/events
no required module provides package github.com/aws/aws-lambda-go/lambda; to add it:
    go get github.com/aws/aws-lambda-go/lambda
```
I ran those two `go get` commands and tried to recompile (`make`).  This time, it was successful (I can tell because now there is a `bin` directory and there were no errors).

I then try to deploy (`serverless deploy`) and invoke (`serverless invoke -f hello`).  The output of the invoke gives a statuscode of 200, which is a good sign!

## Updating the template
Next, I needed to update the `Makefile` to look for `bin/login login/main.go` instead of `bin/hello hello/main.go`.  The serverless.yml file also needed to be updated to replace references of 'hello' to 'login'.

I need to modify my existing go api (read more about that [here](/getting-started-with-go)) to follow the same template pattern as the hello and world functions above.  I found that it is actually really nice that they provided two functions.  I was able to keep one function on one side of my editor and update the other (while keeping the reference of the untouched one).


The output of `serverless deploy` gives the function endpoint.  I updated the [login-ui]() environment variable to be the newly deployed function url and reran the function locally.  Initially, I was getting the same CORs errors that I got when I first wrote the API and was running it locally.  I added cors:true to the functions/login/events/http object within the serverless.yml file, redeployed, and tried again.  

I continued to get CORs errors and spent a lot of time debugging and trying to figure out why the response was not including the correct headers.  Ultimately, I found [this](https://github.com/apex/gateway) *amazing* module that let me use my original code (that was working locally) with only one very minor change: 
```go
log.Fatal(http.ListenAndServe(":10000",nil))
```
was changed to 
```go
log.Fatal(gateway.ListenAndServe(":10000",nil))
```

## Timezones
After getting it deployed, I found that my tokens were always wrong.  For instance, I would input "2125" but the expected would be "225".  This was not an issue I had running locally, but it seems the deployed function is getting UTC time.  I updated the GetToken method to specifically get EST time.  In the future, this should be somehow calculated to fit the user's time and/or at least parameterized so it is easy to pass a different timezone.

## Deploying The UI
I wanted this UI to live as a static website on S3.  I followed the directions in my previous post [here](/Building-an-Angular-Web-App-Part-3), which still worked for me (I did need to run `ng build` again before deploying to s3, as I hadn't built in awhile!).

### Adding HTTPS
Unfortunately, S3 does not natively support HTTPS.  Instead, you have to go through CloudFront. 

1. Within the AWS console, go to CloudFront and click 'Create Distribution'
2. On the 'Select a delivery method for your content' page click 'Get Started'.
3. Fill in the Create Distribution information:
	- The 'origin domain name' is the URL you got from S3
	- Under Default Cache Behavior Settings, set the Viewer Protocol Policy to Redirect HTTP to HTTPS
    - Click Create Distribution

This process can take about 15 minutes.  Once the process is done, you should be able to hit your s3 with HTTPS. 

### Adding a TechTrek subdomain
The provided S3 URL is kind of a nightmare.  Instead, I want it to be something memorable and easy to use.  In order to add it as a techtrek subdomain, I need to add a CNAME record over at TechTrek's registrar:
- Type: CNAME Record
- Host: [subdomain name]
- Value: *CloudFront Distributions domain Name (should be [something].cloudfront.net)*

Back in CloudFront, edit the distribution to set the atlernate domain name to your new URL (including, in this case, techtrek.io).

CNAME records can take up to *72 hours*, so be patient with this change.  As far as I'm aware, there is no real way to test either of these changes (HTTPS or subdomains) without just pinging the sites.  Sometimes caching can get you, so it can be a good idea to delete your cache and hard reload.

## Summary
Overall, I think Go has been decently easy to learn.  The most frustrating part of this project was the CORs issues that I seemed to continually have.  However, this has been a struggle for me in the past, so it is definitely something I need to spend more time learning and understanding.

This is the first new language I've learned in awhile (if you don't include things like ADO pipeline yaml).  I'm encourage by how relatively easy it was.  Three years ago, I would have really struggled with most of the things that have been discussed here.  It's encouraging that learning programming continues to build upon itself.  

In my opinion, learning new languages can help you see problems in different ways.  If you only have a hammer in your toolbox, every problem looks like a nail.  It's good to have more than just a hammer.

*Have another language you think I should learn?  [Let me know](/contact)!*