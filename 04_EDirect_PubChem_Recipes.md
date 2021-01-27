# PubChem EDirect Recipes

**Notes**

> 1. `user@computer:~$` represents an example terminal prompt name. Actual command/argument input is after the `$`.
> 2. Replace `name@xx.edu` with your email address.
> 3. `\` followed by `>` on the next line represents continued terminal input. You will need to delete the `>` symbol in order to run the scripts as a copy/paste into terminal.
> 4. You should validate your own EDirect scripts and results as there may be unintentional mistakes in these recipes. A convenient method is to compare your EDirect results to the NCBI Web interface search results: [https://www.ncbi.nlm.nih.gov/](https://www.ncbi.nlm.nih.gov/).

## PubChem EDirect

### Search PubChem Compound via InChIKey and Retrieve Data

In the below script, we first query the PubChem Compound database (`pccompound`) for "NJTXJDYZPQNTSM-WMZOPIPTSA-N" in the InChIKey (`[IKEY]`) field. Next, the record is retrieved in XML docsum and several properties are extracted with the `xtract` function including the IsomericSmiles, CID, InChIKey, and IUPACName.

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "NJTXJDYZPQNTSM-WMZOPIPTSA-N"[IKEY] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey IUPACName
C[C@]12CCC(=O)C=C1CCC[C@@H]2OC(=O)C3=CC=CC=C3	11044292	NJTXJDYZPQNTSM-WMZOPIPTSA-N	[(1S,8aS)-8a-methyl-6-oxo-1,2,3,4,7,8-hexahydronaphthalen-1-yl] benzoate
```
_tested on 2021.01.27, EDirect 14.4, total count was 1._

### Search PubChem Compound with a list of CIDs and Retrieve Data

If we have a small list of PubChem Compound Identifiers (CIDs) and need to retrieve specific data for each CID, we can write a for loop directly in the terminal. Note that in the below Bash script, I added a sleep of one second within the loop in an effort to not overload the NCBI servers.

```console

user@computer:~$ for myCID in \
>    "146021325" \
>    "11068043" \
>    "11615487" \
>    "10056179" \
>    "169731"
> do
>    esearch -email name@xx.edu -db pccompound -query "$myCID[UID]" |
>    efetch -format docsum |
>    xtract -pattern DocumentSummary -lbl "$myCID" -element IsomericSmiles InChIKey MolecularFormula MolecularWeight
>    sleep 1
> done
146021325	CN1C(=CN=C1Cl)C(/C=C/C2=CC=CC=C2)(C3=CC=CC=C3)O	AKSFJXCUMHAJKP-OUKQBFOZSA-N	C19H17ClN2O	324.800
11068043	CC(C)[Si](C(C)C)(C(C)C)OC(CCCC1=CCC=CC1)CC=C	JDKBJINLKCZQNX-UHFFFAOYSA-N	C22H40OSi	348.600
11615487	CC1=CC(=C(C=C1)NC(=O)C(C)(C)C)OC	WMXHBZHMNCGLQQ-UHFFFAOYSA-N	C13H19NO2	221.290
10056179	CC(=O)N1CN2C3=CC=CC=C3C(=C2C4=CC=CC=C41)C(C(=O)NCC[Se]C5=CC=CC=C5)O	MJWHOMSECISGAK-UHFFFAOYSA-N	C27H25N3O3Se	518.500
169731	C1=CC=C2C(=C1)C=C(N2)CC#N	RORMSTAFXZRNGK-UHFFFAOYSA-N	C10H8N2	156.180
```
_tested on 2021.01.27, EDirect 14.4, total count was 5 (as expected in the for loop)._

### Retrieve Pre-Computed Linked Similar Compounds

