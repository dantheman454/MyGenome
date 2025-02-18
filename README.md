# MyGenome
Repository to store work done throughout the Spring 2025 semester for ABT480 (Applied Bioinformatics)




#Create BioSample Entry in NCBI BioProject (PRJNA926786)
(INSERT SCREENSHOT HERE)

#Checked Quality of Sequence using:
``` bash 
fastqc Pr88167_1.fq.gz Pr88167_2.fq.gz
```
#ADD IN GRAPHS AND MAKE NOTE OF ANY FLAGS AND WHAT THEY MEAN

#Uploaded raw sequence data to NCBI
``` bash
~/.aspera/connect/bin/ascp -i ~/sequences/aspera.openssh -QT -l100m -k1 -d ~/MyGenome/ subasp@upload.ncbi.nlm.nih.gov:uploads/dannyh2004_uky.edu_NJVKm74d
```

#Trim the sequence reads

-count sequence reads in forward and reverse

``` bash
zcat Pr88167_1.fq.gz | wc -l | awk '{print $1/4}'
```
Output = 8293893

``` bash
zcat Pr88167_2.fq.gz | wc -l | awk '{print $1/4}'
```
Output = 8293893

-insert value into metadata sheet under '# raw reads (single end)' column

-Trim files (TODO)(CHECK THIS COMMAND AGAINST MANUAL) (ALSO NEED TO FIND/GET PROPER ADAPTERS FILE FROM FARMAN LAB MAC)(Header 5 in Module 5)
``` bash
java -jar trimmomatic-0.38.jar PE -threads 2 -phred33 -trimlog Pr88167_errorlog.txt Pr88167_1.fq.gz Pr88167_2.fq.gz Pr88167_1_paired.fastq Pr88167_1_unpaired.fastq Pr88167_2_paired.fastq Pr88167_2_unpaired.fastq CROP:280 SLIDINGWINDOW:20:20 MINLEN:150
```

-assess sequence quality using fastqc (TODO)



#Create working directory on the MCC supercomputer 

-login using ssh linkBlueID@mcc.uky.edu
-change into /project/farman_s25abt480
-create a new directory named after linkBlueID (Done up to this point)
-use scp to copy trimmed .fastq files into newly created directory (TODO)
