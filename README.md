# Epipedobates_anthonyi_genomics
https://github.com/gabylumaldonado/Epipedobates_anthonyi_genomics.git

## Bitácora
### Viernes 3 de septiembre 2021
Se realiza la descarga de todos los datos que se encuentran en el servidor de Colorado. Para realizar esto se uso la siguiente línea de comando:

    scp -r paez@correns.colostate.edu:/scratch/users/paez/anthonyi .
    #scp Secure copy protocol

###Lunes 6 de septiembre 2021
Se realiza una lista de todos los archivos de la carpeta para saber cuáles son los archivos faltantes:

    ls -R > carpetaAnthonyi.txt
    #Enlistar todos los subdirectorios y archivos que están dentro de una carpeta y enviar toda esa información a un archivo de texto.    


###Miércoles 8 de septiembre 2021
Se va a bajar los archivos raw de la carpeta:

    scp -r paez@correns.colostate.edu:/scratch/users/paez/anthonyi/raw-data-libs .


Solo se baja la carpeta indicada, es decir raw-data-libs en home (.), esta carpeta es creada una vez que el primer archivo se ha bajado por completo (100%).


####lista de carpetas en raw-data-libs
>* anthonyi-process-radtags **check**
>* barcode-files **check**
>* raw-lib6-pool1 **check**
>* raw-lib6-pool2 **check**
>* raw-lib7-pool1 **check**
>* raw-lib7-pool2 **check**
>* raw-lib8-pool1 **check**
>* raw-lib8-pool2 **check**

>* Undetermined_S0_L006_R1_001.fastq.gz
>* Undetermined_S0_L007_R1_001.fastq.gz
>* Undetermined_S0_L008_R1_001.fastq.gz
>

###Domingo 26 de septiembre 2021
Comando para acceder a Cedia desde computador local y subir archivos raw:

    ssh -i ~/.ssh/llave-privada patricia.salerno@login.hpc.cedia.org.ec

**Llave privada** hace referencia al archivo generado como clave pública en el computador local. Generalmente se encuentra en el directorio oculto .ssh

###Lunes 27 de septiembre 2021
No se puede acceder al servidor hpc de Cedia por puertos bloqueados en la red de la universidad, se habilita una red sin restricciones para acceder a CEDIA.
La **llave privada** es el archivo RSAPUBLICO sin extensión. La linea de comando es:

    ssh -i ~/.ssh/RSAPUBLICO patricia.salerno@login.hpc.cedia.org.ec

Todos los archivos asociados a la creación de la clave deben estar en la misma carpeta **.ssh** Esta carpeta está oculta, fuera de home. Para acceder: **./.ssh** Los archivos deben tener los siguientes permisos:

    gabylu@MacBook-Air-de-Gabriela ~ % ls -ltr ./.ssh total 32
    -rw-r--r--  1 gabylu  staff   590 Jun 14 18:46 RSAPUBLICO.pub
    -rw-------@ 1 gabylu  staff   590 Jun 14 18:51 rsa
    -rw-r--r--  1 gabylu  staff   617 Sep 26 11:04 known_hosts
    -rw-------  1 gabylu  staff  2635 Sep 29 11:14 RSAPUBLICO


Una vez que se haya conectado al servidor CEDIA desde la computadora local, abrir otro terminal de **preferencia de color distinto** para poder diferenciar acceso **LOCAL de SERVIDOR**.

Copiar o subir archivos desde Local hacia el servidor. Esta línea de comando se corre en una nueva terminal local. Y se revisa cada cierto tiempo, en el terminal del servidor, para verificar que los archivos se suban.



    scp -i ~/.ssh/RSAPUBLICO *.fastq.gz patricia.salerno@login.hpc.cedia.org.ec:/home/patricia.salerno/GABY
    ##Secure copy, -i indica el archivo de la clave privada, *.fastq.gz todos los archivos de mi home con esa extensión, destino en el servidor CEDIA



Este ese el ejemplo de visualización en el terminal que accede al **Servidor** :   

    patricia.salerno@dgx-node-0-0:~$ ls -ltr
    total 0
	drwxrwxr-x 2 patricia.salerno patricia.salerno  10 sep  2 11:05 PATRICIA
	drwxrwxr-x 4 patricia.salerno patricia.salerno  76 sep  2 13:00 programs
	drwxrwxr-x 3 patricia.salerno patricia.salerno  66 sep 27 14:51 ALEX
	drwxrwxr-x 3 patricia.salerno patricia.salerno  30 sep 28 17:31 ALBERTO
	drwxrwxr-x 3 patricia.salerno patricia.salerno  31 sep 28 19:09 ABIGAIL
	drwxrwxr-x 2 patricia.salerno patricia.salerno 304 sep 30 03:41 GABY
	patricia.salerno@dgx-node-0-0:~$ cd GABY/
	patricia.salerno@dgx-node-0-0:~/GABY$ ls -ltr


Para **bajar archivos desde el servidor al computador local** se abre un terminal en la computador local, se observará como se descarga el archivo en porcentaje. con la siguiente línea de código:

    gabylu@MacBook-Air-de-Gabriela ~ % scp -i ~/.ssh/RSAPUBLICO patricia.salerno@login.hpc.cedia.org.ec:/home/patricia.salerno/GABY/ANTHONYI/denovo/stderr-denovo_Anthonyi_376 .

    Comando secure copy, -i (llave privada), dirección del archivo a bajar en el servidor, (.) el punto indica que quiero bajar los archivos en la carpeta donde estoy, en este caso home.




#STACKS
###Jueves 4 de noviembre 2021

