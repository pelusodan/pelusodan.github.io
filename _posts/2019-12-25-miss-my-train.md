---
layout: post
title: "Miss My Train"
author: "Dan Peluso"
categories: programming
tags: [programming, design, rxjava, android, retrofit]
image: miss_my_train_code.jpg
---

Miss my train is an Android application meant to help decide whether or not to chance running for the train. The idea came from my lackluster experience catching trains in Boston. Though there are many existing transit apps, I wanted to have this one abstract much of the information of a traditional transit app from the user, and create a simple way of checking a calculated probability.

The end goal for the app is to have a single screen RecyclerView display live data (which can be updated with a drag pulldown event) with the train time, walk time, train label, and a color banner which shows a spectrum from green to red (based on how likely you are to catch that train if you left at that moment)

## Motivations

While I do like the idea of a simple app that uses two sources to display something useful, my primary motivation for this project was to learn some of the most useful libraries for Android Development. These libraries include
- Retrofit
- RxAndroid
- RecyclerViews
- MVVM Architecture Components
- Multi source information display

## First project milestone

Over winter break I was able to finish the first Milestone of the project. I wanted to add in the Retrofit interfaces to reach out to the APIs, a simple RecyclerView to display, and the layout for the blocks to be presented in the RecyclerView.

First, I looked into which APIs to use for the project. For trains, the only option is the public MBTA API V3, which has tons of information on everything you need to know about trains in Boston. To get a better idea of the response JSON to model in my project, I formatted the response:

```kotlin
{
  "data": [
    {
      "attributes": {
        "arrival_time": "2020-01-07T14:47:14-05:00",
        "departure_time": "2020-01-07T14:48:15-05:00",
        "direction_id": 1,
        "schedule_relationship": null,
        "status": null,
        "stop_sequence": 570
      },
      "id": "prediction-43018129-70154-570",
      "relationships": {
        "route": {
          "data": {
            "id": "Green-B",
            "type": "route"
          }
        },
        "stop": {
          "data": {
            "id": "70154",
            "type": "stop"
          }
        },
        "trip": {
          "data": {
            "id": "43018129",
            "type": "trip"
          }
        },
        "vehicle": {
          "data": {
            "id": "G-10033",
            "type": "vehicle"
          }
        }
      },
      "type": "prediction"
    },
```

For my case, I was only concerned about a few of the details with the trains, so I modeled my data objects to reflect that.

```kotlin

// Class for the reply coming from the source
data class MBTAResponse(
    val data: List<Train>
){}

data class Train (
    val attributes: Attribute,
    val relationships: Relationship
){}

data class Attribute(
    val arrival_time: String,
    val direction_id: Int
){}

data class Relationship(
    val route: Route
) {}

data class Route(
    val data: Data
) {}

data class Data(
    val id: String,
    val type: String
){}
```

Thanks to data classes in Kotlin, creating these were a breeze. After manually doing them, I found out about JSON to Kotlin plugins that make these automatically. When I need more fields from my responses, I will definitely consider using this plugin in future projects.

![main_screen_milestone_1](\assets\img\miss_my_train.jpg)

# Skills
- Android
- Kotlin
- Reactive programming
- API usage
- JSON

### Source Code

Check out my project on [Github!](https://github.com/pelusodan/MissMyTrain)
