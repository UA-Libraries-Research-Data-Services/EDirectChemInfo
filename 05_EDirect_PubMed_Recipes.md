# PubMed EDirect Recipes

**Notes**

> 1. `user@computer:~$` represents an example terminal prompt name. Actual command/argument input is after the `$`.
> 2. Replace `name@xx.edu` with your email address.
> 3. `\` followed by `>` on the next line represents continued terminal input. You will need to delete the `>` symbol in order to run the scripts as a copy/paste into terminal.
> 4. You should validate your own EDirect scripts and results as there may be unintentional mistakes in these recipes. A convenient method is to compare your EDirect results to the NCBI Web interface search results: [https://www.ncbi.nlm.nih.gov/](https://www.ncbi.nlm.nih.gov/).

### PubMed

**Description:** Search PubMed by Keyword and/or MeSH and Retrieve References

We can use the EDirect function `esearch` to query PubMed. However, before trying to retrieve any of the results with `efetch`, it is a good idea to check that the count range is manageable (e.g., on the order of several thousand). In addition, using the `-log` option allows you to see exactly how your query is interpreted in PubMed:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "hydrogel-based drug delivery" -log
...
sleeping 1/3 second
<ENTREZ_DIRECT>
  <Db>pubmed</Db>
  <WebEnv>NCID_1_...</WebEnv>
  <QueryKey>1</QueryKey>
  <Count>395</Count>
  <Step>1</Step>
  <Email>name@xx.edu</Email>
  <Log>Y</Log>
</ENTREZ_DIRECT>
hydrogel-based[All Fields] AND ("drug delivery systems"[MeSH Terms] OR ("drug"[All Fields] AND "delivery"[All Fields] AND "systems"[All Fields]) OR "drug delivery systems"[All Fields] OR ("drug"[All Fields] AND "delivery"[All Fields]) OR "drug delivery"[All Fields])
```