In the below script, we use the `esearch` function to query the PubChem Compound database (`pccompound`) for CID 11044292 within the Compound ID field (`[uid]`). The `esearch` results are then piped to `elink` finding related PubChem Compounds via the Entrez link `pccompound_pccompound`.

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "11044292"[UID] | \
> elink -target pccompound -name pccompound_pccompound | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey IUPACName
...
...
CC(C1=CC=CC=C1)OC(=O)C2=CC=C(C=C2)C(C)(C)C	152679150	ZNDWBMXQOLFTJJ-UHFFFAOYSA-N	1-phenylethyl 4-tert-butylbenzoate
CCCCC1(CCC(C(C1)OC(=O)C2=CC=CC=C2)C(C)C)C	152242148	WDOHMQDXGOVKBZ-UHFFFAOYSA-N	(5-butyl-5-methyl-2-propan-2-ylcyclohexyl) benzoate
CCC1=CC=CC=C1C(=O)OC2C=CC(=O)CC2(C)C	150893175	KYLDSGZLUQKTGC-UHFFFAOYSA-N	(6,6-dimethyl-4-oxocyclohex-2-en-1-yl) 2-ethylbenzoate
CC1CCC(C(CC1=O)(C)C)OC(=O)C2=CC=CC=C2	150335011	GQMHTGLOWBLGSB-UHFFFAOYSA-N	(2,2,5-trimethyl-4-oxocycloheptyl) benzoate
...
...
```
_tested on 2021.01.27, EDirect 14.4, total count was 238._

### Find Compounds with Specific Attributes

There are a variety of methods to limit results and find compounds with specific attributes in PubChem Compound. The below script, for example, uses the `efilter` function to limit the `elink` similarity results to compounds with active assays using the query "pccompound_pcassay_active" in the filter (`[FILT]`) field:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "11044292"[UID] | \
> elink -target pccompound -name pccompound_pccompound | \
> efilter -query "pccompound_pcassay_active"[FILT] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey IUPACName
CC1(CC(=O)C=C(C1=O)C2=CC=C(C=C2)COC(=O)C3=CC=CC=C3)C	46904830	DSGHHJWVVKGFNI-UHFFFAOYSA-N	[4-(5,5-dimethyl-3,6-dioxocyclohexen-1-yl)phenyl]methyl benzoate
CC1=CC[C@H](/C(=C\[C@@H](C(CCC1)(C)C)OC(=O)C)/C)OC(=O)C2=CC=CC=C2	46886858	WZEJCPHMOSKQHH-FNXKFTHESA-N	[(1R,2Z,4S)-4-acetyloxy-2,5,5,9-tetramethylcycloundeca-2,9-dien-1-yl] benzoate
CC1=C2[C@H]([C@@H]([C@@]3(C=CC(=O)C(=C)[C@H]3C=C2CC1=O)C)OC(=O)C)OC(=O)C4=CC=CC=C4	44585423	DSOLMHLLLLRMOJ-BNWQNPBSSA-N	[(4R,5R,5aR,9aS)-5-acetyloxy-3,5a-dimethyl-9-methylidene-2,8-dioxo-1,4,5,9a-tetrahydrobenzo[g]azulen-4-yl] benzoate
CCOC(=O)C1=CC=CC(=C1)C2=CC(=O)CC(C2)(C)C	44143998	BBEWYQFSZQEXCH-UHFFFAOYSA-N	ethyl 3-(5,5-dimethyl-3-oxocyclohexen-1-yl)benzoate
CCC1=C(C(C(OC1=O)C2=CC=CC=C2)(C)C)OC(=O)C3=CC=CC=C3	2893657	FEWXNYDYEILDTL-UHFFFAOYSA-N	(5-ethyl-3,3-dimethyl-6-oxo-2-phenyl-2H-pyran-4-yl) benzoate
CC1=C(C(C(OC1=O)C2=CC=CC=C2)(C)C)OC(=O)C3=CC=CC=C3	569453	UXXMZHQXFIACTH-UHFFFAOYSA-N	(3,3,5-trimethyl-6-oxo-2-phenyl-2H-pyran-4-yl) benzoate
```

_tested on 2021.01.27, EDirect 14.4, total count was 6._

