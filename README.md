# Welcome to use GOES  
  
  GOES is a pipeline developed to identify group-specific sequences between two groups of high throughput sequencing samples with long k-mer (generally k=30-40) features. The group-specific here means being present or rich in one group but absent or scarce in another group. GOES is taxonomy-free and only on the frequency of k-mers
    
  GOES was developed with Python and implemented on Apache Spark under Unix and Linux platform.  Apache Spark is a high-performance computing architecture to run on HDFS of Hadoop or on a stand-alone multi-core computer in parallel.
    
  The detail description of GOES’s framework is provided [here](https://github.com/VVsmileyx/GOES/blob/master/README.md#the-detail-description-of-goess-framework).  
    
  The installing and running manual of GOES is [here](https://github.com/VVsmileyx/GOES/blob/master/README.md#the-installing-and-running-manual-of-goes).  
    
## Version Release Notes  
 - Version 1.0  
   1.This is the first version of GOES pipeline. ([Source code Download](https://github.com/VVsmileyx/GOES/blob/master/GOES_SoueceCode.rar))  
   2.An demo of GOES is [here](https://github.com/VVsmileyx/GOES/blob/master/README.md#the-demo-of-goes-on-testing-dataset).  
     
## Development Team  
  The whole source code was developed by Ying Wang's group, Automation Department, Xiamen University, P.R.China. All the suggestions and questions are welcome to wangying AT xmu.edu.cn.  
   
## The detail description of GOES’s framework  
![](https://github.com/VVsmileyx/GOES/blob/master/Figure1.jpg)
The figure shows the profile of the computational framework, which includes following three modules. 1) Building feature vector of each sample. For each metagenomic sequencing data, the feature vector is composed of the number of occurrence for each k-mer through all the reads. 2) Feature preprocessing. After preprocessing and normalization, the union matrix is integrated on the feature vectors across the training samples. The high-sparse features obviously have no contribution to separate the groups are filtered out. 3) Identify group-specific features. The logical and numerical features are filtered and selected with discriminating power.  
  
The following two figures give the processing pipeline for training and testing of group-specific features.   
![](https://github.com/VVsmileyx/GOES/blob/master/Figure2.jpg) ![](https://github.com/VVsmileyx/GOES/blob/master/figure3.jpg) 
Apache Spark is a fast big data analytics engine on MapReduce framework and Hadoop Distributed File System (HDFS) [31]. In parallel programming, Spark supplies Resilient Distributed Datasets (RDDs) and parallel operations on datasets. RDD enables programmers perform in-memory computations on large clusters. The map and reduce RDD frame works is shown in the following figure.  
![](https://github.com/VVsmileyx/GOES/blob/master/figure4.jpg)
  
## The installing and running manual of GOES
### Package installation and environment configuration  
- Pre-install running environment  
   1.Unix or Linux operating system.  
   2.Python 2.7 or above.  
   3. Spark 1.6.2 and later  
   4. DSK 1.6066 and later ([download here](http://minia.genouest.org/dsk/))  
   5. sratoolkit.2.8.2-1-ubuntu64 ([download here](https://trace.ncbi.nlm.nih.gov/Traces/sra/sra.cgi?view=toolkit_doc&f=std))  
     
You need set the dsk and sratoolkit path to environment variables, you can use the following command to set environment variables:  
   
   *`$ echo ‘ export PATH=Your dsk path:$PATH ‘ >> ~/.bashrc`*  
   *`$ echo ‘export PATH=Your sratoolkit path:$PATH ‘ >> ~/.bashrc`*  
	   
then use command *`$ source ~/.bashrc`* to make it effective  
  
- Environmental configuration for Spark  
  
  The detail environmental configuration for Spark is [here]()  
    
- Installation steps  
   1.Download the source code to your directory, e.g:`/home/user/GOES/`  
   2.Enter the directory:*`$ cd /home/user/GOES`*  
   3.Extract the tar file:*`$ tar -xvf GOES_SourceCode.tar`*  
   4.Enter the directory:*`$ cd /home/user/GOES/GOES_SourceCode`*  
     
### Running of GOES Pipeline  
  
#### 1.command line  
- The main running command is *`$ bash GOES.sh`* with following options:  
  
   -I, --inputData: The type of input data, you can only choose 'RAW' or 'MATRIX'.  
   -F, --fileList: The input fileList.  
   -N, --n1: The number of samples belong to group 1.  
   -M, --n2: The number of samples belong to group 2.  
   -K, --kmer: The length of k-mer as features(we recommend using 30 for relative small sample size ~100; using 40 for larger sample size >100).  
   -m, --min: The threshold of minimum k-mer frequency. If the occurrence of one k-mer is smaller than the threshold, the k-mer is removed from the feature vector,the default value is 1.  
   -P, --Piece: In order to keep smooth running under limited computational resources, the feature vector file for each sequencing file can be split into small segments. You can choose 1, 2, 3, 4, … , the default value is 1.  
   -R, --SparseThreshold: The threshold of filtering highly-sparse features, the default value is 0.8.  
   -C, --K2test: The threshold of p-value of chi2-test, the default value is 0.01.  
   -A, --AUCtest: The threshold of AUC by single-logical-feature predictor. If the obtained AUC is higher than the threshold, the k-mer is identified as group-specific feature. The default value is 0.8.  
   -X, --WilcoxonTest: The threshold of p-value of Wilcoxon Test, the default value is 0.01.  
   -L, --LogicalRegress: The threshold of (sn+sp)/2 by single-numerical-feature Logical regression predictor. If the obtained (sn+sp)/2 is higher than the threshold, the k-mer is identified as group-specific feature, the default value is 0.8.  
   -W, filterFuction: The function of the filter, you can choose 'chi2-test' or 'AUC', default is AUC.  
   -O, --outputPath: The path of the output files, default is current directory.  
   -U, --Union: If you choose this option, the initial union matrix files (without any filter ) will be saved and output named ‘tuple-union’ or ‘tuple-union-x’. (x = 1, 2, 3, ... )  
   -S, --sparse: If you choose this option, the union matrix files after filtering high-sparse features will be saved named ‘Chi2_filtered_down’ or ‘Chi2_filtered_down_x ’ or ‘AUC_filtered_down’ or ‘AUC_filtered_down-x’. (x = 1, 2, 3, ... )  
   -Z, --cleanUp: If choose this option, all Intermediate files will be cleaned up, including tuple files, splited files, named ‘G1_tupleFile’ and ‘G2_tupleFile’ and ‘Group1splitedFile’ and ‘Group2splitedFile’ and ‘fastaFile’ separately.  
   -h, --help: show the help message.  
     
- Examples:  
(1)*`$ bash-3.2$ bash GOES.sh –F FileList.txt –N 25 –M 25 –K 10 –m 1 –P 4 –A 0.8 –X 0.01 –L 0.8 –W AUC –O /home/usr/GOES/ -Z`*  
(The input file is Filelist.txt , there are 25 samples in group 1 and 20 samples in group 2, the tuple length is 10 and the mininum tuple frequency is 1.All tuple files are split into 4 slices. The filter function is AUC and the threshold of AUC-test Wilcoxon test and Logical regression are 0.8 0.01 and 0.8 separately. All Intermediate files are not saved.  The union matrix files are not saved except the files that after filtering by AUC, and all saved files are preserved in /home/usr/GOES/)  
(2)*`$ bash-3.2$ bash test.sh –F fileList.txt –N 25 –M 25 –K 10 –m 1 –T 0.01 –C 0.01 –W TK –O /home/usr/GOES/ -S –U –Z`*  
(The input file is filelist.txt , there are 25 samples in group 1 and 20 samples in group 2, the tuple length is 10 and the min tuple frequency is 1. The filter function is T-test and chi2-test and don’t save any Intermediate files but save the union matrix files that without any filtering and save the union matrix files that after filtering high-sparse features and all saved files are perserved in /home/usr/GOES/ )  
  
#### 2.Input file  
The input file is the text of the list of sequencing files with .fa, .fa.gz, .fq, .fq.gz and .sra formats. All the training samples are list with complete path and the samples belong to same group must arrange together. (e.g. line1 to linek of the list belongs to group 1 and linek+1 to lineN belongs to group 2)  
  
Example：The input file is Filelist.txt  
  
  *`$ cat filelist.txt`*  
  `/home/…/H1.fasta`  
  `/home/…/H2.fasta`  
  `…`  
  `/home/…/H25.fasta`  
  `/home/…/P1.fasta`  
  `/home/…/P2.fasta`  
  `…`  
  `/home/…/P25.fasta`  
  
#### 3.Output files  
- (1) The group-specific k-mer features  
  
- Description  
  
  A folder named “AUC_filter_down” and “WR_filtered_down” or “Chi2_filtered_down” or k folders named “AUC_filter_down_x” and “WR_filtered_down_x” or “Chi2_filter_down_x” and "WR_filtered_down_x" (k is The number of pieces of every sliced tuple file and x=1, 2, 3, ... , k) in the dictionary that the user assigned, every folder contain a number of filers named “part-xxx” and them saved the union matrix that after all filtering.  
    
- Example:  
If you choose ‘AUC’ function to filter,the result will be like this:  
AUC_filter_down/part-xxx:  
  
  `AGTCGATTGC	1	1	1	1	1	1	1	0	1	0	1	1	1	1	1	1	1	1	1	1	1	1	1	1	0	0	0	0	0	0	0	0	0	0	0	0	0	0	0	1	0	0	0	0	0	0	0	0	0	AUC:0.944	Label:H`  
  `...`  
    
  WR_filtered_down/part-xxx:  
      
    `AGAAAATGAA      0.0     0.0     57.6735 0.0     45.5851 0.0     0.0     0.0     91.659  0.0     0.0     0.0     0.0     45.608  0.0     0.0     0.0     0.0     0.0     0.0     0.0     0.0     0.0     45.5539 0.0     0.0     0.0     137.0677        45.7917 0.0     0.0     0.0     0.0     0.0     0.0     46.0851 45.7289 0.0     45.5332 0.0     45.5    0.0     45.819  0.0     0.0     0.0     0.0     0.0     91.5583 0.0     AUC:0.56        Wilcoxon_Pvalue:0.455057690499  Regress <sn+sp>/2:0.56  Label:P`  
    `...`  
  If you choose ‘chi2-test’ function to filter,the result will be like this:
    
  Chi2_filtered_down/part-xxx:  
  `CAAGAACGGC      0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0	1       0       0       0       0       0       1       0       0       1       0       0       1       0       0       0       0       1       0       1       0       0       1       0       0       0       0       0       0       1       1       kp:0.0272003506163`  
`...`  
  
  WR_filtered_down/part-xxx:  
  `AAGAAAAAGC      0.0     0.0     57.6735 0.0     0.0     0.0     0.0     0.0     0.0     91.2326 0.0     114.4885        0.0     45.608  45.527  0.0     57.5639 45.8295 45.8127 0.0     0.0     0.0     115.9824        0.0     0.0     0.0     0.0     0.0     45.6454 0.0     0.0     0.0     0.0     45.8295 0.0     0.0     0.0     0.0     45.608  45.527  0.0     57.5639 0.0     45.8127 0.0     57.1559 0.0     0.0     0.0     0.0     kp:0.761760667488       Wilcoxon_Pvalue:0.460934885855  Regress <sn+sp>/2:0.54`  
  
- (2)Temporary middle results  
  
  Including tuple files, splited files, named ‘G1_tupleFile’ and ‘G2_tupleFile’ and ‘Group1splitedFile’ and ‘Group2splitedFile’ separately. If the raw data type is .sra, there would be a temporary file named ‘fastaFile’  
    
- Example  
  
  *`$ ls G1_tupleFile/*`*  
    
  `H1_k_10.txt`  
  `H2_k_10.txt`  
  `...`  
  `H24_k_10.txt`  
  `H25_k_10.txt`  
    
  *`$ less H1_k_10.txt`*  
    
   `CTGTCCATGC 1`  
   `ATCTGCTTAA 1`  
   `GCAAGATCGC 2`  
   `...`  
     
   *`$ ls Group1splitedFile/*`*  
     
   `Group1FileList_1.txt`  
   `Group1FileList_2.txt`  
   `...`  
   `Group1FileList_7.txt`
   `H1_k_10_1.txt`  
   `H1_k_10_2.txt`  
   `...`  
   `H1_k_10_7.txt`  
   `H2_k_10_1.txt`  
   `H2_k_10_2.txt`  
   `...`  
   `H2_k_10_7.txt`  
   `...`  
   `H25_k_10_1.txt`  
   `H25_k_10_2.txt`  
   `...`  
   `H25_k_10_7.txt`  
     
   *`$ less H1_k_10_1.txt`*
     
    `CTGTCCATGC 1`  
    `ATCTGCTTAA 1`  
    `GCAAGATCGC 2`  
    `...`  
      
   If the raw data are .sra files, another temporary middle dictionary named fastaFile will be generated.
      
   *`$ ls fastaFile/*`*  
      
   `H1.fasta.gz`  
   `H2.fasta.gz`  
   `...`  
   `H25.fasta.gz`  
   `P1.fasta.gz`  
   `P2.fasta.gz`  
   `...`  
   `P25.fasta.gz`  
      
   *`$ less H1.fasta.gz`*
     
   `>ERR011087.1 I330_1_FC30JM6AAXX:4:1:0:199 length=44`  
   `TTCANATATGGAAAAACAGGGAGCGGAAATCACGTTACTTGCGT`  
   `>ERR011087.1 I330_1_FC30JM6AAXX:4:1:0:199 length=44`  
   `ATCATCGGAAAAGGCAGGCTGTCCATGCTCCAACCGGTTAATGA`
   `...`  
     
## The demo of GOES on testing dataset  

- Dataset:25 test healthy samples and 25 patient test samples [download](https://github.com/VVsmileyx/GOES/blob/master/testDATA.rar)  
- Running steps:  
	- Step1: download testData and GOES souce code  
	- Step2: get the fileList of test data  
	  
	*`$ cd testData`*  
	*`$ pwd # get the absolute path of testData, e.g /home/usr/testDATA/`*  
	*`$ ls /home/usr/testDATA/H*.txt >> /home/usr/GOES/GOES_SoueceCode/fileList.txt # /home/usr/GOES/GOES_SoueceCode/ is the absolute path of sorce codes of GOES`*
	*`$ ls /home/usr/testDATA/P*.txt >> /home/usr/GOES/GOES_SoueceCode/fileList.txt # get the fileList of test data`*  
	  
	- Step3: run GOES:  
	  
	*`$ cd home/usr/GOES/GOES_SoueceCode`*  
	*`$ bash GOES.sh -I RAW -F testFiles.txt -N 25 -M 25 -K 10 -m 1 -P 7 -R 0.7 -C 0.7 -X 1 -L 0.5 -W chi2-test -O /home/wangying/fulei/program/testResult3 -U -S -Z `*
	
