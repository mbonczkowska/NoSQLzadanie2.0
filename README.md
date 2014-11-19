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


###Agregacja 1  
10 najczęściej występujących temperatur, gdy wiatr nie przekraczał 1 m/s
````
db.weather.aggregate(    
{$match:{"wind speed (m/s)" : {$lte:1}}},   
{ $group : { _id : '$surface temperature (C)', count: { $sum : 1 }}},     
{ $sort : { count : -1 }},     
{ $limit : 10 } )

````
Kod w Ruby:
````
#!/usr/bin/env ruby  

require 'rubygems'
require 'mongo'
@conn = Mongo::Connection.new
@db   = @conn['dataBase']
@coll = @db['weather']    

match =  { :$match => { :'wind speed (m/s)' => { :$lte => 1 }  } }
group = { :$group => { :_id=> '$surface temperature (C)', :count=> { :$sum => 1 } } } 
sort = { :$sort => { :count => -1}}  
limit = {:$limit => 10 }    

agr = @coll.aggregate([match, group,sort,limit])

````
Wynik:
````
{ "_id" : 13.95, "count" : 427 }  
{ "_id" : 10.72, "count" : 415 }  
{ "_id" : 11.56, "count" : 405 }  
{ "_id" : 13, "count" : 400 }  
{ "_id" : 12.51, "count" : 396 }  
{ "_id" : 11.89, "count" : 396 }  
{ "_id" : 10.89, "count" : 395 }  
{ "_id" : 13.46, "count" : 385 }  
{ "_id" : 10.95, "count" : 385 }  
{ "_id" : 12.31, "count" : 383 }  
````
  
  
![GitHub Logo](/images/wykres1.png)
###Agregacja 2  

Ile minut było bezwietrznych
````
db.weather.aggregate( [   
{ $match : { "wind speed (m/s)" : 0  } },   
{ $group: { _id: null, count: { $sum: 1 } } }   
] );
````
Kod agregacji w Ruby: 
````
match =  { :$match => { :'wind speed (m/s)' => 0  } }  
group = { :$group => { :_id=> nil, :count=> { :$sum => 1 } } }    

@coll.aggregate([match, group])
````

Wynik:
```
{ "_id" : null, "count" : 41623 }
```
Ile minut było wietrznych
````
db.weather.aggregate( [   
{ $match : { "wind speed (m/s)" : {$gt:0}  } },   
{ $group: { _id: null, count: { $sum: 1 } } }   
] )
````
Kod agregacji w Ruby:
````
match =  { :$match => { :'wind speed (m/s)' => { :$gt => 0 }  } }  
group = { :$group => { :_id=> nil, :count=> { :$sum => 1 } } }    

agr = @coll.aggregate([match, group])

````

Wynik:
````
{ "_id" : null, "count" : 1884743 }
````
![GitHub Logo](/images/wykres2.png)  

###Agregacja 3  

10 najczęściej występujących temperatur przy promieniowaniu słonecznym większym lub równym 0.3 (Kw/m2)
```
 db.weather.aggregate(    
 {$match:{"solar flux (Kw/m2)" : {$gte:0.3}}},   
 { $group : { _id : '$surface temperature (C)', count: { $sum : 1 }}},     
 { $sort : { count : -1 }},     
 { $limit : 10 }   
 )
 ```
 Kod agregacji w Ruby:
 ````
match =  { :$match => { :'solar flux (Kw/m2)' => { :$gte => 0.3 }  } }  
group = { :$group => { :_id=> '$surface temperature (C)', :count=> { :$sum => 1 } } }  
sort = { :$sort => { :count => -1}}  
limit = {:$limit => 10 }    

agr = @coll.aggregate([match, group,sort,limit])

 ````
 
 Wynik:
 ````
{ "_id" : 15.2, "count" : 347 }  
{ "_id" : 15.59, "count" : 347 }  
{ "_id" : 15.79, "count" : 346 }  
{ "_id" : 15.27, "count" : 346 }  
{ "_id" : 15.78, "count" : 340 }  
{ "_id" : 15.46, "count" : 339 }  
{ "_id" : 15.4, "count" : 337 }  
{ "_id" : 15.29, "count" : 335 }  
{ "_id" : 13.89, "count" : 330 }  
{ "_id" : 13.95, "count" : 325 }
````
  
  ![GitHub Logo](/images/wykres3.png)  
  
###Agregacja 4  

10 najczęściej występującycych ciśnień
````
db.weather.aggregate(  
{$group:{_id: "$atmospheric pressure (mBar)", count:{$sum: 1}}},  
{$sort:{count: -1}},  
{$limit: 10}  
);
````
Kod agregacji w Ruby:
````
group = { :$group => { :_id=> '$atmospheric pressure (mBar)', :count=> { :$sum => 1 } } }  
sort = { :$sort => { :count => -1}}  
limit = {:$limit => 10 }    

agr = @coll.aggregate([ group,sort,limit])  
````

Wynik:
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
````

![GitHub Logo](/images/wykres4.png)


