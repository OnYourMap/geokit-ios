# geokit-ios

## Introduction
The Geokit is an SDK providing a set of tools performing requests on the OnYourMap platform.
Those requests include mapping, geocoding, reverse geocoding and routing.
The [MapBox mapping SDK](https://www.mapbox.com/mapbox-ios-sdk/) is in charge of the map interaction, so this SDK must be included in your project.

## Installation

In order to install the geokit, you have to [install the MapBox SDK first](https://github.com/mapbox/mapbox-ios-sdk#installation) then drag and drop the file Geokit.framework into your project along with the bundle Geokit.bundle.
Then include the geokit header file in your code

```smalltalk
#import <Geokit/geokit.h>
```

## Mapping
The MapBox SDK is in charge of the mapping part. The OnYourMap tiles can be used thanks to the class **RMOymTileSource** that implements **RMAbstractWebMapSource** and can be used as a tile source for the MapBox SDK. This is how a map can be created :

```smalltalk
	RMOymTileSource* ts = [[RMOymTileSource alloc] initWithUrl:OYM_MAP_URL tile_format:OYM_TILE_FORMAT key:OYM_APP_KEY referer:OYM_APP_REFERER andSize:512];
    RMMapView* v = [[RMMapView alloc] initWithFrame:self.view.bounds andTilesource:ts];
    v.zoom = 15;
    v.centerCoordinate = CLLocationCoordinate2DMake(48.86, 2.34); 
    [self.view addSubview:v];
```

The values for OYM_MAP_URL, OYM_TILE_FORMAT, OYM_APP_KEY, OYM_APP_REFERER should have been given to you by your OnYourMap.

## OnYourMap webservices

Using OnYourMap webservices for geocoding and routing is done through the class **OymWSClient**.
This class is initialised like this :
```smalltalk
OymWSClient* OYMClient = [[OymWSClient alloc] initWithUrl:OYM_WS_URL key:OYM_APP_KEY andReferer:OYM_APP_REFERER];
```
### Geocoding
You can search places/adresses using the **search** function.

First you need to create a search request like this
```smalltalk
PlaceSearchRequest* request = [[PlaceSearchRequest alloc] init];
request.address = @"rivoli paris";
````

All the parameters available for the request are described in the [appledoc provided](https://github.com/OnYourMap/geokit-ios/Geokit%20Appledoc.zip).

The request can be processed in a sync or async way :

```smalltalk
/* Sync request */
PlaceSearchResponse* myPlaces = [OYMClient search:request andCall:nil];

/* Async request */
[OYMClient search:request andCall:^(PlaceSearchResponse* response) {
        NSLog(@"%d places found",response.totalHits);
        // do something with the places found
        }];
```

### Reverse geocoding
Places can be retrieved around a location using the **nearest** function.

```smalltalk
PlaceNearestRequest* request = [[PlaceNearestRequest alloc] init];
    request.location = CLLocationCoordinate2DMake(48.866598, 2.322464);
    request.radius = 100;
```

If the radius is 0, only the nearest place will be returned. Otherwise, everything in the radius will be returned.

```smalltalk
/* Sync request */
PlaceNearestResponse* myPlaces = [OYMClient nearest:request andCall:nil];

/* Async request */
[OYMClient nearest:request andCall:^(PlaceNearestResponse* response) {
        NSLog(@"%d places found",response.totalHits);
    }];
```

### Routing
The function "directions" provides a route between two coordinates

```smalltalk
RouteRequest* request = [[RouteRequest alloc] init];
request.start = CLLocationCoordinate2DMake(48.866598,2.322464);
request.end = CLLocationCoordinate2DMake(48.858620,2.293961);
```

All the parameters available for the route request are described in the [appledoc provided](https://github.com/OnYourMap/geokit-ios/Geokit%20Appledoc.zip).

Like the geocoding, route can be computed in a sync or async way

```smalltalk
/* Sync */
RouteResponse* route = [OYMClient directions:request andCall:nil];

/* Async */
[OYMClient directions:request andCall:^(RouteResponse* response) {
        NSLog(@"The route distance is %f%@",response.length,response.distanceUnit);
    }];
```

### Routing utilities
Some functions are here to simplify the developers life when using the "directions" function.

#### checkDisplayLevel
The route geometry is provided with the maximum accuracy available. 
In order to speed up the route shape rendering, an array called "levels" is returned in the **RouteResponse** object.
This array contains, for each point of the route, all the zoom levels thsi point should be displayed.
The function **checkDisplayLevel** will tell you if a point should be displayed or not, based on this value.
That way, the number of points to display can be greatly reduced, with a huge speed boost when rendering long routes.

```smalltalk
if ( [RouteUtilities checkDisplayLevel:displayLevelValue forZoomeLevel:currentZoomLevel] == true ) {
	/* keep the point for this zoom level */
}
```

#### renderInstruction
The instructions returned by the "directions" function must be processed before being displayed on screen.
The function **renderInstruction** will transform an encoded instruction into a human readable string.

```smalltalk
/* Display all the route instructions */
 for (RouteInstruction* instruction in route.instructions) {
 	NSLog(@"%@",[RouteUtilities renderInstruction:instruction]);
 }
```

The file listing all the possible instructions is located in the provided bundle Geokit.bundle/Resources/en.lproj/oym-route.strings .
This file can be modified if needed. A translation can be added in another XX.lproj directory.