El primer paso, para trabajar con los datos genómicos de Illumina, es usar el programa [**STACKS**] (https://catchenlab.life.illinois.edu/stacks/manual/index.php#clean). Este programa examina y evalúa las lecturas raw de Illumina y comprueba la calidad media de las lecturas. El programa presenta pipelines en módulos que permiten **ensamblar** un gran número de secuencias de lectura corta de multiples muestras.
Identifica loci dentro de un grupo de individuos por dos maneras: **de novo** o alineaando las lecturas con un **genoma de referencia**.

El analysis **de novo** tiene 6 etapas:


>* **process_radtags** / demultiplexar y limpiar las secuencias
>* **ustacks** / construcción de loci
>* **cstacks** / crea el catálogo de loci
>* **sstacks** / compara los loci con el catálogo
>* **gstacks** / ensambla y une los contigs, identificación de SNPS, genotipos en cada muestra
>* **population** / filtra datos, estadística de genética poblacional y datos en diferentes formatos para exportar


####PROCESS_RADTAGS
Para correr este módulo es necesario organizar cada archivos RAW (*.fastq.gz) junto con el archivo ejecutable (ejecutable.sh) en un mismo directorio, todos los archivos de barcodes pueden estar en la misma carpeta.

La estructura de archivos en el Servidor Cedia para estos datos es la siguiente:


  	./GABY/ANTHONYI/LIB7POOL1
  	#La carpeta la librería LIB7POOL1 contiene el archivo RAW de datos Ilumina 1595_ATCACG_S199_L007_R1_001.fastq.gz y el archivo ejecutable radtag_lib7_pool1.


 El ejecutable .sh se lo corre desde la carpeta que tiene el archivo y que corresponde a cada librería. La línea de comando es:

 	patricia.salerno@dgx-node-0-0:~/GABY/ANTHONYI/LIB7POOL1$ sbatch radtag_lib7_pool1
 	Submitted batch job 7443

Cuando el proceso ha sido enviado para ser ejecutado debería recibir un **jobID**. En este caso 7443, para revisar que la corrida esté funcionando sin problemas ejecutar el comando **squeue**. Este comando despliega todos los trabajos enviados a la cola de ejecución y que están corriendo. El comando **squeue** puede ser ejecutado desde cualquier ubicación.

	patricia.salerno@dgx-node-0-0:~/GABY/ANTHONYI/LIB7POOL1$ squeue

             JOBID PARTITION     NAME     USER ST       TIME  NODES NODELIST(REASON)
              7118   compute   cornv1 patricia  R 78-16:19:59      1 dgx-node-0-0
              7388   compute  tbdmod8 henry.ve  R 31-13:02:29      1 dgx-node-0-0
              7437   compute denovo-P patricia  R 1-13:32:20      1 dgx-node-0-0
              7438   compute denovo-P patricia  R 1-13:20:17      1 dgx-node-0-0
              7443   compute radtag_l patricia  R       0:18      1 dgx-node-0-0




##DENOVO
Necesita de dos archivos: .sh script para correr denovo y un .txt que contiene la lista de individuos con la población a la que corresponden.

Para realizar el archivo de las poblaciones:

`ls > individuos_list_popmap.txt`

Luego hay que procesar el archivo en Atom para dejarlo en el formato requerido

`Agregar cabecera del archivo`

Para bajarse al archivo del Servidor Cedia al computador local:
`gabylu@MacBook-Air-de-Gabriela ~ % scp -i ~/.ssh/RSAPUBLICO patricia.salerno@login.hpc.cedia.org.ec:/home/patricia.salerno/GABY/ANTHONYI/PROCESSED_RADTAGS/individuos_list_popmap.txt .`

Para subir el archivo al servidor
`scp -i ~/.ssh/RSAPUBLICO individuos_list_popmap.txt patricia.salerno@login.hpc.cedia.org.ec:/home/patricia.salerno/GABY/ANTHONYI/PROCESSED_RADTAGS`


--------------------------- Por arreglar-----
Se realizaron todas las permutaciones m3 y m6 para poder contrastar tiempos. permutaciones con m6 no funcionaron, el error era:

se detiene en muestra 49 que corresponde a individuo : xxxxx, este error para todos las combinaiones de m6.
El mismo error para combinaciones de m4, se detiene en sample 49 que corresponde al individuo ******

se hizo la prueba con m5, mismo error se detiene en muestra 48.

Para solucionar esto se elimino del archivo popmaps estos individuos. y se corrio.

Se realizo el grafico (poner grafico) de el coverage por individuo para poder eliminar los indiviuos con coverage muy bajo.

Al correr permutaciones de m4 sin los indiviuos EA*_014, EA14_014 da error cuando procesa a tsv2bam (Error: Unable to load matches from './denovo_Anthonyi_433R/EA14_015.matches.tsv(.gz)'.
Aborted.)

este es archivo denovo.maplog de la permutacion denovo_Anthonyi_433r

```
Processing sample ./denovo_Anthonyi_433R/EA14_015 [49 of 164]
Parsing ./denovo_Anthonyi_433R/EA14_015.tags.tsv.gz
Parsing ./denovo_Anthonyi_433R/EA14_015.snps.tsv.gz
Parsing ./denovo_Anthonyi_433R/EA14_015.alleles.tsv.gz
Searching for sequence matches...
2094374 loci in the catalog, 152429211 kmers in the catalog hash.
Searching for gapped alignments...
Merging matches into catalog...
0 loci were matched to a catalog locus.
1 loci were matched to a catalog locus using gapped alignments.
0 loci were newly added to the catalog.
0 loci matched more than one catalog locus, linking them.
0 linked catalog loci were merged into 0 loci.

```
