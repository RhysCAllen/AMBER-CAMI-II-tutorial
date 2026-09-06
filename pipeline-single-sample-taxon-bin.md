September 2nd, 2026

The following attempts to describe step by step how to use AMBER to analyze taxon bins from user-created assemblies of CAMI II challenge samples.
We're using information from https://github.com/CAMI-challenge/AMBER/tree/master and from the CAMI web portal pages. 

This pipeline uses Sample 0 from the CAMI II plant rhizosphere challenge. The reads were assembled with metaspades, and binned with MetaBat2.

This pipeline assumes the following:
   AMBER is installed locally  
   you have the following:  
      A) binned contigs (e.g.your MetaBat2 output)   
      B) The names.dmp and nodes.dmp files from the tax-to-accessions download from the CAMI 2019 snapshot:  
      https://cami-challenge.org/reference-databases/ --> https://openstack.cebitec.uni-bielefeld.de:8080/swift/v1/CAMI_2_DATABASES/ncbi_taxonomy_accession2taxid.tar   
      C) Classification of your bins in the form of NCBI taxids, specific to the Jan 2019 RefSeq snapshot provided with the CAMI II challenge.  

This pipeline will obtain or create the following additional files:  
D) CAMI reference gold standard bin mapping file (downloaded).  
E) Your own bin mapping file, created using AMBER repo scripts and also bbtools.   

## Procedure:   

###### Step 1: Obtain the gsa bin mapping file.  
   Download the gsa_mapping.binning file, which will be the gold-standard binning input file for amber.py:  
   https://cami-challenge.org/submit/ →
   https://zenodo.org/records/4982288 →
   https://zenodo.org/records/4982288/files/taxonomic_binning_cami2.tar.gz?download=1  

`curl -JLO https://zenodo.org/records/4982288/files/taxonomic_binning_cami2.tar.gz?download=1`    
`tar -xvf taxonomic_binning_cami2.tar.gz`

You will see three folders in the untarred download: retain the plant-rhizosphere directory. The other two can be deleted. 
    plant_associated_dataset
    strain_associated_dataset
    marine_associated_dataset

Locate the gsa_mapping.binning file in the plant_associated_dataset directory:

`cd plant_associated_dataset/ground_truth`
`tar -xvf rhizosphere_short_read_samples.binning.tar.gz`
Now we have access to the gold-standard read mapping file, `rhizosphere_short_read_sample_0.binning`.

This file will be the input for the -g argument to amber.py.

```
head rhizosphere_short_read_sample_0.binning 
@SampleID:rhimgCAMI2_short_read_sample_0
@@SEQUENCEID	BINID	TAXID	LENGTH
S0R0/1	Otu14.0	1144338	150
S0R0/2	Otu14.0	1144338	150
S0R1/1	LjRoot62	34073	150
```

###### Step 2: Convert your bins to CAMI biobox format using AMBER script.

usage: convert_fasta_bins_to_biobox_format.py [-h] [-o OUTPUT_FILE] paths [paths ...]

Navigate to the directory bins/ containing your bins, such as 

```
sample_0__METABAT2__P.1__bin.11.fa
sample_0__METABAT2__P.1__bin.12.fa
sample_0__METABAT2__P.1__bin.15.fa 
...
sample_0__METABAT2__P.1__bin.7.fa
```
Run script from AMBER repo:  

`convert_fasta_bins_to_biobox_format.py -o biobox-bins-out/sample_0_METABAT2.biobox.profile *.fa`  

This creates a biobox profile with two columns: SEQUENCEID (from fasta headers of the contigs in your bins) and BINID (from MetaBat bin names).

```
head sample_0_METABAT2.biobox.profile
#CAMI Format for Binning
@Version:0.9.0
@SampleID:_SAMPLEID_
@@SEQUENCEID	BINID
c_000000000245	sample_0__METABAT2__P.1__bin.11.fa
c_000000000293	sample_0__METABAT2__P.1__bin.11.fa
c_000000000324	sample_0__METABAT2__P.1__bin.11.fa
...
c_000000013423	sample_0__METABAT2__P.1__bin.7.fa
c_000000013463	sample_0__METABAT2__P.1__bin.7.fa
c_000000013680	sample_0__METABAT2__P.1__bin.7.fa
```

Make sure to edit the name of your sampleID from `_SAMPLEID_` to `rhimgCAMI2_short_read_sample_0`. Sample names can be found from https://cami-challenge.org/taxonomic_binning/   

###### Step 3: Create a read-to-contig mapping file from your bin sam file

The .sam file that was used to determine differential abundance for binning your assemblies is the input file here. 
`awk -v FS='\t' -v OFS='\t' '!/^@/ {print $1, $3}' sample_0.sam >> reads-to-contig.mapping.tsv`

Your output will contain the SEQUENCEID column necessary AMBER to compare your results to the gold standard, and which contigs these correspond to.
The BH tags are added by BayesHammer, they will be removed in the next step. 

```
head reads-to-contig.mapping.tsv 
S0R16554400/1 BH:failed	c_000000131573
S0R16554448/2 BH:changed:10	c_000000004317
S0R16554483/2 BH:changed:5	c_000000057414
S0R16555152/2 BH:failed	c_000000223269
S0R16555191/1 BH:failed	c_000000267876
...
```

###### Step 4: Combine previous results with your taxonomy using a custom script, such as xxx

The format of your taxonomy file may vary. Shown here is the kraken-style report from a sourmash tax metagenome output. 
Feel free to modify the script to fit your input file formats. 

Input files:
reads-to-contig.mapping.tsv
sample_0_METABAT2.biobox.profile
taxonomy.report such as bin.11.kreport.txt




