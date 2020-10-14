---
layout: single
title:  "Bug do Log4Net me fez perder um tempão hoje"
date:   2020-10-14 16:48:00 -0300
categories: log4net bug log4mongo englishposts
---

**TL;DR:** log4net + log4mongo problem, ended up not using log4net

So, I'm a coding a new log system for some stuff at work,
as always, I decided to go with Log4Net + Log4Mongo NuGet packages.

It works, I know it good enough to feel comfortable tweaking it, etc.

After some hours, I tested and everything was fine locally, I pushed the code to a Docker + Fargate combo
and then the head scratching began. After deploy, everything worked but the logs.

### Logs weren't showing up, but exceptions weren't showing up either

After a while, I was certain that I was missing something in log4net.config or maybe I forgot again to add Microsoft.Extensions.Logging.Log4Net.AspNetCore again. Maybe the file was somehow misplaced? Maybe I forgot something in Startup.cs?

My steps were:
* double check log4net.config
* double check NuGet packages and versions

### Next step was asking around, because frankly "it works on my machine"

Questions:
* Does this Docker file do anything out of ordinary?
* Where exactly is this being deployed?
* How much the production environment is different from my local environment?

I got an idea from the person that actually did the push to Fargate, he reminded me that we were deploying in **Linux**.
 
### Console.WriteLine() Everywere!

You know the drill. It wasn't the first time that Log4Mongo was acting up, I was pretty sure that I at least would get the other appenders to work, and hopefully get some data on CloudWatch.

Guess what? Something popped up in the log: 

    PlatformNotSupportedException
    Windows Principal functionality is not supported on this platform

### Time to Google

I can't stress how important this is, however, I got to find a lot of stuff that was *almost but not exactly* what I wanted. there one Stack Overflow post that managed to ask the question I had, but had no answers.

Then I decided to check the issues at GitHub annnnnd...

### Guess what? It's a very very specific bug within Log4Net

Here it is:
https://issues.apache.org/jira/projects/LOG4NET/issues/LOG4NET-652?filter=allopenissues

The idea is:
* you have to use custom fields with Log4Net
* you are deploying to Linux (non-Windows machine, actually)

Frankly, I just read the issues and the comments enough to understand how much of a problem I was facing.
There was one guy in the comments that did a pull request with a fix that wasn't merged (at this post's date).

I even considered building Log4Net locally or even shadowing the classes with some stinky code, but after considering that A: It was jerry-rigged solution that I needed fast and B: I still had a ton of modules to work on.

I decided to go the easy way and just remove Log4Net and log everything from scratch.
