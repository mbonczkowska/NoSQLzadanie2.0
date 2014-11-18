 Jako przykład danych użyłam informacji o pogodzie dla Endynburga. Dane te były sprawdzane co minutę od 1 maja 2006 do 31 grudnia 2009.  
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
Agregacja 4
10 najczęściej występującycych ciśnień
````
db.weather.aggregate({$group:{_id: "$atmospheric pressure (mBar)", count:{$sum: 1}}},{$sort:{count: -1}},{$limit: 10});
````
{ "_id" : 1011, "count" : 62335 }
{ "_id" : 1010, "count" : 61875 }
{ "_id" : 1016, "count" : 58653 }
{ "_id" : 1019, "count" : 57825 }
{ "_id" : 1017, "count" : 57323 }
{ "_id" : 1009, "count" : 56864 }
{ "_id" : 1008, "count" : 56791 }
{ "_id" : 1021, "count" : 56142 }
{ "_id" : 1012, "count" : 55932 }
{ "_id" : 1013, "count" : 54337 }

![GitHub Logo](/images/wykres4.png)
Format: ![Alt Text](url)