Another filtering method could be to add a specific property attribute range, such as compounds containing 8 to 12 rotatable bonds (`[RBC]`):

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "11044292"[UID] | \
> elink -target pccompound -name pccompound_pccompound | \
> efilter -query "8:12"[RBC] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID RotatableBondCount
CCC(CC)(CCCCCOC(=O)C1=CC=CC=C1)C(=O)C2=CC=CC=C2	153964625	12
CCCCCCCC1CCC(CC1)OC(=O)C2=CC=CC=C2	153717993	9
CCCCCCC1CCC(CC1)OC(=O)C2=CC=CC=C2	153717992	8
CC(C(=O)CCCC(C)(C)CCC(C)(C)C)OC(=O)C1=CC=CC=C1	153334776	11
COC(=O)CCCCC[C@H](C1=CC=CC=C1)OC(=O)C2=CC=CC=C2	145778504	11
CCC(C(CC(C)(C)C)OC(=O)C1=CC=CC=C1)OC(=O)C2=CC=CC=C2	142273534	10
...
...
```

_tested on 2021.01.27, EDirect 14.4, total count was 57._

It is also possible to query PubChem Compound directly for compounds with specific attributes (i.e., without the use of `efilter`). However, you will likely need to be very specific in order to retrieve a reasonable number of records. For example, in the the below script, PubChem Compound was queried for compounds containing Uranium in the element field (`[ELMT]`) and 3:5 defined chiral atoms in the AtomChiralDefCount field (`[ACDC]`):


```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "(\"U\"[ELMT]) AND \"3\"[ACDC]:\"5\"[ACDC]" | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID MolecularFormula AtomChiralDefCount
C[C@@H]1CC[C@H]([C@@]2([C@]1(CCC(=C2)C)C)C)[C-]=C.O.[U]	154676185	C16H27OU-	4
C[CH-]O[C@H]1[C@@H]([C@H]([C@@H](O[C@@H]1[CH2-])O)O)C.[U+2]	154572041	C9H16O4U	5
[CH3-].CNCCO[C@@H]1CNC[C@@H](C1C2=CC=C(C=C2)O[C@H]3CCN(C3)C4=CC(=CC=C4)F)OCC5=CC6=C(C=C5)OCCN6CC[CH2-].[U+2]	154550507	C37H49FN4O4U	3
C[C@H]1CC=C(CN1C)C2=CSC(=N2)SC3=C(N4[C-]([C@H]3C)[C@H](C4=O)[C@@H](C)O)C(=O)O.[U]	154536644	C20H24N3O4S2U-	4
CC1CC2C3[C@H](C=C4C[C-](CC[C@@]4(C3CC[C@@]2(C15OCCO5)C)C)OC[CH2-])O.[U+2]	154528690	C24H36O4U	3
COC1=C(C=C2C(=C1)C(=O)N3CC(=C)C[C@H]3[C@@H]([N-]2)O)OCCCCCOC4=C(C=C5C(=C4)N=C[C@@H]6CC(=C)CN6C5=O)OC.[U]	153695434	C33H37N4O7U-	3
...
...
```

_tested on 2021.01.27, EDirect 14.4, total count was 1938._

Note that I escaped (`\`) the internal quotes in the above query. Sometimes this is not necessary (in my experience it depends on the NCBI database). If you are unsure how the query is being interpreted, run the `esearch` function with the `-debug` option. You can then use `nquire` with the link output and extract out the parsed query:


```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "(\"U\"[ELMT]) AND \"3\"[ACDC]:\"5\"[ACDC]" -debug
...
...
user@computer:~$ nquire -url https://eutils.ncbi.nlm.nih.gov/entrez/eutils/ esearch.fcgi -retmax 0 -usehistory y -db pccompound -term "(\"U\"[ELMT]) AND \"3\"[ACDC]:\"5\"[ACDC]" | \
> xtract -pattern eSearchResult -element QueryTranslation
"U"[ELMT] AND "3"[AtomChiralDefCount] : "5"[AtomChiralDefCount]
```

### Find Number of Compounds by Create Date

We can use the Create Date field `[CDAT]` in PubChem Compound to search for compound records created on a specific date. The `esearch` results are then piped into `efetch` to retrieve the XML docsum compound records. Next, the `xtract` function is used to extract out the CreateDate. The extracted data is then piped into the EDirect alias function `sort-uniq-count-rank`, which sorts the data by highest frequency. Finally, I added an additional `sort` command, to sort by date (`-k2,2` for second column), instead of number of compounds.

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "2020/05/01"[CDAT]:"2020/05/31"[CDAT] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element CreateDate | \
> sort-uniq-count-rank | \
> sort -k2,2
448	2020/05/01 00:00
20	2020/05/02 00:00
45	2020/05/04 00:00
3	2020/05/05 00:00
67	2020/05/06 00:00
32	2020/05/07 00:00
97	2020/05/08 00:00
7827	2020/05/11 00:00
42	2020/05/12 00:00
573	2020/05/13 00:00
67	2020/05/14 00:00
75	2020/05/15 00:00
136	2020/05/16 00:00
69	2020/05/18 00:00
52	2020/05/19 00:00
66	2020/05/20 00:00
53	2020/05/21 00:00
790	2020/05/22 00:00
36	2020/05/23 00:00
2	2020/05/24 00:00
5	2020/05/25 00:00
530	2020/05/26 00:00
63	2020/05/27 00:00
169	2020/05/28 00:00
9432	2020/05/29 00:00
26	2020/05/30 00:00
2	2020/05/31 00:00
```
_tested on 2021.01.27, EDirect 14.4._

