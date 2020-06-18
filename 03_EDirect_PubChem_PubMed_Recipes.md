# PubChem <--> PubChem BioAssay <--> PubMed EDirect Recipes

**Notes**

> 1. Replace `name@xx.edu` with your email address.
> 2. `user@computer:~$` represents an example terminal prompt name. Command/argument input is after the `$`.
> 3. `\` followed by `>` on the next line represents continued terminal input.
> 4. You should validate your own EDirect scripts and results as there may be unintentional mistakes in these recipes!

### PubChem

**PubChem Compound --> PubMed Citations**\
**Description: Search for a CID in the PubChem Compound Database and retrieve related PubMed citations**

In the below script, we use the `esearch` function to query the PubChem Compound database (`pccompound`) for CID 174076 within the Compound ID field, `[uid]`. The `esearch` results are then piped to `elink` finding related PubMed citations via the Entrez link `pccompound_pubmed`. Finally, we retrieve the results with `efetch` in xml format and extract out some bibliographic reference information using the `xtract` function.

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
_tested on 2020.06.17, total count was 102._

**PubChem Compound --> PubMed Citations (with filtering)**\
**Description: Search for CID in PubChem Compound Database, find related PubMed citations, then only retrieve references from a specific journal.**

We can filter `elink` results with `efilter` to only include PubMed citations (Entrez linked via `pccompound_pubmed`) to the CID but also matching a specific PubMed query. For example, if we are only interested in linked _Phys Chem Chem Phys_ references to CID 174076, we can use the journal field [JOUR] in an `efilter` query:

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
_tested on 2020.06.17, total count was 11._

**PubChem Compound --> PubMed MeSH (with filtering)**\
**Description: Search for a CID in PubChem Compound, find related PubMed records via MeSH, and retrieve only references that contain the MeSH subheading "chemical synthesis".**

This is my favorite literature search: start with a PubChem CID and then find PubMed literature related to its synthesis. Similarly to the search above, we can filter out references using an `efilter` query for 'chemical synthesis' as a MeSH subheading [SUBH]. Note that we used the `pccompound_pubmed_mesh` Entrez link as the `elink` target name here.

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
_tested on 2020.06.17, total count was 8._

**PubChem Compound --> PubMed Citations OR PubMed MeSH**\
**Description: Search for CID in PubChem Compound, find related PubMed citations and related PubMed via MeSH.**

It appears that you can combine `elink` queries, with either the same Entrez link or a different Entrez link, but within the same database. For example, if we want to retrieve PubMed literature related to PubChem CID 174076 for both the `pccompound_pubmed` and `pccompound_pubmed_mesh` Entrez links in one dataset, we combine two separate `elink` queries with an OR operator:

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
_tested on 2020.06.17, total count was 314._

**PubChem Substance --> PubChem Compound --> PubMed Publisher**\
**Description: Search for a PubChem Substance Data Source Depositor, find related same PubChem compounds, and then retrieve related PubMed references linked via publisher.**

In the below script, we first search the PubChem Substance (`pcsubstance`) database using `esearch` for the data source depositor _Nature Communications_. We can use the Current Source Name `[CSN]` field for this query. Note that an underscore is put in place of the space in the query. This syntax is important for searching in PubChem with the EDirect `esearch` function. After `esearch`, we pipe the results into `elink` twice, first finding related PubChem Compounds via the `pcsubstance_pccompound_same` Entrez link, and then using this new result list to find related PubMed publisher deposited citations from the `pccompound_pubmed_publisher` Entrez link. Finally, similarly to previous searches, we use a combination of `efetch` and `xtract` to retrieve selected data:

```console

