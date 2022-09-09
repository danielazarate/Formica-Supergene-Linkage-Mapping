## Polyergus -- Linkage Mapping Project 
## Goal: To assess the age and formation of the social supergene present in Formica ant species 
## through constructing linkage maps for species of Iberoformica and Polyergus. 
# Did the social supergene evolve from the fusion of multiple pieces? 

# Original McGuire (chp 3 MS thesis) filters
# Filtered genotypes using VCFtools (v0.1.16)
# --minDP 7 : for read depth 
# --max-missing 0.8 :missing data 
# --maf 0.15 : and sites with a minor allele frequency < 0.15 
# Individuals with more than 20% missing data were removed from analysis (N = 13).
# Additionally, excessively homozygous and heterozygous SNPs were observed (--hardy) and subsequently removed (--exclude-positions).
# Resulted in 2928 retained loci in 83 workers. 

# Polyergus.vcf 
# Obtained from J.P, from McGuire thesis work, unfiltered VCF
# original file size: 3.0 GB
# Family of 95 individuals 

vcftools --vcf Polyergus.vcf --minDP 7 --max-missing 0.8 --out polyergus.A --recode 

# After filtering, kept 95 / 95 individuals 
# After filtering, kept 97.037 out of a possible 2.105.771 Sites
# run time: 72 sec
# outputs a .recode.vcf and .log 
# size: 177 M 

# Next, all individuals need to have the same mom and dad 
# use plink to run pca and calculate eigenvalues & eigenvectors 
module load plink 
plink --vcf polyergus.A.recode.vcf --pca --aec --out polyergus.A
# --aec = allow extra chromosomes 
# --pca = principal component analysis
# --out = specifies output name 
# Eigenvectors written to polyergus.A.eigenvec & eigenvalues written to polyergus.A.eigenval 
# Eigenval file: 10 lines 
# Eigenvec file: 96 lines of PC 1-10 
# run time: < 1 min. 

scp dazarate@togepi.ucsd.edu:/home/dazarate/ant_link/polyergus.A.eigenvec ~/Desktop/ANT_POSTDOC

# copy and paste the output (4 columns) into excel
# If data does not fall into discreet columns, then hit the Data Tab \
# and select the "Text to Column" option, select "Delimited" in wizard and hit "Next" to "Finish" 
# Select columns $2 and $3 and create scatterplot of those PCA 1 and PCA 2 
# if individuals share paternity and maternity, they will cluster together. Any outliers must be removed.
# some individuals will have a lot of missing data, these individuals will cluster out and also must be removed. 

# Individuals that cluster out (n = 8) 
# SQPC7W109, SQPC7W120, SQPC7W122, SQPC7W125, SQPC7W126, SQPC7W127, SQPC7W128, SQPC7W133 
# Leaving 87 individuals 

# check out the statistics for missing data for each individual 
vcftools --vcf polyergus.A.recode.vcf --missing-indv --out polyergus.A
# Generates a file reporting the missingness on a per-individual basis. The file has the suffix ".imiss".
# some warnings: Warning: Expected at least 2 parts in INFO entry: ....

# what is the cut of for missing data? ASK JP >20 % 
# Individuals removed for having >20% mising data: (n = 4)
# SQPC7W26(26%), SQPC7W74 (57%), SQPC7W80 (44%), SQPC7W84 (34%), 
# 12 individuals with moderate to high levels of missing data 

# remove 12 individuls. 
vcftools --vcf Polyergus.vcf --remove-indv SQPC7W109.all.bam --remove-indv SQPC7W120.all.bam --remove-indv SQPC7W122.all.bam --remove-indv SQPC7W122.all.bam --remove-indv SQPC7W125.all.bam --remove-indv SQPC7W126.all.bam --remove-indv SQPC7W127.all.bam --remove-indv SQPC7W128.all.bam --remove-indv SQPC7W133.all.bam --remove-indv SQPC7W26.all.bam --remove-indv SQPC7W74.all.bam --remove-indv SQPC7W80.all.bam --remove-indv SQPC7W84.all.bam --out polyergus.clean --recode 
## ASK JP do we remove from the recode or from the OG? I believe we remove from OG VCF. 
# --remove-indv = remove this specified individual 
# OUTPUT: 
# Excluding individuals in 'exclude' list
# After filtering, kept 83 out of 95 Individuals
# Outputting VCF file...
# After filtering, kept 2.105.771 out of a possible 2.105.771 Sites
# Run Time = 308.00 seconds
# Use : --remove <filename> if you want to provide a list of samples instead of putting them all on the command line 
# 2.5 G output - polyergus.clean.recode.vcf and .log 

