# Anvio
Using Anvio on the Bigelow Server

**Notes - This SOP assumes that you have already "fixed" your fasta headers, and mapped your reads to the fixed assembled file. If not, see the metagenome assembly SOP and come back to here. 

First, lets load Anvio, this module is a bit different tha the others...

```module use /mnt/scgc_nfs/opt/modulefiles/common```
```module load anaconda3```
```source activate anvio3```

I like to work in a fresh directory, but you dont have to do what I like to do

```mkdir ANVIO```
```cd ANVIO```

#Generate contigs database for contigs and mapped reads 
```anvi-gen-contigs-database -f /mnt/storage/orcutt/Jackie_bioinformatics/AM_metageome/SC_meta_AllAssembled/All_sc_metagenomes/fixscaffolds.fa -o AM_ALLmeta_Norm_sccare.db -n AM_ALLmeta_Norm_sccare```

#now lets run a hidden markov model 
```anvi-run-hmms -c AM_ALLmeta_Norm_sccare.db```

#lets check out the stats 
```anvi-display-contigs-stats AM_ALLmeta_Norm_sccare.db```

*** I can't figure out how to run the NCBI cogs ,there is a permissions issue somewhere here, so I am skipping that part of the normal protocol even though I would LOVE to have some COGs...

#initialize our sorted bam files, here I am working with 4 metagenomes that I Assembled together using SPAdes
```anvi-init-bam /mnt/storage/orcutt/Jackie_bioinformatics/AM_metageome/SC_meta_AllAssembled/Bowtie/68B1R1scaffolds.bam -o mapped68B1R1normscALL.bam```
```anvi-init-bam /mnt/storage/orcutt/Jackie_bioinformatics/AM_metageome/SC_meta_AllAssembled/Bowtie/69A4R1scaffolds.bam -o mapped69A4R1normscALL.bam```
```anvi-init-bam /mnt/storage/orcutt/Jackie_bioinformatics/AM_metageome/SC_meta_AllAssembled/Bowtie/69A9R2scaffolds.bam -o mapped69A9R2normscALL.bam```
```anvi-init-bam /mnt/storage/orcutt/Jackie_bioinformatics/AM_metageome/SC_meta_AllAssembled/Bowtie/70C3R1scaffolds.bam -o mapped70C3R1normscALL.bam```

#Lets make an ANVIO profile 

```anvi-profile -i mapped68B1R1normscALL.bam -c AM_ALLmeta_Norm_sccare.db --num-threads 8  --output-dir ANVIO_68B1R1_profile --sample-name AM_68B1R1 ```
```anvi-profile -i mapped69A4R1normscALL.bam -c AM_ALLmeta_Norm_sccare.db --num-threads 8  --output-dir ANVIO_69A4R1_profile --sample-name AM_69A4R1```
```anvi-profile -i mapped69A9R2normscALL.bam -c AM_ALLmeta_Norm_sccare.db --num-threads 8  --output-dir ANVIO_69A9R2_profile --sample-name AM_69A9R2```
```anvi-profile -i mapped70C3R1normscALL.bam -c AM_ALLmeta_Norm_sccare.db --num-threads 8  --output-dir ANVIO_70C3R1_profile --sample-name AM_70C3R1```

#merge the profiles 
```anvi-merge */PROFILE.db -o SAMPLES-MERGED -c AM_ALLmeta_Norm_sccare.db```


**** CANNOT USE ANVIO INTERACTIVE OFF OF THE BIGEWLOW SERVER, I know ( sad), so here you should download files to your own computer ( which is hopefully linux or your life will be slightly harder) and run the interactive from there. 
#Welp, that was easy 
```anvi-interactive -p SAMPLES-MERGED/PROFILE.db -c AM_ALLmeta_Norm_sccare.db --server-only -P 8080```