user@computer:~$ esearch -email name@xx.edu -db pcsubstance -query "nature_communications"[CSN] | \
> elink -target pccompound -name pcsubstance_pccompound_same | \
> elink -target pubmed -name pccompound_pubmed_publisher | \
> efetch -format xml | 
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName Author/Initials \
> ISOAbbreviation PubDate/Year Volume Issue MedlinePgn
26673265	Gilbert	ZW	Nat Chem	2016	8	1	63-8
25424885	Yan	T	Nat Commun	2014	5	5602
25422853	Vaidya	AB	Nat Commun	2014	5	5521
25382411	Dommerholt	J	Nat Commun	2014	5	5378
25382259	Wang	B	Nat Commun	2014	5	5354
25377759	Gu	Y	Nat Commun	2014	5	5405
25367152	Iwamoto	T	Nat Commun	2014	5	5353
25355435	Silipo	A	Nat Commun	2014	5	5106
25335580	Thiel	D	Nat Commun	2014	5	5278
25327774	Guillon	P	Nat Commun	2014	5	5268
25322998	Yazaki	K	Nat Commun	2014	5	5179
...
```

_tested on 2020.06.17, total count was 101._

**PubChem Substance --> PubChem Compound <--> PubMed Publisher**\
**Description: Search for a PubChem Substance Data Source Depositor, find related same PubChem compounds, and then retrieve related PubMed PMIDs linked via publisher.**

Building upon the previous search, if needed, it is possible to obtain individual relationships of the CIDs to PubMed IDs (CID <--> PMID). We can do this using the `-cmd neighbor` option in `elink`:

```console

user@computer:~$ esearch -email name@xx.edu -db pcsubstance -query "nature_communications"[CSN] | \
> elink -target pccompound -name pcsubstance_pccompound_same | \
> elink -target pubmed -name pccompound_pubmed_publisher -cmd neighbor | \
> xtract -pattern LinkSet -element Id
146033657	24398593
136286496
136264969	24177669
136264968	24177669
136262920	23385592
136262919	23385592
136247006	24457545
136247005	24457545
136247004	24457545
136247003	24457545
136219971
135922679	22027590
91868204	23764831
...
```
_tested on 2020.06.17, total count was 1594 (returns all CIDs, not all have linked PMIDs)._

The first column contains the PubChem CIDs and the second column contains the linked PMIDs. Additional linked PMIDs are placed in subsequent columns when available.

**PubChem Compound --> PubChem BioAssay**\
**Description: Search for a PubChem CID in PubChem Compound, then retrieve related PubChem active BioAssay data.**

To retrieve BioAssay results labeled as 'Active' that are linked to a CID, we can use the `elink` function with the PubChem BioAssay (`pcassay`) database via Entrez link `pccompound_pcassay_active`. This is followed by `efetch` and `xtract`. In this particular example, we extracted the AID, CurrentSourceName, AssayName, ActiveSidCount, and TargetCount:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "6303"[uid] | \
> elink -target pcassay -name pccompound_pcassay_active | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element AID CurrentSourceName AssayName ActiveSidCount TargetCount
1346987	National Center for Advancing Translational Sciences (NCATS)	P-glycoprotein substrates identified in KB-8-5-11 adenocarcinoma cell line, qHTS therapeutic library screen	1430	0
86858	ChEMBL	Inhibition HCV NS5B-mediated RNA synthesis	12	1
216185	ChEMBL	Anti-Herpes simplex virus type-1 activity in vero cells using plaque inhibition assay	20	0
32353	ChEMBL	Affinity for the Adenosine A1 receptor in the presence of GTP (A1+GTP) by using [3H]-DPCPX as radioligand	15	1
32352	ChEMBL	Affinity for the Adenosine A1 receptor in the absence of GTP (A1-GTP) by using [3H]DPCPX as radioligand	14	1
31719	ChEMBL	Affinity for adenosine A1 receptor was determined, in the presence of GTP in rat brain cortex	19	1
31718	ChEMBL	Affinity for adenosine A1 receptor was determined, in the absence of GTP in rat cortical membrane.	19	1
81069	ChEMBL	Cytotoxic dose required to reduce the viability of normal uninfected MT-4 cells.	9	0
1255098	ChEMBL	Inhibition of TLR4-mediated NF-kappaB signaling pathway in BALB/c mouse RAW264.7 cells assessed as suppression of LPS-stimulated PGE2 production at 1 to 10 ug/ml preincubated for 1 hr followed by LPS challenge measured after 6 hrs by immunoblot analysis	1	1
...
```
_tested on 2020.06.18, total count was 43._


**PubChem Compound <--> PubChem BioAssay**\
**Description: Search for PubChem CID in PubChem Compound Database, find related compounds with same connectivity, then retrieve related AIDs for each CID.**

