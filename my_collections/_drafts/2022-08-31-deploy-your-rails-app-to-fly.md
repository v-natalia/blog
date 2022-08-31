---
layout: post
title:  "Missing Heroku? Deploy your Rails app to Fly with these steps"
date:   2022-08-30 14:20:08
moment: 30th August, 2022
categories: learning, deploying
permalink: /posts/deploy-rails-fly
bg: push.jpg
---

This post was originally posted [here](https://vnatalia.hashnode.dev/missing-heroku-deploy-your-rails-app-to-fly-with-these-steps)

It's not a secret, Heroku is ending their free tier 2 months from now. If you used Heroku and loved its simplicity for the deployment of Rails apps, then we are in the same boat.
Even if I completely understand the rhetoric of "we are a business, we need money even from the free tier users", I will miss Heroku. In this blog, I will show you how I did to deploy one of my apps hosted on Heroku to [Fly](https://fly.io/).

## **Specifications**
- Rails v.6.1.6.1
- Ruby v3.1.1
- Heroku-20
- Database: Postgresql
- Image Upload & Management: Cloudinary

## **Forget about dynos, let's [Fly](https://fly.io/) in VMs**
Fly.io works by deploying your app on micro virtual machines (VMs) located close to your users, and they do it by creating Docker images of your app that they send to the VMs out there.
> **Wait, so should I know how to use Docker, then?**

Not necessarily, I deployed my simple app without touching Docker.

## **Let's talk about Turboku for a sec**
If you have an app already running on Heroku and you want to try Fly without any hassle I recommend you to use their tool [Turboku](https://fly.io/launch/heroku) which allows you to deploy directly from Heroku in a couple of minutes. If you like it, then you can try deploying directly without using Heroku. The only thing that is needed is to connect your Heroku account to Fly.io. (*as Heroku will end up their free tier, you will need to find another way of deploying your app soon and you need to know that Turboku depends on your Heroku app still running, in other words, this solution is only temporary if you are trying to get rid of Heroku).

## **But, I liked to deploy from the command line**
Yeah, me too. That's why I used [**flyctl**](https://fly.io/docs/flyctl/installing/) the command line tool from the people of Fly. Follow the link above for instructions to install int in your favorite OS.

After installing and in order to create the fly app, use the command:
```
fly launch
```
This will scan the code and detect automatically that is a Rails app.
It will prompt you to select an app name(needs to be a unique name), and the region of the world closer to you (form a list). It will create the config file 'fly.toml' and it will ask you if you want to set up a Postgresql database (and I said yes!). To configure my Postgresql database, I chose the custom configuration with a cluster size of 1, a VM 'shared' of 256 MB (to continue using the free tier) and volume size of 1 GB (bigger volumes will ask you to add your credit card to the account).
After that, it will set up and connect your database to your app and will prepare your app for deployment.

## **Let's deploy**
If everything was successful, then you can follow to deployment.
Else, check the troubleshooting section below
```
fly deploy
```
This step will build the Docker image, push it to Fly, and run your database migrations. If you get a message like the one below, then your app is ready!!

![Terminal: Starting clean up. => Monitoring deployment.  1 desired, 1 placed, 1 healthy, 0 unhealthy health checks: 1 total, 1 passing --> v1 deployed successfully ](../assets/images/fly.jpg align="left")

## **Troubleshooting**
> So, great Natalia, but did everything run smoothly?
> Well, no, what is development if everything runs smoothly on the first try. lol!
That's why I put together this small guide.


- Some assets didn't load correctly and the launch failed with error: "Error failed to fetch an image or build from source: error building: unexpected EOF”
I used Cloudinary and Active Storage for my assets and for some reason one of my pictures was causing an issue. Thanks to the forums of [the community of Fly](https://community.fly.io/), I found a solution that worked.
Seems like it is an error due to the remote Docker builder, that we can fix with this experimental feature:

```
fly wg websockets enable
```

I also tried to deploy using ```fly deploy --remote-only``` as I don't have Docker installed /ocally, and it worked, but I encountered another error(when I fixed that error, I didn't meed to deploy with the 'remote-only' flag. More about that error, below).

- Add the gem '[net-smtp'](https://github.com/ruby/net-smtp) to your Gemfile.
After fixing the issue of the assets I got a pretty straightforward message from the terminal telling me that I didn't have the gem 'net-smtp' which was completely true. So I opened the Gemfile and added this line:
```
gem 'net-smtp'
```
And then ran ```bundle install``` to install the gem.

- But nothing is so easy in life and then I got **LoadError: cannot load such file -- net/smtp** and **Unable to load application** errors.

It seems that it came from [an incompatibility between Rails 6 and Ruby 3.1.1](https://stackoverflow.com/questions/70500220/rails-7-ruby-3-1-loaderror-cannot-load-such-file-net-smtp), so my bad. The fix is not an update to Rails 7 but simpler than that and it takes just to correct the line above in the Gemfile, to this:
```
gem 'net-smtp', require: false
```
and while you are there, add also:

```
gem 'net-imap', require: false
gem 'net-pop', require: false
```

Because I realised later that it was the reason why it kept throwing me errors. And then run  ```bundle install``` to install the gems, so you can successfully deploy.


- Don't **forget** to add your secret keys.
For that follow the [guide](https://fly.io/docs/rails/getting-started/migrate-from-heroku/) . If copying your Heroku secrets doesn't work (mine was reading quotes wrongly), you can use:

```
fly secrets set CLOUDINARY_URL=IwillNOTgiveyoumykey@cloudinary
```
To check if they were correctly set up
```
flyctl secrets list
```


- Don't forget to check the logs!!

For any other errors, don't forget to check the logs to get some clues.
```
flyctl logs
```

## **My first impressions**
As a first-time Fly :P I enjoyed the trip, the docs are good and the process is straightforward.
Give it a try and let me know what you think.

Thanks for reading and happy deploying!
