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

Then I got an example of the response from the MapQuest API and formatted that.

```
{
  "route": {
    "hasTollRoad": false,
    "hasBridge": false,
    "boundingBox": {
      "lr": {
        "lng": -71.078873,
        "lat": 42.348316
      },
      "ul": {
        "lng": -71.083916,
        "lat": 42.349674
      }
    },
    "distance": 0.275,
    "hasTimedRestriction": false,
    "hasTunnel": false,
    "hasHighway": false,
    "computedWaypoints": [],
    "routeError": {
      "errorCode": -400,
      "message": ""
    },
    "formattedTime": "00:06:36",
    "sessionId": "5e152370-0128-6750-02b4-1a33-0e7e634cce47",
    "hasAccessRestriction": false,
    "realTime": 396,
    "hasSeasonalClosure": false,
    "hasCountryCross": false,
    "fuelUsed": 0,
    "legs": [
      {
        "hasTollRoad": false,
        "hasBridge": false,
        "destNarrative": "",
        "distance": 0.275,
        "hasTimedRestriction": false,
        "hasTunnel": false,
        "hasHighway": false,
        "index": 0,
        "formattedTime": "00:06:36",
        "origIndex": -1,
        "hasAccessRestriction": false,
        "hasSeasonalClosure": false,
        "hasCountryCross": false,
        "roadGradeStrategy": [
          []
        ],
        "destIndex": -1,
        "time": 396,
        "hasUnpaved": false,
        "origNarrative": "",
        "maneuvers": [
          {
            "distance": 0.275,
            "streets": [
              "Boylston St"
            ],
            "narrative": "Start out going east on Boylston St toward Gloucester St.",
            "turnType": 6,
            "startPoint": {
              "lng": -71.083916,
              "lat": 42.348316
            },
            "index": 0,
            "formattedTime": "00:06:36",
            "directionName": "East",
            "maneuverNotes": [],
            "linkIds": [],
            "signs": [],
            "mapUrl": "http://www.mapquestapi.com/staticmap/v5/map?key=lYrP4vF3Uk5zgTiGGuEzQGwGIVDGuy24&size=225,160&locations=42.34831619262695,-71.08391571044922|marker-1||42.349674224853516,-71.07887268066406|marker-2||¢er=42.348995208740234,-71.08139419555664&defaultMarker=none&zoom=12&rand=-720002964&session=5e152370-0128-6750-02b4-1a33-0e7e634cce47",
            "transportMode": "WALKING",
            "attributes": 0,
            "time": 396,
            "iconUrl": "http://content.mqcdn.com/mqsite/turnsigns/icon-dirs-start_sm.gif",
            "direction": 8
          },
          {
            "distance": 0,
            "streets": [],
            "narrative": "700 BOYLSTON ST is on the right.",
            "turnType": -1,
            "startPoint": {
              "lng": -71.078873,
              "lat": 42.349674
            },
            "index": 1,
            "formattedTime": "00:00:00",
            "directionName": "",
            "maneuverNotes": [],
            "linkIds": [],
            "signs": [],
            "transportMode": "AUTO",
            "attributes": 0,
            "time": 0,
            "iconUrl": "http://content.mqcdn.com/mqsite/turnsigns/icon-dirs-end_sm.gif",
            "direction": 0
          }
        ],
        "hasFerry": false
      }
    ],
    "options": {
      "arteryWeights": [],
      "cyclingRoadFactor": 1,
      "timeType": 0,
      "useTraffic": false,
      "returnLinkDirections": false,
      "countryBoundaryDisplay": true,
      "enhancedNarrative": false,
      "locale": "en_US",
      "tryAvoidLinkIds": [],
      "drivingStyle": 2,
      "doReverseGeocode": false,
      "generalize": -1,
      "mustAvoidLinkIds": [],
      "sideOfStreetDisplay": true,
      "routeType": "PEDESTRIAN",
      "avoidTimedConditions": false,
      "routeNumber": 0,
      "shapeFormat": "raw",
      "maxWalkingDistance": -1,
      "destinationManeuverDisplay": true,
      "transferPenalty": -1,
      "narrativeType": "text",
      "walkingSpeed": -1,
      "urbanAvoidFactor": -1,
      "stateBoundaryDisplay": true,
      "unit": "M",
      "highwayEfficiency": 22,
      "maxLinkId": 0,
      "maneuverPenalty": -1,
      "avoidTripIds": [],
      "filterZoneFactor": -1,
      "manmaps": "true"
    },
    "locations": [
      {
        "dragPoint": false,
        "displayLatLng": {
          "lng": -71.083916,
          "lat": 42.348316
        },
        "adminArea4": "Suffolk",
        "adminArea5": "Boston",
        "postalCode": "02115-3104",
        "adminArea1": "US",
        "adminArea3": "MA",
        "type": "s",
        "sideOfStreet": "R",
        "geocodeQualityCode": "P1AAA",
        "adminArea4Type": "County",
        "linkId": 40524997,
        "street": "899 Boylston St",
        "adminArea5Type": "City",
        "geocodeQuality": "POINT",
        "adminArea1Type": "Country",
        "adminArea3Type": "State",
        "latLng": {
          "lng": -71.083918,
          "lat": 42.348315
        }
      },
      {
        "dragPoint": false,
        "displayLatLng": {
          "lng": -71.078873,
          "lat": 42.349674
        },
        "adminArea4": "Suffolk",
        "adminArea5": "Boston",
        "postalCode": "02116-2813",
        "adminArea1": "US",
        "adminArea3": "MA",
        "type": "s",
        "sideOfStreet": "L",
        "geocodeQualityCode": "P1AAA",
        "adminArea4Type": "County",
        "linkId": 12129140,
        "street": "700 Boylston St",
        "adminArea5Type": "City",
        "geocodeQuality": "POINT",
        "adminArea1Type": "Country",
        "adminArea3Type": "State",
        "latLng": {
          "lng": -71.078874,
          "lat": 42.349676
        }
      }
    ],
    "time": 396,
    "hasUnpaved": false,
    "locationSequence": [
      0,
      1
    ],
    "hasFerry": false
  },
  "info": {
    "statuscode": 0,
    "copyright": {
      "imageAltText": "© 2019 MapQuest, Inc.",
      "imageUrl": "http://api.mqcdn.com/res/mqlogo.gif",
      "text": "© 2019 MapQuest, Inc."
    },
    "messages": []
  }
}
```