It is possible to obtain individual relationships of the CIDs to BioAssay AIDs (CID <--> AID). We can do this using the `-cmd neighbor` option in `elink`. Note that we first found related compounds with same connectivity using the Entrez link `pccompound_pccompound_sameconnectivity_pulldown`. This step was followed by the `pccompound_pcassay_active` Entrez link in the PubChem BioAssay database to retrieve AID links to the CIDs. We used the 'Active' assay links here, note that there are also other Entrez PubChem Compound assay links such as inactive, `pccompound_pcassay_inactive`.

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "6303"[uid] | \
> elink -target pccompound -name pccompound_pccompound_sameconnectivity_pulldown | \
> elink -target pcassay -name pccompound_pcassay_active -cmd neighbor | \
> xtract -pattern LinkSet -element Id
...
6713033
6541389
6335098
688425
451875	1347103	1296009	2551	2546
248010	687016	652245	651719	177	175	173	171	169	167	165	163	161	159	157	155	153	149	147
6303	1346987	1259407	1255098	1255092	1207585	1207584	1207579	1207578	1207577	1207576	1167619	1159565	1159562	1159559	1159557	1065715	1065714	1065710	1065706	1065697	1065696	1065695	1065713	1065705	1065699	751324	686979	686978	651820	652245	651719	602346	602250	588511	493002	463218	463212	416870	416743	216185	86858	81069	32353	32352	31719	31718	2467
```
_tested on 2020.06.18, total count was 20 CIDs (not all have associated AIDs)._

### PubMed

**PubMed --> PubChem Compound**\
**Description: Search for a PubMed article ID (PMID), then retrieve related PubChem Compounds.**

In the below script, we first use `esearch` to query PubMed for the article ID 29407984 in the `[PMID]` field. This result is then piped into `elink` to retrieve linked compounds in the PubChem Compound database (`pubmed_pccompound`). In this case, there was one compound and we used `efetch` to retrieve the CID record as docsum XML, followed by `xtraxt` to extract the IsomericSmiles, CID, and InChIKey values. 

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "29407984"[PMID] | \
> elink -target pccompound -name pubmed_pccompound | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey
C1CC1N2C=C(C(=O)C3=CC(=C(C=C32)N4CCNCC4)F)C(=O)O	2764	MYSWGUAQZAJSOK-UHFFFAOYSA-N

```
_tested on 2020.06.17, total count was 1._

**PubMed --> PubChem Compound (+ mixtures)**\
**Description: Search for a PubMed article ID (PMID), then retrieve linked PubChem Compound mixtures/components.**

