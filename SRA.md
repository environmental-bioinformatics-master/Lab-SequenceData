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

The flag -O and -split-files and are commonly used. -O indicates the output directory.  --split-files will create two fastq files, one for R1 and other R2 (only for **paired-end reads**)

For this example, I will download the file with the accession number SRR3735265

```fastq-dump  --split-files -O sra/ SRR3735265```

if you are trying to download files that contain one direction only reads (such as 454 or SE-single end illumnina reads) omit the "--split-files" flag

#### Download large fastq files
Downloads of data from large projects that contain numerous (and large) sequencing runs require a lot of time. For such downloads:

-go onto poseidon and navigate to your working folder

-start a tmux session

-start a slurm job: e.g. ```srun -p compute --time=24:00:00 --ntasks-per-node=1 --mem=8gb --pty bash```

-load anaconda

-activate your conda environment (I called mine downloading)

-use a for loop to download all the sequencing runs. I recommend modifying the command we learned above to produce compressed files: e.g. ```fastq-dump --split-files --gzip -O sra/ ${i}``` [${i} is your variable i.e. sra-id]



There are also ways to parallelize the dowload (e.g. https://github.com/rvalieris/parallel-fastq-dump)

-go onto poseidon and navigate to your working folder

-start a tmux session

-start a slurm job: e.g. ```srun -p compute --time=24:00:00 --ntasks-per-node=4 --mem=8gb --pty bash``` *in this case you request 4 cores*

-load anaconda

-activate your conda environment

-conda install **parallel-fastq-dump**

-use a for loop to download all the sequencing runs using parallel-fastq-dump and taking advantage of the 4 cores you requested e.g. ```parallel-fastq-dump --s ${i} --threads 4 -O sra/ --split-files --gzip```


----------


#### Get accession list
Go to [NCBI](http://www.ncbi.nlm.nih.go/v) http://www.ncbi.nlm.nih.gov

![Alt text](/images/sra1.png)


Select "SRA"
![Alt text](/images/sra2.png)


Insert the accession number of the BioProject and "Search"
"Send results to Run selector"
![Alt text](/images/sra3.png)




![Alt text](/images/sra4.png)


Select (or not) runs

Download the Accession List and the Run Info Table 
![Alt text](/images/accesion.png)
![Alt text](/images/list.png)

You can transfer files (e.g. the accession list) from your local machine to poseidon (and vice versa) by using scp ()scp *source* *destination* )
```scp path-to-file username@poseidon.whoi.edu:path-to-destination-folder```

> Modify the fast-dump command you used above to automate your downloads from SRA

