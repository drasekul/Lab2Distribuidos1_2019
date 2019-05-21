# Laboratorio N°2 Sistemas Distribuídos
## Integrantes:
- Mario Caceres
- Dragomir Sekul

## Descripción del problema:
El problema consta de disponibilizar un servicio de entorno analítico para poder realizar procesamiento de datos relacionados a Big Data y a otros tipos de datos.
Debido a la necesidad de recursos necesarios para realizar este tipo de servicio, se requiere utilizar un sistema distribuido para que exista una distribución del procesamiento y asi entregar los resultados en un menor lapso de tiempo.
## Enfoque de solución:
La solución a implementar consta de realizar este sistema distribuido dentro del servicio de clouding que entrega la empresa Microsoft mediante el uso de Microsoft Azure. Utilizando este servicio se implementan 3 máquinas virtuales, de los cuales se les va a instalar la herramienta Apache Spark para el procesamiento de Big Data y serán configuradas para que exista un nodo maestro y 2 nodos trabajadores que esten conectados al nodo ya mencionado. Además se les va a instalar los lenguajes a soportar dentro del servicio, los cuales son Python y R.
Para que exista una interfaz para las pruebas de código, se va a instalar Jupyter Notebook, que tiene como objetivo de ser intermediario entre el usuario y el sistema distribuido.
Para los datos a utilizar en el procesamiento de Big Data, se va utilizar una base de datos que entrega Azure, llamado Azure Cosmos DB, que consta en un servicio de base de datos NoSQL que utiliza Table Storage.

## Desarrollo de la actividad:

## Resultados:
