Custom Maps With The ArGIS JS API
=================

This is a quick write-up on extending the standard ArcGIS Javascript API to include calls to
3 types of customized basemaps. [Spoiler Alert] I'm not a developer.

=================

###Using an Esri Web Map  
Through your ArcGIS Online account, you can create any number of styled maps using a combination
of Esri Basemaps, Services and Layers hosted in the AGOL infastructure. Those can also be
customized to include a tiled map service from an non-Esri provider. Lots of details on that
can be found in a previous write-up: **[Custom Basemaps In ArcGIS Online](https://github.com/jonahadkins/Custom-Basemaps-In-AGOL)**. Using an AGOL Web Map requires
a valid webmap id that has been shared publicly from your AGOL account. If you navigate to your
webmap in your browser the webmap id can be copied from the url:  

`http://www.arcgis.com/home/webmap/viewer.html?webmap=df64031882e74a8db5fa5a94b3f25415`

You'll want to grab everything after webmap=, which in this case is **df64031882e74a8db5fa5a94b3f25415**


Code:  
```javascript
var map;  
require([  
      "esri/map",  
      "esri/arcgis/utils",  
      "dojo/domReady!"  
      ], function(Map, arcgisUtils){
      arcgisUtils.createMap("df64031882e74a8db5fa5a94b3f25415", "mapDiv").then(function (response) {
          map = response.map;
      });
});
  ```  
This example includes tiled layers brought over from Stamen Design's cool [MapStack](http://mapstack.stamen.com/) service, which was then saved as an AGOL webmap.

**Live Sample: [AGOL Webmap with ArcGIS JS API](http://jonahadkins.github.io/ags-js-custom-map/agol_webmap.html)**  

=================  

###Using ArcGIS Server or an AGOL Map Service
You can use your public ArcGIS Server instance, or one that is hosted in ArcGIS Online to implement
a custom basemap in the ArcGIS JS API. This method is fairly straight forward and
requires access to a public map service like the [Most Recent Virginia Imagery](http://www.arcgis.com/home/item.html?id=7780125c142c450cb8f6b4db4d121806) service provided by VGIN.
What we look for in this case is the map server address, which usually looks like:  

`http://gismaps.vita.virginia.gov/arcgis/rest/services/MostRecentImagery/MostRecentImagery_WGS/MapServer`  

Code:
```javascript
var map;
require(["esri/map", "dojo/domReady!"], function(Map) {
    map = new Map("map", {
        basemap: "topo",
        center: [-76.386, 37.040], // longitude, latitude
        zoom: 10
    });
    // Adding A New Basemap From An Existing Rest/Map Service
    var baseMapLayer = new esri.layers.ArcGISTiledMapServiceLayer
        ("http://gismaps.vita.virginia.gov/arcgis/rest/services/MostRecentImagery/MostRecentImagery_WGS/MapServer");
    map.addLayer(baseMapLayer);
});
```  
**Live Sample [AGOL Map Service with ArcGIS JS API](http://jonahadkins.github.io/ags-js-custom-map/custom_map_service.html)**  

=================

###Using a Web Tile Layer  
You can also implement a non-Esri web tile layer from any number of service providers. In this example
we will use the popular [Standard Toner](http://maps.stamen.com/#toner/12/37.7706/-122.3782) from Stamen
Design. To get started, you'll need to find the url pattern for thier service which is:

`http://d.tile.stamen.com/toner/14/2626/6331.png`  

It's a standard setup with the tile subdomain listed as `d` and the `14/2626/6331` as the z/x/y configuration.

Code:  

```javascript
var map;
require(["esri/map", "esri/layers/WebTiledLayer", "dojo/parser", "dojo/domReady!"],
    function(Map, WebTiledLayer, parser) {
    parser.parse();
    map = new Map("map", {
          center: [-76.386, 37.040],
          zoom: 10
    });
    var customMap = new WebTiledLayer("http://${subDomain}.tile.stamen.com/toner/${level}/${col}/${row}.png", {
          "copyright": 'Map tiles by <a href="http://stamen.com">Stamen Design</a>, under <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a>. Data by <a href="http://openstreetmap.org">OpenStreetMap</a>, under <a href="http://www.openstreetmap.org/copyright">ODbL</a>.',
          "id": "Toner",
          "subDomains": ["a", "b", "c"]
    });
    map.addLayer(customMap);
});
```  
**Live Sample [Web Tile Layer with ArcGIS JS API](http://jonahadkins.github.io/ags-js-custom-map/toner_tile_layer.html)**

Always remember to include attribution when it's required. For using Toner, include this `html` (which is also described on [their website](http://maps.stamen.com/#watercolor/12/37.7706/-122.3782))

```html
Map tiles by <a href="http://stamen.com">Stamen Design</a>, under <a href="http://creativecommons.org/licenses/by/3.0">CC BY 3.0</a>. Data by <a href="http://openstreetmap.org">OpenStreetMap</a>, under <a href="http://www.openstreetmap.org/copyright">ODbL</a>
```

Feel free to give me a shout on Twitter [@jonahadkins](https://twitter.com/jonahadkins) for any comments or questions. And please fork to add any missing info or lessons learned.


