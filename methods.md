<img width="462" height="273" alt="image" src="https://github.com/user-attachments/assets/c44881e3-f6e1-4300-ac9a-69a3bbda78d4" /><img width="425" height="205" alt="image" src="https://github.com/user-attachments/assets/d036b7e4-e1c2-427f-928d-726e8c7ba76c" /># Methods (Tools, Versions, Commands)


##Software and Tool Versions
------------------------------------------------------------
All analyses were performed using the latest stable releases (as of 2025) in isolated Conda or Docker environments to ensure reproducibility.  
The complete list of tools and their versions is as follows:

- DeepVariant v1.9.0  
- GATK v4.6.2.0 (includes Picard v3.4.0 and HTSJDK v4.2.0)  
- FreeBayes v1.3.10  
- VarScan v2.4.6  
- BCFtools v1.22 (with HTSlib v1.22.1)  
- Samtools v1.22.1  
- BWA-MEM v0.7.17  
- Bowtie2 v2.5.2  
- Minimap2 v2.28  
- RTG Tools v3.12.1  
- FastQC v0.11.9  
- MultiQC v1.15  
  -fastp 0.23.4
-  seqkit v2.8.2


- soapdenovo2 2.40                             
ABySS version 2.3.6
 MEGAHIT v1.2.9



# ✅  Statement (Normal Dataset Only)**

The raw paired-end Illumina NovaSeq 6000 reads for the *Chelonia mydas* normal sample (CH-NORMS1) were obtained from Macrogen Singapore using the TruSeq Nano DNA library preparation kit (151 bp, paired-end). Quality assessment using FastQC (v0.11.9) and MultiQC (v1.15) showed no poor-quality reads, uniform base quality across read positions, and a GC content of approximately 44%. These results confirm that the sequencing data are of high quality and suitable for downstream genome mapping and variant detection analyses.

---

# ✅ **Sequencing Data Summary (Normal Sample Only)**

| Dataset            | File            | Encoding     | Total Sequences | Total Bases | Read length | %GC | Poor-quality reads |
| ------------------ | --------------- | ------------ | --------------: | ----------: | ----------: | --: | -----------------: |
| Normal (CH-NORMS1) | W1-A_1.fastq.gz | Illumina 1.9 |     456,923,725 |    68.9 Gbp |      151 bp |  44 |                  0 |
| Normal (CH-NORMS1) | W1-A_2.fastq.gz | Illumina 1.9 |     456,923,725 |    68.9 Gbp |      151 bp |  45 |                  0 |




## QC
fastqc --threads 16 W1-A_1.fastq.gz W1-A_2.fastq.gz -o CH-NORMS1/FastqcReport
multiqc "/media/work/New Volume1/DataofDNA/" -o "/media/work/New Volume1/DataofDNA/summary/"





**##Trimming**


Tool Versions and Sources:
------------------------------------------------------------


Workflow and Rationale
 fastp (v0.23.4) 

scripts/commands_trimming_all_tools.txt

Quality improvements were confirmed using FastQC (v0.11.9) and summarized with MultiQC (v1.15) after trimming.

After Trimming these are Result
====================================================
 | Dataset              | File                     | Encoding         | Total Sequences | Total Bases | Read length | %GC | Poor-quality reads |
  | -------------------- | ------------------------ | ---------------- | ---------------: | -----------: | -----------: | ---:| ------------------: |
| Normal (CH-NORMS1)   | W1-A_1_trimmed.fastq.gz | Illumina 1.9     |     438,509,696 |      66 Gbp |   15–151 bp |  44 |                   0 |
| Normal (CH-NORMS1)   | W1-A_2_trimmed.fastq.gz | Illumina 1.9     |     438,509,696 |      66 Gbp |   15–151 bp |  44 |                   0 |



--------------------------------------Start Making Truthset for validation --------------------------
==============================================================================ABySS version 2.3.6   (abyss_env)
cd /home/work/Desktop/variants/denovo_try/Abyss/Normal/ && \
abyss-pe k=96 name=W1-A lib=pe \
pe="/home/work/Desktop/variants/DataofDNA/CH-NORMS1/W1-A_1_trimmed.fastq.gz /home/work/Desktop/variants/DataofDNA/CH-NORMS1/W1-A_2_trimmed.fastq.gz" \
j=10 B=600G


