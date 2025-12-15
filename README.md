# Evaluating DeepVariant and Conventional SNP Callers in the *Chelonia mydas* Genome Using an Assembly-Derived Truth Set

## 📌 Project Overview

This repository accompanies the research study **“Evaluating DeepVariant and Conventional SNP Callers in the *Chelonia mydas* Genome Using an Assembly-Derived Truth Set.”**
The project presents a fully reproducible benchmarking framework for single-nucleotide polymorphism (SNP) detection in a non-model marine vertebrate using short-read whole-genome sequencing data.

The workflow integrates read preprocessing, de novo genome assembly, assembly quality evaluation, reference-based alignment, variant calling using five tools, assembly-derived truth-set construction and benchmarking with RTG vcfeval.

---

## 🧬 Study Objectives

* Construct an **assembly-derived SNP truth set** for *Chelonia mydas*
* Benchmark **DeepVariant** against conventional SNP callers (GATK, FreeBayes, VarScan, BCFtools)
* Quantify performance using **precision, recall, F1-score**, and threshold optimization
* Provide a **reproducible pipeline** for variant benchmarking in non-model organisms

---
<img width="2600" height="1200" alt="Consensus Truth Set (Common Variants )" src="https://github.com/user-attachments/assets/13059052-cb76-41bd-8a9e-92088b91cd2d" />
-----------------------------------------------------------------------------------------------------------------------------------------------------------
<img width="2600" height="1200" alt="Consensus Truth Set (Common Variants ) (1)" src="https://github.com/user-attachments/assets/bf0e44a1-65d4-436a-89b7-69faa7e6834a" />

## 📂 Repository Structure

```
Cmydas-Assembly-Derived-TruthSet/
├── README.md                     # Project overview (this file)
├── METHODS.md                    # Detailed methods and reproducible workflow
├── scripts/                      # All shell scripts and command files
├── Results/                      # QUAST, BUSCO, ANI, variant calling & benchmarking outputs
```

---

## 🔁 Reproducible Workflow Summary

1. **Quality Control** – FastQC, MultiQC
2. **Read Trimming** – fastp
3. **De novo Assembly** – ABySS, MEGAHIT, SOAPdenovo2
4. **Assembly Evaluation** – QUAST, BUSCO, ANI (skani)
5. **Reference Alignment** – BWA-MEM, Bowtie2, Minimap2
6. **Variant Calling** – DeepVariant, GATK, FreeBayes, VarScan, BCFtools
7. **Variant Filtering** – AD, VAF, PASS criteria
8. **Truth Set Generation** – Assembly-to-reference alignment
9. **Benchmarking** – RTG vcfeval (precision, recall, F1-score)

👉 **Full methodological details, exact commands, and parameters are provided in [`METHODS.md`](METHODS.md).**

---

## 📊 Key Findings (Summary)

* **ABySS** produced the most contiguous and biologically representative short-read assembly
* **DeepVariant** achieved the highest F1-score without threshold tuning
* **BCFtools** showed the strongest precision at optimized thresholds
* High SNP concordance was observed among DeepVariant, GATK, BCFtools, and VarScan
* Assembly-derived truth sets enable robust benchmarking in species lacking validated variant resources

---

## 📥 Data Availability

* **Raw sequencing data (Illumina NovaSeq, CH-NORMS1):**
  European Nucleotide Archive (ENA): **PRJEB104518**
  [https://www.ebi.ac.uk/ena/browser/view/PRJEB104518](https://www.ebi.ac.uk/ena/browser/view/PRJEB104518)

* **Reference genome:**
  *Chelonia mydas* rCheMyd1.pri.v2 (GCA_015237465.2), NCBI RefSeq (VGP)

* **Processed data and outputs:**
  Zenodo archive (assemblies, truth set, filtered VCFs):
  **DOI: 10.5281/zenodo.17741557**

---

## 🛠 Software and Environments

All analyses were performed using Conda and/or Docker environments to ensure reproducibility.
A complete list of tools and versions is available in **[`METHODS.md`](METHODS.md)**.

---

## 📖 How to Use This Repository

1. Review the complete workflow in **[`METHODS.md`](METHODS.md)**
2. Execute scripts in the order described under `scripts/`
3. Compare your outputs with those provided in `Results/`
4. Reuse or adapt the pipeline for other non-model organisms

---

## 📌 Citation

If you use this pipeline, data, or scripts, please cite:

> Aslam, F. *et al.* (2025). **Evaluating DeepVariant and Conventional SNP Callers in the *Chelonia mydas* Genome Using an Assembly-Derived Truth Set.** *(Manuscript in preparation / under review).*

Zenodo DOI: **10.5281/zenodo.17741557**

---

## 📬 Contact

For questions, reproducibility issues, or collaboration inquiries:

**Fahad Aslam**
PhD Candidate, Computer Science
Institute of Oceanography and Environment (INOS)
Universiti Malaysia Terengganu

---

⭐ *If this repository helps your research, please consider starring it on GitHub.*