In this script, an additional `elink` search is added to find related PubChem Mixture/Component compounds via Entez link `pccompound_pccompound_mixture`.

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "29407984"[PMID] | \
> elink -target pccompound -name pubmed_pccompound | \
> elink -target pccompound -name pccompound_pccompound_mixture | \
> efetch -format docsum | xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey
C1CC(=O)O[C@H]1C(=O)O.C1CC1N2C=C(C(=O)C3=CC(=C(C=C32)N4CCNCC4)F)C(=O)O	145823787	QQQHNTYLFZIZHE-ZYRQIYSTSA-N
C1CC(=O)O[C@@H]1C(=O)O.C1CC1N2C=C(C(=O)C3=CC(=C(C=C32)N4CCNCC4)F)C(=O)O	145823786	QQQHNTYLFZIZHE-HVDRVSQOSA-N
CC.C1CC1N2C=C(C(=O)C3=CC(=C(C=C32)N4CCNCC4)F)C(=O)O	144444845	AKBGPLFNEDZIPX-UHFFFAOYSA-N
CC.CN(/C=C(\C(=O)C1=CC(=C(C(=C1)Cl)N2CCC(C2)N)F)/C(=O)O)C3CC3.C1CC1N2C=C(C(=O)C3=CC(=C(C=C32)N4CCNCC4)F)C(=O)O	143903639	FMVLVHHCQKXFHZ-IGUOPLJTSA-N
CC1CCCC1OC2=CC=C(C=C2)C3=CC(=O)C4=C(C=C(C=C4O3)O)O.CO.C=O.C1CC1N2C=C(C(=O)C3=CC(=C(C=C32)N4CCNCC4)F)C(=O)O	143730536	OMLKGFMLMAMHQX-UHFFFAOYSA-N
CC.CC.CC=C.C1CC1N2C=C(C(=O)C3=CC(=C(C=C32)N4CCNCC4)F)C(=O)O	142343296	FTGZCRDXNNWDDT-UHFFFAOYSA-N
B(O)(O)O.C1CC1N2C=C(C(=O)C3=CC(=C(C=C32)N4CCNCC4)F)C(=O)O	141854175	AQXYOGALLFZHMN-UHFFFAOYSA-N
C1CC1N2C=C(C(=O)C3=CC(=C(C=C32)N4CCNCC4)F)C(=O)O.C(C(=O)O)C(CC(=O)O)(C(=O)O)O	140356467	AMRSFEJKVIJKPC-UHFFFAOYSA-N
C1CC1N2C=C(C(=O)C3=CC(=C(C=C32)N4CCNCC4)F)C(=O)O.C1=CC=NC(=C1)C2=CC(=CC(=N2)C3=CC=C(C=C3)Br)C4=CC(=CC=C4)Br.Cl[Cu]	139238590	LOBDEDALVBTKKY-UHFFFAOYSA-M
...
```
_tested on 2020.06.17, total count was 364._

**PubMed --> PubChem Compound (MESH search)**\
**Description: Search PubMed with a text query, then retrieve linked PubChem Compounds.**

We can also perform text queries in PubMed and retrieve linked PubChem Compounds. Note that in the below script we searched for "ionic liquids" in the `[MESH]` field and Imidazolium in any field. Since this query requires two pairs of quotes, we have to escape the internal quotes in order for the query to be interpreted correctly. The Entrez link `pubmed_pccompound` was used to find related PubChem compounds.

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "\"ionic liquids\"[MESH] AND imidazolium" | \
> elink -target pccompound -name pubmed_pccompound | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey
C1=CC2=CC(=C(C(=C2C(=O)C(=C1)O)O)O)O	135403797	WDGFFVCWBZVLCE-UHFFFAOYSA-N
C1=NC2=C(N1[C@H]3[C@@H]([C@@H]([C@H](O3)CO)O)O)N=C(NC2=O)N	135398635	NYHBQMYGNKIUIF-UUOKFMHZSA-N
CCCCCCCCN1C=C[N+](=C1C2=[N+](C=CN2CCCC)C)C	123995430	DRJFJBHYMOHPHX-UHFFFAOYSA-N
CC(=O)OC1=[N+](C=CN1CC=C)C	123614562	XSXMFLUARQMOLS-UHFFFAOYSA-N
C[N+]1=C(N(C=C1)CCCCCCCCCCCCS)OC(=O)OC2=[N+](C=CN2CCCCCCCCCCCCS)C	123431445	DRJOSAVMFCYCSU-UHFFFAOYSA-P
CCO[Si](CCCN1CCN(C1)C=C)(OCC)OCC	123281277	GPMGDMINBSRUIO-UHFFFAOYSA-N
CCCCN1C=CN=C1OC(=O)C	123237733	PDSPOWFNKFDQNB-UHFFFAOYSA-N
CCCCN1C=C[N+](=C1C2=[N+](C=CN2CC=C)C)C	123167301	XKKAZLLMTICMRC-UHFFFAOYSA-N
CN1CN(C=C1)C[Si](C)(C)C2=CC=CC=C2	91552113	MWHFAPHURSMAOP-UHFFFAOYSA-N
CCCCCCCCCCCCC1=C2C3=CC=CC=C3SC2=CC=C1	91522266	URGNSKYERZCKSJ-UHFFFAOYSA-N
CCN1C=C[N+](=C1C2=[N+](C=CN2C)C)C	91502560	XLXYOCMOYFVXPU-UHFFFAOYSA-N
...
```
_tested on 2020.06.17, total count was 395._

**PubMed --> PubChem Compound (MESH search, and a PubChem filter)**\
**Description: Search PubMed with a text query and retrieve only linked compounds containing defined chiral atoms.**

