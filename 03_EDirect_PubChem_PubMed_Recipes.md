# PubChem <--> PubMed EDirect Recipes

**Notes**

> 1. Replace `name@xx.edu` with your email address.
> 2. `user@computer:~$` represents an example terminal prompt name. Command/argument input is after the `$`.
> 3. `\` followed by `>` on the next line represents continued terminal input.

### Retrieve Associated PubMed References from a PubChem Compound Search

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query 174076[uid] | \
> elink -target pubmed -name pccompound_pubmed | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ISOAbbreviation PubDate/Year Volume Issue MedlinePgn
22957575	Gabl	S	J Chem Phys	2012	137	9	094501
22868451	Zhang	Y	Phys Chem Chem Phys	2012	14	35	12157-64
22859056	Malberg	F	Phys Chem Chem Phys	2012	14	35	12079-82
22852554	Zhang	Y	J Phys Chem B	2012	116	33	10036-48
22662183	Zhang	BB	PLoS ONE	2012	7	5	e37641
22567554	Guolin	H	J Anal Methods Chem	2012	2012	302059
22531153	Fowler	DA	Chem. Commun. (Camb.)	2012	48	43	5262-4
...
```

We can also filter these results with `efilter` to only include PubMed references linked to the CID but also matching a PubMed query. For example, if we were only interested in references from the journal _Phys Chem Chem Phys_:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query 174076[uid] | \
> elink -target pubmed -name pccompound_pubmed | \
> efilter -query "Phys Chem Chem Phys"[JOUR] | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ISOAbbreviation PubDate/Year Volume Issue MedlinePgn
22868451	Zhang	Y	Phys Chem Chem Phys	2012	14	35	12157-64
22859056	Malberg	F	Phys Chem Chem Phys	2012	14	35	12079-82
22451012	Sillars	FB	Phys Chem Chem Phys	2012	14	17	6094-100
21643581	Pensado	AS	Phys Chem Chem Phys	2011	13	30	13518-26
21643580	Schröder	C	Phys Chem Chem Phys	2011	13	26	12240-8
21445427	Chaban	VV	Phys Chem Chem Phys	2011	13	17	7910-20
...
```

One of my favorite searches is to find related PubMed literature that is specifically about chemical synthesis. Similarly to the search above, we can filter out references containing 'chemical synthesis' as a MeSH subheading [SUBH]:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query 94257[uid] | \
> elink -target pubmed -name pccompound_pubmed_mesh | \
> efilter -query "chemical synthesis"[SUBH] | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID ArticleTitle \
> ISOAbbreviation PubDate/Year
28463562	Enantioselective Chemical Syntheses of the Furanosteroids (-)-Viridin and (-)-Viridiol.	J. Am. Chem. Soc.	2017
23040731	Viridin analogs derived from steroidal building blocks.	Bioorg. Med. Chem. Lett.	2012
22849426	Synthetic studies on furanosteroids: construction of the viridin core structure via Diels-Alder/retro-Diels-Alder and vinylogous Mukaiyama aldol-type reaction.	J. Org. Chem.	2012
19644878	Abrogation of antibody-induced arthritis in mice by a self-activating viridin prodrug and association with impaired neutrophil and endothelial cell function.	Arthritis Rheum.	2009
19572524	Pentacyclic furanosteroids: the synthesis of potential kinase inhibitors related to viridin and wortmannolone.	J. Org. Chem.	2009
18630894	Slow self-activation enhances the potency of viridin prodrugs.	J. Med. Chem.	2008
15227545	Synthesis and biological evaluation of synthetic viridins derived from C(20)-heteroalkylation of the steroidal PI-3-kinase inhibitor wortmannin.	Org. Biomol. Chem.	2004
15065284	Synthesis of the furanosteroidal antibiotic viridin.	Angew. Chem. Int. Ed. Engl.	2004
```

You may have noticed that in the above search, I found related PubMed literature via MeSH instead of related PubMed Citations (pccompound_pubmed_mesh vs. pccompound_pubmed). It is a little tricky, but you can actually combine these queries (w/ OR operator) if you would like to retrieve PubMed literature related to a PubChem CID for both the pccompound_pubmed_mesh and pccompound_pubmed Entrez links in one dataset:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query 174076[uid] | \
> elink -target pubmed -name pccompound_pubmed -label pubmed_cit | \
> esearch -email name@xx.edu -db pccompound -query 174076[uid] | \
> elink -target pubmed -name pccompound_pubmed_mesh -label pubmed_mesh_cit | \
> esearch -query "(#pubmed_cit) OR (#pubmed_mesh_cit)" | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ISOAbbreviation PubDate/Year Volume Issue MedlinePgn
31818016	Wang	F	Int J Mol Sci	2019	20	24
31814059	Weber	AL	Orig Life Evol Biosph	2019	49	4	199-211
31675504	Gomez-Herrero	E	Ecotoxicol. Environ. Saf.	2020	187	109836
31520950	Pal	S	Ecotoxicol. Environ. Saf.	2019	184	109634
31500119	Ossowicz	P	Molecules	2019	24	18
31493157	Liu	H	Appl. Biochem. Biotechnol.	2020	190	3	826-838
31428944	Reed	KB	J. Ind. Microbiol. Biotechnol.	2019	46	12	1715-1724
31313573	Kim	SY	ACS Appl Mater Interfaces	2019	11	32	29350-29359
31170486	Domene-López	D	Int. J. Biol. Macromol.	2019	135	845-854
31121553	A	S	Ecotoxicol. Environ. Saf.	2019	180	466-472
...
```

### Retrieve Associated PubMed References from a PubChem Substance Data Source Depositor

### Retrieve Associated PubChem Compounds from a PubMed Search

### Retrieve Associated PubChem Compounds from a list of PubMed IDs (i.e., keep track of the individual links)
