# Assessing Read Quality


## Quality control


**G**arbage **I**n **G**arbage **O**ut: The quality of information coming out cannot be better than the quality of information went in.

![Alt text](/images/csm_TP_0318_Issue_9e948dab97.png)

Assessing the quality of your raw data, it is of critical importance. It will save you a lot of time and a lot of pain down the line.

![Alt text](/images/dilbert_garbage.png)

### Fastq format
https://en.wikipedia.org/wiki/FASTQ_format

Let's go into the "fastq" folder 

```cd /Lab-SequenceData/fastq```

 and look at the contents of the folder
 
 As you will notice the fastq files have an extention "gz". These are compressed files. Since sequence files are usually very large wroking with compressed files saves a lot of time. Most of the bioinformatic tools working with sequencing reads can take as input compressed files.

>View the first complete read (4 first lines) in one of the files our dataset. Will this command work? ```head -n 4 set1_1.fastq.gz```

In order to avoid uncompressing the files we will use a modification of the command above. Use zcat to view the compressed file and *pipe* the output into head

```zcat set1_1.fastq.gz | head -4```

| Line |Description| 
| :----| :--------| 
| 1    | Begins with ‘@’ and then information about the read | 
| 2    | The DNA sequence | 
| 3    | +| 
| 4    | String of characters which representing the quality scores; must have same number of characters as line 2 | 

Line 4 shows the quality for each nucleotide in the read. Quality is interpreted as the probability of an incorrect base call (e.g. 1 in 10) or, equivalently, the base call accuracy (e.g. 90%). To make it possible to line up each individual nucleotide with its quality score, the numerical score is converted into a code where each individual character represents the numerical quality score for an individual nucleotide. The numerical value assigned to each of these characters depends on the sequencing platform that generated the reads. The sequencing machine used to generate our data uses the standard Sanger quality PHRED score encoding, using Illumina version 1.8 onwards. Each character is assigned a quality score between 0 and 40 as shown in the chart below.
![Alt text](/images/phed.png)

Each quality score represents the probability that the corresponding nucleotide call is incorrect. This quality score is logarithmically based, so a quality score of 10 reflects a base call accuracy of 90%, but a quality score of 20 reflects a base call accuracy of 99%. These probability values are the results from the base calling algorithm and depend on how much signal was captured for the base incorporation.

>What is the last read in the set1_1.fastq file? How confident are you in this read?

## Assessing Quality using FastQC
In real life, you won’t be assessing the quality of your reads by visually inspecting your FASTQ files. Rather, you’ll be using a software to assess read quality. We’ll use a program called FastQC to visualize the quality of our reads. 

FastQC has a number of features which can give you a quick impression of any problems your data may have, so you can take these issues into consideration before moving forward with your analyses. Rather than looking at quality scores for each individual read, FastQC looks at quality collectively across all reads within a sample.
https://github.com/s-andrews/FastQC
https://dnacore.missouri.edu/PDF/FastQC_Manual.pdf

## Running FastQC

FastQC can accept multiple file names as input, and on both zipped and unzipped files, so we can use the *.fastq* wildcard to run FastQC on all of the FASTQ files in this directory.

### Install Fastqc

```conda install -c bioconda fastqc```

### Execute
Let's run fastqc for the four set  
```fastqc set4*.fastq.gz```
For each input FASTQ file, FastQC has created a .zip file and a .html file. The .zip file extension indicates that this is actually a compressed set of multiple output files. We’ll be working with these output files soon. The .html file is a stable webpage displaying the summary report for each of our samples.

>We want to keep our data files and our results files separate, so move these output files into a new directory within our results/ directory.

```mkdir fastqc_results```
 ```mv *.zip fastqc_results```
 ```mv *.html fastqc_results```

```cd fastqc_results```


### View the FastQC results
If we were working on our local computers, we’d be able to display each of these HTML files as a webpage. Since we have been working in poseidon, we have transfer them to our local computers.

To transfer a file from a remote server to our own machines, we will use *scp*, which we briefly mentioned during the previous lessons.

First we will make a new directory on our computer to store the HTML files we’re transferring. Let’s put it on our desktop for now. Open a new tab in your terminal program and make a directory:

```mkdir ~/Desktop/fastqc_html```

then initiate the tranfer:

```scp username@poseidon.whoi.edu:/vortexfs1/omics/env-bio/users/username/Lab-SequenceData/fastq/fastqc_results/*html ~/Desktop/fastqc_html```

Now we can go to our new directory and open the HTML files: *set4_1_fastqc.html* and *set4_2_fastqc.html*

The x-axis displays the base position in the read, and the y-axis shows quality scores. In this example, the sample contains reads that are 150 bp long. For each position, there is a box-and-whisker plot showing the distribution of quality scores for all reads at that position. The horizontal red line indicates the median quality score and the yellow box shows the 2nd to 3rd quartile range. This means that 50% of reads have a quality score that falls within the range of the yellow box at that position. The whiskers show the range to the 1st and 4th quartile.

 The plot background is also color-coded to identify good (green), acceptable (yellow), and bad (red) quality scores.

