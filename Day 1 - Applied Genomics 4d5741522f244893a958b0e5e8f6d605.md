# Day 1 - Applied Genomics

Let’s analyse some genomic data:

**Main documentation:** [https://documentation.dnanexus.com/](https://documentation.dnanexus.com/)

**Index of dx commands:** [https://documentation.dnanexus.com/user/helpstrings-of-sdk-command-line-utilities](https://documentation.dnanexus.com/user/helpstrings-of-sdk-command-line-utilities)

**Installing the toolkit:** [https://documentation.dnanexus.com/downloads](https://documentation.dnanexus.com/downloads)

## Basic Terminal Commands in DNAnexus

```bash
# Login to dnanexus
dx login

# Look into the project
dx ls

dx cd /data

# Make your own folder
dx mkdir <your_name>

# Explore the file metadata

dx describe /data/Sample_S48_L001_R1_001.fastq.gz

# Look into a fastq file
dx cat /data/Sample_S48_L001_R1_001.fastq.gz | zcat | head -n 8

```

## **FastQ format**

The file format that contains the raw data from the sequencer. Each fragment or read is represented by 4 lines in the file.

1. Begins with @ followed by information about the read - sequence identifier
2. The nucleic acid sequence
3. Quality score identifier line, begins with + usually empty
4. String of characters that represent the quality scores for each base

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled.png)

> **Can you identify the flowcell ID of this run?**
> 

# Alignment to reference genome:

Tool: **BWA - Burrow Wheeler Aligner**

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%201.png)

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%202.png)

<aside>
💡 Let’s start by aligning our sample to the reference genome

</aside>

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%203.png)

Click on the BWA-MEM FASTQ Read Mapper:

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%204.png)

Set your output folder to the one you made:

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%205.png)

Add your inputs. Note some are prefilled, this means the app was make with defaults. Explore what the options are.

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%206.png)

Reference data, choose the hs37d5.bwa-index.tar.gz reference bundle.

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%207.png)

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%208.png)

You can also run it on the command line:

```bash
dx run bwa_mem_fastq_read_mapper
-ireads_fastqgzs=/data/Sample_S48_L001_R1_001.fastq.gz \
-ireads_fastqgzs=/data/Sample_S48_L002_R1_001.fastq.gz \
-ireads2_fastqgzs=/data/Sample_S48_L001_R2_001.fastq.gz \
-ireads2_fastqgzs=/data/Sample_S48_L002_R2_001.fastq.gz \
-igenomeindex_targz=project-F3zxk7Q4F30Xp8fG69K1Vppj:file-F404y604F30fbxQG68KF3GZb \
--destination=/<your_name>/alignment_output \
--name=bwa-mem-<your-name> \
--instance-type=mem1_ss1_v2_x8 -y 
```

---

# FastQ - Quality with [FastQC](https://www.bioinformatics.babraham.ac.uk/projects/fastqc/)

## Sequence Quality

**Read Quality Scores:**

- The quality score represents the probability of the base to have been misidentified. Minimum of 0 and maximum is the highest quality observed by the technology.
- Usually 40-41 (although 45 has been observed in Illumina’s Long Read Sequencing Service.
- Quality is representd by ASCII characters depending on the sequencing technology:

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%209.png)

**Phred Quality:**

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2010.png)

### FastQC:

To assess quality of sequencing data, FastQC, an open source tool, provides a quick overview of the quality of your data. Main advantage is the summary graphs and tables that enable you to quickly assess the data in question. FastQC outputs the following information:

- Basic statistics: file identifying information followed by total sequences, filtered sequences and % GC content.
- **Per Base Sequence Quality**
- Per Sequence Quality Scores
- Per Base Sequencing content
- Per Base GC Content
- Per Sequence GC Content
- Per Base N Content
- Sequence Length Distribution
- Duplicate Sequences
- Overrepresented sequences
- Overrepresented Kmers

