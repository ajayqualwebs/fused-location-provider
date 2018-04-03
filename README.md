# fused-location-provider
A wrapper for Google Play Service's Fused Location Provider api.

##Overview
There are a lot of tutorials and examples on how to use the FusedLocationAPI from Google's Play Services, however, none provide an easy to use interface to the API to get exactly **one location update on-demand.**

You can make one function call to use a **cached location (no delay)** and specify a certain criteria. If it doesn't meet the critera, the system will request for a **new location (causes delay while the GPS module tries to get a fix).**

This interface is not tied to an activity nor a service, and hence can be included in any project to provide easy to use location information.


##Features
* Self-contained & Simple Integration - can be used by any activity or service. Just include the file and call the functions.
* Callback interface - simply implement the Callback interface and define the method `onLocationResult(Location)` to process the location data.
* **`getLastKnownLocation(long diffTime, int minAccuracy)`** - Try to use the FusedLocationAPI's lastKnownLocation if it meets the criteria specified by diffTime(the max time that may have elapsed since the sampling of lastKnownLocation) and minAccuracy (the minimum # of meters that the lastKnownLocation needs to be accurate to). Otherwise, it tries to sample a new location and delivers that instead.
* **`getCurrentLocation(int maxTries)`** - Sample GPS maxTries times and deliver the location with the best accuracy.
* `showSettingsAlert()` - Show's a dialog to change the user's GPS settings
* predicates - To check if GPS_PROVIDER or NETWORK_PROVIDER are available.


##Usage
###Basic
####Context
* `final Context context = getActivity();` - From Fragments
* `final Context context = this;` - From Activities
* `final Context context = this;` - From Services

#### Setting up the FusedLocaiton Object
```java
FusedLocation fusedLocation = new FusedLocation(context, new FusedLocation.Callback(){
                      @Override
                      public void onLocationResult(Location location){
                            //Do as you wish with location here
                            Toast.makeText(context, 
                            "Latitude " +location.getLatitude() +" Longitude: " + location.getLongitude(), 
                            Toast.LENGTH_LONG).show();
                      }
              });
```

#### Get Current Location (New Sample)
```java
//Sample GPS readings 3 times and choose the best based on accuracy
fusedLocation.getCurrentLocation(3); 
```
              
#### Get Last Known Location
```java
//Accept the lastKnownLocation if it was sampled within the last 60 seconds and is within 50m of accuracy, otherwise, sample it and return the most recent sample
fusedLocation.getLastKnownLocation(60000, 50); 

//Setting 0,0 for both parameters will force requesting a new location
fusedLocation.getLastKnownLocation(0,0); 
//Is the same as
fusedLcoation.getCurrentLocation(1);
```



###Other Features
#### GPS Settings Alert
```java
if (!fusedLocation.isGPSEnabled()){ 
  fusedLocation.showSettingsAlert();
}else{
  //use fusedLocation API calls here
}
```
