vectorformats.DjangoWStyle

DjangoWStyle is a custom Format for the Python library vectorformats (http://packages.python.org/vectorformats/). The purpose of this Format is to extend the Django format to make it easier to work with mapping libraries that add GeoJSON layers (like Leaflet: leaflet.cloudmade.com). 

Leaflet looks for the key 'style' when parsing a GeoJSON object. It then uses the key/value pairs in the style object to style the features on the map. Here is a basic useage example:

	>>> from vectorformats.Formats import DjangoWStyle, GeoJSON
	>>> qs = Model.objects.filter(city="Cambridge")
	>>> djf = Django.Django(geodjango="geometry", 
							properties=['city', 'state'],
							style={'color': '#004070', 'weight': 4})
	>>> geoj = GeoJSON.GeoJSON()
	>>> string = geoj.encode(djf.decode(qs))
	>>> print string 

For a list of the style parameters that Leaflet accepts, visit: http://leaflet.cloudmade.com/reference.html#path-options
For more information on styling GeoJSON layers in Leaflet, see the "Styling Features" section here: http://leaflet.cloudmade.com/examples/geojson.html
