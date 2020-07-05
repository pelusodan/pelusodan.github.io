---
layout: post
title: "Kanesthetic Learning"
author: "Dan Peluso"
categories: programming
tags: [capstone, android, mobile, uhf, neu]
image: kanesthetic_learning/title_logo.PNG
---
### Overview

Kanesthetic Learning is a product for the visually impaired that attaches to canes and reads ultra-high frequency signals to communicate text to the user. The attachment will use a text-to-speech module to translate the encoded information from the tags to headphones. The encoded information will be placed in both static and dynamic tags - static tags being placed on stickers with information that cannot be changed and dynamic tags being powered to give a larger signal radius and the ability to change the encoded information. Users with our attachment will walk by the UHF signals and directly hear the information being placed in that tag. The device only requires charge, and was built with the intention of avoiding tethering to a smartphone or internet connection. This will not only make our product more accessible to the audience that needs it, but also lower the cost of the attachment dramatically.   

### Use Cases
- Bus schedule times and active updates from dyanmic beacons placed at bus stations
- Menu information and hours from restaurants in a sticker tag placed on the front window
- Labeled buildings and classrooms for visually impaired students to navigate campuses independently
- Grocers with labeled rows and refrigerators telling shoppers a brief description of the contents
- Construction site cones with a static sticker warning user's of the ahead work


### Background

Part of Northeastern's Engineering curriculum is to do a Capstone project with a group of graduating seniors. My group and I decided to make a project helping those with disabilities, inspired by the Northeastern Enabling Engineering organization on campus. The idea came from a professor with a visual impairment who told us about some of the issues she's had in the city. The name for our specific product comes from a combination of the words *cane* and *kinesthetic* which describes the *learn by doing* actions we think everyone should be able to do. Kanesthetic Learning allows those with visual impairments to explore areas to learn them, rather than relying on another service for small bits of information.   

### Mobile App

While one of our main goals is to allow users without an internet connection or smartphone to use our product, we knew that we should still offer the ability to use this common tech. For this reason. the product will be designed to connect to an **optional** mobile app. Given my experience with mobile applications, this is my primary contribution to the group.

The app will be written natively using Kotlin, the popular mobile framework of Java. For web requests associated with transit APIs, we will be using Retrofit. The built in Android Bluetooth framework allows for a reactive app architecture, so the app will be using RxJava for graphical displays. The phone having access to better hardware and software capabilities is something we want to expand on the features of the main device. The encoded information will be key to decoding the information presented locally from the marker data.

This is an example of the main screen of the app, which will feature 3 buttons containing the information from the last 3 signals received. These include both the static passive tag info, as well as the dynamic information from beacons.

![mockup1](\assets\img\kanesthetic_learning\screens1.PNG)

Once a user presses any of these tags, they are directed to a screen where they can find additional information. At a minimum, the tags will fill in enough information to display the name of the location, message, timestamp of passing, and coordinates on a map of the tag. Additionally, when the main tag buttons are held down, an intent will be sent from our app to open a mapping application (ex. Google Maps) and show the coordinate from the tag in relation to a better mapping software.

![mockup2](\assets\img\kanesthetic_learning\screens2.PNG)

The app will be designed with the specific intention of fitting Android’s built in accessibility features for easy use. Android accessibility guidelines request that layouts be represented with large buttons and content wrappers that label areas of the screen. This is so that visually impaired users can drag their fingers along the screen and have specifically read content labels to provide the information relevant to the actions in that area. For example, the 3 bubbles on a white background provides a high-contrast, large area that can tell the user the meta-data being stored on the tag ( or a shortened description of it). Our application design will also feature a side-navigation drawer, a popular design for additional screens in most modern applications. This is another feature that plugs into the Android visually-impaired tools as well, for the labels are easily read with the action-to-speech modules and allow for seamless navigation between
screens.

Check out our group's full project proposal below:

<iframe src="\assets\capstone_proposal.pdf" height="1100" width="700"></iframe>


#### Capstone 1 is now completed, and this spring I will update with the full project!

# Skills
- Android
- RxAndroid
- Fragments
- Bluetooth
