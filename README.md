─────────────────────────────  
# MyGenome

Repository to store work completed during the Spring 2025 semester for ABT480 (Applied Bioinformatics).

─────────────────────────────  
## Table of Contents

- [BioSample Entry on NCBI BioProject](#biosample-entry-on-ncbi-bioproject-prjna926786)
- [Quality Check of Raw Sequences](#quality-check-of-raw-sequences)
- [Data Processing](#data-processing)
  - [Pre-Trimming Steps](#pre-trimming-steps)
  - [Trimming Sequence Reads](#trimming-sequence-reads)
  - [Post-Trimming Quality Check](#post-trimming-quality-check)
- [Data Transfer](#data-transfer)
  - [Uploading Raw Data to NCBI](#uploading-raw-data-to-ncbi)
  - [Transferring Data to the MCC Supercomputer](#transferring-data-to-the-mcc-supercomputer)

─────────────────────────────  
## BioSample Entry on NCBI BioProject (PRJNA926786)

This section documents the creation of a BioSample entry in the NCBI BioProject.

<img width="1149" alt="BioSample Entry Screenshot" src="https://github.com/user-attachments/assets/749adfa0-107e-4c18-819a-b1961b06d380" />

─────────────────────────────  
## Quality Check of Raw Sequences

Initial quality of the raw sequence files is verified using FastQC:

```bash
fastqc Pr88167_1.fq.gz Pr88167_2.fq.gz
```

─────────────────────────────  
## Data Processing

This section outlines the steps involved in processing the sequencing data.

### Pre-Trimming Steps

Before trimming, we first inspect the raw data.

#### Pre-Trimming: Forward Reads

<img width="1156" alt="PreTrim Pr88167 Forward" src="https://github.com/user-attachments/assets/4e5ef325-9646-494a-95ea-4199bcb6c1f2" />

#### Pre-Trimming: Backward Reads

<img width="1158" alt="PreTrim Pr88167 Backward" src="https://github.com/user-attachments/assets/1482b3b6-4b20-43ff-b4f3-9c594b9bff71" />

### Trimming Sequence Reads

Before trimming, count the number of sequence reads in both files:

```bash
zcat Pr88167_1.fq.gz | wc -l | awk '{print $1/4}'
```
Output: 8,293,893


```bash
zcat Pr88167_2.fq.gz | wc -l | awk '{print $1/4}'
```
Output: 8,293,893


Remove adapter contamination by running:

```bash
java -jar ../sequences/trimmomatic-0.38.jar PE -threads 2 -phred33 -trimlog Pr88167.txt \
Pr88167_1.fq.gz Pr88167_2.fq.gz \
Pr88167_1_paired.fastq Pr88167_1_unpaired.fastq \
Pr88167_2_paired.fastq Pr88167_2_unpaired.fastq \
ILLUMINACLIP:~/sequences/adaptors.fa:2:30:10 SLIDINGWINDOW:20:20 MINLEN:120
```

After trimming, count the number of sequence reads in the paired files:

```bash
cat Pr88167_1_paired.fastq | wc -l | awk '{print $1/4}'
```
Output: 6,814,125

```bash
cat Pr88167_2_paired.fastq | wc -l | awk '{print $1/4}'
```
Output: 6,814,125

Run FastQC on the trimmed files to generate quality graphs:

```bash
fastqc Pr88167_1_paired.fastq Pr88167_2_paired.fastq
```

### Post-Trimming Quality Check

Examine the quality of the trimmed sequence files:

#### Post-Trimming: Forward Reads

<img width="1151" alt="PostTrim Pr88167 Forward" src="https://github.com/user-attachments/assets/35dcfa03-0b8e-46ea-8af7-75ed44fb970a" />

#### Post-Trimming: Backward Reads

<img width="1150" alt="PostTrim Pr88167 Backward" src="https://github.com/user-attachments/assets/8790521b-62ab-40cb-8f4a-0082290689d3" />

─────────────────────────────  
## Data Transfer

### Uploading Raw Data to NCBI

The raw sequence data is uploaded to NCBI using Aspera with the following command:

```bash
~/.aspera/connect/bin/ascp -i ~/sequences/aspera.openssh -QT -l100m -k1 -d ~/MyGenome/ subasp@upload.ncbi.nlm.nih.gov:uploads/dannyh2004_uky.edu_NJVKm74d
```
SRR # = SRR32568306

### Transferring Data to the MCC Supercomputer

To move data onto the MCC supercomputer:

1. Login to the MCC Supercomputer:
   ```bash
   ssh linkBlueID@mcc.uky.edu
   ```
2. Change to the project directory:
   ```bash
   cd /project/farman_s25abt480
   ```
3. Create a new directory named after linkBlueID:
   ```bash
   mkdir linkBlueID
   ```
4. Use SCP to transfer the trimmed .fastq files from VM to the new directory:
   ```bash
   scp ./Pr88167_?_paired.fastq dha308@mcc.uky.edu:/project/farman_s25abt480/dha308
   ```
   
─────────────────────────────  
### Run Genome Assembly on Super Computer

-use velvetoptimers to find optimal kmer value

``` bash
sbatch velvetoptimiser_noclean.sh Pr88167 61 131 10
```
optimized kmer value = 91

``` bash
sbatch velvetoptimiser_noclean.sh Pr88167 81 101 2
```
optimized kmer value = 97

