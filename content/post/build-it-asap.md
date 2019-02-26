---
layout: post
title: "Build it ASAP"
date: 2017-12-29
---

When I program in my spare time I often listen to music.  Much of the music is sourced across a variety of hosting platforms and media sharing waterholes so for ease of access I bookmark each song in one central folder.  This has led to a large list of bookmarks spread across platforms like Soundcloud and Youtube.

To centralize my listening experience I used Electron to build a giant queue that simply plays every song in the list.  I threw in a search bar and some controls and voila I can get to any spot in the list quickly!  The below image is the app running in the background.

![local-music-player]({{ "/assets/local-music-player-working.png" }})

I worked on this for only a couple weeks during my spare time but each time I discovered something new or added a new feature.  It was fun, I would research on how to add the feature I wanted, look at docs, build it then test it in the development version.  Each time I was pleased that the feature worked and moved on to the next feature.

Then a couple days ago I finished!  Anytime you clicked a song autoplay would start, when the song ended the next would start, or you could click prev or next, and there was a cool search feature.

The time had come to...

# Yarn Build

The app built fine, I fired it up, and then nothing!

![youtube-block]({{ "/assets/embeded-youtube-blocked.png" }})

# Why?

This happened because in Electron's dev environment a local server is used.  When a production app is used it simply opens up the index.html directory.  When the embedded youtube player accesses the bookmark's URL youtube expects an origin referer URL.  The local server in Electron's dev mode provides that but the production app doesn't.

# How could this be avoided?

Within two minutes of adding the youtube player I could have found out that there was a big snag in my plans.  Gliding from song-to-song wouldn't be so easy when 70% of the content was inaccessible due to a malformed api request.  Since youtube requires a referer URL a workaround is needed.  Maybe even a restructuring of how the youtube bookmarks are played!

But now I've programmed myself into a corner and built functionality around the events the youtube API emits.  I may end up moving away from youtube's api and this will have been a few days worth of work for nothing.

# The silver lining
The good news is that I can take this lesson to heart.  As soon as you have a basic environment up and running setup some tests and continuous integration!  That way when you merge a feature into master you can put it into production immedieately and you can avoid snags like this early on.