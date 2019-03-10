---
title: "Creating this blog with jekyll"
date: 2017-08-01
tags:
  - "static site"
  - "blog"
  - "jekyll"
---


[Jekyll](http://jekyllrb.com) is good.  Docs are decent, user guides are swell, overall not bad.  I used this [guide](http://jmcglone.com/guides/github-pages/) and it helped to get the file structure figured out.  Quick tip, just use the command `$jekyll serve` to get a local version up and running.  Jekyll will watch for changes by default so once you're done with the guide you can just mess around locally.  My advice is to pick a theme and run with it, don't waste a week getting all the pixels to line up.

## Command Line Cheat Sheet ##
`$ jekyll serve` - starts a local server so you can mess with your site real time

## Edit 2017-12-29
`$ jekyll serve` has started returning this:

```terminal
rubygems.rb:291:in `find_spec_for_exe': can't find gem jekyll (>= 0.a) with executable jekyll (Gem::GemNotFoundException)
```
As a solution I now run: `bundle exec jekyll serve`.  Further reading at [this issue](https://github.com/jekyll/jekyll/issues/6510) on github.

`YEAR-MONTH-DAY-title.MARKUP` - format for a new post file under the `_posts` directory.


## Applications at Work ##
At work we're constantly moving knowledge around, basic, essential, institutional knowledge that keeps a company running.  Without it, without certain people, we'd fall apart and take weeks or months to recover.  Even today I'm discovering things that my coworkers knew but didn't know that I needed to know.  If I could back a single improvement it would be to give Jekyll access to my whole team and dedicate 30 minutes a week to building a single subject internal-use-only guide.  With that guide we could instantly ease any concerns about who was on call when or who was available to help another team with an issue.  If we acted as gatekeepers for our knowledge base with the other departments we could collect and assist with every layer of the architecture.

## Applictations at Play ##
Without Jekyll and Github pages I may not have started documenting my work.  Signing in to a blogging site or setting up a simple S3 bucket sounds like tedious work.  I'm already overloaded with bookmarks and sign in pages; here I just `$ git push origin master` and I'm in business.  Its beautiful in its simplicity.  I see why some legal firms use Git version control for their documents.  I almost don't want to work on a team document without version control anymore.  But back to my main point, this has changed how I work on personal and work projects.

I no longer just work, and work, and work mindlessly for hours.  I take breaks and jot down what I just worked on for an hour.  I draw diagrams so I can refer back to them and write down what I was thinking.  I can now look back and review my thought process without a subjective memory telling me, "Yeah that was a really good job.  Keep doing that."  Now I can force myself to build things with documentation which I think results in the end product being more user friendly.  If I have to explain it to myself I should be able to explain it to someone else right?  

TLDR; Give Jekyll a try.  Really good stuff.