> Do you notice any trends on the quality score and any difference between the read in _1 and _2?



### Decoding the other FastQC outputs
We’ve now looked at quite a few “Per base sequence quality” FastQC graphs, but there are nine other graphs that we haven’t talked about! Below we have provided a brief overview of interpretations for each of these plots. For more information, please see the FastQC documentation here

>**Per tile sequence quality:** the machines that perform sequencing are divided into tiles. This plot displays patterns in base quality along these tiles. Consistently low scores are often found around the edges, but hot spots can also occur in the middle if an air bubble was introduced at some point during the run.
>
>**Per sequence quality scores:** a density plot of quality for all reads at all positions. This plot shows what quality scores are most common.
>
>**Per base sequence content:** plots the proportion of each base position over all of the reads. Typically, we expect to see each base roughly 25% of the time at each position, but this often fails at the beginning or end of the read due to quality or adapter content.
>
>**Per sequence GC content:** a density plot of average GC content in each of the reads.
>
>**Per base N content:** the percent of times that ‘N’ occurs at a position in all reads. If there is an increase at a particular position, this might indicate that something went wrong during sequencing.
>
>**Sequence Length Distribution:** the distribution of sequence lengths of all reads in the file. If the data is raw, there is often on sharp peak, however if the reads have been trimmed, there may be a distribution of shorter lengths.
>
>**Sequence Duplication Levels:** A distribution of duplicated sequences. In sequencing, we expect most reads to only occur once. If some sequences are occurring more than once, it might indicate enrichment bias (e.g. from PCR). If the samples are high coverage (or RNA-seq or amplicon), this might not be true.
>
>**Overrepresented sequences:** A list of sequences that occur more frequently than would be expected by chance.
>
>**Adapter Content:** a graph indicating where adapter sequences occur in the reads.
>
>**K-mer Content:** a graph showing any sequences which may show a positional bias within the reads.
>


# Trimming and Filtering
## Cleaning Reads
We will use a program called Trimmomatic to filter poor quality reads and trim poor quality bases from our samples
http://www.usadellab.org/cms/index.php?page=trimmomatic
http://www.usadellab.org/cms/uploads/supplementary/Trimmomatic/TrimmomaticManual_V0.32.pdf

To install ```conda install -c bioconda trimmomatic```

### Trimmomatic Options
Trimmomatic has a variety of options to trim your reads. If we run the command, we can see some of our options

```trimmomatic```

This output shows us that we must first specify whether we have paired end (PE) or single end (SE) reads. Next, we specify what flag we would like to run. For example, you can specify threads to indicate the number of processors on your computer that you want Trimmomatic to use. These flags are not necessary, but they can give you more control over the command. The flags are followed by positional arguments, meaning the order in which you specify them is important. In paired end mode, Trimmomatic expects the two input files, and then the names of the output files.

| Option |Description| 
| :----| :--------| 
| inputFile1  | Input reads to be trimmed. Typically the file name will contain an _1 or _R1 in the name | 
| inputFile2    | Input reads to be trimmed. Typically the file name will contain an _2 or _R2 in the name | 
| outputFile1P    | Output file that contains surviving pairs from the _1 file| 
| outputFile1U    | Output file that contains orphaned reads from the _1 file | 
| outputFile2P    | Output file that contains surviving pairs from the _2 file| 
| outputFile2U    | Output file that contains orphaned reads from the _2 file | 

| Parameter |Description| 
| :----| :--------| 
| ILLUMINACLIP   | Perform adapter removal| 
| SLIDINGWINDOW  | Perform sliding window trimming, cutting once the average quality within the window falls below a threshold | 
| LEADING    | 	Cut bases off the start of a read, if below a threshold quality| 
| TRAILING   | 	Cut bases off the end of a read, if below a threshold quality| 
| CROP   | Cut the read to a specified length| 
| HEADCROP  | Cut the specified number of bases from the start of the read | 
| MINLEN    | Drop an entire read if it is below a specified length| 

We will use only a few of these options and trimming steps in our analysis. It is important to understand the steps you are using to clean your data. For more information about the Trimmomatic arguments and options, see the Trimmomatic manual.

Let's try to trim the first set of sequences

```trimmomatic PE -threads 4 set6_1.fastq.gz set6_2.fastq.gz set6pair_1.fastq.gz set6un_1.fastq.gz set6pair_2.fastq.gz set6un_2.fastq.gz SLIDINGWINDOW:4:25 TRAILING:25 MINLEN:75```

> How many pairs survived?



The material above has been adapted from datacarpentry.org

https://datacarpentry.org/wrangling-genomics/02-quality-control/index.html

https://datacarpentry.org/wrangling-genomics/03-trimming/index.html