After deciding the `esearch` query is appropriate, we can start to pipe the `esearch` results into other EDirect functions. For example, the below script first uses `esearch` to query PubMed for "hydrogel-based drug delivery", and then these results are piped (`|`) to `efetch` to retrieve the results as XML format. The `efetch` results are then piped to the `xtract` function where several bibliographic elements of the PubMed XML records are extracted into a table:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "hydrogel-based drug delivery" | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ArticleTitle ISOAbbreviation PubDate/Year Volume Issue MedlinePgn
32575787	Liu	J	Fabrication of 3D-Printed Fish-Gelatin-Based Polymer Hydrogel Patches for Local Delivery of PEGylated Liposomal Doxorubicin.	Mar Drugs	2020	18	6
32564841	Forero-Doria	O	Supramolecular hydrogels based on cellulose for sustained release of therapeutic substances with antimicrobial and wound healing properties.	Carbohydr Polym	2020	242	116383
32522716	Johnson	KA	Drug-impregnated, pressurized gas expanded liquid-processed alginate hydrogel scaffolds for accelerated burn wound healing.	Acta Biomater	2020
32521459	Radwan-Pragłowska	J	ZnO nanorods functionalized with chitosan hydrogels crosslinked with azelaic acid for transdermal drug delivery.Colloids Surf B Biointerfaces	2020	194	111170
32435134	Sheikh	FA	Linseed hydrogel based floating drug delivery system for fluoroquinolone antibiotics: Design, in vitro drug release and in vivo real-time floating detection.	Saudi Pharm J	2020	28	5	538-549
32397180	Askari	E	Stimuli-Responsive Hydrogels for Local Post-Surgical Drug Delivery.	Gels	2020	6	2
32364331	Zhou	X	Biodegradable β-Cyclodextrin Conjugated Gelatin Methacryloyl Microneedle for Delivery of Water-Insoluble Drug.	Adv Healthc Mater	2020	9	11	e2000527
32310635	Wu	M	Injectable and Self-Healing Nanocomposite Hydrogels with Ultrasensitive pH-Responsiveness and Tunable Mechanical Properties: Implications for Controlled Drug Delivery.	Biomacromolecules	2020	21	62409-2420
32298719	Jahanban-Esfahlan	R	A bio-inspired magnetic natural hydrogel containing gelatin and alginate as a drug delivery system for cancer chemotherapy.	Int. J. Biol. Macromol.	2020	156	438-445
32294955	Sun	HCM	Magnetically Powered Biodegradable Microswimmers.	Micromachines (Basel)	2020	11	4
32250466	Bahmanpour	A	Synthesis and characterization of thermosensitive hydrogel based on quaternized chitosan for intranasal delivery of insulin.	Biotechnol. Appl. Biochem.	2020
...
```

_tested on 2020.06.28, total count was 395._

Similarly to the above script, we can specify particular fields to query within PubMed. The below script searches for "ionic liquids" in the MeSH term field (`[MESH]`) and "imidazolium" in all fields. Note that the internal quotes are escaped (`\`), which is sometimes necessary for the query to be interpreted correctly when using phrases.


```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "\"ionic liquids\"[MESH] AND imidazolium" | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ISOAbbreviation PubDate/Year Volume Issue MedlinePgn
32383660	Dos Santos	LM	J. Environ. Manage.	2020	268	110340
32322841	Garcia	IM	J Adhes Dent	2020	22	2	207-214
32171965	Deng	Y	Ecotoxicol. Environ. Saf.	2020	194	110392
32163446	Young	GR	PLoS ONE	2020	15	3	e0229745
32000025	Jin	M	Environ. Pollut.	2020	260	114013
31959458	Fan	C	J Chromatogr A	2020	1618	460872
31906030	Tripathi	T	Int J Environ Res Public Health	2019	17	1
31892100	Li	HY	Molecules	2019	25	1
31726585	Xu	Y	Chemosphere	2020	240	124919
31726264	Patil	V	Chemosphere	2020	243	125302
31704621	Raoufi	A	J. Chromatogr. B Analyt. Technol. Biomed. Life Sci.	2019	1132	121823
31683728	Lu	Y	Molecules	2019	24	21
31677507	Habibul	N	Chemosphere	2020	242	125228
31675504	Gomez-Herrero	E	Ecotoxicol. Environ. Saf.	2020	187	109836
...
```

_tested on 2020.06.28, total count was 970._


**Description:** Calculate the Most Frequent Journal Titles For a PubMed Search

The below script uses `esearch` to query PubMed for "Artificial Intelligence" in the `[MESH]` field and "drug discovery" in the `[ALL]` field. The records are then retrieved as XML format using the `efetch` function, followed by extracting out the journal names (`IsoAbbreviation`) using `xtract`. The `xtract` results are then piped to the EDirect alias function `sort-uniq-count-rank`, which sorts the data by highest frequency:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "\"Artificial Intelligence\"[MESH] AND \"drug discovery\"[ALL]" | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element ISOAbbreviation | \
> sort-uniq-count-rank
138	J Chem Inf Model
50	BMC Bioinformatics
43	PLoS ONE
38	Methods Mol. Biol.
34	Bioinformatics
32	Mol. Pharm.
28	Drug Discov. Today
26	Molecules
24	J. Comput. Aided Mol. Des.
23	Expert Opin Drug Discov
22	Int J Mol Sci
18	Mol Inform
17	Sci Rep
16	Curr Top Med Chem
16	Future Med Chem
14	IEEE/ACM Trans Comput Biol Bioinform
14	Nature
14	Nucleic Acids Res.
14	PLoS Comput. Biol.
13	Assay Drug Dev Technol
13	Comb. Chem. High Throughput Screen.
12	Chem Biol Drug Des
11	Mol. Divers.
10	Comput Biol Chem
10	J Biomed Inform
9	Conf Proc IEEE Eng Med Biol Soc
9	J. Med. Chem.
8	Brief. Bioinformatics
8	ChemMedChem
8	Curr Opin Drug Discov Devel
8	J. Mol. Graph. Model.
...
```