===================================================- soapdenovo2 2.40                             


soapdenovo2 2.40                                     (soapdenovo2_env)

Normal

step 1

=================
soap_config.txt
max_rd_len=151

[LIB]
avg_ins=350
reverse_seq=0
asm_flags=3
rank=1
q1=/home/work/Desktop/variants/DataofDNA/CH-NORMS1/W1-A_1_trimmed.fastq.gz
q2=/home/work/Desktop/variants/DataofDNA/CH-NORMS1/W1-A_2_trimmed.fastq.gz


=====================




cd /home/work/Desktop/variants/denovo_try/soapdenovo2/

SOAPdenovo-63mer all \
  -s soap_config.txt \
  -K 63 \
  -o W1-A-soap \
  -p 30 \
  -a 450

============================================================================

(soapdenovo2_env) work@work-Precision-7920-Tower:~/Desktop/variants/denovo_try/soapdenovo2$ SOAPdenovo-63mer all   -s soap_config.txt   -K 63   -o W1-A-soap   -p 30   -a 450

============================================================================ MEGAHIT v1.2.9
megahit \
  -1 /media/beeu/DATA/fahad/TrimData/CH-NORMS1_trim/W1-A_1_trimmed.fastq.gz \
  -2 /media/beeu/DATA/fahad/TrimData/CH-NORMS1_trim/W1-A_2_trimmed.fastq.gz \
  -o /media/beeu/DATA/fahad/Denovo/Megahit_Normal \
  --min-count 2 \
  --k-min 21 \
  --k-max 141 \
  --k-step 12 \
  -t 40

  ==============================================================================



  =================================================================Busco Results ==================
| Sample (output folder)                           |       C% |   S% |  D% |   F% |   M% |    n |
| ------------------------------------------------ | -------: | ---: | --: | ---: | ---: | ---: |
| Reference\_ncbi\_Reference\_busco\_results       | **99.6** | 98.9 | 0.7 |  0.1 |  0.3 | 7480 |
| abyss\_normal\_W1-A-scaffolds\_busco\_results    | **67.6** | 66.6 | 1.0 | 21.8 | 10.6 | 7480 |
| **polca\_W1-A-scaffolds\_busco\_results**        | **67.6** | 66.6 | 1.0 | 21.8 | 10.6 | 7480 |
| soapdenovo\_normal\_W1-A-soap\_busco\_results    |     59.1 | 58.7 | 0.4 | 25.2 | 15.7 | 7480 |
| magahit\_normal\_final.contigs\_busco\_results   |     56.5 | 56.0 | 0.5 | 28.2 | 15.4 | 7480 |

========================Busco restuls with ANI====
| Assembly Tool / Run      | BUSCO C% |   F% |   M% |   ANI (%) | Align Fraction (Ref) | Align Fraction (Query) | Interpretation                      |
| ------------------------ | -------: | ---: | ---: | --------: | -------------------: | ---------------------: | ----------------------------------- |
| **Reference\_ncbi**      |     99.6 |  0.1 |  0.3 |    100.00 |              100.00% |                100.00% | 🏆 Perfect reference                |
| **ABySS (Normal)**       |     67.6 | 21.8 | 10.6 | **99.45** |           **89.48%** |             **84.86%** | ✅ Best similarity overall           |
| **ABySS + Polca polish** |     67.6 | 21.8 | 10.6 | \~99.45\* |           \~89.48%\* |             \~84.86%\* | ➖ No BUSCO change (likely same ANI) |
| MEGAHIT (Normal)         |     56.5 | 28.2 | 15.4 |     99.40 |               87.91% |                 85.36% | 🟢 Very good                        |
| SOAPdenovo2 (Normal)     |     59.1 | 25.2 | 15.7 |     99.03 |               78.14% |                 72.21% | 🔴 Lowest similarity                |



======================================Quest Version: 5.3.0 report of megahit ,soapdenov and abyss========

