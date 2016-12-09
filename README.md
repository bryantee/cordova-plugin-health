# Cordova Health Plugin


A plugin that abstracts fitness and health repositories like Apple HealthKit or Google Fit.

This work is based on [cordova plugin googlefit](https://github.com/2dvisio/cordova-plugin-googlefit) and on [cordova healthkit plugin](https://github.com/Telerik-Verified-Plugins/HealthKit)

For an introduction about Google Fit versus HealthKit see [this very good article](https://yalantis.com/blog/how-can-healthkit-and-googlefit-help-you-develop-healthcare-and-fitness-apps/).

This plugin is kept up to date and requires a recent version of cordova (6 and on) and recent iOS and Android SDKs.

## Warning

This plugin stores health data in Google Fit, practice that is [discouraged by Google](https://developers.google.com/fit/terms).

## Installation

Just execute this line in your project's folder:

```
cordova plugin add cordova-plugin-health
```

## Requirements for iOS apps

* Make sure your app id has the 'HealthKit' entitlement when this plugin is installed (see iOS dev center).
* Also, make sure your app and AppStore description complies with these Apple review guidelines: https://developer.apple.com/app-store/review/guidelines/#healthkit
* There are [two keys](https://developer.apple.com/library/content/documentation/General/Reference/InfoPlistKeyReference/Articles/CocoaKeys.html#//apple_ref/doc/uid/TP40009251-SW48) to the info.plist file, `NSHealthShareUsageDescription` and `NSHealthUpdateUsageDescription` that are assigned with an empty string by default by the plugin. You may want to put a better description there.

## Requirements for Android apps

* You need to have the Google Services API downloaded in your SDK
* Be sure to give your app access to the Google Fitness API, see [this](https://developers.google.com/fit/android/get-api-key) and [this](https://github.com/2dvisio/cordova-plugin-googlefit#sdk-requirements-for-compiling-the-plugin)
* If you are wondering what key your compiled app is using, you can type `keytool -list -printcert -jarfile yourapp.apk`
* You can use the Google Fitness API even if the user doesn't have Google Fit installed, but there has to be some other fitness app putting data into the Fitness API otherwise your queries will always be empty. See the [the original documentation](https://developers.google.com/fit/overview).
* This plugin is set to use the latest version of the Google Play Services API (`<framework src="com.google.android.gms:play-services-fitness:+" />`). This is done to likely guarantee the compatibility with other plugins using Google Play Services, but bear in mind that a) the plugin was tested until version 9.4.0 of the APIs and b) other plugins may be using a different version of the API. If you run into an issue, check the generated gradle file (build.gradle) under `dependencies` between `// SUB-PROJECT DEPENDENCIES START` and `// SUB-PROJECT DEPENDENCIES END` and amke sure that all versions of the `com.google.android.gms:play-services-xxxx` are the same.

## Supported data types

As HealthKit does not allow adding custom data types, only a subset of data types supported by HealthKit has been chosen.
Google Fit is limited to fitness data and, for health, custom data types are defined with the suffix of the package name of your project.

| data type       | Unit  |    HealthKit equivalent                       |  Google Fit equivalent                   |
|-----------------|-------|-----------------------------------------------|------------------------------------------|
| steps           | count | HKQuantityTypeIdentifierStepCount             | TYPE_STEP_COUNT_DELTA                    |
| distance        | m     | HKQuantityTypeIdentifierDistanceWalkingRunning + HKQuantityTypeIdentifierDistanceCycling | TYPE_DISTANCE_DELTA |
| calories        |  kcal | HKQuantityTypeIdentifierActiveEnergyBurned + HKQuantityTypeIdentifierBasalEnergyBurned   | TYPE_CALORIES_EXPENDED |
| calories.active |  kcal | HKQuantityTypeIdentifierActiveEnergyBurned    | TYPE_CALORIES_EXPENDED - (TYPE_BASAL_METABOLIC_RATE * time window) |
| calories.basal  |  kcal | HKQuantityTypeIdentifierBasalEnergyBurned     | TYPE_BASAL_METABOLIC_RATE * time window  |
| activity        |       | HKWorkoutTypeIdentifier + HKCategoryTypeIdentifierSleepAnalysis | TYPE_ACTIVITY_SEGMENT  |
| height          |  m    | HKQuantityTypeIdentifierHeight                | TYPE_HEIGHT                              |
| weight          |  kg   | HKQuantityTypeIdentifierBodyMass              | TYPE_WEIGHT                              |
| heart_rate      | count/min|  HKQuantityTypeIdentifierHeartRate         | TYPE_HEART_RATE_BPM                      |
| fat_percentage  | %     | HKQuantityTypeIdentifierBodyFatPercentage     | TYPE_BODY_FAT_PERCENTAGE                 |
| gender          |       | HKCharacteristicTypeIdentifierBiologicalSex   | custom (YOUR_PACKAGE_NAME.gender)        |
| date_of_birth   |       | HKCharacteristicTypeIdentifierDateOfBirth     | custom (YOUR_PACKAGE_NAME.date_of_birth) |
| nutrition       |       | HKCorrelationTypeIdentifierFood               | TYPE_NUTRITION                           |
| nutrition.calories | kcal | HKQuantityTypeIdentifierDietaryEnergyConsumed | TYPE_NUTRITION, NUTRIENT_CALORIES |
| nutrition.fat.total | g | HKQuantityTypeIdentifierDietaryFatTotal | TYPE_NUTRITION, NUTRIENT_TOTAL_FAT |
| nutrition.fat.saturated | g | HKQuantityTypeIdentifierDietaryFatSaturated | TYPE_NUTRITION, NUTRIENT_SATURATED_FAT |
| nutrition.fat.unsaturated | g | NA  | TYPE_NUTRITION, NUTRIENT_UNSATURATED_FAT |
| nutrition.fat.polyunsaturated | g | HKQuantityTypeIdentifierDietaryFatPolyunsaturated | TYPE_NUTRITION, NUTRIENT_POLYUNSATURATED_FAT |
| nutrition.fat.monounsaturated | g | HKQuantityTypeIdentifierDietaryFatMonounsaturated | TYPE_NUTRITION, NUTRIENT_MONOUNSATURATED_FAT |
| nutrition.fat.trans | g | NA | TYPE_NUTRITION, NUTRIENT_TRANS_FAT (g) |
| nutrition.cholesterol | mg | HKQuantityTypeIdentifierDietaryCholesterol | TYPE_NUTRITION, NUTRIENT_CHOLESTEROL |
| nutrition.sodium | mg | HKQuantityTypeIdentifierDietarySodium  | TYPE_NUTRITION, NUTRIENT_SODIUM |
| nutrition.potassium | mg | HKQuantityTypeIdentifierDietaryPotassium | TYPE_NUTRITION, NUTRIENT_POTASSIUM |
| nutrition.carbs.total | g | HKQuantityTypeIdentifierDietaryCarbohydrates | TYPE_NUTRITION, NUTRIENT_TOTAL_CARBS |
| nutrition.dietary_fiber | g | HKQuantityTypeIdentifierDietaryFiber | TYPE_NUTRITION, NUTRIENT_DIETARY_FIBER |
| nutrition.sugar | g | HKQuantityTypeIdentifierDietarySugar | TYPE_NUTRITION, NUTRIENT_SUGAR |
| nutrition.protein | g | HKQuantityTypeIdentifierDietaryProtein | TYPE_NUTRITION, NUTRIENT_PROTEIN |
| nutrition.vitamin_a | mcg (HK), IU (GF) | HKQuantityTypeIdentifierDietaryVitaminA | TYPE_NUTRITION, NUTRIENT_VITAMIN_A |
| nutrition.vitamin_c | mg | HKQuantityTypeIdentifierDietaryVitaminC | TYPE_NUTRITION, NUTRIENT_VITAMIN_C |
| nutrition.calcium | mg | HKQuantityTypeIdentifierDietaryCalcium | TYPE_NUTRITION, NUTRIENT_CALCIUM |
| nutrition.iron | mg | HKQuantityTypeIdentifierDietaryIron | TYPE_NUTRITION, NUTRIENT_IRON |
| nutrition.water | ml | HKQuantityTypeIdentifierDietaryWater | TYPE_HYDRATION |
| nutrition.caffeine | g | HKQuantityTypeIdentifierDietaryCaffeine | NA |

Note: units of measurements are fixed !

Returned objects contain a set of fixed fields:

- startDate: {type: Date} a date indicating when the data point starts
- endDate: {type: Date} a date indicating when the data point ends
- sourceBundleId: {type: String} the identifier of the app that produced the data
- sourceName: {type: String} the name of the app that produced the data (as it appears to the user)
- unit: {type: String} the unit of measurement
- value: the actual value

value can be of different types, see examples below:

| data type      | value                             |
|----------------|-----------------------------------|
| steps          | 34                                |
| distance       | 101.2                             |
| calories       | 245.3                             |
| activity       | "walking"  (note: recognized activities and their mapping to Fit / HealthKit equivalents are listed in [this file](activities_map.md)) |
| height         | 185.9                             |
| weight         | 83.3                              |
| heart_rate     | 66                                |
| fat_percentage | 31.2                              |
| gender         | "male"                            |
| date_of_birth  | { day: 3, month: 12, year: 1978 } |
| nutrition      | { item: "cheese", meal_type: "lunch", nutrients: { nutrition.fat.saturated: 11.5, nutrition.calories: 233.1 } } |
| nutrition.X    | 12.4                              |

## Methods

### isAvailable()

Tells if either Google Fit or HealthKit are available.

```
navigator.health.isAvailable(successCallback, errorCallback)
```

- successCallback: {type: function(available)}, if available a true is passed as argument, false otherwise
- errorCallback: {type: function(err)}, called if something went wrong, err contains a textual description of the problem


### promptInstallFit() (Android only)

Checks if recent Google Play Services and Google Fit are installed.
If the play services are not installed, or are obsolete, it will show a pop-up suggesting to download them.
If Google Fit is not installed, it will open the Play Store at the location of the Google Fit app.
The plugin does not wait until the missing packages are installed, it will return immediately.
If both Play Services and Google Fit are available, this function just returns without any visible effect.

This function is only available on Android.


```
navigator.health.promptInstallFit(successCallback, errorCallback)
```

- successCallback: {type: function()}, called if the function was called
- errorCallback: {type: function(err)}, called if something went wrong


### requestAuthorization()

Requests read and write access to a set of data types.
It is recommendable to always explain why the app needs access to the data before asking the user to authorize it.

This function must be called before using the query and store functions, even if the authorization has already been given at some point in the past.

```
navigator.health.requestAuthorization(datatypes, successCallback, errorCallback)
```

- datatypes: {type: Array of String}, a list of data types you want to be granted access to
- successCallback: {type: function}, called if all OK
- errorCallback: {type: function(err)}, called if something went wrong, err contains a textual description of the problem

Quirks of requestAuthorization()

- In Android, it will try to get authorization from the Google Fit APIs. It is necessary that the app's package name and the signing key are registered in the Google API console (see [here](https://developers.google.com/fit/android/get-api-key)).
- In Android, be aware that if the activity is destroyed (e.g. after a rotation) or is put in background, the connection to Google Fit may be lost without any callback. Going through the authorization will ensure that the app is connected again.
- In Android 6 and over, this function will also ask for some dynamic permissions if needed (e.g. in the case of "distance", it will need access to ACCESS_FINE_LOCATION).

### query()

Gets all the records of a certain data type within a certain time window.

Warning: if the time span is big, it can generate long arrays!

```
navigator.health.query({
        startDate: new Date(new Date().getTime() - 3 * 24 * 60 * 60 * 1000), // three days ago
        endDate: new Date(), // now
        dataType: 'height'
        }, successCallback, errorCallback)
```

- startDate: {type: Date}, start date from which to get data
- endDate: {type: Date}, end data to which to get the data
- dataType: {type: String}, the data type to be queried (see above)
- successCallback: {type: function(data) }, called if all OK, data contains the result of the query in the form of an array of: { startDate: Date, endDate: Date, value: xxx, unit: 'xxx', sourceName: 'aaaa', sourceBundleId: 'bbbb' }
- errorCallback: {type: function(err)}, called if something went wrong, err contains a textual description of the problem


Quirks of query()

- In Android it is possible to query for "raw" steps or to select those as filtered by the Google Fit app. In the latter case the query object must contain the field `filtered: true`.
- In Google Fit calories.basal is returned as an average per day, and usually is not available in all days (may be not available in time windows smaller than 5 days or more).
- In Google Fit calories.active is computed by subtracting the basal calories from the total. As basal energy expenditure, an average is computed from the week before endDate.
- While Google Fit calculates basal and active calories automatically, HealthKit needs an explicit input.
- When querying for activities, Google Fit is able to determine some activities automatically (still, walking, running, biking, in vehicle), while HealthKit only relies on the input of the user or of some external app.
- When querying for activities, calories and distance are also provided in HealthKit (units are kcal and meters) and never in Google Fit.
- When querying for nutrition, Google Fit always returns all the nutrition elements it has, while HealthKit returns only those that are stored as correlation. To be sure to get all stored the quantities (regardless of they are stored as correlation or not), it's better to query single nutrients.
- nutrition.vitamin_a is given in micrograms in HealthKit and International Unit in Google Fit. This is because conversion is not trivial and depends on the actual substance (see [this](https://dietarysupplementdatabase.usda.nih.gov/ingredient_calculator/help.php#q9)).

### queryAggregated()

Gets aggregated data in a certain time window.
Usually the sum is returned for the given quantity.

```
navigator.health.queryAggregated({
        startDate: new Date(new Date().getTime() - 3 * 24 * 60 * 60 * 1000), // three days ago
        endDate: new Date(), // now
        dataType: 'steps',
        bucket: 'day'
        }, successCallback, errorCallback)
```

- startDate: {type: Date}, start date from which to get data
- endDate: {type: Date}, end data to which to get the data
- dataType: {type: String}, the data type to be queried (see below for supported data types)
- bucket: {type: String}, if specified, aggregation is grouped an array of "buckets" (windows of time), supported values are: 'hour', 'day', 'week', 'month', 'year'
- successCallback: {type: function(data)}, called if all OK, data contains the result of the query, see below for returned data types. If no buckets is specified, the result is an object. If a bucketing strategy is specified, the result is an array.
- errorCallback: {type: function(err)}, called if something went wrong, err contains a textual description of the problem

Not all data types are supported for aggregated queries.
The following table shows what types are supported and examples of the returned object:

| data type       | example of returned object |
|-----------------|----------------------------|
| steps           | { startDate: Date, endDate: Date, value: 5780, unit: 'count' } |
| distance        | { startDate: Date, endDate: Date, value: 12500.0, unit: 'm' } |
| calories        | { startDate: Date, endDate: Date, value: 25698.1, unit: 'kcal' } |
| calories.active | { startDate: Date, endDate: Date, value: 3547.4, unit: 'kcal' } |
| calories.basal  | { startDate: Date, endDate: Date, value: 13146.1, unit: 'kcal' } |
| activity        | { startDate: Date, endDate: Date, value: { still: { duration: 520000, calories: 30, distance: 0 }, walking: { duration: 223000, calories: 20, distance: 15 }}, unit: 'activitySummary' } (note: duration is expressed in milliseconds, distance in metres and calories in kcal) |
| nutrition       | { startDate: Date, endDate: Date, value: { nutrition.fat.saturated: 11.5, nutrition.calories: 233.1 }, unit: 'nutrition' } (note: units of measurement for nutrients are fixed according to the table at the beginning of this readme) |
| nutrition.X     | { startDate: Date, endDate: Date, value: 23, unit: 'mg'} |

Quirks of queryAggregated()

- In Android, to query for steps as filtered by the Google Fit app, the flag `filtered: true` must be added into the query object.
- When querying for activities, calories and distance are provided when available in HealthKit and never in Google Fit.
- In Android, the start and end dates returned are the date of the first and the last available samples. If no samples are found, start and end may not be set.
- When bucketing, buckets will include the whole hour / day / month / week / year where start and end times fall into. For example, if your start time is 2016-10-21 10:53:34, the first daily bucket will start at 2016-10-21 00:00:00.
- Weeks start on Monday.
- When querying for nutrition, HealthKit returns only those that are stored as correlation. To be sure to get all the stored quantities, it's better to query single nutrients.
- nutrition.vitamin_a is given in micrograms in HealthKit and International Unit in Google Fit.

### store()

Stores a data point.

```
navigator.health.store({
	startDate:  new Date(new Date().getTime() - 3 * 60 * 1000), // three minutes ago
	endDate: new Date(),
	dataType: 'steps',
	value: 180,
	sourceName: 'my_app',
	sourceBundleId: 'com.example.my_app' }, successCallback, errorCallback)
```

- startDate: {type: Date}, start date from which to get data
- endDate: {type: Date}, end data to which to get the data
- dataType: {type: a String}, the data type
- value: {type: a number or an Object}, depending on the actual data type
- sourceName: {type: String}, the source that produced this data. In iOS this is ignored and set automatically to the name of your app.
- sourceBundleId: {type: String}, the complete package of the source that produced this data. In Android, if not specified, it's assigned to the package of the App. In iOS this is ignored and set automatically to the bunde id of the app.
- successCallback: {type: function}, called if all OK
- errorCallback: {type: function(err)}, called if something went wrong, err contains a textual description of the problem


Quirks of store()

- In iOS you cannot store the total calories, you need to specify either basal or active. If you use total calories, the active ones will be stored.
- In Android you can only store active calories, as the basal are estimated automatically. If you store total calories, these will be treated as active.
- In iOS distance is assumed to be of type WalkingRunning, if you want to explicitly set it to Cycling you need to add the field ` cycling: true `.
- In iOS storing the sleep activities is not supported at the moment.
- Storing of nutrients is not supported at the moment.

## Differences between HealthKit and Google Fit

* HealthKit includes medical data (eg blood glucose), Google Fit is only related to fitness data.
* HealthKit provides a data model that is not extensible, Google Fit allows defining custom data types.
* HealthKit allows to insert data with the unit of measurement of your choice, and automatically translates units when queried, Google Fit uses fixed units of measurement.
* HealthKit automatically counts steps and distance when you carry your phone with you and if your phone has the CoreMotion chip, Google Fit also detects the kind of activity (sedentary, running, walking, cycling, in vehicle).
* HealthKit automatically computes the distance only for running/walking activities, Google Fit includes bicycle also.

## External Resources

* The official Apple documentation for HealthKit [can be found here](https://developer.apple.com/library/ios/documentation/HealthKit/Reference/HealthKit_Framework/index.html#//apple_ref/doc/uid/TP40014707).
* For functions that require the `unit` attribute, you can find the comprehensive list of possible units from the [Apple Developers documentation](https://developer.apple.com/library/ios/documentation/HealthKit/Reference/HKUnit_Class/index.html#//apple_ref/doc/uid/TP40014727-CH1-SW2).
* [HealthKit constants](https://developer.apple.com/library/ios/documentation/HealthKit/Reference/HealthKit_Constants/index.html), used throughout the code.
* Google Fit [supported data types](https://developers.google.com/fit/android/data-types).

## Roadmap

short term:

- add storing of nutrition
- allow deletion of data points
- add support for storing HKCategory samples in HealthKit
- add more datatypes
 - body fat percentage
 - oxygen saturation
 - blood pressure
 - blood glucose
 - temperature
 - respiratory rate

long term:

- add registration to updates (in Fit:  HistoryApi#registerDataUpdateListener()).
- store custom data types and vital signs on an encrypted DB in the case of Android.
Possible choice: [sqlcipher](https://www.zetetic.net/sqlcipher/sqlcipher-for-android/).
The file would be stored on shared drive, and it would be shared among apps through a service.
All apps would have the same service because it's part of the plugin, so the service should not auto-start until the first app tries to bind it (see [this](http://stackoverflow.com/questions/31506177/the-same-android-service-instance-for-two-apps) for suggestions).
- add also Samsung Health as a health record for Android.

## Contributions

Any help is more than welcome!
I don't know Objectve C and I am not interested into learning it now, so I would particularly appreciate someone who can give me a hand with the iOS part.
Also, I would love to know from you if the plugin is currently used in any app actually available online.
Just send me an email to my_username at gmail.com
