---
layout: post
title: "Maybe I Don't Like Jenkins"
date: 2018-07-30
---

The below really only applies to [Continuous Integration](https://en.wikipedia.org/wiki/Continuous_integration) practices, which is an effor to make your life easier overall.

## Jenkins is great!  But...
I work with Jenkins on what feels like a daily basis at my day job.  This doesn't mean I am a system admin for a Jenkins instance nor am I a release engineer.  I am also not running my QA builds through a Jenkins pipeline or custom configuration.  But I do get to talk to every single one of these people when my company's plugin ( or service ) doesn't perform as expected.  

Or when something weird happens with Jenkins.  

Or when their expected results don't match their actual results.

Or when some error output doesn't make sense.

Or when a common issue with something like SSL certs and the Java trust store pops up.

This translates to me installing and replicating Jenkins configurations locally, lots of fresh new CI pipelines getting created, and I get to compare a lot of what Jenkins does to other CI/CD systems.  I haven't worked at a software shop that _doesn't_ have Jenkins and I suspect that many people can say the same thing.  This seems to be a pipeline and build system standard.  And why shouldn't it be?  It allows near total control of every step of your softwares:
- building

- testing

- packaging

- deploying

- etc, all steps

This is a huge achievement when you look back at the poor souls who had to string together unversioned untested scripts and high touch processes.  But this also means learning about Jenkins itself.   How to get environment variables that tests may rely on.  How to setup intergrations tests or script the setup.  How to trigger builds, when to trigger builds, what to do with builds when they fail/pass.  Then we get to *plugins*.  

This is where we start introducing dependencies into our processes and things go fine at first but then you have to upgrade Jenkins.  Or then you have to debug a plugin that doesn't have very helpful output or verbose error messages.  Then begins the act of bringing out the crystal bill and divining the true origin of your problems.  That's where things get uncomfortable for medium-to-large organizations.

## The But...
Typically in orgs there's an issue with communication and organization. If everything is flowing smoothly and all the teams are functioning at 100% then getting training for your new admins of Jenkins and/or the CI/CD build pipleline is going to be just another day.  Making that training stick will take concerted effort over longer periods of time and that's where things break down.

With Jenkins you are truly the administrator as well as the maintainer of your pipeline.  This means that if your code base becomes more complex or your application develops certain dependencies you have to be flexible and get Jenkins to change with it.  What this really means is *your organization* must be flexible.  You the individual can probably adapt to any change thrown your way but if your organizaiton has become hardened and calcified over time, like a barnacle, then you're not going to have a good time.

You'll be fighting Jenkins configurations, possibly fighting one of the build, test, or deploy steps, or maybe just fighting to share knowledge among your teammates.  Its rough.  And this assumes you have a team!  If you're solo you have to invest your time into getting Jenkins up and running as well as getting the pipeline to work to your benefit.

## Be like water
The only cure to problems like these are really at the hear of your team or organization.  If the team is flexible then their outputs tend to follow.  This means less fighting configuration and more declaration.  Declare what your build or CI process needs and change those declarations as the world around you changes.  At the end of the day most business domains are ruled by change.  New features, new customers, keeping old customers happy all comes down to constant change.

Instead of fighting to keep something local running in house maybe challenge your team to become more flexible and adapt to a public CI/CD offering.  Businesses like:
- [GitLab](https://about.gitlab.com/)

- [Heroku's Pipelines](https://devcenter.heroku.com/articles/pipelines)

- [CircleCI](https://circleci.com/)

- [Travis-CI](https://travis-ci.org/)

- [Atlassian's Bamboo](https://www.atlassian.com/software/bamboo)

These are all great offerings, as is Jenkins, but I've found that being able to adapt to their configurations and letting them take the headache out of each step makes your product more resilient.


## Or be like stone
If you're in the healthcare, banking, or some other incredibly secure domain then I feel for you.  Good luck!
