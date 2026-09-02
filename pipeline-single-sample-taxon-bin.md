September 2nd, 2026

The following attempts to describe step by step how to use AMBER to analyze taxon bins from user-created assemblies of CAMI II challenge samples..

This example uses Sample 0 from the CAMI II plant rhizosphere challenge. The reads were assembled with metaspades, and binned with MetaBat2.

This procedure assumes you have the following files:

A) binned contigs (MetaBat2 output), B) A mapping file of CAMI II reads to the contigs. The reads use CAMI II challenge sequence identifiers. For this challenge, the read identifiers look like this: S0/1 S0/2 .... S09255736547/1 S03903583058/2

C) Classification of the bins in the form of NCBI taxids specific to the Jan 2019 RefSeq snapshot provided with the CAMI II challenge.

Procedure:

1) Download the gsa_mapping.binning file, which will be the gold-standard binning input file for amber.py:  
   https://cami-challenge.org/submit/ →
   https://zenodo.org/records/4982288 →
   https://zenodo.org/records/4982288/files/taxonomic_binning_cami2.tar.gz?download=1  

`curl -JLO https://zenodo.org/records/4982288/files/taxonomic_binning_cami2.tar.gz?download=1`    
`tar -xvf taxonomic_binning_cami2.tar.gz`

You will see three folders in the untarred download: retain the plant-rhizosphere directory. The other two can be deleted. 
    plant_associated_dataset
    strain_associated_dataset
    marine_associated_dataset

2) Locate the gsa_mapping.binning file in the plant_associated_dataset directory:

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

3) 
