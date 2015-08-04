# geokit-ios

## Introduction
The Geokit is an SDK providing a set of tools performing requests on the OnYourMap platform.
Those requests include mapping, geocoding, reverse geocoding and routing.
The [MapBox mapping SDK](https://www.mapbox.com/mapbox-ios-sdk/) is in charge of the map interaction, so this SDK must be included in your project.

## Mapping
The MapBox SDK is in charge of the mapping part. The OnYourMap tiles can be used thanks to the class ** RMOymTileSource ** that implements ** RMAbstractWebMapSource ** and can be used as a tile source for the MapBox SDK. This is how a map can be created :

```smalltalk
	RMOymTileSource* ts = [[RMOymTileSource alloc] initWithUrl:OYM_MAP_URL tile_format:OYM_TILE_FORMAT key:OYM_APP_KEY referer:OYM_APP_REFERER andSize:512];
    RMMapView* v = [[RMMapView alloc] initWithFrame:self.view.bounds andTilesource:ts];
    v.zoom = 15;
    v.centerCoordinate = CLLocationCoordinate2DMake(48.86, 2.34); 
    [self.view addSubview:v];
```

