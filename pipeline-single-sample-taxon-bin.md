September 2nd, 2026

The following attempts to describe step by step how to use AMBER to analyze taxon bins from user-created assemblies of CAMI II challenge samples.
We're using information from https://github.com/CAMI-challenge/AMBER/tree/master and from the CAMI web portal pages. 

This example uses Sample 0 from the CAMI II plant rhizosphere challenge. The reads were assembled with metaspades, and binned with MetaBat2.

This procedure assumes you are starting with the following files:

A) binned contigs (e.g.MetaBat2 output)   
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

###### Step 2: Convert your MetaBat bins to biobox format using AMBER script.


3) Use convert_fasta_bins_to_biobox_format.py to format your bins:

 usage: convert_fasta_bins_to_biobox_format.py [-h] [-o OUTPUT_FILE] paths [paths ...]