**Per Base Sequencing quality** is represented by a boxplot where at each position gives the following information:

- the median value, represented by the central red line
- the inter-quartile range (25-75%), represented by the yellow box
- the 10% and 90% values in the upper and lower whiskers
- the mean quality, represented by the blue line

Ideally, you want all you’re your bases to fall in the top green bit of the plot (quality between 28 and 40) but it is normal to notice a drop in quality at the end of the read.

**Flags:**

- **Warning:** if the lower quartile for any base **is less than 10**, or if the median for any base is less **than 25**.
- **Failure** if the lower quartile for any base is **less than 5** or if the median for any base is **less than 20**

### Bad Quality Data Example

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2011.png)

### Good Quality Data Example

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2012.png)

<aside>
⚠️ Lets check the quality of our data:

</aside>

1. In the project go to the apps folder
2. Click on multi_fastqc_v1.1.0
3. Set the output folder within your personal folder

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2013.png)

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2014.png)

1. Select all fastq files into your array input:

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2015.png)

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2016.png)

1. Start Analysis

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2017.png)

1. Once finished -  check the quality of your input files. How is it?

In practice we can combine all this data per run to visualise the quality as a whole:

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2018.png)

# After Alignment:

Contents of bam files:

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2019.png)

**Mark Duplicates:**

Why mark duplicates?

- Non-idependent measurements of sequence
    - Sampled from a single template of DNA
    - Violates independence assumptions made in variant calling
    - Errors in sample/library prep are propagates to all the duplicates

Where do they come from?

- Library duplicates - increase with pcr cycles
- Optical duplicates - nearby clusters on the flow cell lane

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2020.png)

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2021.png)

# Pre-processing - ****Base Quality Score Recalibration (BQSR)****

BQSR stands for Base Quality Score Recalibration. In a nutshell, it is a data pre-processing step that detects systematic errors made by the sequencing machine when it estimates the accuracy of each base call.

Unfortunately the scores produced by the machines are subject to various sources of systematic technical error, leading to over- or under-estimated base quality scores in the data. Some of these errors are due to the physics or the chemistry of how the sequencing reaction works, and some are probably due to manufacturing flaws in the equipment. Base quality score recalibration (BQSR) is a process in which we apply machine learning to model these errors empirically and adjust the quality scores accordingly. This allows us to get more accurate base qualities, which in turn improves the accuracy of our variant calls.

**How BQSR works**

1. You provide GATK Base Recalibrator with a set of known variants.
2. GATK Base Recalibrator analyzes all reads looking for mismatches between the read and reference, skipping those positions which are included in the set of known variants (from step 1).
3. GATK Base Recalibrator computes statistics on the mismatches (identified in step 2) based on the reported quality score, the position in the read, the sequencing context (ex: preceding and current nucleotide).
4. Based on the statistics computed in step 3, an empirical quality score is assigned to each mismatch, overwriting the original reported quality score.

As an example, pre-calibration a file could contain only reported Q25 bases, which seems good. However, it may be that these bases actually mismatch the reference at a 1 in 100 rate, so are actually Q20. These higher-than-empirical quality scores provide false confidence in the base calls.

# Variant Calling with HaplotypeCaller:

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2022.png)

**1. Define active regions**

The program determines which regions of the genome it needs to operate on (active regions), based on the presence of evidence for variation.

**2. Determine haplotypes by assembly of the active region**

For each active region, the program builds a De Bruijn-like graph to reassemble the active region and identifies what are the possible haplotypes present in the data. The program then realigns each haplotype against the reference haplotype using the Smith-Waterman algorithm in order to identify potentially variant sites.

**3. Determine likelihoods of the haplotypes given the read data**

For each active region, the program performs a pairwise alignment of each read against each haplotype using the PairHMM algorithm. This produces a matrix of likelihoods of haplotypes given the read data. These likelihoods are then marginalized to obtain the likelihoods of alleles for each potentially variant site given the read data.

