<img width="425" height="205" alt="image" src="https://github.com/user-attachments/assets/d036b7e4-e1c2-427f-928d-726e8c7ba76c" /># Methods (Tools, Versions, Commands)


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
- fastp v0.26.0  
- seqkit v2.8.2

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
fastqc --threads 16 S1_1.fastq.gz S1_2.fastq.gz -o Ab-NormS2/FastqcReport
multiqc "/media/work/New Volume1/DataofDNA/" -o "/media/work/New Volume1/DataofDNA/summary/"



**##Trimming**
Five widely used read-preprocessing tools were evaluated for quality and adapter trimming of Chelonia mydas paired-end Illumina NovaSeq reads (151 bp). All runs were performed using 20 threads on a high-performance workstation (Intel Xeon, 512 GB RAM, Ubuntu 20.04 LTS).

Tool Versions and Sources:
------------------------------------------------------------
1. fastp v0.26.0 — OpenGene GitHub
   Fast, all-in-one trimmer with automatic adapter detection, base correction, and per-sample JSON/HTML QC reports (released August 2024).

------------------------------------------------------------

Workflow and Rationale
All five trimmers were applied to both the normal (CH-NORMS1) and abnormal (Ab-NormS2) datasets to compare performance in adapter removal, read retention, and GC content preservation.
Among them, fastp (v0.26.0) demonstrated the best overall balance of speed, accuracy, and comprehensive reporting. Therefore, fastp-trimmed reads were selected for all downstream analyses, including alignment, variant calling, and benchmarking.

The exact shell commands and parameters for each tool are provided in:
scripts/commands_trimming_all_tools.txt

Quality improvements were confirmed using FastQC (v0.11.9) and summarized with MultiQC (v1.15) after trimming.



               Trimmed Sequencing Data Summary (Normal CH-NORMS1)

               | Dataset            | File            | Encoding     | Total Sequences | Poor-quality reads | Read length | %GC |
| ------------------ | --------------- | ------------ | --------------: | -----------------: | ----------: | --: |
| Normal (CH-NORMS1) | W1-A_1.fastq.gz | Illumina 1.9 |     456,923,725 |                  0 |      151 bp |  44 |
| Normal (CH-NORMS1) | W1-A_2.fastq.gz | Illumina 1.9 |     456,923,725 |                  0 |      151 bp |  45 |






==============================================================================ABySS version 2.3.6     (abyss_env)
cd /home/work/Desktop/variants/denovo_try/Abyss/Normal/ && \
abyss-pe k=96 name=W1-A lib=pe \
pe="/home/work/Desktop/variants/DataofDNA/CH-NORMS1/W1-A_1.fastq.gz /home/work/Desktop/variants/DataofDNA/CH-NORMS1/W1-A_2.fastq.gz" \
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
q1=/home/work/Desktop/variants/DataofDNA/CH-NORMS1/W1-A_1.fastq
q2=/home/work/Desktop/variants/DataofDNA/CH-NORMS1/W1-A_2.fastq


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





========================== mummer4-4.0.0rc1  ===================

nucmer --prefix=/home/work/Desktop/variants/denovo_try/mumer4/abyss_vs_ref \
  /home/work/Desktop/variants/denovo_try/Reference/Reference.fasta \
  /home/work/Desktop/variants/denovo_try/Abyss/Normal/W1-A-contigs.fa



## Alignment 

Alignment
------------------------------------------------------------
Three mappers were evaluated for read alignment against the Chelonia mydas reference genome (rCheMyd1.pri.v2):
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









Variant Calling
------------------------------------------------------------
Five variant calling tools were used to detect SNPs and indels from BWA-MEM–aligned reads:
DeepVariant v1.9.0, GATK v4.6.2.0, FreeBayes v1.3.10, VarScan v2.4.6, and BCFtools v1.22.
Each tool was executed on both normal and abnormal Chelonia mydas datasets using the same reference genome (rCheMyd1.pri.v2).
Command-line parameters for reproducibility are provided in 

