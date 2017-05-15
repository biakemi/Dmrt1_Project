#Dmrt1 isoforms gene expression analysis

After a rough analysis of CPI transcriptome (using bbtools command 'bbduk'), it was observed that only Stages 19 and 22 of 26C individuals had reads of the 1-4 junction Dmrt1 isoform. 
Then, Nicole and I decided to approach this project using bioinformatics tools instead of TaqMan qPCR.

This file will have all steps done to achieve results of Dmrt1 and Dmrt1 isoforms expression pattern in CPI embryos (stg 9, 12, 15, 19, 22); 26C and 31C.

I am using RNA-seq data, collected from CPI embryos from 2015. The RNA of these embryos were collected in 2016 by ZQ and sent to Chicago for Illumina sequencing in pools of around 10 individuals (need to ask ZQ for the file with samples used), with replicates.

ZQ assembled the reads in 2017.  
* Reads already came in clean, with no adaptors  
* TrimmerMax to trim the reads  
* BBNorm (bbmap) to normalize coverage - get a flat coverage distribution  
* Trinity to assemble the transcriptome  
* CD-hit to remove redundancy

ZQ copied to my folder on condo2017 the file with all reads (all stages, all temps) assembled and the raw reads.

I will start my analysis using those files.

##Load modules to work in condo
`$ module load name_of_module`
to check the name of the module, we can type `$ module spider` and copy the name as it is displayed

##Mapping reads to genomic region of Dmrts  
ZQ trimmed the reads and sent me a script to make a genome-guided assembly of the reads to identify new isoforms.  

**To build to reference Dmrt genome region**.   
I will be mapping the reads only on the Dmrt (and potential isoforms) regions. That will reduce the complexity of the run and allow me to do a faster analysis on what I want to investigate (i.e. Dmrt1 and isoforms only). Thus, I will map the reads to a reference file with the genome regions of all Dmrts (Dmrt1, Dmrt2, Dmrt3, Dmrta1, Dmrta2, Dmrtb1, Dmrtc2) and all possible combinations of exons of Dmrt1 (total os 25 combinations - The file is uploaded on github).  
The genome regions and the isoforms were obtained on geneious and exported as a list of sequences on fasta format.  

**Script**  

```
#!/bin/bash
#Submit this script with: sbatch thefilename
#SBATCH -t 04:00:00   # walltime
#SBATCH -e ./Dmrt1_Stg09_26C_Rep1.error
#SBATCH -o ./Dmrt1_Stg09_26C_Rep1.out
#SBATCH -N 1   # number of nodes in this job
#SBATCH -n 16   # total number of processor cores in this job
#SBATCH -J "Dmrt1_Stg09_26C_Rep1"   # job name
#SBATCH --mail-user=biakemi@iastate.edu   # email address
#SBATCH --mail-type=BEGIN
#SBATCH --mail-type=END
# LOAD MODULES, INSERT CODE, AND RUN YOUR PROGRAMS HERE
module purge
module load intel
module load allinea
ulimit -s unlimited

module load bowtie2/2.2.6
#Building index required by bowtie to map the reads
bowtie2-build -f Reference_Dmrt.fasta Dmrt1_ref
#Bowtie mapping the reads, the output is a SAM file, with 16 threads and using single-end reads parameter
bowtie2 --sensitive -x /work/LAS/nvalenzu-lab/biakemi/Working_Data/Dmrt1_ref -p 16 -1 /work/LAS/nvalenzu-lab/zqwu/CPI_RNAseq/Trimed_Data/Stage09/CPI_S12_T26_Male/NV-S20_CPI_S09_T26_Male_rp1_R1_PE.fq.gz -2 /work/LAS/nvalenzu-lab/zqwu/CPI_RNAseq/Trimed_Data/Stage09/CPI_S12_T26_Male/NV-S20_CPI_S09_T26_Male_rp1_R2_PE.fq.gz -S Dmrt1_26C_Stg09_Rep1.sam

module load samtools/1.2
#Converting the SAM file to BAM format
samtools view -@ 16 -bS -t /work/LAS/nvalenzu-lab/biakemi/Working_Data/Reference_Dmrt.fai /work/LAS/nvalenzu-lab/biakemi/Working_Data/Dmrt1_26C_Stg09_Rep1.sam > Dmrt1_26C_Stg09_Rep1.bam
#Sorting the reads
samtools  sort    Dmrt1_26C_Stg09_Rep1.bam Dmrt1_26C_Stg09_Rep1.sort
#Creating an index
samtools index    Dmrt1_26C_Stg09_Rep1.sort.bam
#This command gives some statistics on the alignment
samtools flagstat Dmrt1_26C_Stg09_Rep1.sort.bam > Dmrt1_26C_Stg09_Rep1.sort.bam.BamStats.txt
#For only unique reads
samtools view -@ 16 -H   Dmrt1_26C_Stg09_Rep1.sort.bam > Header.sam
samtools view -@ 16 -F 4 Dmrt1_26C_Stg09_Rep1.sort.bam | grep -v "XS:" | cat Header.sam - | samtools view -@ 16 -b - > Dmrt1_26C_Stg09_Rep1.sort.unique.bam

module load samtools/1.2
module load bbmap/35.82
pileup.sh in=Dmrt1_26C_Stg09_Rep1.sort.unique.bam  out=Dmrt1_26C_Stg09_Rep1.sort.unique.bam.bbtools.out.txt  rpkm=Dmrt1_26C_Stg09_Rep1.sort.unique.bam.bbtools.rpkm.txt
rm Dmrt1_26C_Stg09_Rep1.sam Dmrt1_26C_Stg09_Rep1.bam. 

```

This command was run on all trimmed files.