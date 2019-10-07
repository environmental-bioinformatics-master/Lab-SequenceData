#Under construction

# Assessing Read Quality

## Quality control

### Fastq format

PLACEHOLDER: EXAMPLES gunzip?

>View the first complete read in one of the files our dataset by using head to look at the first four lines.

```head -n 4 SRRXXXX_1.fastq```
 
| Line |Description| 
| :----| :--------| 
| 1    | Begins with ‘@’ and then information about the read | 
| 2    | The DNA sequence | 
| 3    | Begins with ‘@’ and then information about the read| 
| 4    | String of characters which representing the quality scores; must have same number of characters as line 2 | 

Line 4 shows the quality for each nucleotide in the read. Quality is interpreted as the probability of an incorrect base call (e.g. 1 in 10) or, equivalently, the base call accuracy (e.g. 90%). To make it possible to line up each individual nucleotide with its quality score, the numerical score is converted into a code where each individual character represents the numerical quality score for an individual nucleotide. The numerical value assigned to each of these characters depends on the sequencing platform that generated the reads. The sequencing machine used to generate our data uses the standard Sanger quality PHRED score encoding, using Illumina version 1.8 onwards. Each character is assigned a quality score between 0 and 40 as shown in the chart below.

Each quality score represents the probability that the corresponding nucleotide call is incorrect. This quality score is logarithmically based, so a quality score of 10 reflects a base call accuracy of 90%, but a quality score of 20 reflects a base call accuracy of 99%. These probability values are the results from the base calling algorithm and depend on how much signal was captured for the base incorporation.

>What is the last read in the XXXXXX_1.fastq file? How confident are you in this read?

## Assessing Quality using FastQC
n real life, you won’t be assessing the quality of your reads by visually inspecting your FASTQ files. Rather, you’ll be using a software program to assess read quality and filter out poor quality reads. We’ll first use a program called FastQC to visualize the quality of our reads. Later in our workflow, we’ll use another program to filter out poor quality reads.

FastQC has a number of features which can give you a quick impression of any problems your data may have, so you can take these issues into consideration before moving forward with your analyses. Rather than looking at quality scores for each individual read, FastQC looks at quality collectively across all reads within a sample. The image below shows one FastQC-generated plot that indicates a very high quality sample:

The x-axis displays the base position in the read, and the y-axis shows quality scores. In this example, the sample contains reads that are 40 bp long. This is much shorter than the reads we are working with in our workflow. For each position, there is a box-and-whisker plot showing the distribution of quality scores for all reads at that position. The horizontal red line indicates the median quality score and the yellow box shows the 2nd to 3rd quartile range. This means that 50% of reads have a quality score that falls within the range of the yellow box at that position. The whiskers show the range to the 1st and 4th quartile.

For each position in this sample, the quality values do not drop much lower than 32. This is a high quality score. The plot background is also color-coded to identify good (green), acceptable (yellow), and bad (red) quality scores.

Now let’s take a look at a quality plot on the other end of the spectrum.

Here, we see positions within the read in which the boxes span a much wider range. Also, quality scores drop quite low into the “bad” range, particularly on the tail end of the reads. The FastQC tool produces several other diagnostic plots to assess sample quality, in addition to the one plotted above.

## Running FastQC

FastQC can accept multiple file names as input, and on both zipped and unzipped files, so we can use the *.fastq* wildcard to run FastQC on all of the FASTQ files in this directory.

### Install Fastqc

```conda install -c bioconda fastqc```

### Execute
```fastqc sra/*.fastq```
For each input FASTQ file, FastQC has created a .zip file and a .html file. The .zip file extension indicates that this is actually a compressed set of multiple output files. We’ll be working with these output files soon. The .html file is a stable webpage displaying the summary report for each of our samples.

We want to keep our data files and our results files separate, so we will move these output files into a new directory within our results/ directory.
mkdir -p ~/dc_workshop/results/fastqc_untrimmed_reads 
 mv *.zip ~/dc_workshop/results/fastqc_untrimmed_reads/ 
 mv *.html ~/dc_workshop/results/fastqc_untrimmed_reads/

cd ~/dc_workshop/results/fastqc_untrimmed_reads/ 

