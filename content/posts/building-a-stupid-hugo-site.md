---
title: "Building A Stupid Hugo Site"
author: "Paul Doom"
date: "2020-05-15"
---

## Step 1 - Why?

[Hugo](https://gohugo.io/) ships as one Go binary and looks pretty sweet so why not?


## Step 2 - Answer Most Important Question First

When building a web site, there is really only one important choice
that must be made.   Many factors go into the choice, but when all
is said and done, this one choice will decide the ultimate fate
of the site.

Of course, I am speaking about choosing which free off the shelf
theme to use.

I choose [Terminal](https://github.com/panr/hugo-theme-terminal) cause
it has a certain spartan old school savoir faire.  Also it upper cases
everything.  I am extremely loud IRL so it accurately reflects what is
is like for me to talk at you.

## Step 3 - RTFM

You thought this was a HOWTO?  No.

## Step 4 - Change from TOML to YAML

I changed `config.toml` to `config.yaml` cause I work with infrastructure
so I am supposed to do everything in YAML.  This has the added benefit
of no longer being able to cut and paste TOML config examples, thus
fulfilling the first requirement of any project I work on: _Needless Complexity_ :tm:

## Step 5 - Get bored

I wrote an about page.  It stinks.   Not even half interested in finishing
this post.

## Step 6 - Critical Decisions Part Two

Whatever, so now I have a site.  (`hugo server` to preview then
`hugo` to build static HTML under `/public`)

More critical decisions to make:

* Where to host?  On my old DigitalOcean instances "barge"?  Or out of a
  freaking object bucket...
* How to deploy?  Terraform it?

But before answering either of those questions my sharp and focused
engineering mind reminded me:  "Paul, I am already sick of this theme."

## Step 7 - Change Themes Again

Despite the saccharine name, [Hello Friend NG](https://themes.gohugo.io/hugo-theme-hello-friend-ng/) looks nice.  In it goes...
