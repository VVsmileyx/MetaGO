# Welcome to use GOES  
  
  GOES is a pipeline developed to identify group-specific sequences between two groups of high throughput sequencing samples with long k-mer (generally k=30-40) features. The group-specific here means being present or rich in one group but absent or scarce in another group. GOES is taxonomy-free and only on the frequency of k-mers
    
  GOES was developed with Python and implemented on Apache Spark under Unix and Linux platform.  Apache Spark is a high-performance computing architecture to run on HDFS of Hadoop or on a stand-alone multi-core computer in parallel.
    
  The detail description of GOES’s framework is provided [here]().  
    
  The installing and running manual of GOES is [here]().  
    
## Version Release Notes  
 - Version 1.0  
   1.This is the first version of GOES pipeline. ([Source code Download]())  
   2.An demo of GOES is [here]().  
     
## Development Team  
  The whole source code was developed by Ying Wang's group, Automation Department, Xiamen University, P.R.China. All the suggestions and questions are welcome to wangying AT xmu.edu.cn.  
   
## The detail description of GOES’s framework  
The [figure1](https://github.com/VVsmileyx/GOES/blob/master/figure1.jpg) shows the profile of the computational framework, which includes following three modules. 1) Building feature vector of each sample. For each metagenomic sequencing data, the feature vector is composed of the number of occurrence for each k-mer through all the reads. 2) Feature preprocessing. After preprocessing and normalization, the union matrix is integrated on the feature vectors across the training samples. The high-sparse features obviously have no contribution to separate the groups are filtered out. 3) Identify group-specific features. The logical and numerical features are filtered and selected with discriminating power.  
  
The [figure2]() and [figure3]() give the processing pipeline for training and testing of group-specific features.  
  
Apache Spark is a fast big data analytics engine on MapReduce framework and Hadoop Distributed File System (HDFS) [31]. In parallel programming, Spark supplies Resilient Distributed Datasets (RDDs) and parallel operations on datasets. RDD enables programmers perform in-memory computations on large clusters. The map and reduce RDD frame works is shown in the following [figure4]().  
  
## The installing and running manual of GOES
### Package installation and environment configuration  
- Pre-install running environment  
   1.Unix or Linux operating system.  
   2.Python 2.7 or above.  
   3. Spark 1.6.2 and later  
   4. DSK 1.6066 and later ([download here](http://minia.genouest.org/dsk/))  
   5. sratoolkit.2.8.2-1-ubuntu64 ([download here]())  
     
You need set the dsk and sratoolkit path to environment variables, you can use the following command to set environment variables:  
   
   *`$ echo ‘ export PATH=Your dsk path:$PATH ‘ >> ~/.bashrc`*  
	 *`$ echo ‘export PATH=Your sratoolkit path:$PATH ‘ >> ~/.bashrc`*  
then use command *`source ~/.bashrc`* to make it effective

