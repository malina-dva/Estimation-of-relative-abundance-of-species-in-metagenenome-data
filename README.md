# Estimation-of-relative-abundance-of-species-in-metagenenome-data
Complete pipeline for estimating the relative abundance of microbial species using kraken2 and bracken 
## 1. Install the Conda Environment

```{bash, eval=FALSE}
conda config --add channels bioconda  
conda create --yes -n kraken kraken2 bracken  
conda activate kraken 
```
## 2. Obtain kraken DB capped at 8GB
Contains archaea, bacteria, viral, plasmid and human 
```{bash, eval=FALSE}
mkdir krakenDB  
cd krakenDB  
wget https://genome-idx.s3.amazonaws.com/kraken/k2_standard_08gb_20240112.tar.gz    
tar -xvzf k2_standard_08gb_20240112.tar.gz  
!Attention! Takes a while to run...
```
## 3. Run kraken for sample1 
Only run it if you have the computational resources! Even if you do, loeading the database may take up to 30 min ... so be patient    
```{bash, eval=FALSE}
kraken2 --use-names --threads 4 --db /home/malina/krakenDB/  --report sample1.report --paired tara_reads_R1.5000.fastq tara_reads_R2.5000.fastq > sample1.kraken
```
Loading database information... done.  
5000 sequences (1.26 Mbp) processed in 0.480s (624.8 Kseq/m, 157.44 Mbp/m).  
  353 sequences classified (7.06%)  
  4647 sequences unclassified (92.94%)  
## 4. Run kraken for sample2
```{bash, eval=FALSE}
kraken2 --use-names --threads 4 --db /home/malina/krakenDB/ --report sample2.report --paired tara_reads_R1.5000.100.fastq tara_reads_R2.5000.100.fastq > sample2.kraken
```
Loading database information... done.  
5000 sequences (1.26 Mbp) processed in 0.398s (753.2 Kseq/m, 189.81 Mbp/m).  
  408 sequences classified (8.16%)  
  4592 sequences unclassified (91.84%)  
 ## 5. Run bracken for sample1 
 ```{bash, eval=FALSE}
bracken -d /home/malina/krakenDB -i sample1.report -l S -o sample1.bracken.tsv
```
 >> Checking for Valid Options...  
 >> Running Bracken  
      >> python src/est_abundance.py -i sample1.report -o sample1.bracken.tsv -k /home/malina/krakenDB/database100mers.kmer_distrib -l S -t 0  
PROGRAM START TIME: 03-05-2024 13:16:02  
BRACKEN SUMMARY (Kraken report: sample1.report)  
    >>> Threshold: 0  
    >>> Number of species in sample: 104  
          >> Number of species with reads > threshold: 104  
          >> Number of species with reads < threshold: 0  
    >>> Total reads in sample: 5000  
          >> Total reads kept at species level (reads > threshold): 207  
          >> Total reads discarded (species reads < threshold): 0  
          >> Reads distributed: 141  
          >> Reads not distributed (eg. no species above threshold): 5  
          >> Unclassified reads: 4647  
BRACKEN OUTPUT PRODUCED: sample1.bracken.tsv  
PROGRAM END TIME: 03-05-2024 13:16:02  
  Bracken complete.  

## 5. Run bracken for sample2 
```{bash, eval=FALSE}
bracken -d /home/malina/krakenDB -i sample2.report -l S -o sample2.bracken.tsv 
```
>> Checking for Valid Options...  
 >> Running Bracken  
      >> python src/est_abundance.py -i sample2.report -o sample2.bracken.tsv -k /home/malina/krakenDB/database100mers.kmer_distrib -l S -t 0  
PROGRAM START TIME: 03-05-2024 13:18:44  
BRACKEN SUMMARY (Kraken report: sample2.report)  
    >>> Threshold: 0  
    >>> Number of species in sample: 113  
          >> Number of species with reads > threshold: 113  
          >> Number of species with reads < threshold: 0  
    >>> Total reads in sample: 5000  
          >> Total reads kept at species level (reads > threshold): 241  
          >> Total reads discarded (species reads < threshold): 0  
          >> Reads distributed: 156  
          >> Reads not distributed (eg. no species above threshold): 11  
          >> Unclassified reads: 4592  
BRACKEN OUTPUT PRODUCED: sample2.bracken.tsv  
PROGRAM END TIME: 03-05-2024 13:18:44  
  Bracken complete.  

## 6. Merge the bracken output files for teh two samples
```{bash, eval=FALSE}
mkdir bracken_output
cp sample2_bracken.report sample1_bracken.report bracken_output/
python merge_profiling_reports.py -i bracken_output/ -o merged
```