**4. Assign sample genotypes**

For each potentially variant site, the program applies Bayes' rule, using the likelihoods of alleles given the read data to calculate the likelihoods of each genotype per sample given the read data observed for that sample. The most likely genotype is then assigned to the sample.

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2023.png)

# Let’s run a workflow 👩🏻‍💻

A workflow or pipeline is a series of tools/apps which are put together to make processes more efficient and reduce human error.

1. Let’s go back to the tool library
2. Let’s cho ose the **GATK4 Germline Best Practice BAM/CRAM to VCF.** This workflow has three steps
    1. Base quality recalibration as mentioned earlier
    2. HaplotypeCaller - the actual calling of the variants
    3. GenotypeGVCFs - removes positions with no variant called and leads us to our final file.
    
    ![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2024.png)
    
3. Select our project
4. Select your Execution Output folder as before
5. Select your inputs:
    1. Sorted mappings - Choose the bam file you created.
    
    ![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2025.png)
    
    b. Reference genome FASTA file
    
    ![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2026.png)
    
    c. Known variants:
    
    ![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2027.png)
    
    The workflow will automatically pipe the output of the first app to the second and so on.
    
    1. Start Analysis
    
    ## Let’s see how well our sequencing performs.
    
    ## Genome in a Bottle Consortium
    
    **Consortium goals:**
    
    - The Genome in a Bottle Consortium is a public-private-academic consortium hosted by NIST to develop the technical infrastructure (reference standards, reference methods, and reference data) to enable translation of whole human genome sequencing to clinical practice and innovations in technologies.
    - The priority of GIAB is authoritative characterization of human genomes for use in benchmarking, including analytical validation and technology development, optimization, and demonstration.
    
    GIAB has currently characterized: 
    
    - a pilot genome (NA12878/HG001) from the [HapMap project](https://www.genome.gov/10001688/international-hapmap-project/)
    - two son/father/mother trios of Ashkenazi Jewish and Han Chinese ancestry from the [Personal Genome Project](https://www.personalgenomes.org/us)
    
    ### We can use GIAB samples to assess how well our sequencing and analysis is performing.
    
    1. Let’s go to the apps folder in our project
    2. Select vcfecal_hap.py_v1.0.3 and click Run.
    
    ![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2028.png)
    
    1. Select your output folder
    2. Select your inputs ( all in /data/giab):
        1. Query vcf : NA12878-NA12878-CEN-N-EGG5_markdup_recalibrated_Haplotyper.vcf.gz
        2. Truth vcf : HG001_GRCh37_GIAB_highconf_CG-IllFB-IllGATKHC-Ion-10X-SOLID_CHROM1-X_v.3.3.2_highconf_PGandRTGphasetransfer.vcf.gz
        3. Panel bed : CEN_genes_v1.0.0.bed
        4. High Confidence bed : HG001_GRCh37_GIAB_highconf_CG-IllFB-IllGATKHC-Ion-10X-SOLID_CHROM1-X_v.3.3.2_highconf_nosomaticdel.bed
        5. Reference genome: hs37d5.fa.gz
        6. Pkrusche happy docker: pkrusche_happy_v0.3.9.tar.gz
        7. ga4gh [rep.py](http://rep.py) docker: ga4gh_rep.py_v1.0.tar.gz
    3. Write in the Output Prefix: NA12878-<your-name>
    4. Start Analysis
    
    **Let’s review the report.**
    

![Untitled](Day%201%20-%20Applied%20Genomics%204d5741522f244893a958b0e5e8f6d605/Untitled%2029.png)

Useful performance indicators:

- **Precision** (also called positive predictive value ) is the fraction of relevant instances among the retrieved instances
- R**ecall**(also known as sensitivity) is the fraction of relevant instances that were retrieved.

Let’s see how well our assay did by opening the NA12878.summary_report.html.