We can also perform some powerful filtering with `efilter`. In the below script, the `[ACDC]` field is the defined atom chiral count in PubChem. A range of 1 through 100 was added for this `[ACDC]` filter. Since it is unlikely that any of the compounds would have near 100 chiral atoms, we can be fairly confident this should capture all cases in our search.

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "\"ionic liquids\"[MESH] AND imidazolium" | \
> elink -target pccompound -name pubmed_pccompound | \
> efilter -query "1:100"[ACDC] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey
C1=NC2=C(N1[C@H]3[C@@H]([C@@H]([C@H](O3)CO)O)O)N=C(NC2=O)N	135398635	NYHBQMYGNKIUIF-UUOKFMHZSA-N
C([C@H](C(=O)[C@H](CO)O)O)O	54067296	WXYXERHRDKEISL-ZXZARUISSA-N
B(O)(O)OCC(=O)[C@H]([C@@H]([C@@H](CO)O)O)O	53705729	BXAZSOZNRVUIGN-UYFOZJQFSA-N
C([C@H]1[C@@H]([C@H]([C@@H]([C@@H](O1)O[C@@H]2[C@@H](O[C@H]([C@@H]([C@H]2O)O)O)CO)O)O)O)O	46936190	GUBGYTABKSRVRQ-AEDSEYDFSA-N
C1[C@H](OC2=CC(=CC(=C2C1=O)O)OC3C(C(C(C(O3)CO)O)O)O)C4=CC=C(C=C4)O	42607902	DLIKSSGEMUFQOK-CEFFZDIVSA-N
C([C@@H](C1=C(C(=C(O1)O)O)O)O)O	25200375	LPYXWGMUVRGUOY-REOHCLBHSA-N
C[C@@H]1CCCN1C(=O)O	22794415	GULMJNUJAVNDBJ-RXMQYKEDSA-N
C[C@H](CC1=CN(C2=CC=CC=C21)C(=O)O)N	22789058	INMMDWHNQTVWFB-MRVPVSSYSA-N
C1[C@H]([C@@H](O[C@@H]1N2C=C(C(=O)NC2=O)Br)CO)O	6918942	WOVKYSAHUYNSMH-VQVTYTSYSA-N
CC(C)[C@H]1CC[C@H]2[C@H](C1)CC[C@@H]3[C@@]2(CCCC3(C)C)C	6857485	STIVVCHBLMGYSL-ZYNAIFEFSA-N
...
```
_tested on 2020.06.17, total count was 43._

**PubMed --> PubChem Compounds + PubChem Compounds (MeSH) + PubChem Compounds (Publisher)**\
**Description: Search PubMed, then find linked PubChem Compounds, PubChem Compounds via PubMed MeSH, and PubChem Compound PubMed Publisher.**

As seen in the previous PubChem searches, there are several Entrez linkouts from PubMed to PubChem Compound such as pubmed_pccompound, pubmed_pccompound_mesh, pubmed_pccompound_publisher. We can retrieve associated compounds from all three at the same time like this:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "imidazolium AND bacteria" | \
> elink -target pccompound -name pubmed_pccompound -label compounds_01 | \
> esearch -email name@xx.edu -db pubmed -query "imidazolium AND bacteria" | \
> elink -target pccompound -name pubmed_pccompound_mesh -label compounds_02 | \
> esearch -email name@xx.edu -db pubmed -query "imidazolium AND bacteria" | \
> elink -target pccompound -name pubmed_pccompound_publisher -label compounds_03 | \
> esearch -query "(#compounds_01) OR (#compounds_02) OR (#compounds_03)" | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey
CC1=C(C2=CC3=NC(=CC4=C(C(=C([N-]4)C=C5C(=C(C(=N5)C=C1[N-]2)C=C)C)[C@H](CCCC(C)CCCC(C)CCCC(C)C)O)C)C(=C3CCC(=O)[O-])C=O)CCC(=O)[O-].[Fe+3]	146026558	JDCCRDMXPNAUND-UJYLPOGRSA-J
C1=CC=C2C(=C1)C=C(N2)CC3=CC=C(C=C3)C(F)(F)F.C1=CC=C2C(=C1)C=C(N2)CC3=CC=C(C=C3)C(F)(F)F	139191468	NNXWVRXROOQQNO-UHFFFAOYSA-N
CC[C@@H]1[C@@]2([C@@H]([C@H](C(=O)[C@@H](C[C@@]([C@@H]([C@H](C(=O)[C@H](C(=O)O1)C)C)O[C@@H]3[C@@H]([C@H](C[C@H](O3)C)N(C)C)O)(C)OC)C)C)N(C(=O)O2)CCCCN4C=C(N=C4)C5=CN=CC=C5)C	138402871	LJVAJPDWBABPEJ-WMGYHEQLSA-N
CCC1[C@@]2([C@H](C(C(=O)C(C[C@@](C(C(C(=O)C(C(=O)O1)C)C)O[C@H]3C(C(CC(O3)C)N(C)C)O)(C)OC)C)C)N(C(=O)O2)CCCCN4C=C(N=C4)C5=CN=CC=C5)C	137794998	LJVAJPDWBABPEJ-UNVLRUHBSA-N
CCOC(=O)/C(=N\NC1=CC=CC2=C1N=CC=C2)/C3=[N+](C=CN3)C	136199795	ZHOWQGCGVQGEKD-UHFFFAOYSA-O
CCOC(=O)/C(=N\NC1=CC=CC=C1)/C2=CC=CC=[NH+]2	136199794	VDCLBUQGYUHKMX-JXAWBTAJSA-O
CCOC(=O)/C(=N/NC1=CC=CC2=C1N=CC=C2)/C3=NC=CN3	136199793	SHOHIYGUZOKENL-KGENOOAVSA-N
CCOC(=O)/C(=[NH+]\[N-]C1=CC=CC2=C1N=CC=C2)/C3=NC=CN3C.[Zn+2]	136199791	SLYQINQKGUDWPE-UHFFFAOYSA-N
C1=CC(=CC=C1C(=O)N[C@@H](CCC(=O)[O-])C(=O)O)NCC2=CN=C3C(=N2)C(=O)NC(=N3)N.[K+]	135804810	FFGKARQZIVIMDP-YDALLXLXSA-M
C1=CC(=CC=C1C(=O)N[C@H](CCC(=O)[O-])C(=O)[O-])NCC2=CN=C3C(=N2)C(=O)NC(=N3)N.[Na+].[Na+]	135804009	SWIRFWUEJODNRG-CURYUGHLSA-L
C1=CC(=CC=C1N2NC(=NN2C3=CC=C(C=C3)I)C4=C(C=C(C=C4)S(=O)(=O)O)S(=O)(=O)O)[N+](=O)[O-]	135514508	PAJSIAXXSFQVPJ-UHFFFAOYSA-N
C1=CC=C2C(=C1)C(=O)NC=N2	135408753	QMNUDYFKZYBWQX-UHFFFAOYSA-N
...
```
_tested on 2020.06.17, total count was 536._

