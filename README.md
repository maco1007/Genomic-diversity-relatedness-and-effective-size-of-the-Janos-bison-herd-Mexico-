# Genomic-diversity-relatedness-and-effective-size-of-the-Janos-bison-herd-Mexico-
Code and data guide for the manuscript "Genomic diversity, relatedness and effective size of the Janos bison herd (Mexico): a baseline for the Cuatrociénegas translocation" (submitted to Molecular Ecology).
This repository holds the analysis scripts. The genotype files are too large for GitHub; raw reads are in the NCBI SRA.

Resource	Location
Scripts (this repository)	scripts/
Genotype files (VCF, PLINK) 
Raw sequencing reads	NCBI SRA, BioProject PRJNA1543005 (embargoed until publication)
Study in brief

Hair-follicle DNA from 96 American bison (Bison bison) of the Janos Biosphere Reserve (Chihuahua) was genotyped by RAD-seq/GBS (one 96-well plate, sample IDs 384D_A01–384D_H12). The animals belong to the herd that was the source of the bison later translocated to Cuatrociénegas (Coahuila); 30 sampled animals are the ones later relocated ("4C"). We estimated genomic diversity, inbreeding, population structure, relatedness and linkage-disequilibrium effective size (Ne), and compared three independent pipelines as a robustness check.

Pipelines and final datasets
Pipeline	Aligner / assembler	Individuals (all → analysed)	SNPs analysed
TAMU3 (main)	BWA-MEM → TAMU_Bison_Bison_3.0 (GCA_030254855.1), Stacks v2.65	96 → 77 (75 without duplicates)	54,305 (7,033 after LD pruning, 77 ind.)
UMD1.0	BWA-MEM → Bison_UMD1.0 (GCA_000754665.1), Stacks v2.65	95 → 76 (74 without duplicates)	5,846
de novo	Stacks denovo_map.pl (-m 3 -M 2 -n 2)	95 → 72 (70 without duplicates)	1,798 (888 after LD pruning)

Filtering was done with PLINK v1.9 (--geno, --mind 0.20, --maf, --indep-pairwise). Two pairs of near-identical samples (384D_B01/384D_D06 and 384D_G10/384D_H11) were detected; analyses are reported with and without them.

Ne was estimated from linkage disequilibrium (Waples 2006) on unpruned markers, with a leave-one-out jackknife over individuals; for TAMU3 it was computed on five thinned replicates of 4,000 SNPs. Ne is reported with all individuals and with duplicates removed:

Pipeline	Ne, all individuals	Ne, duplicates removed
TAMU3	42.88 (n = 77)	47.24 (n = 75)
UMD1.0	68.84 (n = 76)	77.19 (n = 74)
de novo	180.47 (n = 72)	188.06 (n = 70)
Genotype files (Dryad)

Everything is also listed with md5 checksums in MD5SUMS.txt inside each archive. "Ind." is the number of individuals; PLINK sets are .bed/.bim/.fam triplets.

genotipos_dryad/ (TAMU3 pipeline)
Folder	Content	SNPs	Ind.
TAMU3_prefiltro_96ind/	populations_96ind.snps.vcf — Stacks output before individual filtering	33,912	96
TAMU3_54305_77ind/	populations.snps.vcf — final filtered VCF; final.ped/.map — same data in PLINK text format	54,305	77
TAMU3_Ne/	t3_con_dup (with duplicates), t3_sin_dup (duplicates removed) — input for Ne	54,305	77 / 75
TAMU3_ADMIXTURE/	CON_podado (with duplicates), SIN_podado — LD-pruned input for ADMIXTURE	7,033 / 6,880	77 / 75
TAMU3_PCA/	CON_pca.*, SIN_pca.* — PLINK eigenvalues/eigenvectors (PC1 = 5.62 %, PC2 = 3.97 % in CON_pca)	—	77 / 75
TAMU3_3089_genes/	bison_est_podado (PLINK) and snps_filtrados_tamu3.vcf/.log — SNP set used for the gene-level analyses	3,089 (PLINK set)	77
TAMU3_completo/	bison_completo, bison_completo_hwe, bison_completo_nodup — complete-case panel used by an earlier figure script (bison_completo_nodup: 5,598 SNPs × 75 ind.); duplicados_fuera.txt lists the removed duplicates	5,598 (nodup)	75 (nodup)
genotipos_dryad_umd1_denovo/ (UMD1.0 and de novo pipelines)
Folder	Content	SNPs	Ind.
UMD1_95ind/	populations.snps.vcf (Stacks output) and bison_binary (PLINK, same markers)	5,846	95
UMD1_final/	matriz_umd1_final_76 and matriz_umd1_final_74 (duplicates removed)	5,846	76 / 74
denovo/	populations.snps.vcf (unfiltered Stacks catalogue), plink_denovo_g20 (genotype filter), denovo_g20_mind20 (individual filter), denovo_pruned (LD-pruned)	138,318 (VCF) / 1,798 / 1,798 / 888	95 / 95 / 72 / 72
Ne_raw/	.raw genotype matrices used by the LD-Ne scripts: matriz_umd1_final_76/74, denovo_sin_podar (72), denovo_sin_podar_70, denovo_pruned_70	—	as in file name

Stacks v2.65, PLINK v1.9.0-b.8, ADMIXTURE (Alexander et al. 2009), BWA-MEM, bedtools, Liftoff, R (ggplot2, clusterProfiler; g:Profiler for GO), Python (NumPy, pandas).

Reproducing the analyses
Download the reads from SRA (PRJNA1543005) and the reference genomes (TAMU_Bison_Bison_3.0, Bison_UMD1.0). To start from genotypes, download the Dryad archives instead and skip to step 3.
Run alignment and Stacks (scripts/.../run_radtags*, alineamiento_bwa*, run_gstacks*).
Run filtering, PCA, ADMIXTURE and Ne on the PLINK files listed above.

Scripts were run on a SLURM cluster (heavy jobs with sbatch) and contain absolute cluster paths (/scratch/marco/bison, /scratch/jaahzel/...) that must be edited before rerunning. The gnomAD v4.1 constraint metrics and the sample-to-population map (popmap) are inputs that are not redistributed here.

To check a download: cd <archive folder> && md5sum -c MD5SUMS.txt.

Data notes
Sample IDs match the manuscript (e.g., 384D_B11). Sample metadata (target population, sex, age, DNA quality) are in the Dryad Excel sheet; they contain animal identifiers only, no personal data.
The identity of the near-duplicate sample pairs should be verified before using these data for pedigree or management decisions.
Ne_raw/denovo_sin_podar_71.raw is an intermediate file and was not used for the reported estimates.
Citation

Licence

Data: CC0 (Dryad). Code: [choose and add a LICENSE file, e.g., MIT].

Contact

Marco Solano — Instituto de Ecología, UNAM — msolanodelacruz@iecologia.unam.mx