**Description:** Calculate The Frequency of Author Publications for a University Department in PubMed

The below script uses `esearch` to query PubMed for ("university of alabama" AND tuscaloosa) in the affiliation field (`[AFFL]`). Tuscaloosa was added to limit the number of retrieved records associated with only The University of Alabama at Birmingham and The University of Alabama at Huntsville. Another approach could have been to use the NOT operator: `"(university of alabama[AFFL]) NOT (birmingham[AFFL] OR huntsville[AFFL])"`. However, the latter approach may eliminate any collaborative articles with these institutions (affiliation searches are challenging). Next, the results were retrieved as XML using `efetch`, followed by piping these results to `xtract` to extract out the publication year (`PubDate/Year`) and sort by frequency with `sort-uniq-count-rank`. Note that a conditional statement was used in the `xtract` pattern to only extract results from articles if the affiliation contains both `chemistry` and `tuscaloosa`. The thought here was that this would limit the results (mostly) to author publications from The University of Alabama (Tuscaloosa) Department of Chemistry & Biochemistry:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "(university of alabama[AFFL] AND tuscaloosa[AFFL])" | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -if Affiliation -contains chemistry -and Affiliation -contains tuscaloosa -element PubDate/Year | \
> sort-uniq-count-rank
65	2015
59	2017
52	2018
48	2019
45	2016
42	2020
41	2014
35	2013
30	2012
28	2008
26	2006
23	2007
23	2010
20	2004
19	2003
18	2009
17	1999
17	2001
16	2000
15	2002
15	2011
14	2005
9	1998
8	1997
6	1992
6	1996
5	1988
4	1990
4	1994
4	1995
2	1991
2	1993
1	1974
1	1987
1	1989
```

_tested on 2020.06.26, total count was 35._

If instead we want to know individual Author numbers instead of total publications by year, we can change the `xtract` pattern:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "(university of alabama[AFFL] AND tuscaloosa[AFFL])" | \
> efetch -format xml | \
> xtract -pattern Author -if Affiliation -contains chemistry -and Affiliation -contains tuscaloosa -element LastName Initials | \
> sort-uniq-count-rank
100	Dixon	DA
49	Vasiliu	M
33	Vincent	JB
30	Rogers	RD
21	Bowman	MK
20	Fang	Z
14	Grant	DJ
14	Thanthiriwatte	KS
13	Cassady	CJ
11	Chen	M
11	Shamshina	JL
10	Frantom	PA
10	Kelley	SP
10	Metzger	RM
10	Papish	ET
9	Gerlach	DL
9	Kispert	LD
9	Li	S
9	Timkovich	R
8	Matus	MH
7	Gurau	G
7	Gutowski	KE
7	Jackson	VE
7	Qu	F
6	Hester	TH
6	McCrary	PD
6	Nguyen	MT
6	Zhang	S
5	Aqad	E
5	Arduengo	AJ
5	Bonizzoni	M
5	Chen	Y
...
```
_tested on 2020.06.26, total count was 387._


Let's take a closer look at the conditional `xtract` pattern specifying to extract data only if the affiliation contains chemistry and tuscaloosa:


```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "(university of alabama[AFFL] AND tuscaloosa[AFFL])" | \
> efetch -format xml | \
> xtract -pattern Author -if Affiliation -contains chemistry -and Affiliation -contains tuscaloosa -element LastName Initials Affiliation
Bowman	M	N.N. Vorozhtsov Institute of Organic Chemistry SB RAS, 630090 Novosibirsk, Russia.	Department of Chemistry & Biochemistry, University of Alabama, Tuscaloosa, AL 35487-0336, USA.
Hu	Y	Department of Chemistry and Biochemistry, The University of Alabama, Shelby Hall, Box 870336, Tuscaloosa, Alabama 35487-0336, United States.
Persaud	RR	Department of Chemistry and Biochemistry, The University of Alabama, Shelby Hall, Box 870336, Tuscaloosa, Alabama 35487-0336, United States.
Vasiliu	M	Department of Chemistry and Biochemistry, The University of Alabama, Shelby Hall, Box 870336, Tuscaloosa, Alabama 35487-0336, United States.
Dixon	DA	Department of Chemistry and Biochemistry, The University of Alabama, Shelby Hall, Box 870336, Tuscaloosa, Alabama 35487-0336, United States.
White	PE	Department of Chemistry and Biochemistry, The University of Alabama, Tuscaloosa, AL, 35487-0336, USA.
Vincent	JB	Department of Chemistry and Biochemistry, The University of Alabama, Tuscaloosa, AL, 35487-0336, USA.
Vasiliu	M	Department of Chemistry and Biochemistry, The University of Alabama, Shelby Hall, Box 870336, Tuscaloosa, Alabama 35487-0336, United States.
Dixon	DA	Department of Chemistry and Biochemistry, The University of Alabama, Shelby Hall, Box 870336, Tuscaloosa, Alabama 35487-0336, United States.
Ihde	MH	Department of Chemistry and Biochemistry, The University of Alabama, P.O. Box 870336, Tuscaloosa, Alabama 35487, United States.
Bonizzoni	M	Department of Chemistry and Biochemistry, The University of Alabama, P.O. Box 870336, Tuscaloosa, Alabama 35487, United States.	The Alabama Water Institute, P.O. Box 870206, Tuscaloosa, Alabama 35487, United States.
Pierce	BS	Department of Chemistry and Biochemistry, University of Alabama, 250 Hackberry Lane, Box 870336 Tuscaloosa, AL 35487.
Yadav	J	Department of Chemistry and Biochemistry, The University of Alabama, Tuscaloosa, Alabama 35487-0336, United States.
Liang	Q	Department of Chemistry and Biochemistry, The University of Alabama, Tuscaloosa, Alabama 35487-0336, United States.
Pan	S	Department of Chemistry and Biochemistry, The University of Alabama, Tuscaloosa, Alabama 35487-0336, United States.
Vasiliu	M	Department Chemistry, The University of Alabama, Tuscaloosa, AL, 35487, USA.
Dixon	DA	Department Chemistry, The University of Alabama, Tuscaloosa, AL, 35487, USA.
...
```
_tested on 2020.06.26, total count was 1063._


With a quick look at the ~1000 results, it seemed like we extracted out the intended data, however, I did notice a couple of false positive results. One example was an article from The University of Alabama (Tuscaloosa) Department of Biological Sciences with an external collaborator having "Chemistry" in the Institution name. Other errors could be what we unintentionally excluded such as any records that do not have Tuscaloosa in the affiliation field (i.e., only a partial address or zip code). These type of affiliation searches are tricky, so test often and think through the results carefully.


**Description:** Retrieve Cites and Cited References in PubMed

