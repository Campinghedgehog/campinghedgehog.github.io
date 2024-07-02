---
layout: post
title:  "Why I like my job" 
date:   2024-06-15 10:40:33 -0700
categories: 
---

Writing this down before I forget.

Some parts are vague because NDA or something.

## What I did
My time at Amazon started with an internship in the summer between my junior and senior year of college.
Getting the offer was a little bit of a surprise.
The recruiting process for the previous summer resulted in exactly nothing, so my expectation were thoroughly tempered by the time the next recruitment cycle came around. 
For whatever reason, the bar seemed to be much lower than the previous year; I was asked very basic first-quarter CS curriculum quesitons about lists, reversing lists, and recursively traversing lists.
Though it may have also been due to a much better interviewer. The previous interviewer asked me to implement a hashtable from scratch -- in C. I didn't quite have a list of collision free hash functions off the top of my head, so that was a little difficult. 
Anyways the interview went well and I haven't done another one since.

I was assigned to the silicon team in the [Kuiper](https://www.aboutamazon.com/what-we-do/devices-services/project-kuiper) program (Amazon's Starlink ~~knockoff~~ effort).
The team was reponsible for designing custom silicon for the modems (between the satellites and customer terminals) and digital beam former (DBF) chips (which was responsible for the digital portion of [beamforming](https://en.wikipedia.org/wiki/Beamforming)).
Apparently beamforming is a whole thing in communications and the RF industry. By controlling the delay (or phase) of signals from a set of antennas, one can form and direct a beam through constructive and destructive interference.
Having multiple beams allows for increased throughput; since a single satellites typically covers multiple squared kilometers, and a single user hopefully isn't spread across multiple kilometers, splitting up the area covered into N areas (with N beams) can increase the throughput by a factor of N. Beamforming is also used in 4/5G for similar reasons.

My internship project was to simulate packet traffic on the panel of the satellite from the modem to the DBFs.
The purpose of the project was to verify the specs of the system at the panel level.
The team I was working in was the design verification (DV) team;
DV teams in general are reponsible for verifying the register transfer level (RTL) code (usually verilog) was correct with respect to some set of specifications (usually done in [SystemVerilog](https://www.chipverify.com/tutorials/systemverilog)).
Lucky for me, doing things at the packet-level simplified the task into simulating just the queues, delays, and link jitters (kind of like the variance for how much time it takes for a link to send stuff) with a discrete event simulation framework in python. I did not have to touch RTL.
Due to the packet level granularity, there was only a limited number of things I could do to check that the specs were correct: assertions for queue overflows, and timing of packets, etc. Since it was a discrete event simulation, the "packets" were also timestamped; this allowed us to measure the latencies of the packets with respect to different network topologies between the DBFs. There were a non-trivial number of DBFs on the panel, so an interesting set of topologies was possible. 

This internship was also when I wrote the most documentation I would ever write in my time at Amazon.
At the end of the internship was the internship presentation / meeting. Meetings and presentations are held a little differently at Amazon. Instead of a powerpoint presentation, Amazon presentations start off with ~20 minutes of reading a document that you prepared (called a 6-pager), followed by questions for the remaining of the meeting (the 20 minutes at the beginning is because no one would really read your doc even if you sent it out beforehand). This was foreign and not a style of presentation I was used to (but not one that I mind tbh).
So with a lot of help from my onboarding buddy (I was surprisingly bad at technical writing, perhaps still am), I had my doc all ready, with cool graphs and stuff.
I was told that it would just be a chill presentation with people who were interested in the work that I did.
So I was surprised at the number of people who joined the call. Memory does not serve too well, but I think around 50 to 70 people joined the call; including the director of the silicon org, several principal engineers, and some random engineers from another org (my presentation was scheduled around lunch so I think most people just there for some lunch entertainment). The first 20 minutes of the presentation went quite smoothly (literacy was probably a prerequisite to being hired at Amazon). The next 40 was not as quiet. There was this one senior ASIC designer that was asking these somewhat hardball questions (in hindsight they weren't reall hardball questions, it was just a skill issue on my end) that I did not really know the answers to[^1]. But there were engineers in the call that did know the answers to such questions. This was great except that the questions often lead to prolonged discussions which basically hijacked the presentation. Now, this was kind of on me; I did not, at the time, as an intern, know how to tell these a lot more senior engineers to "take their dicussions offline"[^2], but eventually, my mentor stepped in and helped get the presentation back on track.

I would say that I finished on a strong note. At the end of the day, my project was pretty cool and I was quite enthused to talk about it[^3]. The data collected matched what was expected from what specs. And that was cool to see.

I was offered a fulltime return offer, which I took. I did not really want to spend the extra recruiting effort to shop around for more offers.

[^1]: Now my memory is really starting to fail me but, I sometimes say my thoughts out loud without meaning to. And I remember, at the time, I thought to myself "this guys hates me, i know it, this guy hates me" (dunno why I thought that, but I just did). Since I was presenting I know that my mic was unmuted. This sometimes keeps me up at night.
[^2]: Still wouldn't know how to do that to be honest.
[^3]: I was told by my first manager (after joining full time) that the simulation was later used to validate some timing stuff for the prototype satellites; I'm not sure fully believe him, I think he was just being nice. But if so, that's pretty cool that it wasn't a throwaway intern project.

## What I do
A year later I found myself once again onboarding and ramping up remotely. I was originally supposed to return to the same team as the one I worked with during my internship, but for whatever reason, I was placed into another team (one that worked on the DBFs instead of the modems). 

I did not fully realize it at the time, but full remote onboarding and ramp up is really hard. I did not know who was who, who knew what, and what there is to know. Documention was kind of fragmented; some stuff was on Confluence, other stuff in some Bitbucket repo[^4], more stuff on some SMB server, and some other stuff on Amazon's internal wiki tool. Technically we could go to the office; some people did, but most did not, so that did not help much either.

The team I was on was the systems team for the DBF chip. The systems engineers are RF/DSP people who create a high level[^5] model of the functional aspects of the chip. However, our team doesn't actually implement the designs in verilog; the ASIC team does that; the two teams do work very closely with each other though. 

The first project/task I was assigned was to add a feature to the MATLAB model that transforms the raw output data into a packet. I'm still really not sure what it was for or how it was useful; I think possibly some other teams were able to take the transformed data and feed it into their stuff later on (where they needed packets as inputs). Worked on this for a few months. I really did not know how to test my code. The stuff that was supposed to consume it didn't exist yet; so I mostly sprinkled a bunch of asserts everywhere. 

The second project I was assigned to was to help with the design verification effort of a new DBF chip. The design verification for the DSP stuff was based on matching the raw data from the MATLAB model to the data produced from the verilog implementation, given the same input stream; this is known as bitmatching, a pretty common if not dated methodology in the industry. My job was to dump the data from the model at various checkpoints. I was a little confused at first, because it just seemed like writing some matlab vector to disk. Upon actually working on it, my confusion was quickly cleared up; it was in fact just writing some matlab vectors to disk. Apart from the input signal, the verilog implentation also needed to be programmed with the same arguments and options as the MATLAB model. There were on the order of thousands of configurations, so some software scaffolding needed to be added to the MATLAB model to get that to dump and map to the correct names. All this a few weeks to complete. 

The bulk of the effort in assisting DV was just hunting down bugs. And not the cool ones that has to do with signal processing or the design, but the anemic ones like a mispelled field in a json file, or misconfigured input arguments, etc. MATLAB, being the language (barely) that it is, made it such that bugs could only really be caught at runtime. Our simulations did not take a reasonable amount of time to run. Memory is also an issue with MATLAB (MATLAB does a copy on write inside of a function, and there is really no simple way around it). I will admit, this is trash work; after 20 times of 'fixing' the same type of mispelled-json-field bug, career choices are often questioned. To make matters marginally worse, I am not the one writing the bugs, so I don't have much context to work with; only that something was working a couple weeks ago, and it no longer works. Eventually I got pretty good at fixing MATLAB model bugs. Though I'd really rather be good at other things instead.

Too much of the bugs from the model were irrelevant to the design[^6]. This prompted an effort to setup some sort of CI system for the model. MATLAB once again makes things more difficult than it should be. Because it isn't free -- you need to pay for licenses instead of the other way around -- there is a (private) license server that need to be connected to. I never worked with the cloud or AWS, but somehow managed to cobble together a VPC that handled that stuff. The rest of the CI system was pretty simple; every night we would just run some selected tests on a very large EC2 machine, and send an email to the team with a summary of which tests passed/failed. Nightly regressions helped since instead of a couple weeks worth of commit history, one could just look at a day's worth of commits. What it did not help with was that I was still resident bug catcher. I did not want to be resident bug catcher, was not fun. 

Lucky for me, my manager was hiring a new software development manager, and was putting me under that new team, so there would be no more MATLAB for me.

Unlucky for me, due to changing managers, promotion was delayed by two quarters. Original manager was aiming for Q3 2022, but wanted to give new manager experience in promoting people. Ramp up time for new manager, plus me being stupid and not following up (don't think it was ever communicated from old manager to new one) until Q3 came around, caused delay. The lesson here is of course that you own your promotions (at least from entry to mid level) and should badger (remind) your manager because they can be pretty busy. They are incentivized to promote people who they think can be, so they probably don't mind the reminders. Promo docs also take some time to prepare; feedback needs to be gathered from people asides from your manager, so better to start earlier rather than later. 

Lucky for me, Amazon stock tanked after Q3; so had I been promoted on schedule, RSUs would have been worth a lot less (like 50% less). Instead, end of Q1 was the (local) bottom for amazon stock.

Silver lining, I guess.

### New Team
The new team I was on was supposed to be a "platform" team. I still don't know what that is supposed to be, but in practice, all the software stuff that was not directly related to the DBF firmware was thrown to us. This included IT stuff, imaging computers for the lab, CICD, and other miscellaneous stuff. 

I worked on CICD. The idea of a platform team was that we would be the interface for external customers, so we would handle the CICD (including hardware in the loop testing) and releasing of the firmware artifacts. Originally, I wanted to implement the CICD stuff with Amazon's internal tools for CICD, but for some reason, it was mandated that our CICD things needed to be in a specific AWS account. So instead we used native AWS things (codebuild, lambdas, etc.). Second time around, AWS was a lot easier to use; however instead of using the console for setting things up, I learned to use the Cloud Development Kit (CDK) and all the infrastructure as code things. That was useful to pick up. 

I also dabbled in C++[^7] things. Did some stuff with the host-side code. However most of our existing testing code was written in python, so I was also able to get familiar with C++ to python bindings as well.

Our team was also tasked with creating a database library thing that handled some data that the chip produced. This actually took quite some effort. My coworker who was in charge of this effort chose to basically implement a database from scratch. I have to say, this is not what I would have done. Databases are kind of hard, and across buffer pool managers, query optimizers, B-trees, etc., there is a lot functionality that needs to be reimplemented if starting from scratch. I had suggested using SQLite as a backend, to at least have a reference/benchmark. I would say that we prematurely abstracted, and provided far more generality that was needed, and I don't think this is in hindsight; we knew as much then as we know now in terms of how it was going to be used. A lot of time and effort was spent on dealing with backend (database, or lack thereof) issues. Learned to use protobuf and GRPC in the process. Also learned that a schema version should be included in the schema definitions; so many bugs were due to mismatched versions, and there's no simple way to tell once compiled.

Hardware in the loop, and working with hardware was interesting. It was fun when it worked, but horrible when it did not. What I took away is that the team that creates the hardware and the teams that use it should be very close together in the org chart. There is a lot more to be said about that, but I won't.

### Grey Geese
A year into the new team, we were mandated to move off of the orignal mandate of having our CICD things in a specific AWS account, and to migrate to Amazon's internal build tool/environment. So this means the CICD things we built during the last year would be deprecated. I actually did not mind since the native AWS stuff kind of sucked anyway, but I just kind of wished that we could have built our CICD with the internal tools in the first place. I had vokda for dinner that night.[^8]

Amazon's internal build tool was pretty interesting. It allowed for the number of code bases, packages, and developers to scale to a very large number. Why one would want that is another question[^9].

Learning the build tool was okay. It's not unlike other build systems, but it just aims to be very general, very customisable, and able to accomodate any software package one would need. It really kind of feels like Cargo (Rust) but for any package in Amazon's system. It really doesn't manage the building, but rather just the dependencies. Migrating our repositories was straightforward, just a little tedious, as all dependencies needed to be made compatible with the build tool as well. The not so fun part was occasionally falling into dependency hell. Along with the build tool, there is a thing that gathers packages and their versions, let's called is a VS. Whenever you build any code, it is built with respect some VS. Whenever you build something new into a VS, all the dependencies of that new package (as well as the dependencies of the packages that the new package depends on) is rebuilt to ensure compatiblity. And sometimes things just don't work, and it's hard to figure out why because there could be tens of thousands of package in a VS. I once ran into a build failure where a rebuilt package was failing to build; it turned out to have *nothing* to do with the dependencies changing, but rather because the tests (and thus build) was dependent on time; the tests used some dummy certificates (with an expiration date) that were manually checked into the repo, so rebuilding the same version some time later caused the tests to fail. That bug took a good couple hours to figure out (it did not cross my mind that someone would check in dummy certificates into a repo, rather than just generating them at build time, especially since they were dummy certs...).

The rest of the migration was just setting up pipelines, which reduced down to CDK things and debugging deployments. Did not really learn anything much here; mostly just hunting down orphaned resources.

I will say, Amazon's internal tools made spinning up software pipelines (CICD stuff + releasing etc. basically) very straightforward, at least compared to doing so on native AWS. The difference would be that the whole ecosystem is really managed as a monolith; building the VS's is handled remotely. All code that is built is also all stored internally (3rd party code is mirrored). This only works at scale. A very large scale. All the repos would need to be maintained, and the space needed to host that much code would probably be very expensive. 

The downside to such great tooling is that it would probably be very hard for Amazon to scale down. The internal build tool is really kind of a very micro-servicy tool; it makes it easy to split stuff up. The merits of microservices vs monoliths are probably just post hoc explanations of its prevalence. The prevalence of microservices could probably just be explained by PDD[^10], or "promotion driven development". Promotions are usually given to those who show impact; simplest way to *show* impact is to own a service end to end (writing the code, testing, releasing, CICD). But not everyone can own a service... unless they could. Amazon's tooling kind of makes it super easy to own a service. Each VS is associated with its own pipeline. Deploying is also not too hard. It would take maybe 30 minutes to an hour to spin something up. It's easy for one person to maintain a small number of VS's and pipelines. From personal experience, it gets very tedious very quickly to maintain a larger number of VS's and pipelines; would not recommend. Something something... [Conway's law](https://en.wikipedia.org/wiki/Conway%27s_law) something something. Seems to me, in my limited experience, that a lot of software problems -- and their ad hoc solutions -- often mask larger orgnizational/structural issues; refactoring orgs probably take a lot more effort.


## Work Life Balance
Work life balance tilts very much in the direction of life.

Amazon has a reputation of being a sweatshop, even for software people. This is probably more true for AWS (and generally for cloud providers), but I don't see it, at least for Kuiper. I would say in expectation it's pretty chill at Kuiper. There are a few teams where it can get very busy, but that's usually in bursts (usually trying to meet an external deadline, e.g. for mass production or something). 

That said, I would personally prefer a place with less balance, but a much larger and prolonged gradient of learning. I would say I learned quite a bit in my first year here, but that plateaued pretty quickly. Or at least the learning opportunities were not quite in the direction I'd like them to be.

The great work life balance did provide for a lot of time to learn other things, however. I think I made okay use of that time.[^11]

## Get to the Point!
Long winded an roundabout way just to say, I like my job because:  

It's chill.

Learned some stuff.

Compensation is okay.[^12]

Worked with solid engineers, worked for solid managers.[^13]

4 out of 5 stars.

That said, I promoted myself to customer just yesterday.

Was about time to close this somewhat uneventful chapter. Gradient of excitement pointing elsewhere.

繼續煮飯 :rice_ball:
<br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br /><br />

#### Footnotes
[^4]: Why an Amazon team (or anyone for that matter) used Atlassian products is beyond my understanding
[^5]: MATLAB :upside_down_face:
[^6]: It should and I think can -- with better tooling and an actual language -- be much closer to 0
[^7]: I initially did not like C++, it felt like Rust with no guardrails. But now I can see the appeal; it's nice if you know what you are doing. Still feels a bit dated though
[^8]: No, actually, I did not; just wanted to shoehorn in a pun between the subtitle and migrations...
[^9]: Unclear whether scaling headcount that much is a categorically good thing, especially in software
[^10]: not the rapper
[^11]: 讓我煮飯!
[^12]: More than Microsoft, Apple. Around the same or a little less than Google. Less than Facebook, Netflix, new FANGled startups, quant firms, NVIDIA (if you joined some years ago)
[^13]: Not people managers, but great engineers turned managers out of necessity. Unfortunately, seems like these gems often choose to go back to being individual contributors (ICs) once orgs (and consequently intra-org politics) grow. 