scripts/commands_variant_calling.txt.

Results 

==============================================================================================================
Orignal varaints after varaint calling tools 

Tool          Sample      Total Variants
-----------------------------------------
BCFtools      Abnormal    14,380,356
BCFtools      Normal      14,327,393
DeepVariant   Abnormal    17,536,252
DeepVariant   Normal      17,425,142
FreeBayes     Abnormal    13,935,733
FreeBayes     Normal      13,910,723
GATK          Abnormal    14,671,962
GATK          Normal      14,632,668
VarScan       Abnormal    13,842,258
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
BCFtools      Abnormal    14,285,440
BCFtools      Normal      14,220,913
DeepVariant   Abnormal    13,767,503
DeepVariant   Normal      13,705,037
FreeBayes     Abnormal    13,332,001
FreeBayes     Normal      13,239,802
GATK          Abnormal    14,522,966
GATK          Normal      14,467,559
VarScan       Abnormal    13,833,482
VarScan       Normal      13,753,976
-----------------------------------------------------------------------------        ** SNPS files **---------------------------------
Command-line parameters for reproducibility are provided in 
(https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/make_snps.sh)


File                     SNP_File_Count
----------------------------------------
BCF_abnormal             12726145
BCF_normal               12678209
Deepvariant_abnormal     12103451
Deepvariant_normal       12058893
Freebayes_abnormal       11300566
Freebayes_normal         11247911
Gatk_abnormal            12663916
Gatk_normal              12629773
Varscan_abnormal         12288474
Varscan_normal           12228987



-----------------------------------------------------------------------------        ** Indels files **---------------------------------
Command-line parameters for reproducibility are provided in 
(https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/make_indels.sh)

File                     INDEL_File_Count
------------------------------------------
BCF_abnormal             1559295
BCF_normal               1542704
Deepvariant_abnormal     1684651
Deepvariant_normal       1666433
Freebayes_abnormal       1276508
Freebayes_normal         1254311
Gatk_abnormal            1880614
Gatk_normal              1858658
Varscan_abnormal         1545008
Varscan_normal           1524989

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


---------------------------------------------------- abnormal SNPs overlap------------------------------------------------

BCF_abnormal_SNPs.bed
Deepvariant_abnormal_SNPs.bed
Freebayes_abnormal_SNPs.bed
Gatk_abnormal_SNPs.bed
Varscan_abnormal_SNPs

Command
https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/abnormal_snp_overlap.sh




ToolA                         ToolB                           Overlap
----------------------------------------------------------------------------
BCF_abnormal_SNPs            Deepvariant_abnormal_SNPs        11982196
BCF_abnormal_SNPs            Freebayes_abnormal_SNPs          11026629
BCF_abnormal_SNPs            Gatk_abnormal_SNPs               12282731
BCF_abnormal_SNPs            Varscan_abnormal_SNPs            12153920
Deepvariant_abnormal_SNPs    Freebayes_abnormal_SNPs          10607920
Deepvariant_abnormal_SNPs    Gatk_abnormal_SNPs               11849848
Deepvariant_abnormal_SNPs    Varscan_abnormal_SNPs            11744062
Freebayes_abnormal_SNPs      Gatk_abnormal_SNPs               10894738
Freebayes_abnormal_SNPs      Varscan_abnormal_SNPs            10688109
Gatk_abnormal_SNPs           Varscan_abnormal_SNPs            11959187


---------------------------------------------------- normal Indel overlap------------------------------------------------

BCF_normal_INDELs.bed
Deepvariant_normal_INDELs.bed
Freebayes_normal_INDELs.bed
Gatk_normal_INDELs.bed
Varscan_normal_INDELs.bed



Command
https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/normal_indel_overlap.sh


