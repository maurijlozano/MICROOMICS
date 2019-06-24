MicroOMICS 
==========


La parte práctica del curso la realizaremos utilizando una instanica local de Galaxy, por medio de Docker.
Para instalar docker en sus comutadoras personales (ya estará instalado para el curso) pueden seguir las instrucciones provistas en el enlace siguiente [Instalación de Docker](https://github.com/maurijlozano/MICROOMICS/blob/master/Instalacion_docker_galaxy.md)

Adicionalmente, encontrará información sobre la imagen de docker utilizada en el curso en el enlace siguiente [MicroOmics](https://github.com/maurijlozano/MICROOMICS/blob/master/Instalacion_docker_galaxy.md)


Trabajo Práctico
================

El objetivo de los experimentos de secuenciación es obtener la sucesión de bases nucleotídicas (secuencia) que componen el ADN o ARN. Como ya se vio en las clases teóricas existen diferentes métodos de secuenciación, que a partir de una muestra de ADN/ARN (biblioteca/library) obtienen una colección de fragmentos de secuencia, usualmente cortos (aunque con los años se ha ido aumentando el largo de los mismos) normalmente llamados 'lecturas' o **reads**.
Los métodos modernos de secuenciación obtienen usualmente números masivos de lecturas por cada experimento. Dado que los métodos de secuenciación no son perfectos, las lecturas generalmente tienen un error asociado, como por ejemplo, la probabilidad de haber asignado un nucleótido incorrecto.
Estas secuencias pueden ser obtenidas a partir de muestras de ADN o ARN con diferentes objetivos.
 
* ADN: para reconstruir la secuencia genómica de un organismo, para caracterizar la diversidad de un ambiente (secuenciación de 16S rRNA, secuencias ITS, etc) o para caracterizar el metagenoma de un ambiente determinado.
* ARN: para reconstruir el transcriptoma de un organismo, para estudiar la expresión diferencial de genes o para estudiar el metatranscriptoma asociado a un ambiente determinado.

Dicho esto, como **objetivo del trabajo práctico** nos planteamos los siguientes puntos:

1. Día 1: A partir de datos de un experimento de secuenciación:
..* Analizar la calidad de las secuencias obtenidas
..* Realizar el trimming y filtrado de las secuencias
..* Mapear las secuencias a un genoma de referencia
...* Obtener el genoma y realizar la búsqueda de variantes
..* Realizar el ensamblado *de novo* de las secuencias
...* Analizar la calidad de los resultados
..* Realizar la anotación del genoma

2. Día 2: A partir de datos de un experimento de RNAseq:
..* Analizar la calidad de las secuencias obtenidas
..* Realizar el trimming y filtrado de las secuencias
..* Mapear las secuencias a un genoma de referencia
..* Reconstruir el transcriptoma
..* Realizar el recuento de secuencias para cada transcripto
..* Realizar el análisis de expresión diferencial


Día 1
-----


# Correr Galaxy docker
`docker run -d -p 8080:80 -v ~/galaxy_storage/:/export/ bgruening/galaxy-stable`

En este comando, el texto siguiente *"~/galaxy_storage/"* debe ser reemplazado por la ruta en la cual se guardarán los archivos.

*Ejemplo:*
`docker run -d -p 8080:80 -v ~/Programas/galaxy_storage/:/export/ bgruening/galaxy-stable`

Para la realización del TP, además de esta guía se pueden utilizar la página [Galaxy Training!](https://galaxyproject.github.io/training-material/topics/introduction/tutorials/galaxy-intro-ngs-data-managment/tutorial.html) 


#Crear historia

* Hacer un click en el icono de un engranaje (**History options**) en la parte superior del panel **history**
* Hacer un click en **Create New**
* Hacer un click en **Unnamed history** y luego click en **rename history** en la parte superior del panel **history**
* Escribir el nuevo nombre


#Cargando los datos de secuenciación
Como ya se vio en la teoría existen diversas tecnologías de secuenciación, a partir de las cuales obtendremos 1 o múltiples archivos, en general, de tipo FASTQ.
Para comenzar el análisis de las secuencias, la primer etapa es cargarlas en el servidor de galaxy. Nosotros ahora estamos trabajando en una instancia local del servidor, por lo que la carga de archivos será rápida. **Recuerden, que si utilizaran el servidor online, según el tamaño de los archivos la carga puede ser lenta, y para archivos grandes se recomienda utilizar ftp.**

##Procedimiento
1. En el panel de la izquierda, seleccionar el icono **upload**, o seleccionar **Get Data** -> **Upload File from your computer**
..* Seleccionar la opción: **chose local file**


##Colecciones
Los archivos con las lecturas se pueden organizar en colecciones para simplificar la ejecución de los diferentes programas (procesamiento colectivo), y para organizar de una mejor manera la *historia*.
ej.

   Data sets individuales                           Colección
                                                   ┌─────────────┐
    ┌─ ─ ─ ─ ─ ─ ┐                                 │┌fastq1.f┐ S1│
    ¦fastq1.f S1 ¦                                 │└fastq1.f┘   │
    ¦fastq1.r S1 ├─────────────────────────────────┼ ─────────── ┤       
    ¦fastq2.f S2 ¦                                 │┌fastq2.f┐ S2│
    ¦fastq2.r S2 ¦                                 │└fastq2.r┘   │
    └─ ─ ─ ─ ─ ─ ┘                                 └─────────────┘
                                    
##Como crear una colección!
1. Clickear en la tick-box
2. Seleccionar los dataset de la historia a incluir en la colección
3. Clickear en **for all selected**
4. Seleccionar el tipo de colección a crear
..* Puede ser **Build Dataset List** -> para datasets no **paired-end**
..* Puede ser **Build List of Dataset pairs** -> para datasets no **paired-end**
...* En este caso se pueden usar filtros para que se seleccionen automáticamente los pares de muestras
...* Para lecturas pareadas es conveniente generar ambos tipos de colecciones.

###También se puede cargar las secuencias directamente a una colección!

##Operaciones con colecciones
1. las colecciones se pueden renombrar
2. Etiquetas: hay dos tipos de etiquetas
--* Clickear an el icono de etiqueta
..* normales
..* hashtag -> estas se propagan a lo largo del prcocesamiento

*Es util cuando relicemos un mapeo tildar la opción* ***set reads group information***. Esto nos va a permitir luego unir los data sets en uno solo archivo sin perder información -> NGS: Picard → MergeSam


#Manipulación y control de calidad de archivos FASTQ
##¿Qué es el formato FASTQ?
El formato FASTQ es un formato para datos de secuenciación que además de incluir la secuencia para cada lectura incluye la *calidad* para cada posición. En la actualidad, el estándar es el formato FASTQ sanger que se muestra a continuación.

```
@M02286:19:000000000-AA549:1:1101:12677:1273 1:N:0:23
CCTACGGGTGGCAGCAGTGAGGAATATTGGTCAATGGACGGAAGTCTGAACCAGCCAAGTAGCGTGCAG
+
ABC8C,:@F:CE8,B-,C,-6-9-C,CE9-CC--C-<-C++,,+;CE<,,CD,CEFC,@E9<FCFCF?9
```

La primer linea para cada lectura contiene un **@** seguido de una identificación (read ID) e información adicional de la corrida de secuenciación.
La segunda linea contiene la secuencia asignada.
La tercera linea contiene un **+**, opcionalmente seguido de más información.
La cuarta linea contiene el puntaje de calidad en codificado como una serie de caracteres ASCII, para secuencias FASTQ Illumina >1.8 la codificación es Phred+33 - ASCII desde el caracter 33(!) al 126(~).
La calidad Q es un entero representando la probabilidad *p* de que la base asignada sea incorrecta. Para una probabilidad menor a 0.05 -> Q tiene que ser mayor a ~13. **Q = -10 log(p)** 
En general, el punto de corte utilizado es un valor de ~>20, correspondiente a una p < 0.01, ed decir un error cada 100 bases (99% de precisión).

##Distintos formatos de archivo FASTQ
###Paired end vs mate-pair
Existen distintos tipos de experimentos de secuenciación, que generan diferentes tipos de archivos FASTQ. Estos experimentos fueron pensados con dos objetivos: a. aumentar el largo de cada lectura (paired end) y b. conectar regiones genómicas alejadas en una longitud determinada (2kb, 8kb, etc) para lograr el ensamblado de genomas cerrados (mate pair). Con el advenimiento de los métodos de secuenciación de fragmentos largos (Nanopore, PACBIO) la tecnología mate pair está dejándose de usar.  

En ambos casos un fragmento determinado de la biblioteca de ADN es secuenciado de ambos extremos, quedando ambas secuencias vinculadas (ver ID de la secuencias). En estos casos se pueden obtener dos tipos de archivos FASTQ: 

1. Dos archivos FASTQ (uno por cada extremo secuenciado)
En este caso, los ID deben coincidir, y además, el orden es importante.

**Archivo 1**
```
 @M02286:19:000000000-AA549:1:1101:12677:1273 1:N:0:23
 CCTACGGGTGGCAGCAGTGAGGAATATTGGTCAATGGACGGAAGTCT
 +
 ABC8C,:@F:CE8,B-,C,-6-9-C,CE9-CC--C-<-C++,,+;CE
 @M02286:19:000000000-AA549:1:1101:15048:1299 1:
```

**Archivo 2**

```
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

##Determinando la calidad de las lecturas - FastQC
La principal herramienta que vamos a usar es FastQC. Adicionalmente, el análisis de muestras múltiples se puede realizar utilizando MultiQC, esta herramienta toma el output de varios análisis de FastQC y los muestra de forma comparativa.

1. En el panel de herramientas, seleccionar FASTQ Quality control -> FastQC
..* En *Short read data from your current history* seleccionar las lecturas a analizar (este procedimiento debe realizarse para cada lectura). *Acá, se puede elegir cargar una lista o colección!*. Lo mejor es usar una lista no pareada para poder analizar los datos en conjunto con MultiQC.

* En la *historia* abrir el archivo html generado
* Diferentes datos para analizar
.* Per base sequence quality: x= posición a lo largo de las lecturas, y= calidad (Phred). 
.* Per base sequence content: x= posición a lo largo de las lecturas, y= frecuencia de cada bases
.* Per base N content: x= posición a lo largo de las lecturas, y= %N
.* Adapter Content: x= posición a lo largo de las lecturas, y= % Adaptadores
.* Per sequence quality scores: x= calidad (Phred), y= número de secuencias
.* Per sequence GC content: x= %GC medio, y = número de secuencias
.* Sequence length distribution: x= largo de la secuencia, y= número de secuencias
.* Sequence Duplication Levels: x= grado de duplicación, y= % de secuencias

2. Para analizar una colección en conjunto utilizar MultiQC
* Una vez corrido el FastQC, se puede analizar los datos en su conjunto mediante MultiQC.
..* En MultiQC seleccionar como herramienta con la que se obtuvieron los resultados FastQC. MultiQC puede trabajar con resultados de colecciones tipo flat (no pareadas).

##Filtrado y recortado
La etapa siguiente es filtrar y recortar las secuencias basándonos en el análisis de calidad, ya que secuencias en las que los nucleótidos incorporados tienen alto error podrían generar sesgos en los análisis posteriores.
El procesamiento típico de las secuencias incluye:
* Filtrado de secuencias con 
..* un score medio bajo 
..* secuencias muy cortas
..* con muchas Ns
..* según su %GC

* Cortado y enmascarado de secuencias:
..* en las regiones de baja calidad
..* en su comienzo o final
..* recortado de secuencias correspondientes a adaptadores

1. Filtrado/enmascarado/recortado de secuencias
Para realizar el filtrado/recortado de las secuencias se pueden utilizar varios programas: Trimmomatic, Trim Galore, Cutadapt y otros.

###Trimmomatic! **[Instalado]**
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

###otra opción: Cutadapt
..* Utilizar Cutadapt con los siguientes parámetros
...* “Single-end or Paired-end reads?”: Paired-end
...* param-file “FASTQ/A file #1”: reads_1 (Input dataset)
...* param-file “FASTQ/A file #2”: reads_2 (Input dataset)

..* Read options: Para las opciones de la lectura1 y lectura 2:
...* **En esta parte hay que indicar que adaptadores fueron usados, en algunos casos el proveedor del servicio de secuenciación nos puede enviar las secuencias con los adaptadores ya removidos...**

..* En “Adapter Options” -> “Minimum overlap length”: 3
..* En "filter Options” -> “Minimum length”: 20
..* En “Read Modification Options” -> “Quality cutoff”: 20
..* En “Output Options” -> “Report”: Yes

*En el caso de colecciones pareadas, Cutadapt devuelve las lecturas por separado...*

[Mirar como funciona el algoritmo de Cutadapt!](https://galaxyproject.github.io/training-material/topics/sequence-analysis/tutorials/quality-control/tutorial.html)
 

###otra ocpción -> Trim Galore!
* Seleccionar ->  “Is this paired- or single-end”: Paired-end
* Seleccionar -> param-file “Reads in FASTQ format #1”: 
* Seleccionar -> param-file “Reads in FASTQ format #2”: 


# Mapeo de secuencias en contra un genoma de referencia
El mapeo es el proceso mediante el cual las lecturas se alinean a un genoma de referencia. Los programas para realziar el mapeo toman como input un genoma de referencia y los sets de lecturas. El objetivo es alinear cada lectura en los archivos fastq en el genoma de referencia, permitiendo algunos errores (mismatches), indels (deleciones o inserciones) y el cortado de algunos fragmentos cortos en los extremos de las lecturas-
En la actualidad hay mas de 60 programas diferentes para realizar el mapeo, y el número sigue creciendo. En el trabajo práctico utilizaremos algunos de los más comunes como el Bowtie2, o el BWA.

###Crear Genoma de referencia
1. Lo primero es normalizar el archivo fasta ->  "NormalizeFasta with the options to wrap sequence lines at 80 bases and to trim the title line at the first whitespace"
 
2. Dirigirse a la pestaña User y seleccionar *Custom Build*

##Bowtie2 **[Instalado]**
Para ejecutar el mapeador Bowtie2, necesitamos el genoma de referencia contra el cual alinear las lecturas. Para ello debemos cargar los archivos fasta en la historia. Es recomendable además agregarlo a un *custom build*, que es la forma que usan la mayoría de las aplicaciones para seleccionar genomas de referencia.

El programa Bowtie2 toma los siguientes argumentos:
Ejemplo para lecturas de un experimento paired-end.
1. “Is this single or paired library”: Paired-end
        param-file “FASTA/Q file #1”: 
        param-file “FASTA/Q file #2”: 

2. “Do you want to set paired-end options?”: No
        Acá se pueden configurar varios parámetros, en especial la orientacion de los pares.  
3. “Will you select a reference genome from your history or use a built-in index?”: 
        “Select reference genome”: 

4. “Select analysis mode”: Default setting only
    They can have an impact on the mapping and improving it.
5. “Save the bowtie2 mapping statistics to the history”: **Yes**
.* Esta opción es importante, nos va a permitir mirar la estadística del mapeo.
6. Inspeccionar los resultados del mapeo clickeando en el icono **galaxy-eye**

Es interesante notar que algunas lecturas pueden ser mapeadas en varias posiciones (multi-mapped reds), estas en general corresponden a repeticiones en el genoma de referencia y son más abundantes cuando el largo de lectura es chico. Como es dificil determinar de donde provienen esas lecturas la mayoría de los mapeadores las ignoran.
Otra información de interés es el porcentaje de lecturas no mapeadas, estas pueden deberse a: un par de lecturas que alinean en diferentes regiones genómicas, lecturas multimapeadas y a lecturas que no alinean en nungún lugar. 

### Causa de errores...
1. Artefactos de amplificación por PCR
2. Errores de secuenciación (en general son al azar, por lo que en análisis de variaciones se pueden evidenciar como ssingletones)
3. Erroes de mapeo ( en general en el entorno de regiones repetidas)

## Archivos SAM/BAM
El resultado de los alineamientos es un archivo BAM (Binary Alignment Map). El formato BAM, es un formato comprimido que contiene las secuencias que mapearon contra el genoma de referencia. Los archivos BAM están además acompañados de un índice .bai. 
Los archivos SAM, Sequence Alignment/Map, son la versión leible de los archivos BAM. Son archivos de texto, con campos separados por tabulaciones, que contienen la información del alineamiento.
La estructura es la de una tabla, con un header (encabezado) y a continuación los datos del alineamiento.

### Encabezados
El encabezado contiene información de como el alineamiento fue obtenido. Todas las lineas comienzan con un @XX y pares tag:valor delimitados por tabulaciones.
Existen distintos tipos de datos, por ejemplo filas comenzando con @SQ, contienen información acerca de las secuencias del genoma de referencia. Otros identificadores son @RG (read group), @PG (program).
Mas info en las especificaciones del formato [SAM](http://samtools.github.io/hts-specs/SAMv1.pdf)

### Alineamiento
Col 	Field 	Type 	Brief Description
1 	QNAME 	String 	Nombre de la secuencia (PE: o del par de lecturas)
2 	FLAG 	Integer bitwise FLAG: da información sobre el mapeo. Ver abajo
3 	RNAME 	String 	Nombre de la secuencia de referencia
4 	POS 	Integer	La posición más a la izquierda de la 1-base mapeada (0 si no mapeo)
5 	MAPQ 	Integer	MAPping Quality (Phred)
6 	CIGAR 	String 	CIGAR String (Información detallada del alineamiento)
7 	RNEXT 	String 	Solo en PE. nombre de la secuencia de referencia del alineamiento del mate(next read)
8 	PNEXT 	Integer	Solo en PE. La posición más a la izquierda de la 1-base mapeada del  mate(next read)
9 	TLEN 	Integer	Solo en PE. Largo inferido del fragmento
10 	SEQ 	String 	Secuencia sin indels de hebra directa de la lectura alineada
11 	QUAL 	String 	ASCII Phred-scaled base QUALity+33
12	OPT	depende Campos opcionales. TA,Tipo, ver abajo.

*Ej.*
<QNAME> <FLAG> <RNAME> <POS> <MAPQ> <CIGAR> <RNEXT> <PNEXT> <TLEN>    <SEQ>       <QUAL>     <OPT>
read1     83     chrI  15364   30      51M     =     15535   232    CCA..CTCG     BB?H..    NM:i:0

### El campo **flag**
El campo flag codifica en un número diferente información sobre la lectura. El número se genera a partir de asignar 0 (No) o 1(Si) a distintas características de la lectura.
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

### El campo **CIGAR**
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

### Los opcionales!
Los campos opcionales se incluyen en el último campo del BAM, con la sintaxis: <TAG>:<TYPE>:<VALUE>
Donde type puede ser de las siguientes clases: A - Character, i - Integer, f - Float number, Z - String, H - Hex string.
La etiqueta varía según el ensamblador, pueden ser: AS (alignment score), BC (Barcode sequence), HI (cual es la primer posición de la lectura que alinea), NH (Número de alineamientos para la secuencia), NM (distancia de la secuencia a la referencia, es 0 si son idénticas), MD (Posición exacta de los mismatches) y RG (Read Group).
Los Tags comenzando con X, Y y Z no están estandarizadas. Por ejemplo XS, es usada por TopHat para indicar la hebra, mientras que el mismo tag es usado por BWA y Bowtie2 para guardar el score del mejor alinemiento, en casos de lecturas que mapean en sitios múltiples.

###Otra opción BWA / BWA-MEM
Otro mapeador altamente utilizado es BWA. 




 
### MPileup
samtools mpileup -> para generar la secuencia consenso del secuenciamiento!
Example
Sequence	Position	Reference Base	Read Count	Read Results	Quality
```
seq1	272	T	24	,.$.....,,.,.,...,,,.,..^+.	<<<+;<<<<<<<<<<<=<;<;7<&
seq1	273	T	23	,.....,,.,.,...,,,.,..A	<<<;<<<<<<<<<3<=<<<;<<+
seq1	274	T	23	,.$....,,.,.,...,,,.,...	7<7;<;<<<<<<<<<=<;<;<<6
seq1	275	A	23	,$....,,.,.,...,,,.,...^l.	<+;9*<<<<<<<<<=<<:;<<<<
seq1	276	G	22	...T,,.,.,...,,,.,....	33;+<<7=7<<7<&<<1;<<6<
seq1	277	T	22	....,,.,.,.C.,,,.,..G.	+7<;<<<<<<<&<=<<:;<<&<
seq1	278	G	23	....,,.,.,...,,,.,....^k.	%38*<<;<7<<7<=<<<;<<<<<
seq1	279	C	23	A..T,,.,.,...,,,.,.....	75&<<<<<<<<<=<<<9<<:<<<
```
The columns
Each line consists of 5 (or optionally 6) tab-separated columns:

Sequence identifier
Position in sequence (starting from 1)
Reference nucleotide at that position
Number of aligned reads covering that position (depth of coverage)
Bases at that position from aligned reads
Phred Quality of those bases, represented in ASCII with -33 offset (OPTIONAL)
Column 5: The bases string

```
. (dot) means a base that matched the reference on the forward strand
, (comma) means a base that matched the reference on the reverse strand
</> (less-/greater-than sign) denotes a reference skip. This occurs, for example, if a base in the reference genome is intronic and a read maps to two flanking exons. If quality scores are given in a sixth column, they refer to the quality of the read and not the specific base.
AGTCN denotes a base that did not match the reference on the forward strand
agtcn denotes a base that did not match the reference on the reverse strand
A sequence matching the regular expression \+[0-9]+[ACGTNacgtn]+ denotes an insertion of one or more bases starting from the next position
A sequence matching the regular expression -[0-9]+[ACGTNacgtn]+ denotes a deletion of one or more bases starting from the next position
^ (caret) marks the start of a read segment and the ASCII of the character following `^' minus 33 gives the mapping quality
$ (dollar) marks the end of a read segment
* (asterisk) is a placeholder for a deleted base in a multiple basepair deletion that was mentioned in a previous line by the -[0-9]+[ACGTNacgtn]+ notation
Column 6: The base quality string
This is an optional column. If present, the ASCII value of the character minus 33 gives the mapping Phred quality of each of the bases in the previous column 5. This is similar to quality encoding in the FASTQ format.
```

###Convert, Merge, Randomize BAM tools. 
Convert to fasta!


## Visualizar en JBrowse


### Otra opción IGV
Para cargar genomas de referencia en IGV, los mismos tienen que estar indexados. Para generar el índice hay que abrir el fasta de referencia y entrar en la opción de edición (click en el lápiz). Dentro, elegir convertir, y seleccionar fai.
Se puede despues abrir el IGV online y cargar los archivos de la referencia (fas,fai) y del alineamiento (bam,bai).
 


# Read Groups
Los read groups son útiles para combinar múltiples alinemientos en un solo archivo, sin perder la información acerca de la muestra de la cual provienen. Los read groups se agregan al header del archivo SAM, con @RG seguido de los tags correspondientes. Si hay más de un RG, entonces habrá varias lineas con @RG.
Los tags mas usados son los siguientes:
* ID, requerido, Id del grupo, en general corresponde a la celda del Illumina, el número y nombre de la calle. Las lecturas en un mismo read group, se asumen de la misma corrida y por ende tienen el mismo error. Este tag tiene que coincidir con el tag RG, de las entradas del archivo BAM.
* SM, requerido, Id de la Muestra. Todas las lecturas con el mismo SM, corresponden a la misma muestra.
* PL, tag correspondiente a la tecnología de secuenciación utilizada para generar las lecturas.
* LB, Identidad de la preparación de ADN. Es utilizado por MarkDuplicates para determinar que grupos podrían tener duplicados, en el caso en que se hallan usado muchas calles. 


#Mirar lo de crear custom bubilds (genomas para poder utilizar visualización!!!




Día 2: RNAseq
-------------

#Introducción
[Referencia](https://galaxyproject.org/tutorials/rb_rnaseq/)
[Referencia2](https://galaxy-au-training.github.io/tutorials/modules/dge/#upload-files-to-galaxy)

En este caso, las lecturas que utilizaremos corresponden a *E. coli*, creciendo en dos medios de cultivo diferentes. El set de datos, es una versión reducida al 1% para acortar los tiempos de cálculo, tomada de [Galaxy Australia Training - RNA-Seq: Bacteria - Autor: Syme, Anna](https://doi.org/10.5281/zenodo.1311269). 


Existen dos métodos principales, los basados en genomas de referencia, o los independientes de genoma de referencia. En este último caso se ensamblan las lecturas en transcriptos usando métodos *de novo*.

En lineas muy generales el protocolo experimental cumple con los siguientes pasos:
1. Purificación de RNA
2. Transcripción reversa -> Generación del cDNA
3. Síntesis de la segunda cadena utilizando una DNA polimerasa
4. Preparación de una biblioteca de secuenciamiento

Las etapas anteriores omiten muchas consideraciones experimentales que en algunos casos son necesarios. Sin embargo, entre estos hay dos que describiremos por su importancia:

## Síntesis de la primera cadena.
Como la transcriptasa reversa requiere un oligonucleótido que actúe como cebador, según como se elige este cebador tendremos diferentes aproximaciones experimentales.
Por ejemplo, para Eucariotas es común utilizar como cebador oligo-dT, ya que los mRNAs procesados están poliadenilados.
Otra estrategia muy utilizada es utilizar oligonucleótidos de secuencia aleatoria (random priming) que permitirán la transcripción reversa en múltiples sitios internos.

##RNAseq específico de hebra (Strand-specific)
Los RNAs son de una sola cadena y por consiguiente tienen polaridad. En los experimentos mas típicos de RNAseq la polaridad se pierde ya que se construye una biblioteca a partir del DNA doble cadena. Existen diversos métodos para generar una biblioteca strand-specific, que involucran en general la ligación de adaptadores en los extremos del RNA (los más comunes son Illumina TrueSeq RNAseq kits and dUTP tagging).

Dependiendo del método utilizado, tenemos que tener cuidado de como interpretar los datos. En general una inspección visual de las lecturas mapeadas puede darnos información sobre el tipo de biblioteca que tenemos.
(En rojo -> lecturas mapeadas directas)
(En azul -> lecturas mapeadas en RC)
 
# Ya tenemos nuestras secuencias. y ahora?
El procesamiento en términos generales implica:
1. Determinación de calidad de las lecturas
2. Filtrado y cortado de adaptadores
3. Mapeo contra el genoma de referencia 
.* Acá, tenemos que tener en cuanta si el organismo en estudio es pro- o eucariota. En el caso de organismos eucariotas tendremos que tener en cuenta el que as secuencias solo mapearán en los exones, y algunas de ellas atravesarán de un exón a otro. En estos casos se utilizan *spliced mappers* como TopHat, HiSat y STAR.
4. Reconstrucción del transcriptoma
.* En esta etapa, las lecturas mapeadas al genoma y con las uniones de exones, son utilizadas para construir modelos de transcriptoma (Splicing alternativos). Existen distintas herramientas, una de las más utilizadas es cufflinks (y ahora del mismo grupo Stringtie). Nuevamente, esto no será necesario para el análisis del transcriptoma de procariotas.
5. La siguiente etapa es la cuantificación de los transcriptos. 
.* Esto implica asignar las lecturas a los transcriptos. En este punto, Cufflinks (Stringtie) ya tienen la información sobre que lecturas mapean contra cada transcripto, por lo que solo resta contarlas! Stringtie, adicionalmente realiza la cuantificación.
.* Existen otras herramientas que mapean directamente las lecturas al transcriptoma, asumiendo que existe un transcriptoma de buena calidad para el organismo en cuestión (en general se usa en humanos o ratón; Sailfish, Kallisto, Salmon)
.* El recuento de las lecturas asociadas a cada transcripto en general es una muy mala aproximación a la cantidad de transcripto, esto se debe distintos factores, principalmente el largo del transcripto. Por ende, los recuentos deben ser normalizados. Existen distintos métodos de normalización:
..* Normalización dentro de una misma muestras (RPKM, FPKM, TPM, rlog)
..* Normalización entre muestras
6. Análisis de expresión diferencial
.* Es importante tener en cuanta que los valores normalizados anteriormente no deben compararse entre condiciones para determinar el el grado de expresión diferencial. Con este objetivo existen herramientas específicas que calcualrán la magnitud del cambio y su significancia estadística, teniendo en cuenta, si existe, la información de las réplicas biológicas.
.* Las mejores aplicaciones para realizar el análisis diferencial son edgeR, DESeq/DESeq2, y limma-voom.


#Generar las colecciones
En este caso nos va a interesar generar dos colecciones, una para las lecturas correspondientes a E. coli* crecida en medio LB y otra para las correspondientes a *E. coli* crecidas en medio MG.
Adicionalmente, podemos crear una colección con todas las lecturas para hacer el control de calidad.

#Determinar la calidad de las lecturas - FastQC / filtrados y recortes (Trimmomatic)
Igualmente que en el caso anterior, es importante verificar la calidad de las lecturas obtenidas y realizar los filtrados y recortes necesarios.
*Correr el FastQC en la colección con todas las lecturas
*Correr el MultiQC con los resultados de FastQC
*Analizar los datos

#Genoma de referencia
El genoma de referencia es el de *E. coli* K12. Acá es mecesario convertir el archivo .gtf a .gff.
Para ello en la historia seleccionar el archivo, clickear en el icono 'edit' y seleccionar la opción convertir.

#Mapeo de las lecturas obtenidas
Como las lecturas que tenemos provienen de un estudio de RNAseq de procariotas, no es necesario que realicemos un mapeo que tenga en cuenta splicing. Por ello realizaremos el mapeo con Bowtie2. **Recordar seleccionar la opción para que reporte la estadística del mapeo**
Para analizar los resultados de todas las lecturas utilizar MultiQC.
Adicionalmente, no será necesario realizar la reconstrucción del transcriptoma.

#Visualizar las lecturas mapeadas en JBROWSE
De la misma forma que antes se puede utilizar JBROWSE para ver las lecturas mapeadas al transcriptoma de *E. coli*. Acá hay que agregar las pistas del genoma con su anotación y de los archivos SAM del mapeo.
Mirando los resultados, tratar de determinar si nuestras secuencias vienen de una biblioteca 'stranded'.

#Recuento y análisis de expresión diferencial de secuencias que mapean en cada transcripto
Utilizaremos el programa featurecounts
En opciones hay que elegir:
.* Alignment file -> Alineamiento hecho con Bowtie2 (SAM/BAM)
.* Specify strand information -> Stranded (Forward)
..* Esto se puede ver a partir de los resultados del alineamiento con Bowtie2, o con el protocolo utilizado para obtener las lecturas.
.* Gene annotation file	-> history -> Ecoli_k12.gtf


##Inspección de los resultados utilizando el Scratchbook! 
El icono a la derecha de User en la barra superior, es el Scratchbook, cuando está activado nos permite ver resultados de varios análisis de forma simultánea!
Utilizar el Scratchbook para visualizar los resultados de featurecounts!

#Análisis de expresión diferencial
Aca hay que tener las listas correspondientes a cada condición..
Si tenemos una lista única se puede separar usando: Apply Rule to Collection
* Agregar filtrado, por expresión regular -> Lb (en un caso y en el otro MG)
* Agregar definición de columna -> 'column definition' -> List identifier

## Deseq2
Para realiar el análisis de expresión diferencial utilizaremos el programa Deseq2.
Pasos:
*“how” -> Select datasets per level
.*En factor” -> “1: Factor” -> “Specify a factor name”: por ejemplo 'Treatment'
.*En “Factor level” -> “1: Factor level” -> “Specify a factor level”: por ejemplo 'LB'
..* Counts file(s) Acá hay que seleccionar los datasets
.*En “2: Factor level” -> “Specify a factor level”: por ejemplo 'MG'
..* Counts file(s) -> Acá hay que seleccionar los datasets
*“Files have header?”: YES
*“Output normalized counts table”: Yes

Como resultado obtendremos una tabla y varias figuras, incluyendo:
* PCA (Principal component analysis) de las muestras: Sirve para analizar que tan similares son las muestras entre si. Es esperables que las diferentes condiciones estén separadas y las réplicas se encuentren cercanas entre si.
* Heatmap y  clustering jerárquico de las muestras: Similar al anterior, es una representación de las distancias entre las muestras basado en el logFC para cada gen.
* Dispersion estimates: la raíz cuadrada de la dispersión es el coeficiente de variación biológica (biological coefficient of variation - BCV), que es una estimación de la variabilidad entre réplicas biológicas. Por ejemplo: si la dispersión estimada es 0.19 -> BCV = 0.44 -> lo que quiere decir que la variación de expresión entre réplicas biológicas es de 44%.
El BCV representa la variabilidad relativa que uno observaría si pudiera medir perfectamente los niveles reales de expresión de cada muestra. 
* Histograma de p-values: distribución de p-values en la muestra.
* MA−plot for Condition: LB vs MG: El gráfico de M.value -> logFC, versus la intensidad de la señal (recuentos normalizados). Con puntos rojos, los genes con un p-value significativo.

##Heatmap2
Crear un heatmap para los genes. Podemos hacer un heatmap para tratar de ver genes con comportamiento similar en base a los recuentos normalizados. Este gráfico lo vamos a hacer solo para los genes con una diferencia estadisticamente significativa.


1. Usando: Filter -> Filtrar los resultados de Deseq2 en base al adj-pvalue
.*param-text “With following condition”: c7 < 0.01
.*param-text “Number of header lines to skip”: 0
..* Luego, podríamos quedarnos, por ejemplo, con los 100 genes con mayor diferencia significativa utilizando la función Sort. Esto no será necesario en nuestro caso por que tenemos menos de 100 genes expresados diferencialmente.
.*Bajar la tabla filtrada y agregar los headers!
2. Ahora necesitamos cruzar los datos entre la tabla de recuentos normalizados y la tabla de resultados de Deseq2. Esto se puede hacer utilizando la función Join.
.* Join user data: 'data_set1' using column c
.* with user data: 'data_set2' using column c' 
3. Recortar los datos necesarios para el heatmap. Para ello utilizamos la función cut.
.* Cut columns -> columnas separadas por coma: c1,c2,c3
.* Delimited by -> delimitador de campos: tab
4. Heatmap2
.* Coloring groups -> blue->white->red
.* Data scaling -> by row




## Otros analisis
## Degust -> Web
Para correr el servidor Degust, deberán bajar la tabla generada por el Deseq2.
Esta herramienta online genera una serie de figuras de interés.





















links útiles

http://manuals.bioinformatics.ucr.edu/workshops/dec-12-16-2013
https://galaxyproject.org/learn/

> < = | ~ ¬ « » ░ ▒ ▓ │ ┤ ╣ ║ ╗ ╝ ┐└  ┴ ┬ ├ ─ ┼ ╚ ╔ ╩ ╦ ╠ ═ ╬ ┘ ┌ █ ▄
¦ ¯ ≡ ‗ ■ ┌ ┘ ─ ├ ┤ _ ┴ ┬ ┼

*cosas para instalar
STAR (idem HISAT)
htseq_count
DeSeq2

























 
