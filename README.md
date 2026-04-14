# GOA2021_mifish_mb

this repo includes updated data processing for 2021 GOA pcod survey metabarcoding mifish data

Part 1: Initial data processing - location: sedna (161.55.52.157)
A) Prepare raw sequence data for dadasnake (https://github.com/a-h-b/dadasnake)
- upload raw sequence data to sedna and create sample sheet .tsv (see "sample_names_for_dadasnake.Rmd" for an example using different data)
- also see: https://docs.google.com/document/d/1c1M6ogmohUGKUzT1WgDech0DI_4ZvXzaO0uVcYC_TdQ/edit?usp=sharing

B) Run cutadapt and dada2 using dadasnake
- set parameters within config.mifish.2021GOA.yaml and run dadasnake.sh on sedna  
- default setting were used except for (1) trunc_length: 100/100; (2) min_overlap: 10; (3) pool: pseudo; (4) target_min_length: 162; (5) target_max_length: 186 

C) Run blastn on ASVs (post/filtered.seqs.fasta) using MIDORI2 database as reference 

conda activate cutadptenv
blastn -query data/dadasnake_20241106/filtered.seqs.fasta \
       -db /Users/kimberly.ledger/Documents/metaprobes_BeringSea2024/MIDORI2_UNIQ_NUC_GB267_srRNA_BLAST_db/MIDORI2_UNIQ_NUC_GB267_srRNA_BLAST_db \
       -out data/MIDORI2_blastnresults \
       -perc_identity 96 -qcov_hsp_perc 100 \
       -outfmt "6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore sscinames staxids" \
       -num_threads 10


Part 2: Taxonomic assignment using blast output
A) run "1_MiFish_blastn_taxonomy.Rmd"
- this includes blastn of custom gadid and salmon referece database 

B) run LULU to id erroneous ASVs 


Part 3: Data decontamination 
A) run "3_MiFish_curate_and_decon". the decontamination steps included are:
step 1: tax assignment based on lulu (except for when original ID does better than the parent)
step 2: remove ASVs that don't get a fish taxonomic assignment 
step 3: tag-jumping - subtract the proportion of reads that jumped into the positive control samples from each environmental sample 
step 4: subtract reads based on low-levels of contamination in the PCR and extraction negative controls 
step 5: remove low read depth samples based on ASV accumulation curve (1000 reads) 


Part 4: Format taxon table in "4_taxon_summary.Rmd"