ToolA                         ToolB                           Overlap
---------------------------------------------------------------------------
BCF_normal_INDELs            Deepvariant_normal_INDELs        1496337
BCF_normal_INDELs            Freebayes_normal_INDELs          1247269
BCF_normal_INDELs            Gatk_normal_INDELs               1500247
BCF_normal_INDELs            Varscan_normal_INDELs            1448813
Deepvariant_normal_INDELs    Freebayes_normal_INDELs          1254057
Deepvariant_normal_INDELs    Gatk_normal_INDELs               1608289
Deepvariant_normal_INDELs    Varscan_normal_INDELs            1484214
Freebayes_normal_INDELs      Gatk_normal_INDELs               1220823
Freebayes_normal_INDELs      Varscan_normal_INDELs            1189766
Gatk_normal_INDELs           Varscan_normal_INDELs            1512186




---------------------------------------------------- abnormal indel overlap------------------------------------------------
BCF_abnormal_INDELs.bed
Deepvariant_abnormal_INDELs.bed
Freebayes_abnormal_INDELs.bed
Gatk_abnormal_INDELs.bed
Varscan_abnormal_INDELs.bed



Command
https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/abnormal_indel_overlap.sh


ToolA                          ToolB                            Overlap
----------------------------------------------------------------------------
BCF_abnormal_INDELs           Deepvariant_abnormal_INDELs       1513275
BCF_abnormal_INDELs           Freebayes_abnormal_INDELs         1269730
BCF_abnormal_INDELs           Gatk_abnormal_INDELs              1518467
BCF_abnormal_INDELs           Varscan_abnormal_INDELs           1467759
Deepvariant_abnormal_INDELs   Freebayes_abnormal_INDELs         1275264
Deepvariant_abnormal_INDELs   Gatk_abnormal_INDELs              1629128
Deepvariant_abnormal_INDELs   Varscan_abnormal_INDELs           1502048
Freebayes_abnormal_INDELs     Gatk_abnormal_INDELs              1239822
Freebayes_abnormal_INDELs     Varscan_abnormal_INDELs           1207299
Gatk_abnormal_INDELs          Varscan_abnormal_INDELs           1531127


----------------------------------------------------------------------------------------------------------Jaccard similarity Normal SNPs

Jaccard similarity analysis of SNP calls from the normal dataset showed a consistently high level of agreement among the five variant callers. DeepVariant, GATK, BCFtools and VarScan exhibited the strongest similarity scores, while FreeBayes showed comparatively lower but still substantial overlap. Overall, the normal‐sample SNP concordance indicates strong cross-caller consistency in high-confidence SNP regions.

command 

https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/normal_snp_jaccard.sh

----------------------------------------------------------------------------------------------------------Jaccard similarity abnormal SNPs
In the abnormal dataset, SNP-level Jaccard similarities followed a pattern similar to the normal sample. DeepVariant, GATK, BCFtools and VarScan again demonstrated strong mutual overlap, whereas FreeBayes maintained moderate similarity values. The abnormal sample displayed slightly reduced inter-caller similarity compared to the normal dataset, likely reflecting biological differences or coverage variation, but overall SNP concordance remained high

command

https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/abnormal_snp_jaccard.sh

----------------------------------------------------------------------------------------------------------Jaccard similarity Normal Indel
Jaccard similarity for INDELs in the normal dataset was lower than for SNPs, consistent with known challenges in INDEL calling. DeepVariant and GATK shared the highest INDEL concordance, followed by BCFtools and VarScan, whereas FreeBayes showed the lowest similarity with the other callers. These patterns align with previously reported INDEL-calling inconsistencies across tools.

command 
https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/normal_indel_jaccard.sh


----------------------------------------------------------------------------------------------------------Jaccard similarity abnormal Indel