The `elink` function can retrieve associated cites and cited references for PubMed records. Cites are the available references in the article (i.e. bibliography list) and cited are references to the article. Not all PubMed articles have associated citation reference data. The available reference data are from the [NIH Open Citation Collection Dataset](https://pubmed.ncbi.nlm.nih.gov/31600197/).

To retrieve the number of cites for a PubMed article, we can use the `elink` function, followed by `xtract` to extract out the Count element:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "29978703[PMID]" | \
> elink -cites | \
> xtract -pattern ENTREZ_DIRECT -element Count
11
```

Add `efetch` to your script if you want to retrieve the records:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "29978703[PMID]" | \
> elink -cites | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ISOAbbreviation PubDate/Year Volume Issue MedlinePgn
29382051	Stefanachi	A	Molecules	2018	23	2
27709885	Stempel	E	Acc. Chem. Res.	2016	49	11	2390-2402
26661053	James	MJ	Chemistry	2016	22	9	2856-81
26313158	Liu	BY	Org. Lett.	2015	17	17	4380-3
22969063	Han	X	Angew. Chem. Int. Ed. Engl.	2012	51	41	10390-3
18620434	Martin	R	Acc. Chem. Res.	2008	41	11	1461-73
17134268	Liu	KG	Org. Lett.	2006	8	25	5769-71
11878938	Birman	VB	J. Am. Chem. Soc.	2002	124	10	2080-1
10918363	Concannon	S	Rapid Commun. Mass Spectrom.	2000	14	14	1157-66
29711506	Boymond	L	Angew. Chem. Int. Ed. Engl.	1998	37	12	1701-1703
9364778	Estévez-Braun	A	Nat Prod Rep	1997	14	5	465-75
```


Getting the cited records only requires changing `-cites` to `-cited`:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "29978703[PMID]" | \
> elink -cited | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ISOAbbreviation PubDate/Year Volume Issue MedlinePgn
32317969	Lautié	E	Front Pharmacol	2020	11	397
30707497	Ivanova	OA	Chem Rec	2019
30259622	Tymann	D	Angew. Chem. Int. Ed. Engl.	2018	57	47	15553-15557
```

We can answer some interesting questions with the NIH Open Citation Collection Data. For example, I noticed that the PubMed XML records for articles in *J Cheminform* contain a reference list for articles in PubMed. So, theoretically, if we query PubMed for *J Cheminform*, extract out all of the references, and sort these by frequency, we should get the most cited articles in *J Cheminform* article bibliographies (caveat: in the available PubMed citation data).

In the below script, the `xtract` pattern creates a new line for each extracted reference citation PMID (`ArticleId IdType="pubmed"`):

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "J Cheminform[JOUR]" | \
> efetch -format xml | \
> xtract -pattern Reference -element ArticleId IdType="pubmed" | \
> sort-uniq-count-rank
70	21948594
69	21982300
66	20426451
61	12653513
42	11259830
38	16796559
36	10592235
32	26400175
31	17154509
29	19498078
...
```

_tested on 2020.06.26, total count was 8731._

We can take a quick look at these top 10 cited references using a for loop::

```console
user@computer:~$ for refs in \
>     "21948594" \
>     "21982300" \
>     "20426451" \
>     "12653513" \
>     "11259830" \
>     "16796559" \
>     "10592235" \
>     "26400175" \
>     "17154509" \
>     "19498078"
> do
>     esearch -email name@xx.edu -db pubmed -query "$refs[PMID]" |
>     efetch -format xml |
>     xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
>     Author/Initials ArticleTitle ISOAbbreviation PubDate/Year Volume Issue MedlinePgn    
>     sleep 1 
> done
21948594	Gaulton	A	ChEMBL: a large-scale bioactivity database for drug discovery.	Nucleic Acids Res.	2012	40	Database issue	D1100-7
21982300	O'Boyle	NM	Open Babel: An open chemical toolbox.	J Cheminform	2011	3	33
20426451	Rogers	D	Extended-connectivity fingerprints.	J Chem Inf Model	2010	50	5	742-54
12653513	Steinbeck	C	The Chemistry Development Kit (CDK): an open-source Java library for Chemo- and Bioinformatics.	J Chem Inf Comput Sci	43	2	493-500
11259830	Lipinski	CA	Experimental and computational approaches to estimate solubility and permeability in drug discovery and development settings.	Adv. Drug Deliv. Rev.	2001	46	1-3	3-26
16796559	Steinbeck	C	Recent developments of the chemistry development kit (CDK) - an open-source java library for chemo- and bioinformatics.	Curr. Pharm. Des.	2006	12	17	2111-20
10592235	Berman	HM	The Protein Data Bank.	Nucleic Acids Res.	2000	28	1	235-42
26400175	Kim	S	PubChem Substance and Compound databases.	Nucleic Acids Res.	2016	44	D1	D1202-13
17154509	Huang	N	Benchmarking sets for molecular docking.	J. Med. Chem.	2006	49	23	6789-801
19498078	Wang	Y	PubChem: a public information system for analyzing bioactivities of small molecules.	Nucleic Acids Res.	2009	37	Web Server issue	W623-33
```

Another interesting question would be what is the most cited Journal in *J Cheminform* articles (in the available PubMed citation data)? In the below script, we take a similar approach to above, but instead of extracting out the PMIDs, we extract out the Citation element. The line `cut -d "." -f 1` deletes any data after the Journal abbreviation (e.g., "Drug Discov Today. 2006 Dec;11(23-24):1046-53" becomes "Drug Discov Today"):

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "J Cheminform[JOUR]" | \
> efetch -format xml | \
> xtract -pattern Reference -element Citation | \
> cut -d "." -f 1 | \
> sort-uniq-count-rank
1808	J Chem Inf Model
981	J Cheminform
946	Nucleic Acids Res
771	J Med Chem
512	Bioinformatics
502	J Chem Inf Comput Sci
367	J Comput Aided Mol Des
333	BMC Bioinformatics
282	Drug Discov Today
215	J Comput Chem
...
```
_tested on 2020.06.28, total count was 1037 unique Journals._


**Description:** Number of Records in PubMed by Create Date

Here is an interesting script to retrieve the count of PubMed records by create date (`[CRDT]`) for each month of 2020. Since there are over 100,000 records added to PubMed every month, a strategy using `efetch` likely would not work (i.e., trying to retrieve 500,000+ records would take a long time).

```console

user@computer:~$ for date in \
>    "2020/01" \
>    "2020/02" \
>    "2020/03" \
>    "2020/04" \
>    "2020/05" \
>    "2020/06" 
> do
>    esearch -email name@xx.edu -db pubmed -query "$date[CRDT]" |
>    xtract -pattern ENTREZ_DIRECT -lbl "$date" -element Count
>    sleep 1 
> done
2020/01	109064
2020/02	107836
2020/03	106776
2020/04	125067
2020/05	121981
2020/06	111400
```

**Description:** Find Number of Records in PubMed that are Also freely Available in PubMed Central

Let's say we wanted to know how many articles in *J Chem Inf Model* (indexed in PubMed) are available freely in PubMed Central. We can first get a count for *J Chem Inf Model* records in PubMed by querying PubMed in the Journal field (`[JOUR]`), followed by retrieving the records, extracting out the PubDate, and then sorting by frequency:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "J Chem Inf Model[JOUR]" | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element PubDate | \
> cut -d " " -f 1 | \
> sort-uniq-count-rank | \
> sort -k2
216	2005
280	2006
246	2007
225	2008
268	2009
203	2010
297	2011
306	2012
300	2013
309	2014
247	2015
232	2016
283	2017
237	2018
492	2019
370	2020
```
_tested on 2020.06.28, total count was 4511._

In the above script, the line `cut -d " " -f 1` deletes any data appearing after the year and `sort -k2` sorts the data by the second column. Next, we can add `elink` into our script to find the linked records in PubMed Central (`pmc`) from the Entrez link `pubmed_pmc`:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "J Chem Inf Model[JOUR]" | \
> elink -target pmc -name pubmed_pmc | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element PubDate | \
> cut -d " " -f 1 | \
> sort-uniq-count-rank | \
> sort -k2
1	2005
3	2006
7	2007
14	2008
31	2009
26	2010
62	2011
37	2012
55	2013
59	2014
36	2015
32	2016
39	2017
43	2018
47	2019
19	2020
```

Note that if you have a query returning tens of thousands of results, you would likely want to use a strategy without `efetch`, such as adding a date into your `esearch` query, followed by extracting out the count element from the XML.


