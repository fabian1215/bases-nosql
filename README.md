# bases-nosql
GRUPO: Fabian Stiven Ovalle Muñoz codigo :1529887
       Daniel Alejandro Calero    codigo :1529118
1) Convertimos dos archivos de 2000 registros de articulos a .json con nodejs:                                                  
-csvtojson archivoscsv/scopus1.csv > archivosjson/newscopus1.json

-csvtojson archivoscsv/scopus2.csv > archivosjson/newscopus2.json

2) creamos una instancia de mongodb donde vamos a guardar los archivos (nosotros utilizamos docker toolbox)

docker run --name CancerDB -d -p 27017:27017 mongo --noauth --bind_ip=0.0.0.0

3) con la instancia corriendo, ingresamos a la base de datos CancerDB con el siguiente comando:    
use CancerDB
4) creamos una coleccion que solo permita 2500 articulos

-db.createCollection("cancerTotal", { capped : true, size : 10000000, max : 2500 } )


5) en la otra ventana que tenemos con la instancia de mongo corriendo, importanmos temporalmente los archivos en formato
.json para poder utilizarlos mas adelante al agregarlos a la coleccion.

-docker cp scopus.json CancerBaseDatos:/tmp/tem1.json

-docker cp scopus.json CancerBaseDatos:/tmp/tem2.json

6) Cargamos los registros a la base de datos haciendo uso de mongoimport, tener en cuenta que como en el diseño de la colección
se especifico que el maximo era 2500 articulos, asi subamos 4000 como tenemos en los dos archivos nunca pasara de 2500

Nota:el numero "94b5bbaf5f57" es el id del contenedor

-docker exec 94b5bbaf5f57 mongoimport -d CancerBD --collection cancerTotal --file/tmp/tem1.json --jsonarray

-docker exec 94b5bbaf5f57 mongoimport -d CancerBD --collection cancerTotal --file/tmp/tem2.json --jsonarray

7) hacemos consultas en la base de datos para comprobar que todo funciona bien por ejemplo:

-consulta total de articulos
-db.cancerTotal.count()

retorno 2500 que corresponde a todos los registros 

-consulta total articulos año 2019
-db.cancerTotal.count({"year": 2019}).count()

retorno 2499 articulos (habia un articulo que le cambiamos la fecha en el json para comprobar que este bien)

-consulta titulo de referencia 
-db.cancerTotal.find({"Source title": "Artificial Cells, Nanomedicine and Biotechnology"}).count()

retorna 4, ya que esa referencia se encuentra en 4 articulos.
