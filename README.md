# MyGenome

Repository to store work completed during the Spring 2025 semester for ABT480 

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
- [Genome Assembly](#genome-assembly)
- [Genome Quality Assessment](#genome-quality-assessment)
- [Genome Analysis](#genome-analysis)
- [Genome Annotation](#genome-annotation)

## BioSample Entry on NCBI BioProject (PRJNA926786)

This section documents the creation of a BioSample entry in the NCBI BioProject.

<img width="1149" alt="BioSample Entry Screenshot" src="https://github.com/user-attachments/assets/749adfa0-107e-4c18-819a-b1961b06d380" />

## Quality Check of Raw Sequences

Initial quality of the raw sequence files is verified using FastQC:

```bash
fastqc Pr88167_1.fq.gz Pr88167_2.fq.gz
```

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

#### Count the Total # of bases in paired end reads (forward + reverse reads)

```bash
awk 'NR % 4 == 0' ORS="" Pr88167_1_paired.fastq | wc -m
```
Output = 1008347290

```bash
awk 'NR % 4 == 0' ORS="" Pr88167_2_paired.fastq | wc -m
```
Output = 1021720317

Total # of bases = 1008347290 + 1021720317 = 2030067607

Examine the quality of the trimmed sequence files:

#### Post-Trimming: Forward Reads

<img width="1151" alt="PostTrim Pr88167 Forward" src="https://github.com/user-attachments/assets/35dcfa03-0b8e-46ea-8af7-75ed44fb970a" />

#### Post-Trimming: Backward Reads

<img width="1150" alt="PostTrim Pr88167 Backward" src="https://github.com/user-attachments/assets/8790521b-62ab-40cb-8f4a-0082290689d3" />

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

## Genome Assembly

### Finding Optimal K-mer Value

Use velvetoptimers to find optimal kmer value:

```bash
sbatch velvetoptimiser_noclean.sh Pr88167 61 131 10
```
Optimized kmer value = 91

```bash
sbatch velvetoptimiser_noclean.sh Pr88167 81 101 2
```
Optimized kmer value = 97

## Genome Quality Assessment

### Check Gene Completeness using BUSCO

```bash
sbatch /project/farman_s25abt480/dha308/scripts/BuscoSingularity.sh /project/farman_s25abt480/dha308/Pr88167/Pr88167_final.fasta
```

### Process MyGenome Files

Cull short contigs:
```bash
perl ./scripts/CullShortContigs.pl ./Pr88167/Pr88167_nh.fasta
```

Check that it worked correctly:
```bash
perl ./scripts/SeqLen.pl ./Pr88167/Pr88167_final.fasta
```

## Genome Analysis

### BLAST Analysis of Final Genome

BLAST the mitochondrial genome sequence against the final genome assembly:
```bash
blastn -query /project/farman_s25abt480/dha308/MoMitochondrion.fasta -subject /project/farman_s25abt480/dha308/Pr88167/Pr88167_nh.fasta -evalue 1e-50 -max_target_seqs 20000 -outfmt '6 qseqid sseqid slen length qstart qend sstart send btop' -out MoMitochondrion.Pr88167.BLAST
```

Export a list of contigs that comprise mitochondrial sequences:
```bash
awk '$4/$3 > 0.9 {print $2 ",mitochondrion"}' MoMitochondrion.Pr88167.BLAST > Pr88167_mitochondrion.csv
```

Count the total length of contigs that align with the mitochondrial genome:
```bash
#!/bin/bash

# File containing BLAST results
input_file="MoMitochondrion.Pr88167.BLAST"

# Check if file exists
if [ ! -f "$input_file" ]; then
    echo "Error: File $input_file not found."
    exit 1
fi

# Extract unique contig names and their lengths, then sum them
total_contig_length=$(awk '{print $2, $3}' "$input_file" | sort -u | awk '{sum += $2} END {print sum}')

# Check if the calculation was successful
if [ -z "$total_contig_length" ]; then
    echo "Error: Failed to calculate total contig length."
    exit 1
fi

echo "Total length of contigs aligning with mitochondrial genome: $total_contig_length"
```
Output: Total length of contigs aligning with mitochondrial genome: 34804

BLAST genome assembly against a repeat-masked version of the B71 reference genome:
```bash
blastn -query /project/farman_s25abt480/dha308/B71v2sh_masked.fasta -subject /project/farman_s25abt480/dha308/Pr88167/Pr88167_nh.fasta -evalue 1e-50 -max_target_seqs 20000 -outfmt '6 qseqid sseqid qstart qend sstart send btop' -out B71v2sh.Pr88167.BLAST
```

Created a directory for both BLAST outputs and copied over B71v2sh.Pr88167.BLAST to the class directory.

## Genome Annotation

### Using MAKER for Genome Annotation

Create the MAKER config files:
```bash
maker -CTL
```

Update the _makers_opts.ctl_ to match my files exactly.

Run MAKER:
```bash
maker 2>&1 | tee maker.log
```

Merge everything into one GFF file:
```bash
gff3_merge -d Pr88167.maker.output/Pr88167_master_datastore_index.log -o Pr88167-annotations.gff
```

Export sequences of predicted proteins:
```bash
fasta_merge -d Pr88167_final.maker.output/Pr88167_final_master_datastore_index.log -o Pr88167-genes.fasta
```

Count number of predicted proteins:
```bash
grep -c "^>" Pr88167-genes.fasta.all.maker.proteins.fasta
```
Output = 13171

Transfer GFF and Proteins file into class directory.