All statistics are based on contigs of size >= 500 bp, unless otherwise noted (e.g., "# contigs (>= 0 bp)" and "Total length (>= 0 bp)" include all contigs).

Assembly                    MEGAHIT Assembly  SOAPdenovo2 Assembly  ABySS Assembly
# contigs (>= 0 bp)         2016526           4302510               2118419       
# contigs (>= 1000 bp)      188829            178561                128235        
# contigs (>= 5000 bp)      108682            95240                 90825         
# contigs (>= 10000 bp)     67450             60102                 62645         
# contigs (>= 25000 bp)     19178             19989                 24430         
# contigs (>= 50000 bp)     3057              4334                  6212          
Total length (>= 0 bp)      2556992461        2558046622            2412364772    
Total length (>= 1000 bp)   2001525914        1919780233            1999634395    
Total length (>= 5000 bp)   1802063399        1721813040            1889199532    
Total length (>= 10000 bp)  1502934610        1468244755            1683849457    
Total length (>= 25000 bp)  739349188         832894057             1068318912    
Total length (>= 50000 bp)  198556342         299482189             440085929     
# contigs                   297228            254624                201612        
Largest contig              165262            250385                299793        
Total length                2072540175        1972334240            2048669361    
GC (%)                      43.86             43.53                 43.70         
N50                         18275             20633                 26214         
N90                         3816              3886                  6191          
auN                         23300.9           27698.7               33849.9       
L50                         33113             26743                 22712         
L90                         123096            107293                82698         
# N's per 100 kbp           0.00              4415.14               324.62        
========================================================================================

----------------------------------------------------files--------------------------------------------

| Assembler   | File to Use                         | Use Case                            |
| ----------- | ----------------------------------- | ----------------------------------- |
| MEGAHIT     | `final.contigs.fa`                  | ✅ All evaluations                   |
| SOAPdenovo2 | `W1-A-soap.scafSeq`                 | ✅ All evaluations                   |
| ABySS       | `W1-A-scaffolds.fa` (or `.contigs`) | ✅ All evaluations (prefer scaffold) |

=========================================ANI results --------------------------------------------
| Assembly Tool   | ANI (%)   | Align Fraction (Ref) | Align Fraction (Query) | Interpretation            |
| --------------- | --------- | -------------------- | ---------------------- | ------------------------- |
| **ABySS**       | **99.45** | **89.48%**           | **84.86%**             | ✅ Best similarity overall |
| **MEGAHIT**     | 99.40     | 87.91%               | 85.36%                 | 🟢 Very good              |
| **SOAPdenovo2** | 99.03     | 78.14%               | 72.21%                 | 🔴 Lowest similarity      |




==========================================truth set 

 minimap2 -ax asm5 Reference/Reference.fasta Abyss/Normal/W1-A-contigs.fa > minimap2.sam

samtools sort -@ 8 -o /home/work/Desktop/variants/denovo_try/minmap/minimap2_sorted.bam /home/work/Desktop/variants/denovo_try/minmap/minimap2.sam

# Step 1: Generate BCF using bcftools mpileup
bcftools mpileup -f Reference/Reference.fasta /home/work/Desktop/variants/denovo_try/minmap/minimap2_sorted.bam \
  -Ou | bcftools call -mv -Oz -o /home/work/Desktop/variants/denovo_try/minmap/variants_minimap2.vcf.gz

# Step 2: Index the VCF (recommended)
bcftools index /home/work/Desktop/variants/denovo_try/minmap/variants_minimap2.vcf.gz



--------------------------------------End Making Truthset for validation --------------------------


## Alignment 

Alignment
------------------------------------------------------------
Three mappers were evaluated for read alignment against the Chelonia mydas reference genome rCheMyd1.pri.v2 (GCA_015237465.2):
1. BWA-MEM v0.7.17
2. Bowtie2 v2.5.4
3. Minimap2 v2.28

Each aligner was executed using 15–40 threads on paired-end 151 bp Illumina NovaSeq reads.
Outputs were sorted and indexed with Samtools (v1.22.1).

