---
layout: post
title: "Miss My Train (Android)"
author: "Dan Peluso"
categories: programming
tags: [programming, design, rxjava, android, retrofit]
image: miss_my_train_img/miss_my_train_rx.png
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

## First project milestone - Retrofit and RecyclerView

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
  // and so on and so forth
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
![recyclerViewCode](\assets\img\miss_my_train_img\miss_my_train_code.jpg)

![main_screen_milestone_1](\assets\img\miss_my_train_img\miss_my_train_smol.jpg)

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

## Second project milestone - RxAndroid, ViewModels, and Gradients

After a rather stressful semester in classes I was able to return work to finish up the second goal of my MissMyTrain application. This time around I was able to make our Retrofit service return an observable data stream from 2 separate API calls and move our application logic to the backend in a ViewModel instance. Additionally, I was able to add in the color gradient mapping based on the 'likelihood' that you would make a train (calculated from the MapQuest API walktime route and the MBTA arrival time prediction).  

### RxAndroid

This part was a pretty quick change, and with Kotlin generics combining the two data streams was easier than I'd guessed.
#### MBTAService.kt
```kotlin
fun connect(walktimeSetter: (String)->Unit, dataAdapter:
        (List<RecyclerViewCell>)->Unit){
        val MBTAClient = MBTAClient.retrofitInstance!!
            .create(MBTAService::class.java)
        val MBTACall = MBTAClient.getPredictions("place-coecl")
        val mapQuestClient = MapQuestClient.retrofitInstance!!
            .create(MapQuestService::class.java)
        val mapQuestCall = mapQuestClient
            .getRoute(rp.getString(R.string.MapQuestAPIKey),
            Location("Boston",rp.getString(R.string.Address),
                "Massachusetts")
                .toString(),
            // Currently hardcoded for Copley Station
            Location("Boston","640 Boylston Street","Massachusetts")
                .toString(),
            "pedestrian")
        Observable.zip(MBTACall,
            mapQuestCall,
            BiFunction<MBTAResponse,
                    MapQuestResponse,
                    List<RecyclerViewCell>>
            {mbta, mapquest ->
                return@BiFunction zipper(mapquest.route.formattedTime,mbta)})
            .subscribeOn(Schedulers.io())
            .observeOn(AndroidSchedulers.mainThread())
            .subscribe {
                Log.v(TAG,it.toString())
                dataAdapter(it)
            }
    }

    private fun zipper(t: String, u: MBTAResponse)
    : List<RecyclerViewCell>{
        var out = ArrayList<RecyclerViewCell>()
        u.data.forEach {
            out.add(RecyclerViewCell(
                t,
                it.attributes.arrival_time,
                it.relationships.route.data.id,
                it.attributes.direction_id))
        }
        return out
    }
```

The most important call is to dataAdapter in the .subscribe{} call - callback should be directly linked to the RecyclerViewAdapter. In this implementation, I did that in the ViewModel class I made.
### ViewModel

#### MainActivityViewModel.kt and MainActivityViewModelFactory.kt
```kotlin
class MainActivityViewModel(val rp: ResourceProvider) : ViewModel() {
  // the above functions
}
class MainActivityViewModelFactory(val resourceProvider: ResourceProvider)
    : ViewModelProvider.Factory {
    override fun <T : ViewModel?> create(modelClass: Class<T>): T {
        return MainActivityViewModel(resourceProvider) as T
    }
}
```

The factory was necessary for adding a custom class to the constructor, our `ResourceProvider` which was really only added so I could make my project public without posting the API Key or home address I was using for the MapQuest walk times.
#### ResourceProvider.kt
```kotlin
// for grabbing resources for the viewModel
class ResourceProvider(val context: Context) {
    fun getString(resID: Int): String{return context.getString(resID)}
}
```
This pretty drastically reduced the size of my MainActivity class (always a good thing with Android!)
#### MainActivity.kt
```kotlin
class MainActivity : AppCompatActivity() {
    companion object { val TAG = "MainActivity" }
    lateinit var recyclerAdapter: RecyclerViewAdapter
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)
        // resource provider for secret strings
        val vmFactory = MainActivityViewModelFactory(ResourceProvider(this))
        val model = ViewModelProvider(this, vmFactory)
            .get(MainActivityViewModel::class.java)
        recyclerAdapter = RecyclerViewAdapter(this)
        recyclerView.adapter = recyclerAdapter
        recyclerView.layoutManager = LinearLayoutManager(this)
        mainButton.setOnClickListener {
            Log.v(TAG,"Attempting to connect")
            model.connect {
                fillRecyclerView(it)
            }
        }
    }
    private fun fillRecyclerView(cells: List<RecyclerViewCell>){
        recyclerAdapter.setData(cells)
        recyclerAdapter.notifyDataSetChanged()
    }
}
```

