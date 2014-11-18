 Jako przykład danych użyłam informacji o pogodzie dla Endynburga. Dane te są sprawdzane co minutę od 1 maja 2006 do 31 grudnia 2009.  
 Przykładowy rekord  
 ```
 {
	"_id" : ObjectId("546a68e8d4cf463efb9e99cd"),
	"date-time" : "2006/05/01 17:32",
	"atmospheric pressure (mBar)" : 998,
	"rainfall (mm)" : 0,
	"wind speed (m/s)" : 6.729,
	"wind direction (degrees)" : 275,
	"surface temperature (C)" : 9.82,
	"relative humidity (%)" : 54.8,
	"solar flux (Kw/m2)" : 0.167217,
	"battery (V)" : -9999
}
````
Ilość rekordów: 1926366  


Agregacja 1  
Ile było minut z temperaturą wyższą niż 28.5 stopnia
````
db.weather.aggregate( [ { $match : { "surface temperature (C)" : { $gt :28.5 } } }, { $group: { _id: null, count: { $sum: 1 } } } ] );
````
{ "_id" : null, "count" : 92 }

Agregacja 2
Ile było minut, w których było bezwietrznie
````
db.weather.aggregate( [ { $match : { "wind speed (m/s)" : 0  } }, { $group: { _id: null, count: { $sum: 1 } } } ] );
````
{ "_id" : null, "count" : 41623 }   
Agregacja 3
Ile było minut, z ciśnieniem atmosferycznym przekraczającym 1040 mBar w 2008 roku
````
db.weather.aggregate( [{ $match : { "date-time" : /^2008/,"atmospheric pressure (mBar)": {$gt:1040} } }, { $group: { _id: null, count: { $sum: 1 } } } ] );
````
{ "_id" : null, "count" : 5362 }  