Exact shell commands are provided in

scripts/commands_alignment.txt.

Mapping quality was evaluated using Samtools flagstat and alignment summary metrics.



Metric                                   Minimap2        Bowtie2         BWA
-------------------------------------------------------------------------------
Total Reads (QC-passed)                  879,215,458     877,019,392     880,634,868
Mapped Reads (%)                         99.45%          99.64%          99.75%
Properly Paired (%)                      97.63%          69.69%          98.14%
Singletons (%)                           0.27%           0.15%           0.10%
Mate Mapped to Different Chr (%)         0.83%           1.54%           1.06%
Mate Mapped to Diff. Chr (MAPQ >= 5) (%) 0.43%           1.06%           0.58%



gatk MarkDuplicates \
  -I "/media/work/New Volume1/Alignment/FastpAllignment/CH-NORMS1_trimmed_sorted_rg.bam" \
  -O "/home/work/Desktop/variants/latest_tools_varains_with_reference_file/dedup_allignments/CH-NORMS1.dedup.bam" \
  -M "/home/work/Desktop/variants/latest_tools_varains_with_reference_file/dedup_allignments/CH-NORMS1.metrics.txt" \
  --CREATE_INDEX true

  GATK MarkDuplicates Results Summary
====================================

Sample: CH-NORMS1)
--------------------------
READ_PAIRS_EXAMINED: 436,970,646
READ_PAIR_DUPLICATES: 37,780,610
READ_PAIR_OPTICAL_DUPLICATES: 3,774,873 (~10% of duplicate pairs)
PERCENT_DUPLICATION: 8.67%
ESTIMATED_LIBRARY_SIZE: 2,612,848,101

Interpretation:
- Duplication rate is low (under 10%), which is good for downstream variant calling.
- Library complexity is high (large estimated library size).


Variant Calling
------------------------------------------------------------
Five variant calling tools were used to detect SNPs and indels from BWA-MEM–aligned reads:
DeepVariant v1.9.0, GATK v4.6.2.0, FreeBayes v1.3.10, VarScan v2.4.6, and BCFtools v1.22.
Each tool was executed on both normal and abnormal Chelonia mydas datasets using the same reference genome rCheMyd1.pri.v2 (GCA_015237465.2).
Command-line parameters for reproducibility are provided in 

scripts/commands_variant_calling.txt.

Results 

==============================================================================================================
Orignal varaints after varaint calling tools 



Tool          Sample      Total Variants
-----------------------------------------
BCFtools      Normal      14,327,393
DeepVariant   Normal      17,425,142
FreeBayes     Normal      13,910,723
GATK          Normal      14,632,668
VarScan       Normal      13,762,684




**Variant Calling then filtering**  


Variants were retained if they:
(i) passed the caller’s internal filters (FILTER=PASS or “.”),
(ii) had alternate allele depth (AD) greater than 3, and
(iii) had variant allele frequency (VAF) above 2%.
Variants not meeting these criteria were removed.

Variants were filtered to retain only those with FILTER=PASS/., AD > 3, and VAF > 0.02.


(FILTER="PASS" or ".")
AND
ALT depth > 3
AND
VAF > 0.02)

Command-line parameters for reproducibility are provided in 
https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/filter_all_tools.sh

-----------------------------------------------------------------------------        **   After Filter results **---------------------------------

Tool          Sample      Total Variants
-----------------------------------------
BCFtools      Normal      14,220,913
DeepVariant   Normal      13,705,037
FreeBayes     Normal      13,239,802
GATK          Normal      14,467,559
VarScan       Normal      13,753,976
-----------------------------------------------------------------------------        ** SNPS files **---------------------------------
Command-line parameters for reproducibility are provided in 
(https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/make_snps.sh)


File                     SNP_File_Count
----------------------------------------
BCF_normal               12678209
Deepvariant_normal       12058893
Freebayes_normal         11247911
Gatk_normal              12629773
Varscan_normal           12228987

                                  Evalution using RTG

     commands 
     /script/RTG.txt




     (A) Unfiltered performance (Threshold = None)

