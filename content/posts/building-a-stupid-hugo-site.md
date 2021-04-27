---
title: "Building A Stupid Hugo Site"
author: "Paul Doom"
date: "2020-05-15"
---
It is amazing what can be done with a modern static web site.   With the right
tools and a little work, you can create an online presence and serve it up
for a fraction of a virtual server or other hosting options.

This step by step guide will help you create your own static site!
## Why?

[Hugo](https://gohugo.io/) ships as one Go binary and looks pretty sweet so why not?
It is apparently really fast, which is particularly important when you may have
up to 10 pages to build some day.

(Note - Notice how I completely skipped addressing the problem I want to solve?
Is "content no one wants to read is not online" a __problem__ per se?)

## Answer the Most Important Question First

When building a web site, there is really only one important engineering choice
that must be made.   Many factors go into the choice, but when all
is said and done, this one choice will decide the ultimate fate
of the site.

Of course, I am speaking about choosing which free off the shelf theme to use.

I choose [Terminal](https://github.com/panr/hugo-theme-terminal) cause
it has a certain spartan old school savoir faire.  Also it upper cases
everything.  I am extremely loud IRL so it accurately reflects what is
is like for me to talk at you.

## RTFM

You thought this was a HOWTO?  No.  Go here: <https://gohugo.io/>
Return when you have learned something.

## Change from TOML to YAML

I changed `config.toml` to `config.yaml` cause I work with infrastructure
so I am supposed to do everything in YAML.  This has the added benefit
of no longer being able to cut and paste TOML config examples, thus
fulfilling the first requirement of any project I work on: _Needless Complexity_ :tm:

## Get bored

I wrote an about page.  It stinks.   Not even half interested in finishing
this post.

## Critical Decisions Part Two

Whatever, so now I had a site.  (`hugo server` to preview then
`hugo` to build static HTML under `/public`)

More critical decisions to make:

* Where to host?  On my old DigitalOcean instances "barge"?  Or out of a
  freaking object bucket...?
* How to deploy?  Terraform it?

Before answering either of those questions my sharp and focused
engineering mind reminded me:  "Paul, I am already sick of this theme."

## Change Themes Again

Despite the saccharine name, [Hello Friend NG](https://themes.gohugo.io/hugo-theme-hello-friend-ng/) looks nice.  In it goes...

## Deploy to DigitalOcean Spaces

Simply copy the `public/` directory up to a space, setup CDN and a DNS CNAME,
and alakazam!  It's there!

Then notice that spaces does not serve `index.html` as the default for
a directory and get mad and file a support ticket and get told that
you could use their PaaS service instead.

I didn't want to run anything.  It's static files.

## OK, Deploy to AWS

Setting up a static site on AWS is easy!  You simply:

* Create a S3 bucket that does not allow public sharing
* Create an OAI (origin access identity) and give it read access to the bucket
* Create an ACM certificate
* No, first create a DNS record to validate domain ownership
* Create an ACM certificate
* Create a CloudFront distribution
* Recreate the ACM certificate in `us-east-1`, you fool!   Can't you read documentation?
* Upload content to S3
* Realize that CloudFront only serves `index.html` from the root and not subdirectories
* ARGHHHH!
* Research, and find a nice Lambda@Edge solution for forwarding subdirectories to their
  child `index.html` files
* Zip the Lambda code
* Deploy the Lambda code
* Redeploy the Lambda code to `us-east-1` (When will you learn?  CloudFront loads everything
  from `us-east-1`)
* Reconfigure CloudFront to use the Lambda
* Oh, and you did set up logging destinations for all of the above, right?

The power of AWS is knowing that after sufficent suffering, you WILL get something working.
You may not be able to explain it to anyone, and if you are not careful it may cost a car payment
every month, but you will get something working.

Also, I did all of this in Terraform cause I am not going to remember all those steps!
I am a civilized human who uses Infrastructure as Code to make my life... _uh... better?
Not exactly... Easier?  Ha, no..._  intellectually stimulating.

Check it out here: <https://github.com/pauldoom/vn-infra>

## Step 10 - Themes Again

I changed my mind and switched to [Beautiful Hugo](https://github.com/halogenica/beautifulhugo)
cause it is more tasteful and I want to pretend to be sophisticated.

Maybe I will try to modify it later and break it horribly, then maintain
a broken fork for a few years.
## Step 11 - CI/CD

Clearly a site no one reads deserves a full featured CI/CD system, and
needless to say, you would not use some commoner off the shelf solution
like GitHub Actions.  Yawn.

Nope, you run Kubernetes on your workstation and GitLab on top of that.
(*There is actually a good reason for this, and it keeps me sharp for work,
or at least that is the lie I tell myself.)

How do you do that?  That's for another day.  For now, just see
the `.gitlab-ci.yml` CI/CD definition file in <https://github.com/pauldoom/vn-web>

It takes care of building new content, pushing it to S3, and invalidating the
CloudFront cache to start serving the new stuff.

## Lessons Learned

In the end, was it worth it?

Nope.  I could have used a billion other tools to publish this "content".

The journey on the other hand, well... OK, that was not quite worth
it either.  Should have written an app or something.   This static
site is just files in an object store... that I pay for every month.

