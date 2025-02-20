# MyGenome
Repository to store work done throughout the Spring 2025 semester for ABT480 (Applied Bioinformatics)


#Create BioSample Entry in NCBI BioProject (PRJNA926786)
<img width="1149" alt="Screenshot 2025-02-18 at 2 51 24 PM" src="https://github.com/user-attachments/assets/749adfa0-107e-4c18-819a-b1961b06d380" />



#Checked Quality of Sequence using:
``` bash 
fastqc Pr88167_1.fq.gz Pr88167_2.fq.gz
```


#Pre-Trimmomatic Pr88167 forward
<img width="1156" alt="PreTrim_Pr88167_1" src="https://github.com/user-attachments/assets/4e5ef325-9646-494a-95ea-4199bcb6c1f2" />



#Pre-Trimmomatic Pr88167 backward
<img width="1158" alt="PreTrim_Pr88167_2" src="https://github.com/user-attachments/assets/1482b3b6-4b20-43ff-b4f3-9c594b9bff71" />



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

-Trim files for adapter contamination 
``` bash
java -jar ../sequences/trimmomatic-0.38.jar PE -threads 2 -phred33 -trimlog Pr88167.txt Pr88167_1.fq.gz Pr88167_2.fq.gz Pr88167_1_paired.fastq Pr88167_1_unpaired.fastq Pr88167_2_paired.fastq Pr88167_2_unpaired.fastq ILLUMINACLIP:~/sequences/adaptors.fa:2:30:10 SLIDINGWINDOW:20:20 MINLEN:120
```

 
#Post-Trimmomatic Pr88167 forward
<img width="1151" alt="Screenshot 2025-02-20 at 2 48 33 PM" src="https://github.com/user-attachments/assets/35dcfa03-0b8e-46ea-8af7-75ed44fb970a" />



#Post-Trimmomatic Pr88167 backward
<img width="1150" alt="Screenshot 2025-02-20 at 2 49 05 PM" src="https://github.com/user-attachments/assets/8790521b-62ab-40cb-8f4a-0082290689d3" />




#Create working directory on the MCC supercomputer 

-login using ssh linkBlueID@mcc.uky.edu
-change into /project/farman_s25abt480
-create a new directory named after linkBlueID (Done up to this point)
-use scp to copy trimmed .fastq files into newly created directory (TODO)
