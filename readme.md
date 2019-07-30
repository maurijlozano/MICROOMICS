MicroOmics
==========
![alt text](https://github.com/maurijlozano/MICROOMICS/blob/master/dockerweb.png)

# Índice
1. [Información del curso](#id1)
2. [Trabajo Práctico](#id2)
3. [Día 1](#id3)
4. [Día 2](#id4)

<a name="id1"></a>
# Información del curso 

## Objetivos:
Que los alumnos logren:
- Adquirir habilidades para la obtención y análisis de resultados a escala *ómica* en el área de la microbiología  
- Generar un espacio para el análisis de casos y discusión de resultados de trabajos científicos  

## Programa resumido
Las actividades del curso constarán de clases teóricas, seminarios y un trabajo práctico.  
**Día 1**: Genómica (8hs) y meta-genómica. Introducción del tema, estudio de casos y trabajo práctico.  
**Día 2**: Transcriptómica (8hs). Introducción del tema, estudio de casos y trabajo práctico.  
**Día 3**: Proteómica (8hs) y Metabolómica(8hs). Introducción del tema y estudio de casos.  
**Día 4**: Exposiciones de alumnos. Integración de datos obtenidos con plataformas ómicas.  
**Día 5**: Exposiciones de alumnos, Casos de estudio integrales. Evaluación

- **Genómica**: Plataformas de secuenciación. Historia y últimas tecnologías. Procesamiento de lecturas y ensamblado. Anotación de genomas. Homología. Filogenia. Genómica comparativa.
- **Transcriptómica**: Antecedentes, otros métodos para la evaluación diferencial de la expresión. RNAseq. Procesamiento, análisis y visualización de los resultados. RNAseq de reguladores transcripcionales. Búsqueda de reguladores, mutagenesis y transcriptómica. Búsqueda de determinantes involucrados en la comunicación rizobio-leguminosa durante la simbiosis mediante Dual-RNAseq. Redes regulatorias de la expresión de flagelos en Bradyrhizobium diazoefficiens. Caracterización molecular de los mecanismos que regulan la transferencia conjugativa de plásmidos en rizobios. 
- **Proteómica**: Teoría e historia. Aplicaciones. Métodos. DIGE / Orbitrap. Análisis de los datos cuantitativos.
- **Metabolómica**: Definiciones. Aproximaciones experimentales para la determinación de metabolitos celulares. Visualización de redes metabólicas. Respuesta severa en Bradyrhizobium diazoefficiens y su relación con la simbiosis en soja. Aproximaciones ómicas. 

## **Trabajo práctico**: Manipulación de secuencias. Ensamblaje de un genoma y análisis comparativo del mismo. Mapeo de lecturas de RNAseq, análisis diferencial y visualización de resultados.

La parte práctica del curso la realizaremos utilizando una instanica local de Galaxy, por medio de Docker.
Para instalar docker en sus comutadoras personales (ya estará instalado para el curso) pueden seguir las instrucciones provistas en el enlace siguiente [Instalación de Docker](https://github.com/maurijlozano/MICROOMICS/blob/master/Instalacion_docker.md).  
En este documento encontrarán las instrucciones para realizar la instalación tanto en linux como en windows. Para usuarios de Mac, encontrarán información para la instalación de docker en la página siguiente: [Docker-mac ](https://docs.docker.com/docker-for-mac/install/).  

Adicionalmente, encontrará información sobre la imagen de docker utilizada en el curso en el enlace siguiente [MicroOmics](https://github.com/maurijlozano/MICROOMICS/blob/master/Microomics_docker/Info_docker_Image.md).  

<a name="id2"></a>
Trabajo Práctico 
================

El objetivo de los experimentos de secuenciación es obtener la sucesión de bases nucleotídicas (secuencia) que componen el ADN o ARN. Como ya se vio en las clases teóricas existen diferentes métodos de secuenciación, que a partir de una muestra de ADN/ARN (biblioteca/library) obtienen una colección de fragmentos de secuencia, usualmente cortos llamados comunmente 'lecturas' o **reads**.  
Los métodos modernos de secuenciación obtienen usualmente números masivos de lecturas por cada experimento. Dado que los métodos de secuenciación no son perfectos, las lecturas generalmente tienen un error asociado, como por ejemplo, la probabilidad de haber asignado un nucleótido incorrecto.

Estas secuencias pueden ser obtenidas a partir de muestras de ADN o ARN con diferentes objetivos.
* ADN: para reconstruir la secuencia genómica de un organismo, para caracterizar la diversidad de un ambiente (secuenciación de 16S rRNA, secuencias ITS, etc) o para caracterizar el metagenoma de un ambiente determinado.
* ARN: para reconstruir el transcriptoma de un organismo, para estudiar la expresión diferencial de genes o para estudiar el metatranscriptoma asociado a un ambiente determinado.

Dicho esto, como **objetivos del trabajo práctico** nos planteamos los siguientes puntos:

1. **Día 1**: A partir de datos de un experimento de secuenciación:
    * Analizar la calidad de las secuencias obtenidas
    * Realizar el *trimming* y filtrado de las secuencias
    * Realizar el ensamblado *de novo* de las secuencias
        * Analizar la calidad de los resultados
    * Realizar la anotación del genoma
    * Mapear las secuencias a un genoma de referencia y realizar la búsqueda de variantes
        
2. **Día 2**: A partir de datos de un experimento de RNAseq:
    * Analizar la calidad de las secuencias obtenidas
    * Realizar el *trimming* y filtrado de las secuencias
    * Mapear las secuencias a un genoma de referencia
    * Realizar el recuento de secuencias para cada transcripto
    * Realizar el análisis de expresión diferencial
    
DATA SETs -> los set de datos que vamos a utilizar se pueden descargar del siguiente [link](http://www.mediafire.com/file/6oq2yk7qkmzjfwy/datasets.tar.gz/file). Los Datos deberían ya estar descargados en las computadoras de la sala, pero los alumnos que utilicen sus notebooks deberán descargar este archivo.    

<a name="id3"></a>
Día 1 - Obtención de la secuencia genómica un Staphylococcus aureus imaginario que posee un genoma reducido 
-----------------------------------------------------------------------------------------------------------
En el trabajo práctico utilizaremos, por cuestiones de tiempo, un set de datos artificial perteneciente a un *Staphylococcus aureus* imaginario con un genoma miniatura. Este set de datos fue tomado de [Galaxy australia training](https://galaxy-au-training.github.io/tutorials/modules/spades/). El link de descarga del set de datos es [zenodo.org/record/582600](https://zenodo.org/record/582600).
Las lecturas corresponden a un experimento de secuenciación con Illumina de una cepa mutante de *S. aureus*. Los archivos que necesitamos son mutant_R1.fastq y mutant_R2.fastq, correspondientes a lecturas paired-end, de 150 bases de largo. Además, el número de bases secuenciadas equivale a una cobertura de 19x del genoma de la cepa salvaje (19x es una cobertura bastante baja!).

* Si desea utilizar otros set de datos revise la siguiente dirección. [Enlace](https://github.com/maurijlozano/MICROOMICS/blob/master/datos_alternativos.md)  
Los sets de datos provistos por Unicycler poseen tanto secuencias largas como cortas, pero se requiere mucho más tiempo para realizar el ensamblado.  

## Iniciar Galaxy docker
El primer paso será iniciar el Docker de Galaxy en un puerto local, y montando la carpeta de trabajo. Dado que las instacias de Docker son de solo lectura, es necesario exportar los datos en una carpeta compartida.

`docker run -d -p 8080:80 -v ~/galaxy_storage/:/export/ maurijlozano/microomics`

En este comando, el texto siguiente *"~/galaxy_storage/"* debe ser reemplazado por la ruta en la cual se guardarán los archivos.


Para la realización del TP, además de esta guía se pueden mirar los tutoriales de la página [Galaxy Training!](https://galaxyproject.github.io/training-material/topics/introduction/tutorials/galaxy-intro-ngs-data-managment/tutorial.html) 


## Crear historia

* Hacer un click en el icono de un engranaje (**History options**) en la parte superior del panel **history**
* Hacer un click en **Create New**
* Hacer un click en **Unnamed history** y luego click en **rename history** en la parte superior del panel **history**
* Escribir el nuevo nombre


## Cargando los datos de secuenciación
Como ya se vio en la teoría existen diversas tecnologías de secuenciación, a partir de las cuales obtendremos uno o múltiples archivos, en general, de tipo FASTQ.
Para comenzar el análisis de las secuencias, la primer etapa es cargarlas en el servidor de galaxy. Nosotros ahora estamos trabajando en una instancia local del servidor, por lo que la carga de archivos será rápida. **Recuerden, que si utilizaran el servidor online, según el tamaño de los archivos la carga puede ser lenta, y para archivos grandes (> 2gb) es necesario utilizar un ftp.**

**Procedimiento**  
1. En el panel de la izquierda, seleccionar el icono **upload**, o seleccionar **Get Data** -> **Upload File from your computer**
    * Seleccionar la opción: **chose local file**
Recuerde que pueden cargarse múltiples archivos en una sola operación.

## Colecciones
Los archivos con las lecturas se pueden organizar en colecciones para simplificar la ejecución de los diferentes programas (procesamiento colectivo), y para organizar de una mejor manera la *historia*.  
ej. Se cuenta con cuatro archivos correspondientes a un experimento de secuenciación paired-end. Este dataset contiene por lo tanto secuencias forward (directas) y reverse (reversas), que pueden organizarse en colecciones para indicar a los programas los datos que estan pareados (f,r), y que pertenecen a una misma muestra (S1 o S2).  
```
   Data sets individuales                           Colección
   
                                                   ┌─────────────┐
    ┌─ ─ ─ ─ ─ ─ ┐                                 │┌fastq1.f┐ S1│
    ¦fastq1.f S1 ¦                                 │└fastq1.f┘   │
    ¦fastq1.r S1 ├─────────────────────────────────┼ ─────────── ┤
    ¦fastq2.f S2 ¦                                 │┌fastq2.f┐ S2│
    ¦fastq2.r S2 ¦                                 │└fastq2.r┘   │
    └─ ─ ─ ─ ─ ─ ┘                                 └─────────────┘
```

### Como crear una colección!
1. Clickear en la tick-box
2. Seleccionar los dataset de la historia a incluir en la colección
3. Clickear en **for all selected**
4. Seleccionar el tipo de colección a crear
    * Puede ser **Build Dataset List** -> para datasets no **paired-end**
    * Puede ser **Build List of Dataset pairs** -> para datasets **paired-end**
        * En este caso se pueden usar filtros para que se seleccionen automáticamente los pares de muestras
        * Para lecturas pareadas es conveniente generar ambos tipos de colecciones.

#### También se puede cargar las secuencias directamente a una colección!

### Operaciones con colecciones
1. Renombrar, para facilitar su identificación
2. Etiquetar: hay dos tipos de etiquetas que facilitan el seguimiento de los datos
    * Para crear una etiqueta, clickear en el icono de etiqueta (tag). Hay dos tipos de etiquetas:
        * normales
        * hashtag (#) -> estas se propagan a los resultados!

## Manipulación y control de calidad de archivos FASTQ
### ¿Qué es el formato FASTQ?
El formato FASTQ es un formato para datos de secuenciación que además de incluir la secuencia nucleotídica para cada lectura incluye la *calidad* para cada posición. En la actualidad, el estándar es el formato FASTQ sanger que se muestra a continuación.

```
@M02286:19:000000000-AA549:1:1101:12677:1273 1:N:0:23
CCTACGGGTGGCAGCAGTGAGGAATATTGGTCAATGGACGGAAGTCTGAACCAGCCAAGTAGCGTGCAG
+
ABC8C,:@F:CE8,B-,C,-6-9-C,CE9-CC--C-<-C++,,+;CE<,,CD,CEFC,@E9<FCFCF?9
```

La primer linea para cada lectura contiene un **@** seguido de una identificación (read ID) e información adicional de la corrida de secuenciación.  
La segunda linea contiene la secuencia asignada.  
La tercera linea contiene un **+**, opcionalmente seguido de más información.  
La cuarta linea contiene el puntaje de **calidad** codificado como una serie de caracteres ASCII. Para secuencias FASTQ Illumina >1.8 la codificación es Phred+33 - ASCII desde el caracter 33(!) al 126(~).  
La calidad Q es un entero representando la probabilidad *p* de que la base asignada sea incorrecta. Para una probabilidad menor a 0.05 -> Q tiene que ser mayor a ~13. **Q = -10 log(p)**  
En general, el punto de corte utilizado es un valor de ~>20, correspondiente a una p < 0.01, es decir un error cada 100 bases (99% de precisión).  

### Distintos formatos de archivo FASTQ
#### Paired end vs mate-pair
Existen distintos tipos de experimentos de secuenciación, que generan diferentes tipos de archivos FASTQ. Estos experimentos fueron pensados con dos objetivos: a. aumentar el largo de cada lectura (paired-end) y b. conectar regiones genómicas alejadas en una longitud determinada (2kb, 8kb, etc) para lograr el ensamblado de genomas cerrados (mate-pair). Con el advenimiento de los métodos de secuenciación de fragmentos largos (Nanopore, PACBIO) la tecnología mate-pair está dejándose de usar.  

En ambos casos un fragmento determinado de la biblioteca de ADN es secuenciado de ambos extremos, quedando ambas secuencias vinculadas (ver ID de la secuencias). En estos casos se pueden obtener dos tipos de archivos FASTQ: 

1. Dos archivos FASTQ (uno por cada extremo secuenciado)
En este caso, los ID deben coincidir, y además, el orden es importante.

```
Archivo 1
 @M02286:19:000000000-AA549:1:1101:12677:1273 1:N:0:23
 CCTACGGGTGGCAGCAGTGAGGAATATTGGTCAATGGACGGAAGTCT
 +
 ABC8C,:@F:CE8,B-,C,-6-9-C,CE9-CC--C-<-C++,,+;CE
 @M02286:19:000000000-AA549:1:1101:15048:1299 1:

Archivo 2
 @M02286:19:000000000-AA549:1:1101:12677:1273 2:N:0:23
 CACTACCCGTGTATCTAATCCTGTTTGATACCCGCACCTTCGAGCTTA
 +
 --8A,CCE+,,;,<CC,,<CE@,CFD,,C,CFF+@+@CCEF,,,B+C,
 @M02286:19:000000000-AA549:1:1101:15048:1299 2:N:0:23
```

2. Un único archivo conteniendo las dos secuencias pareadas
En este caso, el ID coincide y se designa cada lectura pareada con /1 o /2.

```
@1/1
AGGGATGTGTTAGGGTTAGGGTTAGGGTTAGGGTTAGGGTTAGGGTTA
+
EGGEGGGDFGEEEAEECGDEGGFEEGEFGBEEDDECFEFDD@CDD<ED
@1/2
CCTAACCCTAACCCTAACCCTAACCCTAACCCTAACCCTAACCCTAAC
+
```

## Determinando la calidad de las lecturas - FastQC
La principal herramienta que vamos a usar es FastQC. Adicionalmente, el análisis de muestras múltiples se puede realizar utilizando MultiQC, esta herramienta toma el output de varios análisis de FastQC y los muestra de forma comparativa.

1. En el panel de herramientas, seleccionar Quality control -> FastQC
    * En *Short read data from your current history* seleccionar las lecturas a analizar (este procedimiento debe realizarse para cada lectura). *Acá, se puede elegir cargar una lista o colección!*. Lo mejor es usar una lista *no pareada* para poder analizar los datos en conjunto con MultiQC.

* FastQC generará dos archivos: RawData y Webpage.
* En la *historia* abrir el archivo html generado, haciendo click en el icono del ojo.
* Diferentes datos para analizar
    * Per base sequence quality: x= posición a lo largo de las lecturas, y= calidad (Phred). 
    * Per base sequence content: x= posición a lo largo de las lecturas, y= frecuencia de cada base
    * Per base N content: x= posición a lo largo de las lecturas, y= %N
    * Adapter Content: x= posición a lo largo de las lecturas, y= % Adaptadores
    * Per sequence quality scores: x= calidad (Phred), y= número de secuencias
    * Per sequence GC content: x= %GC medio, y = número de secuencias
    * Sequence length distribution: x= largo de la secuencia, y= número de secuencias
    * Sequence Duplication Levels: x= grado de duplicación, y= % de secuencias

2. Para analizar una colección en conjunto utilizar MultiQC
* Una vez corrido el FastQC, se puede analizar los datos en su conjunto mediante MultiQC.
    * En MultiQC seleccionar como herramienta con la que se obtuvieron los resultados FastQC. MultiQC puede trabajar con resultados de colecciones tipo flat (no pareadas). Como set de datos para MultiQC seleccionar la colección "RawData" generada por FastQC.
    * Visualizar los resultados haciendo click en el ojo de galaxy.

## Filtrado y recortado
La etapa siguiente es filtrar y recortar las secuencias basándonos en el análisis de calidad, ya que secuencias en las que los nucleótidos incorporados tienen alto error podrían generar sesgos en los análisis posteriores.
El procesamiento típico de las secuencias incluye:
* Filtrado de secuencias con 
    * un score medio bajo 
    * secuencias muy cortas
    * con muchas Ns
    * según su %GC

* Cortado y enmascarado de secuencias:
    * en las regiones de baja calidad
    * en su comienzo o final
    * recortado de secuencias correspondientes a adaptadores

**Filtrado/enmascarado/recortado de secuencias**
Para realizar el filtrado/recortado de las secuencias se pueden utilizar varios programas: Trimmomatic, Trim Galore, Cutadapt y otros. Nosotros utilizaremos el programa trimmomatic que permite procesar colecciones de secuencias pareadas.

### Trimmomatic
Para filtrar las secuencias o extremos con baja calidad utilizaremos Trimmomatic. Lo primero es seleccionar nuestras lecturas pareadas, ya sea de forma individual o como colección.

* Single-end or paired-end reads? -> Paired-end (as collection)
    * Select FASTQ dataset collection with R1/R2 pair
    
* Perform initial ILLUMINACLIP step? -> No (nuestras secuencias no poseen los adaptadores de Illumina)
* Trimmomatic Operation  
Por defecto, Trimmomatic realiza una operación de Slidingwindow.

* Output trimlog file?  
* Output trimmomatic log messages?  
    *Los archivos log son necesarios si queremos analizar los resultados con MultiQC*.


**Operaciones que puede realizar Trimmomatic:**
ILLUMINACLIP: remueve adaptadores y secuencias específicas de illumina.  
SLIDINGWINDOW: realiza el recorte por 'sliding window', cortando una vez que la calidad promedio de la ventana cayó por debajo de un determinado valor.  
MINLEN: elimina la lectura si tiene un largo menor al mínimo.  
LEADING: corta bases del comienzo de la lectura si su calidad es menor a un valor dado.  
TRAILING: corta bases del final de la lectura si su calidad es menor a un valor dado.  
CROP: corta las secuencias a un largo uniforme.  
HEADCROP: corta un número especificado de bases del comienzo de cada lectura.  
AVGQUAL: elimina las secuencias con un score medio menor al especificado.  
MAXINFO: corte adaptativo, maximiza el valor de cada lectura balanceando el largo y la calidad.  

Para secuencias pareadas Trimmomatic devuelve dos resultados, uno en el que ambos pares están presentes, y un segundo resultado conteniendo las secuencias no pareadas.

### otra ocpción -> Trim Galore!
* Seleccionar ->  “Is this paired- or single-end”: Paired-end
    * Seleccionar -> param-file “Reads in FASTQ format #1”: 
    * Seleccionar -> param-file “Reads in FASTQ format #2”: 

### otra opción: Cutadapt (No instalado)
* Utilizar Cutadapt con los siguientes parámetros
* “Single-end or Paired-end reads?”: Paired-end
    * param-file “FASTQ/A file #1”: reads_1 (Input dataset)
    * param-file “FASTQ/A file #2”: reads_2 (Input dataset)

* Read options: Para las opciones de la lectura1 y lectura 2:
* **En esta parte hay que indicar que adaptadores fueron usados, en general el proveedor del servicio de secuenciación nos enviará las secuencias con los adaptadores ya removidos...**

* En “Adapter Options” -> “Minimum overlap length”: 3
* En "filter Options” -> “Minimum length”: 20
* En “Read Modification Options” -> “Quality cutoff”: 20
* En “Output Options” -> “Report”: Yes

* En el caso de colecciones pareadas, Cutadapt devuelve las lecturas por separado...*

[Mirar como funciona el algoritmo de Cutadapt!](https://galaxyproject.github.io/training-material/topics/sequence-analysis/tutorials/quality-control/tutorial.html)

## Ensamblado de la secuencias utilizando Unicycler
Unicycler es un ensamblador híbrido para genomas bacterianos. Puede ensamblar lecturas generadas con la plataforma Illumina por medio de SPAdes, pero tambien puede utilizar lecturas largas generadas con PacBio o Nanopore utilizando una tubería *miniasm+Racon*. Para generar el mejor ensamblado, si se dispone de lecturas cortas y largas, unicycler realizará un ensamblado híbrido facilitando la obtención de un genoma cerrado de alta calidad.  
Para más información mirar la página de [Unicycler](https://github.com/rrwick/Unicycler).

Las opciones son las siguientes:
* Paired or Single end data?  -> seleccionar el tipo de datos de secuencia
 *Select a paired collection -> seleccionar los datos obtenidos por trimmomatic
    
* Select long reads. If there are no long reads, leave this empty

* Select Bridging mode
   Afectará principalmente ensamblados realizados utilizando tanto secuencias largas como cortas.  
* Exclude contigs from the FASTA file which are shorter than this length (bp) -> defecto 100  
* The expected number of linear (i.e. non-circular) sequences in the assembly -> defecto 0  

El resto de los parámetros se pueden dejar en un principio por defecto. Hay muchos parámetros que corresponden a otros programas utilizados en la tubería, principalmente SPAdes, TBLASTN, Pilon, etc.  

Unicycler generará dos archivos: Final Assembly y Final Assembly Graph.


## Análisis de la calidad del ensamblado mediante QUAST
Quast es un programa muy completo para analizar la calidad de un genoma ensamblado *de novo*, incluyendo además la opción de búsqueda y anotación de genes (Glimmer o GeneMarkS). Si se cuenta con un genoma de referencia, realizará además una serie de análisis de interés, como la búsqueda de polimorfismos y el alineamiento de los contigs al genoma de referencia.

Existen una serie de parámetros de uso común para evalaur la calidad de un ensamblado:
**N50** es la longitud del contig para el cual, si sumamos su longitud y la de todos los contigs de mayor tamaño, tenemos por lo menos el 50% del total secuenciado.  
**NG50**, solo se calcula si tenemos un genoma de referencia, y es igual a N50, pero tomando como largo total el del genoma de referencia.  
Análogamente se definen N75 y NG75.  
**L50** (L75, LG50, LG75) es el número mínimo de contigs cuyo largo sumado es mayor al 50% del largo total ensamblado.  
**NAxx** (NA50,NGA50, ...) corresponde a los valores anteriores pero calculados teniendo en cuenta los alineamientos en vez de los contigs.  
Si utilizamos un genoma de referencia, el Quast reportará también los contigs que están mal ensamblados (misassemblies) con respecto a la referencia. Para que un contig sea reportado como mal ensamblado debera alinear incorrectamente con el genoma de referencia, esto es, por ejemplo:  
* relocation: el extremo izquierdo alinea a más de 1kb de distancia del extremo derecho en el genoma de referencia, o están solapados en más de 1kb (Los contigs no son perfectamente contiguos).  
* translocation: las secuencias alinean en cromosomas diferentes  
* inversion: la secuencia alinea en hebras diferentes  
Para más información mirar el [manual](http://quast.bioinf.spbau.ru/manual.html).  

**Para correr Quast completar los siguientes campos:**
* Use customized names for the input files? -> si queremos nombres personalizados en los gráficos  
   * Contigs/scaffolds file -> seleccionar el archivo Final Assembly genardo por Unicycler
* Type of assembly -> Genome  
* Use a reference genome? -> yes  
   * Reference genome -> seleccionar el genoma de referencia
   * Genomic feature positions in the reference genome -> seleccionar el archivo GFF de features  
   * Type of organism -> prokaryote
* Lower threshold for a contig length (in bp)-> defecto 500
* Are assemblies scaffolds rather than contigs? -> No
* Is genome large (> 100 Mbp)? -> No

En la pestaña *Genes* se puede pedir que realize la predicción de genes. 
En la pestaña *alingment* se pueden modificar varios parámetros del alineamiento. En este caso los dejaremos con los valores por defecto.  

Las opciones siguientes tienen que ver con la detección de misassemblies (Las dejaremos en los valores por defecto).  
* Lower threshold for the relocation size (gap or overlap size between left and right flanking sequence)  
* Max allowed scaffold gap length difference for detecting corresponding type of misassemblies--scaffold-gap-max-size)  
* Lower threshold for detecting partially unaligned contigs  

*Algunos de los gráficos no son mostrados en galaxy pero mirando el archivo log se puede buscar la ruta en la que han sido guardados dentro de la carpeta ~/Galaxy_storage/*
  
  
## Anotación del genoma utilizando Proka

En esta parte utilizaremos el software Prokka para realizar la anotación del genoma. El Prokka es una tubería que utiliza programas de terceros para identificar regiones codificantes de proteinas y genes codificantes de RNAs (tRNAs, rRNAs).  
Para la anotación de proteinas, primero usa el programa Prodigal para identificar la secuencia codificante, y luego asigna una función probable por transferencia por homología utilizando una o varias bases de datos de proteinas y dominios protéicos.  
Como resultado, genera un archivo anotado en difenentes formatos, incluyendo GFF3.

**Procedimiento**
* Contigs to annotate -> seleccionar los contigs en formato fasta.  
* Locus tag prefix -> prefijo que agregará en el locus_tag de cada región codificante
* Locus tag counter increment -> 1
* GFF version -> versión del formato gff -> 3
* Force GenBank/ENA/DDJB compliance -> si queremos que sea compatible con genbank
* Add 'gene' features for each 'CDS' feature -> No/Yes
* Minimum contig size -> 200 (NCBI needs 200)

Luego hay parámetros que describen el centro de secuenciamiento, especie, género, etc.
* Sequencing centre ID (--centre)
* Genus name (--genus)
* Species name (--species)
* Strain name (--strain)
* Plasmid name or identifier (--plasmid)
* Kingdom (--kingdom) -> Bacteria
   * Genetic code (transl_table) -> 11 (se elige automaticamente con la selección del reino)
* Use genus-specific BLAST database -> Tiene que estar instalado
* Optional FASTA file of trusted proteins to first annotate from (--proteins)
* Improve gene predictions for highly fragmented genomes (--metagenome) -> No
* Fast mode (--fast) -> No
* Similarity e-value cut-off -> 0.000001
* Enable searching for ncRNAs with Infernal+Rfam (SLOW!) -> Yes/No
* Don't run rRNA search with Barrnap -> No
* Don't run tRNA search with Aragorn -> No
* Additional outputs
   * Select/Unselect all
      * Annotation in GFF3 format, containing both sequences and annotations (.gff)
      * Standard GenBank file. If the input was a multi-FASTA, then this will be a multi-GenBank, with one record for each sequence (.gbk)
      * Nucleotide FASTA file of the input contig sequences (.fna)
      * Protein FASTA file of the translated CDS sequences (.faa)
      * Nucleotide FASTA file of all the annotated sequences, not just CDS (.ffn)
      * An ASN1 format "Sequin" file for submission to GenBank. It needs to be edited to set the correct taxonomy, authors, related publication, etc. (.sqn)
      * Nucleotide FASTA file of the input contig sequences, with extra Sequin tags in the sequence description lines (.fsa)
      * Feature Table file (.tbl)
      * Annotations in tabular format including COGs etc.
      * Unacceptable annotations - the NCBI discrepancy report (.err)
      * Statistics relating to the annotated features found (.txt)

## Búsqueda de variantes polimórficas

En esta parte utilizaremos el software Snippy para detectar cambios entre el genoma secuenciado y el genoma de referencia. El programa [Snippy](https://github.com/tseemann/snippy) está diseñado con la velocidad en mente y es capaz de utilizar todos los procesadores/nucleos disponibles.  
Como resultado genera una serie de archivos, entre ellos:
* Tabla de snps -> una tabla con campos separados por tabulaciones o comas 
* Archivo VCF -> Variant Call Format es un derivado de GFF, para indicar las variaciones genómicas. Una descripción del formato VCF se puede ver en este [enlace](http://vcftools.sourceforge.net/VCF-poster.pdf).

**Procedimiento**
* Reference File (either in fasta or genbank format) -> usar el formato fasta (El gbk esta tirando un error.. :( )
* Single or Paired-end reads
   * Select a paired collection -> seleccionar las lecturas procesadas con trimmomatic
Luego hay una serie de parámetros avanzados que los dejaremos como están, y la selección de los formatos de salida que queremos.
* Output selection
   * Select/Unselect all
      * The final annotated variants in VCF format
      * The variants in GFF3 format
      * A simple tab-separated summary of all the variants
      * A summary of the samples and mapping
      * A log file with the commands run and their outputs
      * A version of the reference but with - at position with depth=0 and N for 0 to depth to --mincov (does not have variants)
      * A version of the reference genome with all variants instantiated
      * Output of samtools depth for the .bam file
      * The alignments in BAM format. Note that multi-mapping and unmapped reads are not present.
      * Zipped files needed for input into snippy-core

De estos formatos de salida seleccionaremos las variantes en formato VCF, GFF3, la tabla separada por tabs y el archivo BAM. Este último lo seleccionamos para realziar la visualización con JBROWSE, contiene todas las lecturas que mapearon contra el genoma de referencia con el software BWA.


## Alineamiento de los Contigs generados con LastZ / Minimap2
Para mapear los contigs contra el genoma de referencia utilizaremos el programa [LastZ](http://www.bx.psu.edu/miller_lab/dist/README.lastz-1.02.00/README.lastz-1.02.00a.html). 

**Procedimiento**
* Select TARGET sequnce(s) to align against -> 'from your history'
   * Select a reference dataset -> seleccionar el genoma de referencia
* Select QUERY sequence(s) -> seleccionar los contigs obtenidos por el ensamlador
Solo modificaremos alguna de la opciones para lograr que los fragmentos alineados sean largos.
* Chaining
   * Perform chaining of HSPs with no penalties -> yes
* Gapped extension
   * Perform gapped extension of HSPs -> Yes
Como resultado obtendremos un archivo BAM que podremos visualizar con programas como JBROWSE, IGV, Tablet, y otros.
Alternativamente se puede utilizar el programa minimap2 -> este no está instalado, pero se puede instalar ingresando a galaxy con la cuenta de administrador (admin@galaxy.org,admin).

## Visualización de los resultados con JBROWSE
JBROWSE es un visualizador de alineamientos y lecturas NGS. Para visualizar los resultados necesitamos en general archivos del tipo BAM (Mapeo de secuencias contra una referencia) o del tipo GFF (Genome Features).  
Las opciones disponibles son:

* Reference genome to display
   * Use a genome from history -> seleccionar el genoma de referencia
* Produce Standalone Instance -> Yes
* Genetic Code -> Standard
* JBrowse-in-Galaxy Action -> New instance

Luego hay que cargar la información de las pistas que queremos ver. Acá podemos organizar las pistas en varias categorías (Track groups), cada una con varias pistas:
1. Anotación del genomoa de referencia
   * GFF3, los datos de anotación del genoma de referencia
2. Alineamiento de los Contigs
   * BAM pileup: Resultados del programa LastZ/Minimap2
3. SNPs
   * BAM pilup: lecturas mapedas por Snippy
   * GFF3: SNPs en GFF3 obtenidos con Snippy

Por cada uno de estos items crearemos un track group, y dentro cargaremos las pistas de interés.

* Track Group
   * Insert Track Group
      * Track Category -> Nombre de la categoria a visualizar
      * Annotation Track -> Insertar una pista de anotación
         * Track Type -> seleccionar BAM pileup / GFF según corresponda
               * Seleccionar los datos desde la historia.
      * This is match/match_part data -> defecto No
      * Index this track -> Yes/No
      * JBrowse Track Type [Advanced] -> las dejamos como están
      * Track Visibility -> lo dejamos como está
      * Override Apollo Plugins -> lo dejamos como está
      * Override Apollo Draggability -> lo dejamos como está
* General JBROWSE Optional (Advanced) -> lo dejamos como está
* Plugins -> GC Content -> Acá se puede activar el GC y GC Skew
   * GC skew es cuando los nucleótidos G o C son más o menos abundantes un una región particular del ADN o ARN. En condiciones de equilibrio (si no hay presiones de seleccion y los nucleótidos están distribuidos al azar) la frecuencia de las cuatro bases en ambas hebras es igual. Sin embargo en la mayoría de las bacterias y algunas arqueas, la composición nucleotídica es asimétrica entre la hebra lider y resagada: la hebra lider contiene en general más G y T. Matematicamente se calcula como -> GC skew = (G - C)/(G + C).

  
  
  
<a name="id4"></a>
Día 2: RNAseq 
-------------

## Introducción
El estudio de RNAseq tiene como principal objetivo obtener la secuencia de los diferentes RNAs de un organismo, con principal interes en los los RNAs mensajeros (obtención del tranascriptoma). Adicionalmente, es de interés conocer la abundancia relativa de los mismos y como varía su expresión en diferentes condiciones de crecimiento, tejidos, o variantes genómicas.
En los siguientes enlaces encontrará información de utilidad para la realización del análisis de datos de RNAseq con Galaxy [1](https://galaxyproject.org/tutorials/rb_rnaseq/) [2](https://galaxy-au-training.github.io/tutorials/modules/dge/#upload-files-to-galaxy).

En este caso, las lecturas que utilizaremos corresponden a *Escherichia coli*, creciendo en dos medios de cultivo diferentes. El set de datos, es una versión reducida al 1% para acortar los tiempos de cálculo, tomada de [Galaxy Australia Training - RNA-Seq: Bacteria - Autor: Syme, Anna](https://doi.org/10.5281/zenodo.1311269). 
  
Existen dos métodos principales, los basados en genomas de referencia, o los independientes de genoma de referencia. En este último caso se ensamblan las lecturas en transcriptos usando métodos *de novo*. En el trabajo práctico solo nos dedicaremos a los métodos basados en un genoma de referencia.
  
En lineas muy generales el protocolo experimental cumple con los siguientes pasos:
1. Purificación de RNA
2. Transcripción reversa -> Generación del cDNA
3. Síntesis de la segunda cadena utilizando una DNA polimerasa
4. Preparación de una biblioteca de secuenciamiento

Las etapas anteriores omiten muchas consideraciones experimentales que en algunos casos son necesarias. Sin embargo, entre estas hay dos que describiremos a continuación por su importancia:

### Síntesis de la primera cadena.
Como la transcriptasa reversa requiere un oligonucleótido que actúe como cebador, según como se elige este cebador tendremos diferentes aproximaciones experimentales.
Por ejemplo, para Eucariotas es común utilizar como cebador oligo-dT, ya que los mRNAs procesados están poliadenilados.
Otra estrategia muy utilizada es utilizar oligonucleótidos de secuencia aleatoria (random priming) que permitirán la transcripción reversa en múltiples sitios internos.

### RNAseq específico de hebra (Strand-specific)
Los RNAs son de una sola cadena y por consiguiente tienen polaridad. En los experimentos mas típicos de RNAseq la polaridad se pierde ya que se construye una biblioteca a partir del DNA doble cadena. Existen diversos métodos para generar una biblioteca específica de hebra (strand-specific), que involucran en general la ligación de adaptadores en los extremos del RNA (los más comunes son Illumina TrueSeq RNAseq kits y dUTP tagging).

Dependiendo del método utilizado, tenemos que tener cuidado de como interpretar los datos. En general una inspección visual de las lecturas mapeadas puede darnos información sobre el tipo de biblioteca que tenemos. (Utilizando JBROWSE, En rojo -> lecturas mapeadas directas, En azul -> lecturas mapeadas en RC)
 
## Procesamiento de las Secuencias
El procesamiento en términos generales implica:  
1. Determinación de calidad de las lecturas
2. Filtrado y cortado de adaptadores
3. Mapeo contra el genoma de referencia 
    * Acá, tenemos que tener en cuenta si el organismo en estudio es pro- o eucariota. En el caso de organismos eucariotas tendremos que tener en cuenta el que as secuencias solo mapearán en los exones, y algunas de ellas atravesarán de un exón a otro. En estos casos se utilizan *spliced mappers* como TopHat, HiSat y STAR.
4. Reconstrucción del transcriptoma
    * En esta etapa, las lecturas mapeadas al genoma y las uniones de exones, son utilizadas para construir modelos de transcriptoma (Splicing alternativos). Existen distintas herramientas, una de las más utilizadas es cufflinks (y ahora del mismo grupo, Stringtie). Nuevamente, esto no será necesario para el análisis del transcriptoma de procariotas.
5. La siguiente etapa es la cuantificación de los transcriptos. 
    * Esto implica asignar las lecturas a los transcriptos. En este punto, Cufflinks (Stringtie) ya tienen la información sobre que lecturas mapean contra cada transcripto, por lo que solo resta contarlas! Stringtie, adicionalmente realiza la cuantificación.
    * Existen otras herramientas que mapean directamente las lecturas al transcriptoma, asumiendo que existe un transcriptoma de buena calidad para el organismo en cuestión (en general se usa en humanos o ratón; Sailfish, Kallisto, Salmon)
    * El recuento de las lecturas asociadas a cada transcripto en general es una *muy mala aproximación* a la cantidad de transcripto, esto se debe distintos factores, principalmente el **largo del transcripto**. Por ende, los recuentos deben ser normalizados. Existen distintos métodos de normalización:
    * Normalización dentro de una misma muestras (RPKM, FPKM, TPM, rlog)
    * Normalización entre muestras
6. Análisis de expresión diferencial
    * Es importante tener en cuanta que los valores normalizados anteriormente no deben compararse directamente entre condiciones para determinar el el grado de expresión diferencial. Con este objetivo existen herramientas específicas que calcualrán la magnitud del cambio y su significancia estadística, teniendo en cuenta, si existe, la información de las réplicas biológicas.
    * Las mejores aplicaciones para realizar el análisis diferencial son edgeR, DESeq/DESeq2, y limma-voom.

## Generar las colecciones
En este caso nos va a interesar generar una colección con todas las lecturas, tanto las correspondientes a E. coli* crecida en medio LB y como las correspondientes a *E. coli* crecidas en medio MG. Más adelante, si es necesario, estas se podran separar utilizando las herramientas para colecciones.

## Determinar la calidad de las lecturas - FastQC / filtrados y recortes (Trimmomatic)
Igualmente que en el caso anterior, es importante verificar la calidad de las lecturas obtenidas y realizar los filtrados y recortes necesarios.
* Correr el FastQC en la colección con todas las lecturas. Esto lo haremos para las dos colecciones generadas anteriormente.
* Correr el MultiQC con los resultados de FastQC
* Analizar los datos
* Correr Trimmomatic para eliminar secuencias con baja calidad.
* Volver a analizar los datos con FastQC para ver la calidad de los datos procesados.

## Genoma de referencia
El genoma de referencia es el de *E. coli* K12. Además de la secuencia en formato fasta tenemos las anotaciones de transcriptos en formato GTF. Para la visualización con JBROWSE utilizaremos el formato GFF, por lo que será mecesario convertir el archivo .gtf a .gff. Para ello utilizaremos la aplicación GTF2GFF. 
* Convert this query -> Acá deberemos seleccionar el archivo GTF. 
   * Si no sale el archivo, verificar que haya sido correctamente identificado como tipo GTF. Si fue identificado como GFF, hacer click en el icono del lápiz (edición) -> datatype -> buscar el formato GTF y presionar *change datatype*.

## Mapeo de secuencias en contra un genoma de referencia
El mapeo es el proceso mediante el cual las lecturas se alinean a un genoma de referencia. Los programas para realziar el mapeo toman como input un genoma de referencia y los sets de lecturas. El objetivo es alinear cada lectura en los archivos fastq en el genoma de referencia, permitiendo algunos errores (mismatches), indels (deleciones o inserciones) y el cortado de algunos fragmentos cortos en los extremos de las lecturas.
En la actualidad hay mas de 60 programas diferentes para realizar el mapeo, y el número sigue creciendo. En el trabajo práctico utilizaremos algunos de los más comunes como el **Bowtie2**, o el BWA.

### Mapeo de las lecturas obtenidas
Como las lecturas que tenemos provienen de un estudio de RNAseq de procariotas, no es necesario que realicemos un mapeo que tenga en cuenta splicing. Por ello realizaremos el mapeo con Bowtie2. **Recordar seleccionar la opción para que reporte la estadística del mapeo**. Adicionalmente, no será necesario realizar la reconstrucción del transcriptoma.

### Bowtie2
Para ejecutar el mapeador Bowtie2, necesitamos el genoma de referencia contra el cual alinear las lecturas. Para ello debemos cargar los archivos fasta en la historia.

El programa Bowtie2 toma los siguientes argumentos:
Ejemplo para lecturas de un experimento RNAseq. Solo describiremos los parámetros que ajustaremos a nuestros datos.

* Is this single or paired library -> single-end
   * FASTA/Q file -> seleccionar el archivo/colección

* Will you select a reference genome from your history or use a built-in index? -> genome from your history
   * Select reference genome -> seleccionar el genoma de referencia

* Select analysis mode -> Default setting only
* Save the bowtie2 mapping statistics to the history -> **Yes**
   * Esta opción es importante, nos va a permitir mirar la estadística del mapeo.
* Inspeccionar los resultados del mapeo clickeando en el icono **galaxy-eye**

Es interesante notar que algunas lecturas pueden ser mapeadas en varias posiciones (multi-mapped reads), estas en general corresponden a repeticiones en el genoma de referencia y son más abundantes cuando el largo de lectura es chico. Como es dificil determinar de donde provienen esas lecturas la mayoría de los mapeadores las ignoran.
Otra información de interés es el porcentaje de lecturas no mapeadas, estas pueden deberse a: un par de lecturas que alinean en diferentes regiones genómicas, lecturas multimapeadas y a lecturas que no alinean en nungún lugar. 

#### Causa de errores...
1. Artefactos de amplificación por PCR
2. Errores de secuenciación (en general son al azar, por lo que en análisis de variaciones se pueden evidenciar como ssingletones)
3. Erroes de mapeo ( en general en el entorno de regiones repetidas)

### Archivos SAM/BAM
El resultado de los alineamientos es un archivo BAM (Binary Alignment Map). El formato BAM, es un formato comprimido que contiene las secuencias que mapearon contra el genoma de referencia. Los archivos BAM están además acompañados de un índice .bai. 
Los archivos SAM, Sequence Alignment/Map, son la versión leible de los archivos BAM. Son archivos de texto, con campos separados por tabulaciones, que contienen la información del alineamiento.
La estructura es la de una tabla, con un **header (encabezado)** y a continuación los datos del **alineamiento**.

#### Encabezado
El encabezado contiene información de como el alineamiento fue obtenido. Todas las lineas comienzan con un @XX y pares tag:valor delimitados por tabulaciones.
Existen distintos tipos de datos, por ejemplo, filas comenzando con @SQ, contienen información acerca de las secuencias del genoma de referencia. Otros identificadores son @RG (read group), @PG (program).
Mas info en las especificaciones del formato [SAM](http://samtools.github.io/hts-specs/SAMv1.pdf).

#### Alineamiento

Col 	Field 	Type   	Brief Description  
1     QNAME 	String   Nombre de la secuencia (PE: o del par de lecturas)  
2     FLAG   	Integer  bitwise FLAG: da información sobre el mapeo. Ver abajo  
3     RNAME  	String 	Nombre de la secuencia de referencia  
4     POS    	Integer	La posición más a la izquierda de la 1-base mapeada (0 si no mapeo)  
5     MAPQ   	Integer	MAPping Quality (Phred)  
6     CIGAR 	String 	CIGAR String (Información detallada del alineamiento)  
7     RNEXT 	String 	Solo en PE. nombre de la secuencia de referencia del alineamiento del mate(next read)  
8     PNEXT    Integer	Solo en PE. La posición más a la izquierda de la 1-base mapeada del  mate(next read)  
9     TLEN 	   Integer	Solo en PE. Largo inferido del fragmento  
10    SEQ 	   String 	Secuencia sin indels de hebra directa de la lectura alineada  
11    QUAL 	   String 	ASCII Phred-scaled base QUALity+33  
12    OPT   	depende  Campos opcionales. TA,Tipo, ver abajo.  

*Ej.*  
<QNAME> <FLAG> <RNAME> <POS> <MAPQ> <CIGAR> <RNEXT> <PNEXT> <TLEN>    <SEQ>       <QUAL>     <OPT>  
read1     83     chrI  15364   30      51M     =     15535   232    CCA..CTCG     BB?H..    NM:i:0  


#### El campo **flag**
El campo flag codifica en un número diferente información sobre la lectura. El número se genera a partir de asignar 0 (No) o 1 (Si) a distintas características de la lectura.  
Ej.  
Binario     dec   Hex   Descripción  
00000000001    1 -> 0x1   Es la lectura pareada? 0 -> No 1 -> SI  
00000000010    2 -> 0x2   Están las dos lecturas correctamente mapeadas?  
00000000100    4 -> 0x4   La lectura no mapeo?  
00000001000    8 -> 0x8   La lectura pareda mapeo correctamente?  
00000010000   16 -> 0x10  La lectura mapeo a la hebra reversa?  
00000100000   32 -> 0x20  La lectura pareada mapeo en la hebra reversa?  
00001000000   64 -> 0x40  Es la lectura la primera del par?  
00010000000  128 -> 0x80  es la segunda del par?  
00100000000  256 -> 0x100 no es un alineamiento primario?  
01000000000  512 -> 0x200 La lectura falla la prueba de calidad?  
10000000000 1024 -> 0x400 La lectura es un duplicado óptico o de PCR?  
10000000000 2048 -> 0x800 es un alineamiento suplementario?  

Los números se suman para generar un código decimal. Ej.  
flag 69 -> (= 1 + 4 + 64) lectura pareada, primera del par y no mapeo.  
Para ver los diferentes flags es útil la siguiente página: [Flags](https://broadinstitute.github.io/picard/explain-flags.html)  

#### El campo **CIGAR**
CIGAR viene de **"Concise Idiosyncratic Gapped Alignment Report"**. Contiene información sobre las operaciones que tuvieron que hacerse para poder mapear la secuencia.  
Las operaciones son las siguientes:  
* M - Alineamiento (puede ser match o mismatch)
* I - Inserción en la lectura con respecto al genoma de referencia
* D - Deleción en la lectura con respecto al genoma de referencia
* N - Región salteada de la referencia. En alinemientos mRNA-genoma alignments, las N representan intrones. En otros casos no está definido.
* S - Soft clipping (Secuencias recortadas presentes en la lectura)
* H - Hard clipping (Las secuencias recortadas no están en el alineamiento); solo pueden estar presentes en la primer o última operación.
* P - Padding (silent deletion from padded reference)
* = - Sequence match (no muy usado)
* X - Sequence mismatch (no muy usado)

La suma de todos los simbolos debe ser igual al largo de la lectura. Ej.  
Referencia  A**GATAGCTG   CIGAR        Explicación  
lectura     AAGGATA*CTG   1M2I4M1D3M   1 match, 2 inserciones, 4 matches, 1 deleción y 3 matches! 

#### Los opcionales!
Los campos opcionales se incluyen en el último campo del BAM, con la sintaxis: <TAG>:<TYPE>:<VALUE>  
Donde type puede ser de las siguientes clases: A - Character, i - Integer, f - Float number, Z - String, H - Hex string.  
La etiqueta varía según el ensamblador, pueden ser: AS (alignment score), BC (Barcode sequence), HI (cual es la primer posición de la lectura que alinea), NH (Número de alineamientos para la secuencia), NM (distancia de la secuencia a la referencia, es 0 si son idénticas), MD (Posición exacta de los mismatches) y RG (Read Group).  
Los Tags comenzando con X, Y y Z no están estandarizadas. Por ejemplo XS, es usada por TopHat para indicar la hebra, mientras que el mismo tag es usado por BWA y Bowtie2 para guardar el score del mejor alinemiento, en casos de lecturas que mapean en sitios múltiples.  

#### Otra opción BWA / BWA-MEM (No instalado)
Otro mapeador altamente utilizado es BWA. 

### Para analizar los resultados de todas las lecturas utilizar MultiQC.
Los resultados del mapeo se pueden visualizar utilizando MultiQC, siempre y cuando se haya seleccionado que Bowtie2 genere el archivo de estadística.

### Visualizar en JBrowse
Para visualizar las secuencias mapeadas sobre el genoma de referencia se puede utilizar el progrma JBROWSE. Acá hay que agregar las pistas del genoma con su anotación y los archivos SAM del mapeo (Ver Día 1).
Mirando los resultados, tratar de determinar si nuestras secuencias vienen de una biblioteca de tipo **'stranded'**. *Recordar que en rojo se muestran las lecturas que alinearon en la hebra directa y en azul las que alinearon en la hebra complementaria*.  

#### Otra opción IGV
Para cargar genomas de referencia en IGV, los mismos tienen que estar indexados. Para generar el índice hay que abrir el fasta de referencia y entrar en la opción de edición (click en el lápiz). Dentro, elegir convertir, y seleccionar fai.
Se puede despues abrir el IGV online y cargar los archivos de la referencia (fas,fai) y del alineamiento (bam,bai).

## Recuento y análisis de expresión diferencial de secuencias que mapean en cada transcripto
### Utilizando el programa htseq-count
* Aligned SAM/BAM File -> seleccionar la colección de BAM generada por Bowtie2
* GFF File -> Archivo de anotación de *E. coli*.
* Mode -> Existen diferentes modos para realziar el recuento cuando las lecturas se solapan con más de un feature. Usaremos el modo de Union.
* Stranded -> Yes (Como determinamos previamente, o según nuestro protocolo experimental)
* Minimum alignment quality -> defecto 10

Los siguientes campos dependen del archivo GFF y hay que mirarlos con cuidado para que funcione!  
* Feature type -> exon (en general está ok)
* ID Attribute -> Hay que ver en el archivo GFF, columna Group cual es el campo con el nombre del feature, en nuestro caso *Parent*. 
* Set advanced options -> dejamos sin cambiar.


### También se puede utilizar el programa featurecounts...
En opciones hay que elegir:
* Alignment file -> Alineamiento hecho con Bowtie2 (SAM/BAM)
* Specify strand information -> Stranded (Forward en nuestro caso)
    * Esto se puede ver a partir de los resultados del alineamiento con Bowtie2, o con el protocolo utilizado para obtener las lecturas.
* Gene annotation file	-> seleccionar el archivo GFF
En este caso en los parámetros avanzados hay que especificar 
* GFF feature type filter -> *exon*
* GFF gene identifier -> *Parent*
El resto lo dejamos como está...

### Inspección de los resultados utilizando el Scratchbook! 
El icono a la derecha de User en la barra superior, es el Scratchbook, cuando está activado nos permite ver resultados de varios análisis de forma simultánea!
Utilizar el Scratchbook para visualizar los resultados de featurecounts!

## Análisis de expresión diferencial
Aca hay que tener las listas correspondientes a cada condición, o los datos de forma individual..  
Si tenemos una lista única se puede separar usando: Apply Rule to Collection.  
* Agregar filtrado, por expresión regular -> Lb (en un caso y en el otro MG)
* Agregar definición de columna -> 'column definition' -> List identifier -> columna A
La idea es obtener una colección LB_counts y otra MG_counts que utilizaremos a continuación en Deseq2.  

### Deseq2
Para realiar el análisis de expresión diferencial utilizaremos el programa Deseq2.
Pasos:
* how -> Select datasets per level
   * Factor -> Specify a factor name: por ejemplo 'Treatment'
   * Factor level -> 1:Factor level -> Specify a factor level: por ejemplo 'LB'
      * Counts file(s) -> Acá hay que seleccionar los datasets
   * Factor level -> 2:Factor level -> Specify a factor level: por ejemplo 'MG'
      * Counts file(s) -> Acá hay que seleccionar los datasets
* Files have header?: YES (para featurecounts) / No (para htseq-count)
* Choice of Input data -> count data
* “Output normalized counts table”: Yes
El resto de los parámetros los dejamos como están.  

Como resultado obtendremos una tabla y varias figuras, incluyendo:
* Tabla con los resultados del análisis de expresión diferencial, con los siguientes campos:
   * GeneID	
   * Base mean	-> el promedio normalizado del recuento en todas las condiciones
   * log2(FC)	-> el M-value
   * StdErr	-> el error estandard del M-value
   * Wald-Stats	
   * P-value	-> p-value de la significancia estadística de la diferencia observada
   * P-adj -> p-value ajustado por el procedimiento de Benjamini-Hochberg que controla el false discovery rate (FDR), que es la probabilidad de falsos positivos
* PCA (Principal component analysis) de las muestras: Sirve para analizar que tan similares son las muestras entre si. Es esperables que las diferentes condiciones estén separadas y las réplicas se encuentren cercanas entre si.
* Heatmap y  clustering jerárquico de las muestras: Similar al anterior, es una representación de las distancias entre las muestras basado en el logFC para cada gen.
* Dispersion estimates: la raíz cuadrada de la dispersión es el coeficiente de variación biológica (biological coefficient of variation - BCV), que es una estimación de la variabilidad entre réplicas biológicas. Por ejemplo: si la dispersión estimada es 0.19 -> BCV = 0.44 -> lo que quiere decir que la variación de expresión entre réplicas biológicas es de 44%.
El BCV representa la variabilidad relativa que uno observaría si pudiera medir perfectamente los niveles reales de expresión de cada muestra. 
* Histograma de p-values: distribución de p-values en la muestra.
* MA−plot for Condition: LB vs MG: El gráfico de M.value -> logFC, versus la intensidad de la señal (recuentos normalizados). Con puntos rojos, los genes con un p-value significativo.

### Crear un heatmap para los genes usando Heatmap2
Podemos hacer un heatmap para tratar de ver genes con comportamiento similar en base a los recuentos normalizados. Este gráfico lo vamos a hacer solo para los genes con una diferencia estadisticamente significativa.
* Usando: Filter -> Filtrar los resultados de Deseq2 en base al adj-pvalue
    * With following condition: c7 < 0.01 *donde c7 es la columna que contiene el p-value adj*
    * Number of header lines to skip: 0
        * Luego, podríamos quedarnos, por ejemplo, con los 100 genes con mayor diferencia significativa utilizando la función Sort. Esto no será necesario en nuestro caso por que tenemos menos de 100 genes expresados diferencialmente.
    * Bajar la tabla filtrada y agregar los headers!
    * Subir la tabla con la herrramienta upload

Para el Heatmap que queremos crear necesitamos una tabla con los recuentos normalizados de todas las muestras y para los genes seleccionados como diferencialmente expresados. Para esto tenemos que extraer de la tabla de recuentos normalizados las entradas correspondientes a los IDs de la tabla de expresión diferencial de Deseq2.  

*  Esto se puede hacer utilizando la función **Join two Datasets side by side** on a specified field  
   * join -> seleccionar la tabla de recuentos normalizados
      * using column -> 1
   * with -> seleccionar tabla filtrada con encabezados
      * using column -> 1
   * Keep lines of first input that do not join with second input -> No
   * Keep lines of first input that are incomplete -> No
   * Fill empty columns -> No
   * Keep the header lines -> Yes

Ahora nos quedamos con las primeras 7 columnas  
* Cut columns from a table (Galaxy Version 1.0.2)
   * Cut columns -> c1,c2,c3,c4,c5,c6,c7
   * Delimited by -> tab

Con la tabla generada podemos crear el Heatmap!  
* Heatmap2
    * Coloring groups -> blue->white->red
    * Data scaling -> by row

### Otros analisis
### Degust -> Web
Para correr el servidor Degust, deberán bajar la tabla generada por el Deseq2.
Esta herramienta online genera una serie de figuras de interés.



 
