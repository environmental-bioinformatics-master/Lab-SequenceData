## Download sequences from SRA

#### Install sra-tools
We will download the raw data through conda

```module load anaconda```

Let's create a new conda environment called *downloading*, activate it and install sra-tools

```conda create --name downloading```

```conda activate downloading```

```conda install -c bioconda sra-tools```

#### Download fastq files based on the run number 
Now, we are ready to start the download using the fastq-dump command.
Use help to see the options
```fastq-dump -h```

The flag -O and -split-files and are commonly used. -O indicates the output directory.  --split-files will create two fastq files, one for R1 and other R2

For this example, I will download the file with the accession number SRR3735265

```fastq-dump  --split-files -O sra/ SRR3735265```


----------


#### Get accession list
Go to [NCBI](http://www.ncbi.nlm.nih.go/v) http://www.ncbi.nlm.nih.gov

![Alt text](/images/sra1.png)


Select "SRA"
![Alt text](/images/sra2.png)


Insert the accession number of the BioProject and "Search"
"Send results to Run selector"
![Alt text](/images/sra3.png)




![Alt text](./Screen Shot 2019-10-02 at 11.48.00 AM.png)


Select (or not) runs

Download the Accession List and the Run Info Table 
![Alt text](/images/accesion.png)
![Alt text](/images/list.png)

You can transfer files (e.g. the accession list) from your local machine to poseidon (and vice versa) by using scp ()scp *source* *destination* )
```scp path-to-file username@poseidon.whoi.edu:path-to-destination-folder```

> Modify the fast-dump command you used above to automate your downloads from SRA

