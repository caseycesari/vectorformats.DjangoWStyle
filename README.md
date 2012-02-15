#vectorformats.DjangoWStyle v .0.5.0
##Overview
DjangoWStyle is a custom Format for the Python library vectorformats (http://packages.python.org/vectorformats/). DjangoWStyle extends the Django format in two ways:
1. It adds an arbitrary `style` object to the `property` key. This is useful when working with a mapping library that can process GeoJSON layers (like Leaflet: http://leaflet.cloudmade.com).
2. It provides a way to get related attributes of a queryset that are not direct attributes of the queryset being serialized (like foo.bar_set.count(), for instance).

##Functionality
###Style
Leaflet looks for the key 'style' when parsing a GeoJSON object. It then uses the key/value pairs in the style object to style the GeoJSON features on the map. Here is a basic usage example:

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

###Related Data

DjangoWStyle can retrieve attributes and aggregate information from related models.

Assume the following Model setup:

    class City(models.Model):
		name = models.CharField("Name",max_length=50)
		state = models.ForeignKey("State")

	class State(models.Model):
		name = models.CharField("Name",max_length=50)

To get the number of cities in Pennsylvania, you would execute:
	>>> pa = State.objects.get(name="Pennsylvania")
	>>> pa.city_set.count()

To have the city count seralized, DjangoWStyle provides a `relation_data` kwarg that expects a dictionary in the following format: 
	
	{'method_name': 'model_name'}

__At the moment (v. 0.5.0), the only supported key is 'set_count'.__ For example, following the above example, here is how to get the city count information and serialize it along with the rest of the QuerySet data:

	>>> from vectorformats.Formats import DjangoWStyle, GeoJSON
	>>> qs = Model.objects.filter(state="Pennsylvania")
	>>> djf = Django.Django(geodjango="geometry", 
							properties=['name'],
							style={},
							relation_data = {'set_count': 'city'})
	>>> geoj = GeoJSON.GeoJSON()
	>>> string = geoj.encode(djf.decode(qs))
	>>> print string 

The above code will add the following key/value to the GeoJSON `property` object, assuming the count was 200 (just as an example):

	'city_set_count' : 200

If the query fails, which is most likely to be caused by there being no relation between the model being serialized and the specified related model, the output will be:
	
	'city_set_count' : 'AttributeError'
