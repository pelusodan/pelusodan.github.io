---
layout: post
title: "Concentration Game (iOS)"
author: "Dan Peluso"
categories: programming
tags: [programming, swift, mobile, ios, xcode, mac, mvc]
image: concentration/fullscreen2.png
---
# Concentration
### A very shallow dive into the gorgeous world of iOS develpment
Recently a friend of mine offered a free iMac from 2012. I've never owned any
Mac products, and (am) currently employed as an Android Developer - but a free
desktop is a free desktop. Immediately I fell in love with typing with the magic
keyboard, and the UI of XCode development was simple to learn. Even after a fair
number of Etre Checks to make sure the hardware was ready, it's still a beast
when it comes to development. Many people say 'if you know Kotlin, you know Swift'-
I do think the language has it's quirks (as you'd expect from Apple), but as an
Objective C framework it does a great job prettying up the ugly stuff.

## The Course

This application pretty closely follows a Stanford iOS course I found for free
on iTunes U. The lectures were awesome, and I think it helps pick up the foundations
within a few hours.


## The View Controller Class

Below is an example of just how simple Swift UI controlling is. Even though Kotlin is incredible,
I can certainly understand the appeal of writing native iOS applications. I definitely do not think
this will be my last iOS app.
<script src="https://gist.github.com/pelusodan/164d604721ce40409d98cc8a76bbd7e5.js"></script>

## Data Objects

Below are some of the simple objects associated with this game. It's not surprising
that Swift (given it's close relationship to C) has its reflection of Structs. I found
it to be particularly helpful with the Card and Theme objects. Thanks to Gists
I'm able to show the same emojis used in my themes in the static examples below.

<script src="https://gist.github.com/pelusodan/f05ad2c1f9ccae49dafb2e6a079c4c64.js"></script>


![screen5](\assets\img\concentration\screen5.png)


## Status

The game is currently working! I've added in 6 new themes, from which a random
one will be chosen at the beginning of the new game. The flip counter resets with
a new game. Next I'll probably be adding some better Swift coding practices and
support for adaptable layouts. 

![screen3](\assets\img\concentration\screen3.png) ![screen4](\assets\img\concentration\screen4.png)


Stay tuned for more updates!
