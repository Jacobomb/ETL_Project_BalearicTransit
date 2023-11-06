# ETL - Transito Balear de buques de pasaje

En este repositorio se encuentra el proceso de ETL completo para crear una base de datos del transito de buques de pasaje en el mar mediterráneo. 

Se puede seguir el proceso llevado a cabo por el autor en la siguiente [carpeta](https://github.com/Jacobomb/ETL_Proyect_BalearicTransit/tree/main/notebooks). Aquí se encuentran los notebooks con el código comentado para facilitar la comprensión del lector.

A continuación se comentan los pasos más importantes del trabajo, así como las herramientas y software utilizado. Finalmente se comentarán también trabajos futuros sobre los que el proyecto puede desarrollarse.

## Web Scraping

En este apartado se ha llevado a cabo lo que comunmente se conoce por el anglicismo *web scraping*, o raspado web en castellano.

Se trata de una técnica utilizada en este caso mediante aplicación del entorno [Selenium](https://www.selenium.dev/). Con ayuda de esta herramienta se simula la navegación de un humano en la web de interés incrustando un navegador en una aplicación.

Para este proyecto se ha aplicado la técnica a la web del [Ministerio de Transportes, Moviliad y Agenda Urbana](https://www.mitma.gob.es/) del Gobierno de España.

En esta [web](https://www.portsdebalears.com/es/buques-en-puerto) el autor ha automatizado la búsqueda de la previsión de tráfico marino de buques de pasaje para el rango de fechas comprendido entre el 1 de junio de 2023 y el 31 de agosto del mismo año. Se pretende llevar a cabo un estudio de la movilidad marítima en aguas españolas durante el verano.

Una vez el raspado ha terminado el autor consigue una tabla con 1500 filas, para cada una de ellas se tiene la siguiente información:
* *Barco*: nombre de la embarcación
* *Origen*: puerto de origen	
* *Destino*: puerto de destino
* *Llegada*: fecha de llegada al puerto de destino
* *salida*: fecha de llegada del puerto de destino
* *Alineacion*: ubicación en el puerto de destino (ver imagen 1)
* *Consignatorio*: representante de la naviera en el puerto de destino
* *GT*: Gross Tonnage - medida de capacidad del barco que cuantifica el volumen de todos los espacios interiores del buque (camarotes, alojamientos, etc.)
* *Escala*: asignación de atraque para un buque en un determinado puerto	
* *Bandera*: indica la nacionalidad de un barco, también conocida como pabellón
* *Eslora*: longitud de un buque desde la proa a la popa
* *Calado*: profundidad que alcanza en el agua la parte sumergida del buque.


![Puerto de palma](./images/puerto_atraques.png)

Se anima al lector a repasar el código encontrado en el fichero [1.TransitScrapper.ipynb](https://github.com/Jacobomb/ETL_Proyect_BalearicTransit/blob/main/notebooks/1.TransitScrapper.ipynb) de la carpeta [notebooks](https://github.com/Jacobomb/ETL_Proyect_BalearicTransit/tree/main/notebooks).

## Weather API

En este apartado se hecho uso de una API  para enriquecer la base de datos. Dado que en el primer paso se consiguió obtener una tabla donde se incluía la fecha de llegada y partida del buque a puerto, se decide hacer uso de la API [*Open-Meteo*](https://open-meteo.com/en/docs) para hallar el clima dado en la fecha de llegada del barco.

Entre la oferta de la API se incluían numerosas variables, pero se tomaron la suma total del precipitación (*daily_rain_sum*) y el viento máximo medido en la jornada (*daily_wind_speed_10m_max*) como variables más importantes de estudio.

Dado que no se podía pedir la altura de las olas ni el estado de la mar a la API, se decide trabajar con el viento máximo por estar directamente relacionado con la bravura del mar.

Para llevar a cabo la llamada a la API se necesitaba proporcionar las coordenadas (*latitud* y *longitud*) del puerto donde se encontraba el puerto para así conseguir el clima más preciso posible. 

Para conseguir las coordenadas de cada puerto se creó una lista de los valores únicos de los puertos de destino y se hizo uso del bot conversacional de inteligencia artificial Bard, ya que al estar conectado a internet puede proporcionar este tipo de datos con gran rapidez y ahorrar una cantidad de tiempo razonable al usuario.


![Puerto de palma](./images/coords.png)

Ya que la API necesitaba como argumentos de entrada las fechas en formato YYYY-MM-DD, se decidió separar las columnas *llegada* y *salida* en *llegada_fecha* y *llegada_hora* e idénticamente con *salida_fecha* y *salida_hora*.


## Obtenicón BBDD adicional del OTLE