If we want to obtain the number of compounds in PubChem by create date over a longer period of time (e.g., several months to years), it probably does not make sense to use `efetch`, as the number of compounds will be hundreds of thousands or even millions. Trying to download all of the docsums for this many record likely won't work. As an alternative, we can use `esearch` in a for loop, and extract out the Count value from the `esearch` ENTREZ_DIRECT query XML summary. For example, if we wanted the number of compounds created in PubChem for 2019 by month:

```console

user@computer:~$ for date in \
>   "2019/01" \
>   "2019/02" \
>   "2019/03" \
>   "2019/04" \
>   "2019/05" \
>   "2019/06" \
>   "2019/07" \
>   "2019/08" \
>   "2019/09" \
>   "2019/10" \
>   "2019/11" \
>   "2019/12"
> do
>   esearch -email name@xx.edu -db pccompound -query "$date[CDAT]" |
>   xtract -pattern ENTREZ_DIRECT -lbl "$date" -element Count
>   sleep 1 
> done
2019/01	1843612
2019/02	7970
2019/03	219313
2019/04	469125
2019/05	324068
2019/06	64691
2019/07	302326
2019/08	154938
2019/09	119817
2019/10	236148
2019/11	308727
2019/12	5444411
```
_tested on 2021.01.27, total count was 12 (as expected in the for loop)._

In the above for loop bash script, I added a sleep of one second between each `esearch` query in an effort to not overload the NCBI servers.

### Find Related PubChem Substances (same)

To find the number of related PubChem substances for a PubChem compound, we can use `elink` with Entrez link `pccompound_pcsubstance_same`:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "14333"[UID] | \
> elink -target pcsubstance -name pccompound_pcsubstance_same | \
> xtract -pattern ENTREZ_DIRECT -element Count
51
```

And then to retrieve information about the PubChem substances, we can pipe these results into `efetch` and `xtract`, to extract out specific information such as the SID, CurrentSourceName, SourceID, and DepositDate:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "14333"[UID] | \
> elink -target pcsubstance -name pccompound_pcsubstance_same | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element SID CurrentSourceName SourceID DepositDate
...
439452693	THE BioTek	bt-308998	2020/12/31 00:00
438657242	Alfa Chemistry	ACM1132394	2020/12/09 00:00
438538915	3WAY PHARM INC	SWOT-0105728	2020/12/08 00:00
435642079	Chem-Space.com Database	CSSB00032005459	2020/11/21 00:00
410573132	Google Patents	15237363	2020/08/12 00:00
404911410	The University of Alabama Libraries	UALIB-1927	2020/03/21 00:00
403383863	PATENTSCOPE (WIPO)	ORQWTLCYLDRDHK-UHFFFAOYSA-N	2020/01/24 00:00
387135315	NORMAN Suspect List Exchange	ORQWTLCYLDRDHK-UHFFFAOYSA-N	2019/11/22 00:00
386279116	Wiley	140582	2019/10/23 00:00
...
...
```
_tested on 2021.01.27, total count was 51._

