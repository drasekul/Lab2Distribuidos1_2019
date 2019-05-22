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
### Creación y Configuración Maquinas Virtuales 
Para empezar con implementear el sistema se requeire una cuenta en el portal de Microsoft Azure, al momento de realizar la cuenta se detecta la primera barrera para utilizar el servicio de Microsoft, el cual es que se requiere obligatoriamente el ingreso de una tarjeta de crédito. Pensando que esto conllevaría gastos monetarios, nos dimos cuenta que al crear la cuenta se nos entregó US$200 por un mes para utilizarlos en los servicios que entrega Azure, por lo que continuamos con la implementación del sistema distribuído.
Para crear las maquians virtuales, se realizan los siguitnes pasos:
1. Hacer click en la opción "Crear un recurso"
2.  Hacer click en "Ubuntu Server 18.04 LTS"
![image alt](https://i.imgur.com/yWJTsfm.png=WidthxHeight)
Esto llevará a los datos básicos necesarios para crear una máquina virtual, se les debe ingresar el tipo de suscripción a utilizar y el "grupo de recursos", este último corresponde a una coleccion de recursos que comparten los mismos permisos, ciclo de vida y directivas y si no existe uno creado se puede crear uno nuevo ene l momento. Otros de los datos necesarios son el nombre de la MV, la región en donde se desea almacenar (en este caso se almacenarán en Brasil), el tamaño de la MV y los daos para la cuenta de administrador, mietras que el resto de las opciones se mantienen por defecto. Este proceso se repite segun la cantidad de maquinas viertuales a utilizar, teniendo en cuenta que para una mejor interacción entre las máquinas se utiliza los mismos datos de administrador (usuario y contraseña).
![image alt](https://i.imgur.com/Zk77dVP.png=WidthxHeight)
Al momento de crear las MV's nos dimos cuneta de otra barrera, la cual consistia en que la suscripción que se utilizaba para crear estos recursos solo nos permitía utilizar un total de 4 vcpu, por lo que al momento de crear estos recursos, uno de ellos, considerado como Master para la implementación de Spark, tiene 2 vcpu mientras que los otros dos, considerados para ser Slaves, tendrán 1 vcpu.

### Conexión con Maquinas Virtuales
Finalmente para conectarse con las VM se tienen las siguientes direcciones, en donde la sintaxis para conectarse es la siguiente (master):
```
    ssh SDlab2@sparkmaster.brazilsouth.cloudapp.azure.com
```
La contraseña para conectarse a todas las máquinas es 'distribuidos1_2019'.

| Nombre  | Dirección                                         |
|---------|---------------------------------------------------|
| Master  | SDlab2@sparkmaster.brazilsouth.cloudapp.azure.com |
| Slave 1 | SDlab2@191.232.199.190                            |
| Slave 2 | SDlab2@191.232.215.192                            |

### Instalación de Herramientas
#### Python y R
Para la instalación de los lenguajes Python y R, se utiliza una conexión mediante ssh para poder ingresar a cada MV y realizar los siguientes comandos:
- Python:
    ```
    sudo apt-get update
    sudo apt-get install python3
    ```

- R:
    ```
    sudo apt-get update
    sudo apt-get install r-base
    ```
#### Java
Para la instalación de Java se usa el JDK 8 ya que es el que se recomienda para la utilización de Spark, ya que las versiones mas recientes resultaron en problemas a la hora de ejecutar Spark.
Para instalarlo simplemente se usa los siguientes comandos en cada una de las maquinas virtuales.
```
    sudo apt-get install openjdk-8-jdk
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64/

```

#### Spark
Para instalar spark se debe descargar la última versión disponible en https://spark.apache.org/downloads.html, en este caso se utiliza la versión 2.4.3.

Luego se descarga el archivo .tar en cada una de las maquinas virtuales con el siguiente comando:
```
wget http://us.mirrors.quenda.co/apache/spark/spark-2.4.3/spark-2.4.3-bin-hadoop2.7.tgz
```
Luego de descargado el archivo se descomprime con el siguiente comando:
```
tar -xvf spark-2.4.3-bin-hadoop2.7.tgz
```
Al tener todas las VM con la carpeta de spark disponible se procede a configurar spark para que funcione con un maestro y dos esclavos. Para esto se cambia al directorio que contiene los ejecutables de shell (.sh).
```
cd spark-2.4.3-bin-hadoop2.7/sbin

```
Una vez dentro se ejecuta cada .sh dependiendo de la VM en que se encuentre. Para los esclavos, se debe indicar la red local en donde se hacen correr para que sean visibles para el master, esta dirección se obtiene del portal de azure en base al  siguiente diagrama de la topologia de red.
![image alt](https://i.imgur.com/sggWPu0.png=WidthxHeight)
Luego para comenzar los servicios de cada uno se utilizan los siguientes comandos.
Master:
```
./start-master.sh

```
Slave:
```
./start-slave.sh spark://10.0.0.4:7077

```

Con esto ya se pueden ejecutar tareas directamente en el master, y seran ejecutadas como un sistema distribuido,y cuyas pruebas se detallaran en el apartado de resultados.
#### Jupyter Notebook
Instalar Jupyter Notebook es un proceso simple, que se realiza con los siguientes comandos.
```
 python3 -m pip install --upgrade pip
 python3 -m pip install jupyter
```
Luego para poder crear Notebooks de Python o R se deben configurar correctamente. Para python se tiene disponible la opción de iniciar Notebooks por defecto, por lo que no se necesita otra acción.
Para el caso de R se debe instalar el paquete IRkernel, de modo de hacer visible la instalación de R a Jupyter, para esto se debe acceder a la consola de R y utilizar los siguientes comandos.
```
 install.packages('IRkernel')
 IRkernel::installspec()
```
Con esto se pueden crear Notebooks de R.

#### Base de Datos Cosmos db
Para crear la base de datos con Table Storage se utilizará el servicio de Azure Cosmos DB, un motor de base de datos NoSQL. Para crear esta base de datos desde el portal de Azure se debe dirigir a **Crear un Recurso** -> **Base de Datos** -> **Azure Cosmos DB**.
![image alt](https://i.imgur.com/pfkKjXx.png=WidthxHeight)
Luego se debe entregar los datos necesarios como la suscripción a utilizar y el grupo de recursos en dodne se utilizará la base de datos, además del nombre u la ubicación de esta. Para que la base de datos trabaje como Table Storage se debe elegir la opcion de **Tabla de Azure** en la opción de API, mientras que el resto de las opciones se dejan por defecto
![image alt](https://i.imgur.com/K3uauIn.png=WidthxHeight)

Ya teniendo la base de datos creada se puede ir a este recurso para agregar los datos, para eso dentro de este esta la opcion **Agregar Tabla** en donde abrirá el explorador de datos con el que podemos crear tanto tablas e identidades.
![image alt](https://i.imgur.com/uphv2Ya.png=WidthxHeight)

Para conectar la base de datos a spark encontramos una barrera de que hay falta de documentacion respecto a conectar Spark a este tipo de base de datos, debido a la documentación se centra en el uso de spark con otra herramienta de Azure llamada HDInsight, por lo que no se ha podido realizar la conexión a la base de datos.

## Resultados:
### Jupyter Notebook: Python y R
En primer lugar, para conectarse con jupyter se debe realizar de manera distinta a la convencional, utilizando un tunel, para asignar una direccion local con el puerto en la maquina virtual que contiene el servicio de Jupyter, esto se hace de la siguiente manera.
```
 ssh -L 8000:localhost:8888 SDlab2@sparkmaster.brazilsouth.cloudapp.azure.com
```
Esto redirige a la máquina local en el puerto 8000 el servicio de Jupyter, para poder verlo en el navegador.
Luego en la VM Master se debe ejecutar el siguiente comando para iniciar el servicio de Jupyter.
```
 jupyter notebook
```
Para el caso de Jupyter, y luego de configurados los kernels en el paso anterior estos son visibles y pueden ser creados como nuevos notebooks,como se muestra en la siguiente figura. 

![image alt](https://i.imgur.com/j0C5mkV.png=WidthxHeight)

Para probar R, se crea un notebook y se realizan operaciones simples, como de suma y operaciones con  vectores.
![image alt](https://i.imgur.com/4UrGRhD.png=WidthxHeight)
Luego para probar Python se realiza lo mismo, pero esta vez se crea una función para determinar el factorial de un numero.
![image alt](https://i.imgur.com/0SuOYlS.png=WidthxHeight)
### Spark
Para el caso de las MV's con spark, se puede ingresar a la direccion ip del Master, el cual es http://191.232.50.8:8080/ donde se puede ver la conexion con los Slaves, como muestra la siguiente figura, en donde se puede ver el estado de los workers, su dirección (local) y la cantidad de memoria disponible.
![image alt](https://i.imgur.com/Yg31nna.png=WidthxHeight)

Luego para probar el funcionamiento en cluster, se ejecuta un ejemplo del calculo del numero Pi con 1000 iteraciones con el siguiente comando.
```
 cd spark-2.4.3-bin-hadoop2.7/bin/
 ./spark-submit --master spark://10.0.0.4:7077 ../examples/src/main/python/pi.py 1000
```
Luego de lanzada la tarea se puede revisar el estado de ésta en el mismo dashboard mencionado anteriormente, en donde se puede ver que la tarea esta corriendo.
![image alt](https://i.imgur.com/y5fKtIs.png=WidthxHeight)
Mas detalladamente se puede ver datos de la tarea y de los workers involucrados, que corresponden a los dos esclavos.
![image alt](https://i.imgur.com/Vf3kgOQ.png=WidthxHeight)