### View the FastQC results
If we were working on our local computers, we’d be able to display each of these HTML files as a webpage:
open SRR2584863_1_fastqc.html

web browsers installed on it, so the remote computer doesn’t know how to open the file. We want to look at the webpage summary reports, so let’s transfer them to our local computers (i.e. your laptop).

To transfer a file from a remote server to our own machines, we will use scp, which we learned yesterday in the Shell Genomics lesson.

First we will make a new directory on our computer to store the HTML files we’re transfering. Let’s put it on our desktop for now. Open a new tab in your terminal program (you can use the pull down menu at the top of your screen or the Cmd+t keyboard shortcut) and type:

mkdir -p ~/Desktop/fastqc

scp dcuser@ec2-34-238-162-94.compute-1.amazonaws.com:~/dc_workshop/results/fastqc_untrimmed_reads/*.html ~/Desktop/fastqc_html

The second part starts with a : and then gives the absolute path of the files you want to transfer from your remote computer. Don’t forget the :. We used a wildcard (*.html) to indicate that we want all of the HTML files.

The third part of the command gives the absolute path of the location you want to put the files. This is on your local computer and is the directory we just created ~/Desktop/fastqc_html.

You should see a status output like this:
Now we can go to our new directory and open the HTML files.


### Decoding the other FastQC outputs
We’ve now looked at quite a few “Per base sequence quality” FastQC graphs, but there are nine other graphs that we haven’t talked about! Below we have provided a brief overview of interpretations for each of these plots. For more information, please see the FastQC documentation here

>**Per tile sequence quality:** the machines that perform sequencing are divided into tiles. This plot displays patterns in base quality along these tiles. Consistently low scores are often found around the edges, but hot spots can also occur in the middle if an air bubble was introduced at some point during the run.
>**Per sequence quality scores:** a density plot of quality for all reads at all positions. This plot shows what quality scores are most common.
>**Per base sequence content: ** plots the proportion of each base position over all of the reads. Typically, we expect to see each base roughly 25% of the time at each position, but this often fails at the beginning or end of the read due to quality or adapter content.
>**Per sequence GC content:** a density plot of average GC content in each of the reads.
>**Per base N content:** the percent of times that ‘N’ occurs at a position in all reads. If there is an increase at a particular position, this might indicate that something went wrong during sequencing.
>**Sequence Length Distribution:** the distribution of sequence lengths of all reads in the file. If the data is raw, there is often on sharp peak, however if the reads have been trimmed, there may be a distribution of shorter lengths.
>**Sequence Duplication Levels:** A distribution of duplicated sequences. In sequencing, we expect most reads to only occur once. If some sequences are occurring more than once, it might indicate enrichment bias (e.g. from PCR). If the samples are high coverage (or RNA-seq or amplicon), this might not be true.
>**Overrepresented sequences:** A list of sequences that occur more frequently than would be expected by chance.
>**Adapter Content:** a graph indicating where adapter sequences occur in the reads.
>**K-mer Content:** a graph showing any sequences which may show a positional bias within the reads.

### Working with the FastQC text output
Now that we’ve looked at our HTML reports to get a feel for the data, let’s look more closely at the other output files. Go back to the tab in your terminal program that is connected to your AWS instance (the tab label will start with dcuser@ip) and make sure you’re in our results subdirectory.


# Trimming and Filtering
## Cleaning Reads
We will use a program called Trimmomatic to filter poor quality reads and trim poor quality bases from our samples

### Trimmomatic Options
Trimmomatic has a variety of options to trim your reads. If we run the command, we can see some of our options

```conda install -c bioconda trimmomatic```

```trimmomatic```

This output shows us that we must first specify whether we have paired end (PE) or single end (SE) reads. Next, we specify what flag we would like to run. For example, you can specify threads to indicate the number of processors on your computer that you want Trimmomatic to use. These flags are not necessary, but they can give you more control over the command. The flags are followed by positional arguments, meaning the order in which you specify them is important. In paired end mode, Trimmomatic expects the two input files, and then the names of the output files. These files are described below.

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

The above tutorial is adopted from datacarpentry.org:

https://datacarpentry.org/wrangling-genomics/02-quality-control/index.html

https://datacarpentry.org/wrangling-genomics/03-trimming/index.html