### Gradient Display and Liklihood Calculation

The final part of this iteration was to make the display prettier, and I chose to do so by calculating a likelihood that (at walking distance) you catch certain upcoming trains at a station. I added this logic to the `RecyclerViewCell` class (as this is the main storage unit our RecyclerViewAdapter uses).
#### RecyclerViewCell.kt
```kotlin
class RecyclerViewCell (
    var walkTime: String,
    val trainTime: String,
    val trainName: String,
    val trainDirection: Int
) {
    companion object {
        val TAG = "RecyclerViewCell"
    }
    // Difference calculated by walk time + TIME_GRADIENT_LIMIT
    private val UPPER_TIME_GRADIENT_LIMIT =  300000L
    private val LOWER_TIME_GRADIENT_LIMIT = -240000L

    fun displayGradient(): Color {
        val likelihood = calculateLikelihood()
        val invLikelihood = 1f - likelihood
        val green = Color.GREEN
        val red = Color.RED
        return Color.valueOf(ArgbEvaluator().evaluate(likelihood,red,green) as Int)
    }
    private fun calculateLikelihood(): Float{
        var mapQuestTime: Long
        var mbtaTime: Long
        try{
            mapQuestTime = parseTimeToMilli(walkTime)
            mbtaTime = ISO8601Utils.parse(trainTime, ParsePosition(0)).time
        }catch(e: Exception){
            Log.v(TAG,e.message)
            return 0f
        }
        val eta = System.currentTimeMillis() + mapQuestTime
        //return gradient if its in bewteen time restraints
        if( mbtaTime in eta + LOWER_TIME_GRADIENT_LIMIT .. eta + UPPER_TIME_GRADIENT_LIMIT){
            // time to spare at the station including bia
            val timeToSpare = (mbtaTime - eta) - LOWER_TIME_GRADIENT_LIMIT
            return timeToSpare.toFloat() / (UPPER_TIME_GRADIENT_LIMIT - LOWER_TIME_GRADIENT_LIMIT)

        }
        // return red if its greater than lower limit
        if(System.currentTimeMillis() + mapQuestTime >
            mbtaTime + LOWER_TIME_GRADIENT_LIMIT)  { return 0f}
        // return green if its less than upper limit
        if(System.currentTimeMillis() + mapQuestTime <
            mbtaTime + UPPER_TIME_GRADIENT_LIMIT) { return 1f}
        return -200f
    }
    private fun parseTimeToMilli(hourFormat: String): Long {
        var ms = 0L
        val split: List<String> = hourFormat.split(":")
        try {
            // hours
            ms += (split[0].toLong() * 60 * 60 * 1000)
            // minutes
            ms += split[1].toLong() * 60 * 1000
            // seconds
            ms += split[2].toLong() * 1000
            return ms
        } catch (e: Exception) {
            return -1
        }
    }
```
It was easy to incorperate this into the `RecyclerViewAdapter.kt` class:
#### RecyclerViewAdapter.kt
```kotlin
override fun onBindViewHolder(holder: MyViewHolder, position: Int) {
        holder.trainName.text = entry_list[position].trainName
        holder.trainTime.text = Utils.displayTrainString(entry_list[position].trainTime)
        holder.walkTime.text = Utils.displayWalkString(entry_list[position].walkTime)
        holder.trainDirection.text = if (entry_list[position].trainDirection==1)  "Inbound" else "Outbound"
        holder.cellLayout.background = ColorDrawable(entry_list[position].displayGradient().toArgb())
        Log.v(TAG,"VIEW BINDED")
    }
```

The final result is a much prettier screen, with a gradient of chance you'll be able to make the Copley train
![main_screen_milestone_2](\assets\img\miss_my_train_img\miss_my_train_green.png)

## Future work
My main goals in the project were to familiarize myself with some popular Android libraries used in many commercial applications. I definitely think the gradient is a cool view for the app, but I don't think this is a unique concept, so I won't be adding in new features in the near future. If I do choose to flesh out MissMyTrain more it'll be to add settings for users to pick which Station they'd like to receive train information about and where they're walking from. This would be much more customizable than my current hardcoded method of displaying the information.

# Skills
- Android
- RxAndroid
- RecyclerViews
- MVVM
- Kotlin
- Reactive programming
- API usage
- JSON

### Source Code

Check out my project on [Github!](https://github.com/pelusodan/MissMyTrain)
