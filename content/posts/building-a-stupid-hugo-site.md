---
title: "Building A Stupid Hugo Site"
author: "Paul Doom"
date: "2021-04-28"
---
![Hugo](/images/hugo-logo.png)

It is astounding what can be done with a modern static web site.   With the right
tools and a little work, you can create an online presence and serve it up
for a fraction of the price of a virtual server or other hosting option.  After
all: it's _just files_!

This detailed step by step tutorial will help you create your own static site!
## Why?

[Hugo](https://gohugo.io/) ships as one Go binary and looks pretty sweet, so why not?
It is also apparently really fast, which is particularly important when you may have
up to 10 pages to build some day.

(Notice how I completely skipped explaining the problem I want to solve?
Is "content no one wants to read is not online" a __problem__ per se?  Let's not
overthink it.)

## Answer the Most Important Question First

When building a web site, there is really only one engineering choice
that must be made up front.   Many factors go into the decision, but when all
is said and done, this one choice will decide the ultimate fate
of the site.

Of course I am speaking about choosing which free theme to use.

I chose [Terminal](https://github.com/panr/hugo-theme-terminal) cause
it has a certain spartan old school savoir faire.  Also it upper cases
everything.  I am extremely loud IRL so it accurately reflects what is
is like for me to talk to/at you.

## RTFM

You thought this was a HOWTO?  Nope.  No one said it was anything like
that.  Go here: <https://gohugo.io/>  Return when you have learned something.

## Change from TOML to YAML

Next I changed `config.toml` to `config.yaml` because I work with _infrastructure_
so I do everything in YAML.  This has the added benefit
of making it impossible to cut and paste TOML configuration examples, thus
fulfilling the overarching value proposition of any project I work on: _Minimum Function.  Needless Complexity._
## Get bored

I wrote an [about](/about/) page.  It stinks.   Not even half interested in finishing
this post.

## Critical Decisions Part Two

Whatever, so now I had a site.  (`hugo server` to preview then
`hugo` to build static HTML under `/public`)

More critical decisions needed to be made:

* Where to host?  On my old DigitalOcean instance?  Or maybe just out of a
  freaking object bucket...?
* How to deploy the site?  Terraform it?

Before answering either of those questions my finely tuned engineering mind
caught a flaw requiring immediate attention:  "Paul, I am already sick of this theme."

## Change Themes Again

Despite the saccharine name, [Hello Friend NG](https://themes.gohugo.io/hugo-theme-hello-friend-ng/) looks nice.  In it went...

## Deploy to DigitalOcean Spaces

I copied the `public/` directory up to a DigitalOcean Space, setup their handy
CDN and a DNS CNAME with a few clicks, and alakazam!  It was online!  And broken.

Spaces does not serve `index.html` as the default for
a directory.  After exhausting all other options, I relented and
opened a classic Paul style support case which can be summarized
as: "I already have an answer to my question, and it is that your product
does not meet my need.  I am very very disappointed in you."

DigitalOcean support replied with a courteous and utterly useless response about
how I could use their PaaS service instead, pointing out its simplicity
and how other customers were happy with the solution.

I was not the least bit interested.  As a tech saavy customer, I want choices.
I want so many choices I don't have to be expected to
ever complete a task because I am so busy pondering all the choices.

I didn't want to run anything.  It is a static site.  No servers!*

(*I was actually just being cheap.  I would spend 1000 hours to
avoid $10/mo.  It makes sense.  If you disagree, perhaps you should go learn
a bit about **business**.)

## OK, Deploy to AWS

Off to the land of choice and freedom.  Setting up a static site on AWS is easy!  You simply:

* Create a private S3 bucket in a region other than `us-east-1` (EVERYONE uses `us-east-1` making it
  the least cool region)
* Create an OAI (origin access identity) and give it read access to the bucket
* Create an ACM certificate
* No, first create a DNS record to validate domain ownership....
* Then create an ACM certificate
* Create a CloudFront distribution
* Recreate the ACM certificate in `us-east-1`  Can't you read documentation?
* Upload content to S3
* Upload content to S3 with the correct content-types
* Realize that CloudFront only serves `index.html` from the root and not subdirectories
* Die inside (a little)
* Research and find a nice Lambda@Edge solution for forwarding subdirectories to their
  child `index.html` files
* Zip the Lambda code
* Deploy the Lambda code
* Redeploy the Lambda code to `us-east-1` (When will you learn?  CloudFront loads everything
  from `us-east-1`)
* Reconfigure CloudFront to use the Lambda
* Reconfigure CloudFront to redirect HTTP to HTTPS
* Wait an hour for some reason... Sometimes modifying CloudFront takes a longgggggg time.
* Oh, and you did set up logging destinations for all of the above, right?

The power of AWS is knowing that after sufficient suffering you WILL get something working.
You may not be able to explain it to anyone, and if you are not careful it may cost a car payment
every month, but you will get something working.

Also, I did all of this in Terraform.  I don't write steps.  I declare the world I want then make it so.
I am a civilized human who uses Infrastructure as Code to make my life... *uh... better? Not exactly... Easier?  Ha, no...*
To make my life **intellectually stimulating**.

Check out the code here: <https://github.com/pauldoom/vn-infra>  It includes 
two stacks:

* `account` - AWS account wide resources, like the S3 buckets for logs,
  Terraform state, and also the root Route53 hosted DNS zone
* `web` - Static hosting site using S3, CloudFront, and Lambda@Edge

## Themes... Again

I changed my mind and switched to [Beautiful Hugo](https://github.com/halogenica/beautifulhugo)
as it is more tasteful and I want to pretend to be sophisticated.

Maybe I will try to modify it later and break it horribly, then maintain
a broken fork for a few years, then open a giant PR back to the project
that could only be merged by the best coder from and advanced alien civilization.
## CI/CD

Clearly a site no one reads deserves a full featured CI/CD system, and
needless to say, you would not use some common off the shelf solution
like *GitHub Actions*.  Yawn.

No, you would run Kubernetes on your workstation and GitLab on top of that,
and that is precisely what I did.  (*There is actually a good reason for this,
and it keeps me sharp for work, or at least that is the lie I tell myself.)

How do you do that?  That's a story for another day.  For now, just see
the `.gitlab-ci.yml` CI/CD definition file in <https://github.com/pauldoom/vn-web>

It takes care of building new content, pushing it to S3, and invalidating the
CloudFront cache to start serving the new stuff.

## Lessons Learned

In the end, was it worth it?

No.  I could have used a billion other more efficient services to publish this "content".

It's not about the destination.  It's about the journey, and the journey was, well... OK,
the journey was not quite worth it either.  I should have written an application or something
to sharpen skills someone might still care about in 4 years.   Instead I published
this static site.  Just files in an object store... that I pay for every month.

See you next time!
