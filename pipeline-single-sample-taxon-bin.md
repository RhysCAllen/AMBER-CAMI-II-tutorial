September 2nd, 2026

The following attempts to describe step by step how to use AMBER to analyze taxon bins from user-created assemblies of CAMI II challenge samples..

This example uses Sample 0 from the CAMI II plant rhizosphere challenge. The reads were assembled with metaspades, and binned with MetaBat2.

This procedure assumes you have the following files:

A) binned contigs (MetaBat2 output), B) A mapping file of CAMI II reads to the contigs. The reads use CAMI II challenge sequence identifiers. For this challenge, the read identifiers look like this: S0/1 S0/2 .... S09255736547/1 S03903583058/2

C) Classification of the bins in the form of NCBI taxids specific to the Jan 2019 RefSeq snapshot provided with the CAMI II challenge.

Procedure:

    Download the gsa_mapping.binning file, which will be the gold-standard binning input file for amber.py.. https://cami-challenge.org/submit/ → https://zenodo.org/records/4982288 → https://zenodo.org/records/4982288/files/taxonomic_binning_cami2.tar.gz?download=1 curl -JLO https://zenodo.org/records/4982288/files/taxonomic_binning_cami2.tar.gz?download=1 tar -xvf taxonomic_binning_cami2.tar.gz rm taxonomic_binning_cami2.tar.gz

You will see three folders in the untarred download: retain the plant-rhizosphere directory.
