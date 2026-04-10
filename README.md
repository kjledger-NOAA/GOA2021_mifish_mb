# GOA2021_mifish_mb

this repo includes updated data processing for 2021 GOA pcod survey metabarcoding mifish data

Part 1: Initial data processing - location: sedna (161.55.52.157)
A) Prepare raw sequence data for dadasnake (https://github.com/a-h-b/dadasnake)
- upload raw sequence data to sedna and create sample sheet .tsv (see "sample_names_for_dadasnake.Rmd" for an example using different data)
- also see: https://docs.google.com/document/d/1c1M6ogmohUGKUzT1WgDech0DI_4ZvXzaO0uVcYC_TdQ/edit?usp=sharing
B) Run cutadapt and dada2 using dadasnake
- set parameters within config.mifish.2021GOA.yaml and run dadasnake.sh on sedna  
- default setting were used except for (1) trunc_length: 100/100; (2) min_overlap: 10; (3) pool: pseudo; (4) target_min_length: 162; (5) target_max_length: 186 
C) Run blastn on ASVs (post/filtered.seqs.fasta) using ncbi nt database as reference 
- update input/output file paths within blastn.sh, and make a folder for blast outputs (home/kledger/blast/GOA2021_mifish_20241106)
- blastn parameters: -perc_identity 96 -qcov_hsp_perc 98

(updated dec 30, 2024: also ran blast againsed UPDATED custom salmon and gadid reference databases)
blastn -query dadasnake_20241106/filtered.seqs.fasta -db /genetics/edna/custom_dbs/gadid_mifish_db_20241227 -out gadidae_results.txt -perc_identity 96 -qcov_hsp_perc 98 -num_threads 10 -outfmt '6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore sscinames staxids'

blastn -query dadasnake_20241106/filtered.seqs.fasta -db /genetics/edna/custom_dbs/salmon_mifish_db_20241227 -out oncorhynchus_results.txt -perc_identity 96 -qcov_hsp_perc 96 -num_threads 10 -outfmt '6 qseqid sseqid pident length mismatch gapopen qstart qend sstart send evalue bitscore sscinames staxids'


Part 2: Taxonomic assignment using blast output - location: eDNA VM (161.55.97.134)
A) run "part2_taxonomic_assignment_blastn.Rmd"

note: looks pretty good but could likely benefit from additional filtering and grouping of taxa ids made up of distinct species 
working document on tax assignment thoughts: https://docs.google.com/document/d/1IwNT3KOPqcSyNkB1Q4D5HayEa8FHgSdr--XiVQwj-N0/edit?usp=sharing

Part 3: Data decontamination - location: eDNA VM (161.55.97.134)
A) run "part3_decontamination.Rmd". the decontamination steps included are: 
step 1: tag-jumping - subtract the proportion of reads that jumped into the positive control samples from each environmental sample 
step 2: remove ASVs that don't get a fish taxonomic assignment 
step 3: remove any ASVs that don't show up in field samples 
step 4: remove low read depth samples based on ASV accumulation curve  
step 5: check dissimilarity across PCR replicates and remove any replicates with high dissimilarity or only one PCR replicate remaining
step 6: remove the samples from unknown sampling locations 
step 7: remove ASVs with no reads in remaining PCR replicates

note: depending on how we use the data, removing dissimilar/singular PCR replicates may not be the way to go. 

Part 4: Data exploration
- ongoing






