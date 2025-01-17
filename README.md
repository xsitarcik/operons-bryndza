# Evaluation of bacterial consortia associated with dairy fermentation by ribosomal RNA (rrn) operon metabarcoding strategy using MinION device

This repository contains AEROS-DB (Almost Entire Ribosomal Operon Sequences DataBase), created from contigs of representative genomes downloaded from [proGenomes v2.1 database](https://progenomes.embl.de/download.cgi), and other supplementary material, which is used in our to-be submitted paper:     
Plany M., Sitarcik J., Pavlovic J., Budis J., Korenova J., Kuchta T., Pangallo D.: Evaluation of bacterial consortia associated with dairy fermentation by ribosomal RNA (rrn) operon metabarcoding strategy using MinION device.

## AEROS-DB
AEROS-DB contains 7192 operon sequences, where each operon sequence is associated with an unique organism. The database was constructed with the use of [BioPython](https://biopython.org/docs/1.75/api/index.html) package.
Database consists of two files:

 - [aeros-db.fa.gz](https://github.com/xsitarcik/operons-bryndza/blob/main/aeros-db.fa.gz "aeros-db.fa.gz") - gzipped file of FASTA sequences representing rrn operon sequences with the NCBI header 
 - [aeros-db.json.gz](https://github.com/xsitarcik/operons-bryndza/blob/main/aeros-db.json.gz "aeros-db.json.gz") - JSON file storing mapping between headers and NCBI lineage for fast and consistent taxonomy retrieval

If you use AEROS-DB, or any other scripts in this repository, please, consider citing our paper and papers mentioned below in References, if appropriate.

### AEROS-DB usage
AEROS-DB serves two purposes. At first, to map reads to the database. Then, to assign taxonomy using mapping results. Start using AEROS-DB by cloning this repository, i.e.:

    git clone git@github.com:xsitarcik/AEROS-DB.git

#### Mapping to AEROS-DB
[Minimap2](https://github.com/lh3/minimap2) was tested for mapping reads to the database. For ONT nanopore reads, for example when having two sets of reads obtained from two replicates, named as `replicate01.fastq` and  `replicate02.fastq`, the mapping to the database can be run as follows (if running from the cloned directory):

    minimap2 -t 32 -cx map-ont aeros-db.fa.gz replicate01.fastq -z 70 \
    > replicate01.paf
    minimap2 -t 32 -cx map-ont aeros-db.fa.gz replicate02.fastq -z 70 \
    > replicate02.paf

#### Assigning taxonomy
 1. Create conda environment with requirements: `conda env create -f conda_req_usage.yaml`
 2. Activate conda: `conda activate aeros-db`
 3. Run assign taxonomy for example: `python assign_taxa.py -p replicate01.paf replicate02.paf -r genus -o genus_results.csv`

This creates `genus_results.csv` file as given by `-o` argument (omitting `-o` argument will output results to stdout). This file records the composition in percentage and of the `genus` rank, as given by `-r` argument. Taxonomy can be assigned on various ranks, recommended to use are: `species`, `genus`, `family`, `order`.
Outputted file is .csv file with the header row denoting the paf filenames, while the first column denotes ranks. For illustration:

    ,replicate01,replicate02
    Lactococcus,60,62.5
    Lacticaseibacillus,20,22.5
    Streptococcus,20,15

**All information about parameters of the assign_taxa.py script:**

    usage: assign_taxa.py [-h] -p PAFS_LIST [PAFS_LIST ...] -r RANK        
				    [-o OUT_PATH] [-m MIN_LENGTH] [-s MIN_SCORE]
    optional arguments:
      -h, --help            show this help message and exit
      -p PAFS_LIST [PAFS_LIST ...], --pafs_list PAFS_LIST [PAFS_LIST ...]
                            path to PAF file(s)
      -r RANK, --rank RANK  lineage rank used for classification
      -o OUT_PATH, --output OUT_PATH
                            path where to store output
      -m MIN_LENGTH, --min_length MIN_LENGTH
                            Minimum aligned block length to use
      -s MIN_SCORE, --min_score MIN_SCORE
                            Percentage of the best aligned reads to use

## Scripts - AEROS-DB construction
[Scripts folder](https://github.com/xsitarcik/AEROS-DB/tree/main/scripts "scripts") contains 2 scripts:     
 - [db_build.py](https://github.com/xsitarcik/AEROS-DB/blob/main/scripts/db_build.py) - used to parse operon sequences from database of contigs     
 - [db_annot.py](https://github.com/xsitarcik/AEROS-DB/blob/main/scripts/db_annot.py) - used to annotate operon sequences with NCBI lineage taxonomy

At first, we used `wget` to download contig databse from Progenomes:    
`wget https://progenomes.embl.de/data/repGenomes/freeze12.contigs.representatives.fasta.gz`    
Then we used `db_build.py` and `db_annot.py` to construct AEROS-DB. 

## References
Cock, P.A., Antao, T., Chang, J.T., Chapman, B.A., Cox, C.J., Dalke, A., Friedberg, I., Hamelryck T., Kauff, F., Wilczynski, B., de Hoon, M.J.L. (2009). Biopython: freely available Python tools for computational molecular biology and bioinformatics. _Bioinformatics_, 25, 1422-1423. doi:10.1093/bioinformatics/btp163

Li, H. (2018). Minimap2: pairwise alignment for nucleotide sequences. _Bioinformatics_, 34, 3094-3100. doi:10.1093/bioinformatics/bty191

Mende, D., Letunic, I., Maistrenko, O., Schmidt, T., Milanese, A., Paoli, L., Hernández-Plaza, A., Orakov, A., Forslund, S., Sunagawa, S., Zeller, G., Huerta-Cepas, J., Coelho, L.P., Bork, P. (2019). proGenomes2: an improved database for accurate and consistent habitat, taxonomic and functional annotations of prokaryotic genomes. _Nucleic Acids Research_, 48, D621–D625. doi:10.1093/nar/gkz1002

## License

[Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-nc-sa/4.0/)