**PubMed <--> PubChem Compound**\
**Description: Search PubMed for an affiliation, find related PubChem Compounds, then retrieve related CIDs for each PMID.**

If we want to retrieve the PMID <--> CID relationships (for Entrez link `pubmed_pccompound`), we can achieve this using the `-cmd neighbor` option in `elink`:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "(university of alabama[AFFL]) \
> NOT (birmingham[AFFL] OR huntsville[AFFL])" \
> -datetype PDAT -mindate 2010 -maxdate 2020 | \
> elink -target pccompound -name pubmed_pccompound -cmd neighbor | \
> xtract -pattern LinkSet -element Id
...
22735985
22730471
22726124	31423	10401	702
22721834
22717749	5910
22700334	136065590	57191410	57098251	11239199	176
22696533
22690894
22676430
22675270
22662746	439524
22653754
22642521
22629217	702
22616693	679	300	284	176
22607168	289
22594615	15743871	12749
22592862
22589227
22582378
...
```

_tested on 2020.06.17, total count was 3302 (returns all PMIDs, not all have linked CIDs)._

The first column contains the PMIDs and the second column contains the linked PubChem CIDs (from the pubmed_pccompound links). As an aside, the PubMed query for the University of Alabama in the affiliation field [AFFL] excludes any results containing huntsville or birmingham in the affiliation. This was an attempt to only retrieve The University of Alabama (Tuscaloosa) records. It's not a perfect search, but gets us close.

**PubMed --> PubChem BioAssay**\
**Description:**

**PubMed <--> PubChem BioAssay**\
**Description**:

### PubChem BioAssay

**PubChem BioAssay --> PubMed**\
**Description**:

**PubChem BioAssay --> PubChem Compound**\
**Description**:

**PubChem BioAssay <--> PubChem Compound**\
**Description**:


