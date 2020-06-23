# PubChem EDirect Recipes

**Notes**

> 1. `user@computer:~$` represents an example terminal prompt name. Actual command/argument input is after the `$`.
> 2. Replace `name@xx.edu` with your email address.
> 3. `\` followed by `>` on the next line represents continued terminal input. You will need to delete the `>` symbol in order to run the scripts as a copy/paste into terminal.
> 4. You should validate your own EDirect scripts and results as there may be unintentional mistakes in these recipes. A convenient method is to compare your EDirect results to the NCBI Web interface search results: [https://www.ncbi.nlm.nih.gov/](https://www.ncbi.nlm.nih.gov/).

### PubChem Searching

**Description:** Search PubChem Compound via InChIKey and Retrieve Data

In the below script, we first query the PubChem Compound database (`pccompound`) for "NJTXJDYZPQNTSM-WMZOPIPTSA-N" in the InChIKey (`[IKEY]`) field. Next, the record is retrieved in XML docsum and several properties are extracted with the `xtract` function including the IsomericSmiles, CID, InChIKey, and IUPACName.

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "NJTXJDYZPQNTSM-WMZOPIPTSA-N"[IKEY] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey IUPACName
C[C@]12CCC(=O)C=C1CCC[C@@H]2OC(=O)C3=CC=CC=C3	11044292	NJTXJDYZPQNTSM-WMZOPIPTSA-N	[(1S,8aS)-8a-methyl-6-oxo-1,2,3,4,7,8-hexahydronaphthalen-1-yl] benzoate
```
_tested on 2020.06.23, total count was 1._


**Description:** Search PubChem Compound with a list of CIDs and Retrieve Data

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
_tested on 2020.06.22, total count was 5 (as expected in the for loop)._


**Description:** Retrieve Pre-Computed Linked Similar Compounds

In the below script, we use the `esearch` function to query the PubChem Compound database (`pccompound`) for CID 11044292 within the Compound ID field, `[uid]`. The `esearch` results are then piped to `elink` finding related PubMed Compounds via the Entrez link `pccompound_pccompound`.

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "11044292"[UID] | \
> elink -target pccompound -name pccompound_pccompound | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey IUPACName
CCC(=O)C1CCC(CC1)OC(=O)C2=CC=CC=C2	145831559	YSBCGQCTXNPELX-UHFFFAOYSA-N	(4-propanoylcyclohexyl) benzoate
COC(=O)CCCCC[C@H](C1=CC=CC=C1)OC(=O)C2=CC=CC=C2	145778504	JBIQRMBECWAGMJ-LJQANCHMSA-N	[(1R)-7-methoxy-7-oxo-1-phenylheptyl] benzoate
CCC(C)(C)CC(C(C)(C)C)OC(=O)C1=CC=CC=C1	144028402	MUFYYXSXZGYWBT-UHFFFAOYSA-N	2,2,5,5-tetramethylheptan-3-yl benzoate
CCCCC1CC=C2CC(CCC2(C1CC)C)OC(=O)C3=CC=CC=C3	144010186	GRAQMEKPDSWBNB-UHFFFAOYSA-N	(6-butyl-5-ethyl-4a-methyl-2,3,4,5,6,7-hexahydro-1H-naphthalen-2-yl) benzoate
CC1C(C2=CC=CC=C2C(=O)O1)(C)C	143663857	BJNREZQDTMCPSA-UHFFFAOYSA-N	3,4,4-trimethyl-3H-isochromen-1-one
CCCC1(C=CC=CC1OC(=O)C2=CC=CC=C2)C3=CC=CC=C3	142715462	ZTBHSBSKBIJMJK-UHFFFAOYSA-N	(6-phenyl-6-propylcyclohexa-2,4-dien-1-yl) benzoate
CCC(C(CC(C)(C)C)OC(=O)C1=CC=CC=C1)OC(=O)C2=CC=CC=C2	142273534	UUBKFQSDIJJLSL-UHFFFAOYSA-N	(4-benzoyloxy-6,6-dimethylheptan-3-yl) benzoate
...
```

_tested on 2020.06.23, total count was 219._

**Description:** Find Compounds with Specific Attributes

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

_tested on 2020.06.23, total count was 6._


Another filtering method could be to add a specific property attribute range, such as compounds containing 8 to 12 rotatable bonds (`[RBC]`):

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "11044292"[UID] | \
> elink -target pccompound -name pccompound_pccompound | \
> efilter -query "8:12"[RBC] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID RotatableBondCount
COC(=O)CCCCC[C@H](C1=CC=CC=C1)OC(=O)C2=CC=CC=C2	145778504	11
CCC(C(CC(C)(C)C)OC(=O)C1=CC=CC=C1)OC(=O)C2=CC=CC=C2	142273534	10
CC(C)C(CC(C(C)(C)C)OC(=O)C1=CC=CC=C1)OC(=O)C2=CC=CC=C2	141326409	10
CCC(C(CC1=CC=CC=C1)C(=O)CC)OC(=O)C2=CC=CC=C2	141218691	9
C1=CC=C(C=C1)C(=O)CCCCCCCCOC(=O)C2=CC=CC=C2	141148745	12
CCCC(C(C(C)(C)C)OC(=O)C1=CC=CC=C1)OC(=O)C2=CC=CC=C2	141093597	10
CC(CCCC(C(C)(C)C)OC(=O)C1=CC=CC=C1)OC(=O)C2=CC=CC=C2	141068132	11
CC(CCCCC(C)(C(C)(C)C)OC(=O)C1=CC=CC=C1)OC(=O)C2=CC=CC=C2	141068107	12
...
```

_tested on 2020.06.23, total count was 51._


It is also possible to query PubChem Compound directly for compounds with specific attributes (i.e., without the use of `efilter`). However, you will likely need to be very specific in order to retrieve a reasonable number of records. For example, in the the below script, PubChem Compound was queried for compounds containing Uranium in the element field (`[ELMT]`) and 1:100 defined chiral atoms in the AtomChiralDefCount field (`[ACDC]`):


```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "(\"U\"[ELMT]) AND \"1\"[ACDC]:\"100\"[ACDC]" | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID MolecularFormula AtomChiralDefCount
C[C@@H]([C@]1([C@H]([C@H]([CH-]O1)OC(=O)C)OCC2=CC=CC=C2)COC(=O)C)OS(=O)(=O)C.[U]	146028793	C19H25O9SU-	4
CC(C)N(C(C)C)P(OC#N)OC1[C@@H]([CH-]O[C@@H]1COC(C2=CC=CC=C2)(C3=CC=C(C=C3)OC)C4=CC=C(C=C4)OC)[Se]C.[U]	145833685	C34H42N2O6PSeU-	2
COC1=CC=C(C=C1)C(C2=CC=CC=C2)(C3=CC=C(C=C3)OC)OC[C@@H]4C([C@@H]([CH-]O4)[Se]C)O.[U]	145833683	C27H29O5SeU-	2
CC1=CC=C(C=C1)S(=O)(=O)OC[C@]2(C(C([CH-]O2)O)O)CO.[U]	145818183	C13H17O7SU-	1
CC1=CC=C(C=C1)S(=O)(=O)OC[C@]2(C3C([CH-]O2)OC(O3)C4=CC=CC=C4)CO.[U]	145818181	C20H21O7SU-	1
C1[C@]2(C(C(O1)[CH-]O2)OCC3=CC=CC=C3)CO.[U]	145818179	C13H15O4U-	1
CC1=CC=C(C=C1)S(=O)(=O)OC[C@]2(C(C([CH-]O2)O)OCC3=CC=CC=C3)CO.[U]	145818176	C20H23O7SU-	1
CC1=CC=C(C=C1)S(=O)(=O)OC[C@]2(C3C([CH-]O2)OC4(O3)CCCCC4)CO.[U]	145818172	C19H25O7SU-	1
...

_tested on 2020.06.23, total count was 682._

Note that I escaped (`\`) the internal quotes in the above query. Sometimes this is not necessary (in my experience it depends on the NCBI database). If you are unsure how the query is being interpreted, run the `esearch` function with `-log`. This reports out the parsed query at the end of the returned XML:


```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "(\"U\"[ELMT]) AND \"1\"[ACDC]:\"100\"[ACDC]" -log
<ENTREZ_DIRECT>
  <Db>pccompound</Db>
  <WebEnv>NCID_1...</WebEnv>
  <QueryKey>1</QueryKey>
  <Count>682</Count>
  <Step>1</Step>
  <Email>name@xx.edu</Email>
  <Log>Y</Log>
</ENTREZ_DIRECT>
"U"[ELMT] AND "1"[AtomChiralDefCount] : "100"[AtomChiralDefCount]
```

**Description:** Find Number of Compounds by Create Date

We can use the Create Date field `[CDAT]` in PubChem Compound to search for compound records created on a specific date. The `esearch` results are then piped into `efetch` to retrieve the XML docsum compound records. Next, the `xtract` function is used to extract out the CreateDate. The extracted data is then piped into the EDirect alias function `sort-uniq-count-rank`, which sorts the data by highest frequency. Finally, I added an additional `sort` command, to sort by date (`-k2` for second column), instead of number of compounds.

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "2020/05/01"[CDAT]:"2020/05/31"[CDAT] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element CreateDate | \
> sort-uniq-count-rank | \
> sort -k2
463	2020/05/01 00:00
20	2020/05/02 00:00
15	2020/05/04 00:00
3	2020/05/05 00:00
67	2020/05/06 00:00
32	2020/05/07 00:00
97	2020/05/08 00:00
7821	2020/05/11 00:00
42	2020/05/12 00:00
573	2020/05/13 00:00
69	2020/05/14 00:00
75	2020/05/15 00:00
140	2020/05/16 00:00
69	2020/05/18 00:00
53	2020/05/19 00:00
14	2020/05/20 00:00
53	2020/05/21 00:00
790	2020/05/22 00:00
36	2020/05/23 00:00
2	2020/05/24 00:00
5	2020/05/25 00:00
536	2020/05/26 00:00
63	2020/05/27 00:00
163	2020/05/28 00:00
9431	2020/05/29 00:00
26	2020/05/30 00:00
2	2020/05/31 00:00
```

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
2019/01	1867389
2019/02	7998
2019/03	219369
2019/04	468582
2019/05	328275
2019/06	65251
2019/07	302355
2019/08	154940
2019/09	116476
2019/10	236147
2019/11	309252
2019/12	5408549
```
_tested on 2020.06.22, total count was 12 (as expected in the for loop)._

In the above for loop bash script, I added a sleep of one second between each `esearch` query in an effort to not overload the NCBI servers.

**Description** Find Related PubChem Substances (same)

To find the number of related PubChem substances for a PubChem compound, we can use `elink` with Entrez link `pccompound_pcsubstance_same`:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "14333"[UID] | \
> elink -target pcsubstance -name pccompound_pcsubstance_same | \
> xtract -pattern ENTREZ_DIRECT -element Count
46
```

And then to retrieve information about the PubChem substances, we can pipe these results into `efetch` and `xtract`, to extract out specific information such as the SID, CurrentSourceName, SourceID, and DepositDate:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "14333"[UID] | \
> elink -target pcsubstance -name pccompound_pcsubstance_same | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element SID CurrentSourceName SourceID DepositDate
...
386279116	Wiley	140582	2019/10/23 00:00
384255927	VWR, Part of Avantor	101188-940	2019/05/24 00:00
377906080	Alichem	019131604	2019/01/17 00:00
376645298	Thieme Chemistry	13049013-294698918	2018/11/30 00:00
376199840	Alfa Aesar	A18364	2018/11/08 00:00
375100488	3B Scientific (Wuhan) Corp	3B3-050245	2018/07/17 00:00
355162532	MuseChem	M046509	2018/03/27 00:00
...
```
_tested on 2020.06.23, total count was 46._

