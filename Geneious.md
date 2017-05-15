#Geneious analysis - Dmrt1 isoforms project  
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

## Initial steps

1. Imported raw reads into Geneious. 
2. Made a list with gene regions of each DMRT gene --> collected from NCBI tool of Geneious 
3. Made a list with all possible junctions of Dmrt1 isoforms
4. Selected the files of paired reads > Clicked on _"Sequence"_ > Clicked on _"Set paired ends"_ 


##Map reads to gene regions:
- Selected the reads to be mapped
- Clicked on _"Map to reference"_
		
	- Reference sequence: list of DMRT gene regions
	- Mapper: Geneious for RNA-Seq
	- Sensitivity: medium-low sensitivity/fast
	- Spam annotated _CDS_ introns
	- Trim sequences
	- Save list of used reads (include mates)

		
##Map reads to junctions:

- Selected reads to be mapped
- Clicked on _"Map to reference"_

	
	- Reference sequence: list of all possible junctions
	- Mapper: Geneious
	- Sensitivity: Custom sensitivity
	- Fine tunning: iterate up to 5 times
	- Use existing trim regions (since the reads had already been trimmed to be mapped to the DMRT gene regions) 
		

		 
##Assembly:  

- Pool the reads mapped to gene regions and junctions
- Use the pooled file to do the assembly:
		
	- Clicked on _"Align/Assemble"_
	- _"Map to reference"_
			
		- Reference sequence: Dmrt sequences and all possible isoforms
		- Mapper: Tophat RNAseq aligner
		- Initial mapper: Bowtie2 - fast and accurate read mapper
		- Intron range: 70 to 500,000
		- Do not trim
		- Save in subfolder
		- Save contigs

##Results of assembly 

Geneious returned a sub-folder with contigs. The contigs were separated according to the DMRT region or isoform that the reads mapped to.
I did the analysis looking into each file and doing a visual check to which contigs had reads mapped through its entire length (parameter used to consider an isoform "real").  

Isoforms identified and its respective stages and temperatures are on [this excel file](https://docs.google.com/spreadsheets/d/1yOXVMR5z7Iu-80mqBVcjA7KrQUrJQmn1qUkuNZxXOTI/edit#gid=0).