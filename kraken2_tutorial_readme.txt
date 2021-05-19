# Installation

We first need to install the Kraken2 docker into its own environment. From this, we also need to download a Kraken2 database, which is necessary to assign the taxonomic labels to their sequences. This is done below:

## Downloading Kraken2 with Conda:
```bash
  # If you have the conda package installer this can be done with the command below:
$ conda create --yes -n kraken kraken2 bracken
$ conda activate kraken
```

```bash
  # Picotte contains a Kraken2 executable, which we will use.
/ifs/groups/eces450650Grp/ containers/kraken2_latest.sif
```

The database is meant for kraken to use to assign the taxonomic labels to the data.

## Downloading Database
```bash
  # The Kraken2 database can be downloaded from the Kraken2 website:
$ curl -O ftp://ftp.ccb.jhu.edu/pub/data/kraken2_dbs/minikraken2_v2_8GB_201904_UPDATE.tgz

  # Unzip the file for its contents
$ tar -xvzf minikraken2_v2_8GB_201904_UPDATE.tgz
```

```bash
  # Picotte also contains a Kraken2 database, which we will use.
/ifs/groups/eces450650Grp/data/kraken2_db_plus_protozoa_fungi/k2_pluspf_20210127
```

# Running Kraken2 docker with singularity:
The kraken2 docker is compatible with singularity, so we will use it to run the data. 
(After) requesting a node to run on picotte: `srun --nodes=1 --ntasks=20 --cpus-per-task=1 --mem=120GB --time=00:30:00 --pty /bin/bash`

We will also need our database: `/ifs/groups/eces450650Grp/data/kraken2_db_plus_protozoa_fungi/k2_pluspf_20210127`
And the fastq formatted files: `/ifs/groups/eces450650Grp/data/mappings/evol1.sorted.unmapped.R2.fastq`, `/ifs/groups/eces450650Grp/data/mappings/evol1.sorted.unmapped.R1.fastq`

Our output is a kraken file: `evol1.kraken`

```bash
  # Running all parts together with singularity:
$ srun --nodes=1 --ntasks=20 --cpus-per-task=1 --mem=120GB --time=00:30:00 --pty /bin/bash
$ singularity run --bind /ifs/groups/eces450650Grp/ containers/kraken2_latest.sif
$ kraken2 --use-names --threads 4 --db ./data/kraken2_db_plus_protozoa_fungi/k2_pluspf_20210127 --fastq-input --paired ./data/mappings/evol1.sorted.unmapped.R1.fastq ./data/mappings/evol1.sorted.unmapped.R2.fastq > ./ECES450650_SP21/Tutorial8
ial8/evol1.kraken
```

## Additional Usage Notes:
To run our inference, we must include several flags:
* `--use-names` 
* `--db` Database reference file path
* `--fastq-input` We are using fastq formatted files
* `--paired` We are dealing with paired end data
* `--report FILE` This provides us with a sample-wide report 

We report additional usage notes in the presentation.

# Results Sample
`evol1.kraken` Sample from our classifications.
```
C	NS500207:12:H04WYAFXX:2:21109:17714:12546	Enterobacteriaceae (taxid 543)	149|33	543:28 91347:3 543:84 |:|
C	NS500207:12:H04WYAFXX:2:21109:17844:12588	Enterobacteriaceae (taxid 543)	98|18	543:64 |:|
U	NS500207:12:H04WYAFXX:2:21109:19073:12016	unclassified (taxid 0)	16|23	|:|
U	NS500207:12:H04WYAFXX:2:21109:19488:10369	unclassified (taxid 0)	15|19	|:|
```

`report` We can get a sample-wide report of all taxa found with the report flag.
```
 95.04	16815	16815	U	0	unclassified
  4.96	877	1	R	1	root
  4.93	872	0	R1	131567	  cellular organisms
  4.54	804	0	D	2	    Bacteria
  4.37	774	0	P	1224	      Proteobacteria
  4.35	769	0	C	1236	        Gammaproteobacteria
  4.31	763	0	O	91347	          Enterobacterales
  4.31	762	726	F	543	            Enterobacteriaceae
  0.14	25	0	G	561	              Escherichia
```

Check our powerpoint for information on understanding these classifications.

# Acknowledgements
* This tutorial instruction is adapted to a kraken2 container on Picotte from a previous version made by [Derrick Wood](https://github.com/DerrickWood/kraken2).