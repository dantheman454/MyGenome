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
-insert value into metadata sheet
-assess sequence quality using fastqc
