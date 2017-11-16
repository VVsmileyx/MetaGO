# Welcome to use GOES:Identifying Group-Specific Sequences 
  
  GOES is a pipeline developed to identify group-specific sequences between two groups of high throughput sequencing samples with long k-mer (generally k=30-40) features. The group-specific here means being present or rich in one group but absent or scarce in another group. GOES is taxonomy-free and only on the frequency of k-mers.
    
  GOES was developed with Python and implemented on Apache Spark under Unix and Linux platform, which can be run on HDFS of Hadoop or on a stand-alone multi-core server in parallel.
    
  The detail description of GOES’s framework is provided [here](https://github.com/VVsmileyx/GOES/blob/master/README.md#the-detail-description-of-goess-framework).  
    
  The installing and running manual of GOES is [here](https://github.com/VVsmileyx/GOES/blob/master/README.md#the-installing-and-running-manual-of-goes).  
    
## Version Release Notes  
 - Version 1.0  
   1.This is the first version of GOES pipeline. ([Source code Download](https://github.com/VVsmileyx/GOES/raw/master/GOES_SoueceCode.rar))  
   2.An demo of GOES is [here](https://github.com/VVsmileyx/GOES/blob/master/README.md#the-demo-of-goes-on-testing-dataset).  
     
## Development Team  
  The whole source code was developed by Ying Wang's group, Automation Department, Xiamen University, P.R.China. All the suggestions and questions are welcome to wangying AT xmu.edu.cn.  
   
## 1  The description of GOES’s framework  
![](https://github.com/VVsmileyx/Results-and-figures/blob/master/figure1.jpg)
The figure shows the profile of the computational framework, which includes following three modules. 1) Building feature vector of each sample. For each metagenomic sequencing data, the feature vector is composed of the number of occurrence for each k-mer through all the reads. 2) Feature preprocessing. After preprocessing and normalization, the union matrix is integrated on the feature vectors across the training samples. The high-sparse features obviously have no contribution to separate the groups are filtered out. 3) Identify group-specific features. The logical and numerical features are filtered and selected with discriminating power.  
  
The following two figures give the processing pipeline for training and testing of group-specific features.   
![](https://github.com/VVsmileyx/Results-and-figures/blob/master/figure23.jpg)  
Apache Spark is a fast big data analytics engine on MapReduce framework and Hadoop Distributed File System (HDFS) [31]. In parallel programming, Spark supplies Resilient Distributed Datasets (RDDs) and parallel operations on datasets. RDD enables programmers perform in-memory computations on large clusters. The map and reduce RDD frame works is shown in the following figure.  
![](https://github.com/VVsmileyx/Results-and-figures/blob/master/figure4.jpg)
  
## 2  The installing and running manual of GOES
### 2.1 Package installation and environment configuration  
#### 2.1.1 Pre-install environments and softwares  
   1.Unix or Linux operating system.  
   2.Python 2.7 or above.  
   3. Spark 1.6.2 and later.  
   4. DSK 1.6066([download](https://github.com/VVsmileyx/Tools/raw/master/dsk-1.6066.tar.gz)) for counting k-mers.  
   5. sratoolkit.2.8.2-1-ubuntu64 ([download](https://ftp-trace.ncbi.nlm.nih.gov/sra/sdk/2.8.2-1/sratoolkit.2.8.2-1-ubuntu64.tar.gz)) for convert SRA to fa.gz when the sequencing file is SRA format.  
     
The dsk and sratoolkit paths are added to the environment variables for easy running with the following commands:  
   
   *`$ echo ‘ export PATH=Your dsk path:$PATH ‘ >> ~/.bashrc`*  
   *`$ echo ‘export PATH=Your sratoolkit path:$PATH ‘ >> ~/.bashrc`*  
	   
then use command *`$ source ~/.bashrc`* to make it effective  
  
#### 2.1.2 Environmental configurations for Spark  
  
  Our codes are tested on a local mode of a server with 128G-memory and Intel(R) Xeon(R) CPU E5-2620 v4 with 8 CPU cores at 2.10 GHz.The instruction of environmental configuration for Spark with local mode can be download [here](https://github.com/VVsmileyx/GOES/raw/master/Environmental_configuration_of_Spark.docx).For running on the cluster mode of HDFS of Hadoop, the configuration instruction can be found [here](https://spark.apache.org/docs/latest/spark-standalone.html), but we have not tested the program on this mode.  
    
### 2.2 Installation steps  
   1.Download the source code to your directory, e.g:`/home/user/GOES/`  
   2.Enter the directory:*`$ cd /home/user/GOES`*  
   3.Extract the tar file:*`$ tar -xvf GOES_SourceCode.tar`*  
   4.Enter the directory:*`$ cd /home/user/GOES/GOES_SourceCode`*  
     
## 3  Running of GOES Pipeline  
  
### 3.1 command line  
- The main running command is *`$ bash GOES.sh`* with following options:  
  
short option |  long option  |   description  
  :-----------: | :-----------:  | :----------------:   
  -I | --inputData  |The type of input data, you can only choose 'RAW' or 'MATRIX'.'RAW' means the input file format is 'SRA, fasta, fastq, fa.gz, fq.gz', 'MATRIX' means the input file is the feature matrix without any filtering.   
  -F | --fileList   | The input fileList when the input option is 'RAW'. 
  -N | --n1   | The number of samples belong to group 1.  
  -M | --n2   | The number of samples belong to group 2.  
  -K | --kmer | The length of k-mer as features(For the total number of training samples is about 100 or fewer, we recommend using 30; For the number of training sample is bigger than 100 or more, we recommend 40. The default value is 40 ).  
  -m | --min  | The threshold of minimum k-mer frequency. If the occurrence of one k-mer is smaller than the threshold, the k-mer is removed from the feature vector,the default value is 2, which means the k-mer occurring once will be removed.  
  -P | --Piece | In order to keep smooth running under limited computational resources, the feature vector file for each sequencing file can be split into small segments. The options are 1, 4, 8 and 16 and the default value is 1.The option 1 means that all  sequencing files are not splited. 
  -R | --SparseThreshold | The threshold of filtering out highly-sparse features, the default value is 0.8.  
  -C |--K2test | Chi-square test is applied as metrics to select the group-specific k-mers for logical features. The threshold of p-value of chi2-test. The default value is 0.01.  
  -A | --ASS | The ASS(average of sensitivity and specificity) of Single-logical-feature predictor is applied as metrics to select the group-specific k-mers for logical features. The threshold of ASS by single-logical-feature predictor. If the obtained ASS is higher than the threshold, the k-mer is identified as group-specific feature. The default value is 0.8.  
  -X | --WilcoxonTest | The threshold of p-value of Wilcoxon Test for selecting the numerical features. The default value is 0.01.  
  -L | --LogicalRegress | The threshold of ASS(average of sensitivity and specificity) for selecting group-specific numerical k-mers by single-numerical-feature Logical regression predictor. If the obtained ASS is higher than the threshold, the k-mer is identified as group-specific feature, the default value is 0.8.  
  -W | --filterFuction | The function of the filter, you can choose 'chi2-test' or 'ASS', default is ASS.  
  -O | --outputPath | The path of the output files, default is current directory.  
  -U | --Union | If you choose this option, the integrated feature matrix (without any filter ) will be saved and output named ‘tuple-union’ or ‘tuple-union-x’. (x = 1, 2, 3, ... )  
  -S | --sparse | If you choose this option, the union matrix files after filtering high-sparse features will be saved named ‘Chi2_filtered_down’ or ‘Chi2_filtered_down_x ’ or ‘AUC_filtered_down’ or ‘AUC_filtered_down-x’. (x = 1, 2, 3, ... ) The filtered matrix can be the input of GOES.sh as '-I MATRIX' when it is required to adjust the threshold of group-specific selections.    
  -Z | --cleanUp | If choose this option, the temporary files produced during the running will be cleaned up, and only output the selected group-specific k-mers. The temporary files includ tuple files, splited files, which have been named as ‘G1_tupleFile’ and ‘G2_tupleFile’ and ‘Group1splitedFile’ and ‘Group2splitedFile’ and ‘fastaFile’ separately.  
  -h | --help | show the help message.  
  
- Notice:  
  
  (1) It takes a few days(60 hours for 250GB fa.gz of 100 samples) to finish the whole running. So the integrated feature matrix (without any filter) is output for the following thresholds adjustment of the threshold of filtering out highly-sparse features, ASS, p-value. So using -U|--Union to output the integrated feature matrix. And then use -I  MATRIX is useful when we set the threshold of filtering out highly-sparse features, ASS, p-value too stringent to get group-specific k-mers. So we use the feature matrix without any filtering as input to select group-specific k-mers with relaxed throsholds.  
    
 (2) If you choose 'MATRIX' in option -I, you should not choose the following options: -F, -K, -m, -U, -Z. And make sure the integrated feature matrix ('tuple_union_x' or 'tuple_union') is in the folder which you choosed in option -O (e.g. /home/user/GOES/Results). Furthermore the following files should not be in /home/user/GOES/Results: 'filter_sparse_x','AUC_filtered_down_x','Chi2_filtered_down_x','WR_filtered_down_x'(x=1,2,...,n; n is the number in option '-P') or 'filter_sparse','AUC_filtered_down','Chi2_filtered_down','WR_filtered_down' if n=1.  
    
  (3) If you choose 'ASS' in option -W, you should not choose -C; IF you use 'chi2-test' in option -W, you should not choose -A.
     
#### 3.1.2 Examples:  
##### 3.1.2.1 InputData is raw data  
If the input file you selected is the list of sequencing files with .fa, .fa.gz, .fq, .fq.gz and .sra formats(choose 'RAW' in option '-I'), the following examples can be used.  
  
(1)*`$ bash-3.2$ bash GOES.sh -I RAW –F FileList.txt –N 25 –M 25 –K 10 –m 1 –P 4 -R 0.8 –A 0.8 –X 0.01 –L 0.8 –W ASS –O /home/usr/GOES/ -Z`*  
(The input file is Filelist.txt, there are 25 samples in group 1 and 20 samples in group 2, the tuple length is 10 and the mininum tuple frequency is 1.All tuple files are split into 4 slices. The threshold of filtering out highly-sparse features is 0.8. The filter function is ASS and the threshold of ASS, Wilcoxon test and Logical regression are 0.8, 0.01 and 0.8 separately. All Intermediate files are not saved.  The union matrix files are not saved except the files that after filtering by AUC, and all saved files are preserved in /home/usr/GOES/)  
  
The Filelist.txt must obey the following rules:  
Each line is the complete path of raw data, such as the first line is '/home/user/GOES/testData/H1.fasta', and the second line is '/home/user/GOES/testData/H2.fasta' ... Furthermore, and the samples belong to same group must arrange togther(e.g. line1 to linek of the list belongs to group 1 (health) and linek+1 to lineN belongs to group 2 (patient) ).  
  
(2)*`$ bash-3.2$ bash test.sh -I RAW –F fileList.txt –N 25 –M 25 –K 10 –m 1 -P 4 -R 0.8 –T 0.01 –C 0.01 –W chi2-test –O /home/usr/GOES/ -S –U –Z`*  
(The input file is filelist.txt , there are 25 samples in group 1 and 20 samples in group 2, the tuple length is 10 and the min tuple frequency is 1. All tuple files are split into 4 slices. The threshold of filtering out highly-sparse features is 0.8. The filter function is chi2-test and don’t save any Intermediate files but save the union matrix files that without any filtering and save the union matrix files that after filtering highly-sparse features and all saved files are perserved in /home/usr/GOES/ )  
  
(3)*`$ bash-3.2$ bash test.sh -I RAW –F Filelist.txt –N 25 –M 25 –K 10 –m 1 -P 4 -R 0.9 –A 0.9 –X 0.001 -L 0.8 –W ASS –O /home/usr/GOES/ –U –Z`*  
(The input file is Filelist.txt, there are 25 samples in group 1 and 20 samples in group 2, the tuple length is 10 and the mininum tuple frequency is 1.All tuple files are split into 4 slices. The threshold of filtering out highly-sparse features is 0.9. The filter function is ASS and the threshold of ASS, Wilcoxon test and Logical regression are 0.9, 0.1 and 0.8 separately. All Intermediate files are not saved. The threshold of filtering out highly-sparse features, ASS, p-value is too stringent, so we choose -U to save the feature matrix without any filtering, and that we can adjust the thresholds by using them as input. All the outputs are preserved in /home/usr/GOES/ )
  
#### 3.1.2.2 InputData is the integrated feature matrix  
If the input file you selected is the integrated feature matrix (without any filter), the following examples can be used.  
*`$ bash-3.2$ bash test.sh -I RAW –F fileList.txt –N 25 –M 25 –K 10 –m 1 -P 4 -R 0.9 –A 0.9 –X 0.001 -L 0.8 –W ASS –O /home/usr/GOES/ –U –Z`*  
The threshold of filtering out highly-sparse features, ASS, p-value is too stringent, so we choose -U to save the feature matrix without any filtering, and that we can adjust the thresholds by using them as input as following command:  
*`$ bash-3.2$ bash GOES.sh -I MATRIX –N 25 –M 25 -R 0.8 –P 4 –A 0.8 –X 0.01 –L 0.8 –W ASS –O /home/usr/GOES/ `*  
(The input file is the feature matrix without any filtering in /home/usr/GOES/, which named "tuple_union_1","tuple_union_2","tuple_union_3","tuple_union_4", there are 25 samples in group 1 and 20 samples in group 2.The number of input files is the 4. The filter function is ASS and the threshold of filtering highly-sparse features, AUC-test Wilcoxon test and Logical regression are 0.8 0.01 and 0.8 separately.All saved files are preserved in /home/usr/GOES/)  
  
### 3.2 Input file  
#### 3.2.1 InputData is the text of the list of sequencing files  
  
- Description  
  
  The input file is the text of the list of sequencing files with .fa, .fa.gz, .fq, .fq.gz and .sra formats. All the training samples are list with complete path and the samples belong to same group must arrange together. (e.g. line1 to linek of the list belongs to group 1 and linek+1 to lineN belongs to group 2)  
  
- Example：The input file is Filelist.txt  
  
  *`$ cat filelist.txt`*  
  `/home/…/H1.fasta`  
  `/home/…/H2.fasta`  
  `…`  
  `/home/…/H25.fasta`  
  `/home/…/P1.fasta`  
  `/home/…/P2.fasta`  
  `…`  
  `/home/…/P25.fasta`  
  
#### 3.2.2 InputData is the integrated feature matrix   
  
- Description  
  
  The input file(files) should be 'tuple_union_x' (x=1,2,...,n; n is the number in option '-P',and if n=1, the input file's name is 'tuple_union') in the floder you choose in option '-O',which are the results of running the program last time with chooseing 'RAW' in option '-I'.  
    
- Example:  
tuple_union/part-xxx:  
  
  `ACCGTGCGTA      0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       1       2       0       0       0       0       0       0       0       0       0       0       0`  
  `CAAGAACGGC      5       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       1       0       0       0       0       0       1       0       0       3       0       0       2       0       0       0       0       1       0       1       0       0       1       0       0       0       0       0       0       1       1`  
`...`
### 3.3 Output files  
#### 3.3.1 InputData is the text of the list of sequencing files  
  
(1) The group-specific k-mer features  
  
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
  
(2) Temporary middle results  
  
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
     
(3) Other results  
  
- Description  
  
  If you running GOES with choosing  '-U' and '-S',you can get 2 results in the path you choosed in '-O':'tuple_union_x' and 'filter_sparse_x'(x=1,2,...,n;n is the number you choosed in option '-P') or 'tuple_union' and 'filter_sparse'(n=1)  
  
- Example  
  
tuple_union/part-xxx:  
  
  `ACCGTGCGTA      0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       1       2       0       0       0       0       0       0       0       0       0       0       0`  
  `CAAGAACGGC      5       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       1       0       0       0       0       0       1       0       0       3       0       0       2       0       0       0       0       1       0       1       0       0       1       0       0       0       0       0       0       1       1`  
  `...`  
  
filter_sparse/part-xxx:  
  
  `CAAGAACGGC      5       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       1       0       0       0       0       0       1       0       0       3       0       0       2       0       0       0       0       1       0       1       0       0       1       0       0       0       0       0       0       1       1`  
  `...`  
  
#### 3.3.2 InputData is the integrated feature matrix  
(1) The group-specific k-mer features   
  
  The output are same with the group-specific k-mer features wich input is the text of the list of sequencing files.  
  
(2) Other results  
  
- Description  
  
  If you running GOES with choosing '-S',you can get the results in the path you choosed in '-O' named 'filter_sparse_x'(x=1,2,...,n;n is the number you choosed in option '-P') or 'filter_sparse'(n=1)  
  
- Example  
  
  filter_sparse/part-xxx:  
    
  `CAAGAACGGC      5       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       0       1       0       0       0       0       0       1       0       0       3       0       0       2       0       0       0       0       1       0       1       0       0       1       0       0       0       0       0       0       1       1`  
  `...`  
  
## 4  The demo of GOES on testing dataset  

### 4.1 Dataset  
   25 healthy test samples and 25 patient test samples ([download](https://github.com/VVsmileyx/TestData/raw/master/testDATA.rar))  
### 4.2 Running steps:  
#### 4.2.1 InputData is the text of the list of sequencing files
- Step1: download testData and GOES souce code  
- Step2: get the fileList of test data  
	  
	*`$ cd testData`*  
	*`$ pwd # get the absolute path of testData, e.g /home/usr/testDATA/`*  
	*`$ ls /home/usr/testDATA/H*.txt >> /home/usr/GOES/GOES_SoueceCode/fileList.txt # /home/usr/GOES/GOES_SoueceCode/ is the absolute path of sorce codes of GOES`*  
	*`$ ls /home/usr/testDATA/P*.txt >> /home/usr/GOES/GOES_SoueceCode/fileList.txt # get the fileList of test data`*  
	  
- Step3: run GOES:  
	  
	*`$ cd home/usr/GOES/GOES_SoueceCode`*  
	*`$ bash GOES.sh -I RAW -F testFiles.txt -N 25 -M 25 -K 10 -m 1 -P 4 -R 0.7 -A 0.6 -X 0.1 -L 0.5 -W AUC -O /home/usr/GOES_Result -U -S # Filter with AUC_test `*  
	*`$ bash GOES.sh -I RAW -F testFiles.txt -N 25 -M 25 -K 10 -m 1 -P 4 -R 0.7 -C 0.1 -X 0.1 -L 0.5 -W chi2-test -O /home/usr/GOES_Result -U -S # Filter with chi2-test `*  
	  
- Step4: Output files  
	   
	 If you choose AUC-test to filtering,the output files:  
	   
	 Output file1: single logical feature with (sensitivity+specificity)/2>=0.6 for AUC-test. ([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/AUC_test_RAW.rar))  
	 Output file2: single numerical feature with p-value<=0.1 for Wilcoxon sum rank test and (sensitivity+specificity)/2>=0.5 for logistic regression.([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/WR_filtered_with_AUCtest_RAW.rar))  
	   
	 If you choose chi2-test to filtering, the output files:  
	   
	 Output file1: single logical feature with p-value<=0.1 for chi2-test ([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/Chi2_test_RAW.rar))  
	 Output file2: single numerical feature with p-value<=0.1 for Wilcoxon sum rank test and (sensitivity+specificity)/2>=0.5 for logistic regression.([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/WR_filtered_with_Chi2test_RAW.rar))  
	   
	 Output files for both AUC-test and chi2-test:  
	   
	 Output file3: features without any filtering.([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/TupleUnion.rar))  
	 Output file4: features after removing highly-sparse features.([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/filter_sparse_RAW.rar))  
	   
	 Middle temporary files:([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/Middle_temporary_files.rar))
  
#### 4.2.2 InputData is feature matrix without any filtering  
  
- Notice  
  
  The matrix files are the files named 'tuple_union_x' (x=1,2,...,n; n is the number in option '-P',and if n=1, the input file's name is 'tuple_union') in the folder you choose in option '-O',which are the results of running the program last time with chooseing 'RAW' in option '-I'., and you should remove or change the path of the following folders: 'filter_sparse_x','AUC_filtered_down_x','Chi2_filtered_down_x','WR_filtered_down_x'(x=1,2,...,n; n is the number in option '-P') or 'filter_sparse','AUC_filtered_down','Chi2_filtered_down','WR_filtered_down' if n=1.  
  
- Step1: run GOES with choosing 'RAW' in option '-I'  
	*`$ bash GOES.sh -I RAW -F testFiles.txt -N 25 -M 25 -K 10 -m 1 -P 4 -R 0.7 -A 0.6 -X 0.1 -L 0.5 -W AUC -O /home/usr/GOES_Result -U -S # Filter with AUC_test `*  
	*`$ bash GOES.sh -I RAW -F testFiles.txt -N 25 -M 25 -K 10 -m 1 -P 4 -R 0.7 -C 0.1 -X 0.1 -L 0.5 -W chi2-test -O /home/usr/GOES_Result -U -S # Filter with chi2-test `*  
  
- Step2: remove or change the path of the following folders:'filter_sparse_x','AUC_filtered_down_x','Chi2_filtered_down_x','WR_filtered_down_x'(x=1,2,...,n; n is the number in option '-P') or 'filter_sparse','AUC_filtered_down','Chi2_filtered_down','WR_filtered_down' if n=1.   
	*`$ cd /home/usr/GOES_Result`*  
	*`$ rm -r  filter_sparse* AUC_filtered_down* Chi2_filtered_down* WR_filtered_down* # remove all folders`*  
	*`$ mv filter_sparse* AUC_filtered_down* Chi2_filtered_down* WR_filtered_down* ../ # move all folders to parent directory`*  
	*`$ cd home/usr/GOES/GOES_SoueceCode`*
  
- Step3: run GOES with choosing 'MATRIX' in option '-I'  
	*`$ bash GOES.sh -I MATRIX -N 25 -M 25 -P 4 -R 0.8 -A 0.6 -X 0.1 -L 0.5 -W AUC -O /home/usr/GOES_Result -S # Filter with AUC_test `*  
	*`$ bash GOES.sh -I MATRIX -N 25 -M 25 -P 4 -R 0.8 -C 0.1 -X 0.1 -L 0.5 -W chi2-test -O /home/usr/GOES_Result -S # Filter with chi2-test `*   
  
- Step4: Output files  
  
	 If you choose AUC-test to filtering,the output files:  
	   
	 Output file1: single logical feature with (sensitivity+specificity)/2>=0.6 for AUC-test. ([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/AUC_test_MATRIX.rar))  
	 Output file2: single numerical feature with p-value<=0.1 for Wilcoxon sum rank test and (sensitivity+specificity)/2>=0.5 for logistic regression.([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/WR_filtered_with_AUCtest_MATRIX.rar))  
	   
	 If you choose chi2-test to filtering, the output files:  
	   
	 Output file1: single logical feature with p-value<=0.1 for chi2-test ([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/Chi2_test_MATRIX.rar))  
	 Output file2: single numerical feature with p-value<=0.1 for Wilcoxon sum rank test and (sensitivity+specificity)/2>=0.5 for logistic regression.([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/WR_filtered_with_Chi2test_MATRIX.rar))  
	   
	 Output files for both AUC-test and chi2-test:  
	   
	 Output file3: features after removing highly-sparse features.([download](https://github.com/VVsmileyx/Results-and-figures/blob/master/filter_sparse_MATRIX.rar))  