The abnormal dataset exhibited similar INDEL concordance patterns to the normal dataset, with DeepVariant and GATK forming the most consistent pair. Overall Jaccard values for INDELs were modest, reflecting typical inter-caller variability for small insertions and deletions. FreeBayes again showed weaker similarity to the remaining tools, while BCFtools and VarScan showed intermediate concordance.

command
https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/scripts/abnormal_indel_jaccard.sh


---------------------------------------------------- For Venn SNP diagram------------------------------------------------
Venn diagrams were generated to compare the overlap of SNP calls among the five variant calling tools (DeepVariant, GATK, FreeBayes, VarScan, and BCFtools). SNPs were extracted from each filtered VCF using bcftools view, and unique variant identifiers (CHROM:POS:REF:ALT) were obtained using bcftools query. These variant sets were then imported into R, and multi-set Venn diagrams were constructed using the ggVennDiagram package to visualize shared and unique variants across tools
command 
https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/venn_analysis/venn_plot_abnormal.R
https://github.com/Wasiq54/Chelonia-mydas-variant-benchmark/blob/main/venn_analysis/venn_plot_abnormal.R




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








### Venn Diagram Visualization of Variant Callers

To assess concordance among the five variant callers (DeepVariant, GATK, FreeBayes, VarScan, and BCFtools), SNP positions were extracted from normalized and sorted VCF files using **bcftools** and compared visually through 5-way Venn diagrams.  

The pipeline involved:
1. Normalization and indexing of VCFs using `bcftools norm`, `bcftools sort`, and `bcftools index`.
2. Extraction of SNP coordinates (`CHROM:POS`) from each caller using `bcftools query`.
3. Visualization of overlapping SNPs using the **ggVennDiagram** (R v1.3.2) package.

Scripts for complete reproducibility are available in the `venn_analysis/` directory:
- `venn_pipeline.sh` — generates normalized SNP coordinate files.  
- `venn_plot_normal.R` — visualizes overlap for the normal dataset.  
- `venn_plot_abnormal.R` — visualizes overlap for the abnormal dataset.  

The resulting figures (`FiveTool_Venn_Normal.png` and `FiveTool_Venn_Abnormal.png`) illustrate shared and unique variant calls among the tools for each dataset.


### Discordance Analysis (Missed and Unique Variants)

To evaluate discordance among variant callers, a Python-based script was developed to identify **missed** and **unique** SNPs across the five tools (DeepVariant, GATK, FreeBayes, VarScan, and BCFtools).  
The analysis used the SNP coordinate text files generated during the Venn diagram pipeline (`*_SNPs.txt`) for both normal and abnormal datasets.

**Workflow Summary**
1. Each SNP coordinate (`CHROM:POS`) was loaded from the tool-specific SNP files.
2. A combined union of all SNPs was generated.
3. For each caller:
   - **Missed SNPs** were defined as variants detected by all other tools but absent in the current one.  
   - **Unique SNPs** were defined as variants detected exclusively by that tool.
4. Counts of missed and unique SNPs were summarized per caller.

The script (`discordance_analysis.py`) is located in the `venn_analysis/` directory and can be executed as:
```bash
python venn_analysis/discordance_analysis.py






## Consensus Truth Set & Benchmarking

High-confidence variant truth sets were generated by intersecting five callers (DeepVariant, GATK, FreeBayes, VarScan, BCFtools) for normal (CH-NORMS1) and abnormal (Ab-NormS2) datasets.  

**Steps:**
1. Sort, index, and normalize VCFs using bcftools.
2. Create intersections (3+, 4+, 5+) with bcftools isec.
3. Filter caller VCFs to consensus loci using bcftools view -R.
4. Generate RTG reference SDF:
   rtg format -o reference.sdf <reference.fasta>
5. Benchmark each caller using RTG vcfeval:
   rtg vcfeval -b <truth.vcf.gz> -c <caller.vcf.gz> -t reference.sdf -o <out_dir> --vcf-score-field QUAL

All full commands are available in:

scripts/commands_consensus_truthset.txt