Next, I defined the data object for the MapQuest API response:

```kotlin
data class MapQuestResponse(
    val formattedTime: String
){}


```
**much much easier than MBTA**

Building the RecyclerView was pretty easy, and with some basic icons from Favicon, I was able to make the first screen of the app.

![main_screen_milestone_1](\assets\img\miss_my_train_smol.jpg)

The walk time here was set default to 99min because I have yet to add in...


#### ***OBSERVABLES***

My current method of displaying the data is on the basis of response from the MBTA API call:

```kotlin
MBTACall.enqueue(object : Callback<MBTAResponse> {
            override fun onFailure(call: Call<MBTAResponse>,
             t: Throwable) {
                Log.wtf(TAG,t.message)
            }

            override fun onResponse(call: Call<MBTAResponse>,
             response: Response<MBTAResponse>) {
                if(!response.isSuccessful) {
                    Log.v(TAG,response.code().toString())
                    return
                }

                recyclerAdapter.setTrains(response.body()!!.data)
                recyclerView = findViewById(R.id.recyclerView)
                recyclerView.layoutManager = LinearLayoutManager(parent)
                recyclerView.adapter = recyclerAdapter
            }
        })
```

To fix this, I will be implementing RxAndroid observables as the response object (rather than call):
```kotlin
public interface MBTAService {

    @GET("predictions")
    fun getPredictions(@Query("filter[stop]") stopName: String):
     Call<MBTAResponse>

}
```

***TO BE CONTINUED IN MY NEXT UPDATE***

# Skills
- Android
- Kotlin
- Reactive programming
- API usage
- JSON

### Source Code

Check out my project on [Github!](https://github.com/pelusodan/MissMyTrain)