# filters again 
vcftools --vcf polyergus.clean.recode.vcf --minDP 7 --max-missing 0.8 --out polyergus.filter1 --recode 
# After filtering, kept 83 out of 83 Individuals
# Outputting VCF file...
# After filtering, kept 103.440 out of a possible 2.105.771 Sites
# Run Time = 65.00 seconds
# 167 M output files: polyergus.filter1.recode.vcf and .log 

vcftools --vcf polyergus.filter1.recode.vcf --out polyergus.filter1.2 --maf 0.1 --recode 
# --mac : minor allele count only use for small families 
# --maf of 0.15 or .1 is fine. 
# MUST specify entire file name for input, including the .recode.vcf extension 
# Some INFO warnings 
# After filtering, kept 83 out of 83 Individuals
# Outputting VCF file...
# After filtering, kept 6.293 out of a possible 103.440 Sites
# Run Time = 3.00 seconds

vcftools --vcf polyergus.filter1.2.recode.vcf  --hardy --out polyergus
# --hardy =  Reports a p-value for each site from a Hardy-Weinberg Equilibrium test \
#  (as defined by Wigginton, Cutler and Abecasis (2005)). The resulting file \
#  (with suffix ".hwe") also contains the Observed numbers of Homozygotes and Heterozygotes\
#  and the corresponding Expected numbers under HWE.
# .hwe file has 8 columns: CHR, POS, OBS(HOM1/HET/HOM2), E(HOM1/HET/HOM2), ChiSq_HWE, P_HWE, P_HET_DEFICIT, P_HET_EXCESS 
# Is Hom1 the refernece and Hom2 the alternate? 

# Some INFO warnings 
# After filtering, kept 83 out of 83 Individuals
# Outputting HWE statistics (but only for biallelic loci)
# HWE: Only using biallelic SNPs.
# After filtering, kept 6.293 out of a possible 6.293 Sites
# Run Time = 0.00 seconds
# output file : polyergus.hwe (557K) and a .log 

# create a list of positions for all those high-quality SNPs that we want to use 
cut -f 1-3 polyergus.hwe | tr '/' '\t' | awk '$4<0.8*($3+$4+$5)' | awk '($3*$5) < ($3+$5)' | cut -f 1-2 > polyergus.good.snps.txt
# 3.778 line count 
# two columns 

# keep good positions with provided file 
# run on filtered file 
vcftools --vcf polyergus.filter1.2.recode.vcf --positions polyergus.good.snps.txt --recode --out polygood
# Some INFO warnings...
# After filtering, kept 83 out of 83 Individuals
# Outputting VCF file...
# After filtering, kept 3.781 out of a possible 6.293 Sites
# Run Time = 1.00 seconds
# OUTPUT: polygood.recode.vcf (6.0 M) and .log 

# recode to a 012 format 
vcftools --vcf polygood.recode.vcf  --012 --out polygood

# This option outputs the genotypes as a large matrix. Three files are produced. \
# The first, with suffix ".012", contains the genotypes of each individual on a separate line.\
# Genotypes are represented as 0, 1 and 2, where the number represent that number of \
# non-reference alleles. Missing genotypes are represented by -1. The second file,\
# with suffix ".012.indv" details the individuals included in the main file. \
# The third file, with suffix ".012.pos" details the site locations included in the main file.

# After filtering, kept 83 out of 83 Individuals
# Writing 012 matrix files ...    012: Only outputting biallelic loci.
# After filtering, kept 3.781 out of a possible 3.781 Sites
# Run Time = 0.00 seconds
# OUTPUT: polygood.012. (624K), .012.indv (1.4K), .pos (70K). 

# add the file names to a new column in 
# add all columns except first column in .012 file because it has a weird default numerical order column 
paste polygood.012.indv polygood.012 > polygood.012.matrix 

# download onto local, open in excel
# only possible with small files 
# deleted second column which was just numerical order (1- )
# transpose file so individuals are column headers 
# insert two cols into excel - SNP name and position 
# text-to-column 
# replace -1s with U (Find Entire Cell Only Option)
# Change Zeros to A and ones to B and change twos to A 

# Duplicate entire data set and paste on bottom 
# Change A to B and B to A but use a Q mid-step to avoid overwriting 


population_type  DH
population_name   nylO
distance_function   kosambi
cut_off_p_value   0.00005
no_map_dist 30
no_map_size   1
missing_threshold   0.2
estimation_before_clustering   no
detect_bad_data   yes
objective_function   ML
number_of_loci   13028
number_of_individual   19

# parameters to tweak 
# no_map_dist 
# estimation_before_clustering -- change to yes? *read up* 

# blank space between header and genotype data 
# add a third column in excel using concatenate that pastes scaffold and position like so scaffold_position

# upload to cluster