These rows correspond to “use all variants as they are in the VCF” (no score cutoff).
| Tool        | TP (baseline) | TP (call) | FP        | FN        | Precision  | Sensitivity | F1         |
| ----------- | ------------- | --------- | --------- | --------- | ---------- | ----------- | ---------- |
| VarScan     | 5,088,509     | 5,088,505 | 7,140,482 | 3,079,542 | 0.4161     | 0.6230      | 0.4989     |
| GATK        | 5,068,348     | 5,068,342 | 7,561,404 | 3,099,703 | 0.4013     | 0.6205      | 0.4874     |
| FreeBayes   | 4,465,838     | 4,456,957 | 6,589,960 | 3,702,213 | 0.4035     | 0.5467      | 0.4643     |
| BCFtools    | 5,125,613     | 5,125,609 | 7,552,600 | 3,042,438 | 0.4043     | 0.6275      | 0.4918     |
| DeepVariant | 5,081,165     | 5,081,160 | 6,975,659 | 3,086,886 | **0.4214** | 0.6221      | **0.5025** |


(B) Best-score point (vcfeval “Threshold” row)
| Tool        | Threshold | TP (baseline) | TP (call) | FP        | FN        | Precision  | Sensitivity | F1         |
| ----------- | --------- | ------------- | --------- | --------- | --------- | ---------- | ----------- | ---------- |
| VarScan     | None      | 5,088,509     | 5,088,505 | 7,140,482 | 3,079,542 | 0.4161     | 0.6230      | 0.4989     |
| GATK        | 1402.060  | 4,169,967     | 4,169,967 | 545,323   | 3,998,084 | 0.8844     | 0.5105      | 0.6473     |
| FreeBayes   | 1026.160  | 3,758,225     | 3,750,978 | 448,749   | 4,409,826 | 0.8931     | 0.4601      | 0.6073     |
| BCFtools    | 222.417   | 4,403,711     | 4,403,711 | 268,293   | 3,764,340 | **0.9426** | **0.5391**  | **0.6859** |
| DeepVariant | 39.100    | 3,672,379     | 3,672,379 | 497,597   | 4,495,672 | 0.8807     | 0.4496      | 0.5953     |





==================================================inter Tools concordance -----==============================
step1: make bed fils for snps and indels

Command
https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/make_snp_and_indels_bed.sh


step2: SNP-only overlaps

i. Pairwise overlap (BED + bedtools intersect)    
---------------------------------------------------- normal SNPs  overlap------------------------------------------------
BCF_normal_SNPs.bed
Deepvariant_normal_SNPs.bed
Freebayes_normal_SNPs.bed
Gatk_normal_SNPs.bed
Varscan_normal_SNPs.bed


Command
https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/normal_snp_overlap.sh



ToolA                         ToolB                           Overlap
----------------------------------------------------------------------------
BCF_normal_SNPs              Deepvariant_normal_SNPs          11936185
BCF_normal_SNPs              Freebayes_normal_SNPs            10988076
BCF_normal_SNPs              Gatk_normal_SNPs                 12246438
BCF_normal_SNPs              Varscan_normal_SNPs              12096398
Deepvariant_normal_SNPs      Freebayes_normal_SNPs            10566527
Deepvariant_normal_SNPs      Gatk_normal_SNPs                 11805679
Deepvariant_normal_SNPs      Varscan_normal_SNPs              11687696
Freebayes_normal_SNPs        Gatk_normal_SNPs                 10867399
Freebayes_normal_SNPs        Varscan_normal_SNPs              10648860
Gatk_normal_SNPs             Varscan_normal_SNPs              11916478




----------------------------------------------------------------------------------------------------------Jaccard similarity Normal SNPs

Jaccard similarity analysis of SNP calls from the normal dataset showed a consistently high level of agreement among the five variant callers. DeepVariant, GATK, BCFtools and VarScan exhibited the strongest similarity scores, while FreeBayes showed comparatively lower but still substantial overlap. Overall, the normal‐sample SNP concordance indicates strong cross-caller consistency in high-confidence SNP regions.

command 

https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/normal_snp_jaccard.sh




