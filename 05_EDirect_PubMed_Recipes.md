# PubMed EDirect Recipes

**Notes**

> 1. `user@computer:~$` represents an example terminal prompt name. Actual command/argument input is after the `$`.
> 2. Replace `name@xx.edu` with your email address.
> 3. `\` followed by `>` on the next line represents continued terminal input. You will need to delete the `>` symbol in order to run the scripts as a copy/paste into terminal.
> 4. You should validate your own EDirect scripts and results as there may be unintentional mistakes in these recipes. A convenient method is to compare your EDirect results to the NCBI Web interface search results: [https://www.ncbi.nlm.nih.gov/](https://www.ncbi.nlm.nih.gov/).

## PubMed EDirect

### Search PubMed by Keyword and/or MeSH and Retrieve References

We can use the EDirect function `esearch` to query PubMed. However, before trying to retrieve any of the results with `efetch`, it is a good idea to check that the count range is manageable (e.g., on the order of several thousand). In addition, see the [EDirect Query Translation Instructions](https://github.com/vfscalfani/EDirectChemInfo/blob/master/01_EDirect_Intro.md#edirect-query-translation-via-debug-flag) for how to use the `-debug` option to view how your query is interpreted in PubMed.

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "hydrogel-based drug delivery"
<ENTREZ_DIRECT>
  <Db>pubmed</Db>
  <WebEnv>MCID...</WebEnv>
  <QueryKey>1</QueryKey>
  <Count>436</Count>
  <Step>1</Step>
  <Email>name@xx.edu</Email>
</ENTREZ_DIRECT>
```

After deciding if the `esearch` query is appropriate, we can start to pipe the `esearch` results into other EDirect functions. For example, the below script first uses `esearch` to query PubMed for "hydrogel-based drug delivery", and then these results are piped (`|`) into `efetch` to retrieve the results as XML format. The `efetch` results are then piped to the `xtract` function where several bibliographic elements of the PubMed XML records are extracted into a table:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "hydrogel-based drug delivery" | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ArticleTitle ISOAbbreviation PubDate/Year Volume Issue MedlinePgn
33424262	El-Masry	SM	Hydrogel-based matrices for controlled drug delivery of etamsylate: Prediction of in-vivo plasma profiles.	Saudi Pharm J	2020	28	12	1704-1718
33398321	Chen	W	Magnetically actuated intelligent hydrogel-based child-parent microrobots for targeted drug delivery.	J Mater Chem B	2021
33396629	Dehshahri	A	New Horizons in Hydrogels for Methotrexate Delivery.	Gels	2020	7	1
33387892	Amiri	M	Hydrogel beads-based nanocomposites in novel drug delivery platforms: Recent trends and developments.	Adv Colloid Interface Sci	2020	288	102316
33378390	Kloepping	KC	Triphenylphosphonium derivatives disrupt metabolism and inhibit melanoma growth in vivo when delivered via a thermosensitive hydrogel.	PLoS One	2020	15	12	e0244540
33359482	Agarwal	P	Structural characterization and developability assessment of sustained release hydrogels for rapid implementation during preclinical studies.	Eur J Pharm Sci	2021	158	105689
...
...
```

_tested on 2021.01.27, EDirect 14.4, total count was 436._


Note that if we want to extract out the DOIs, we can use the `xtract` `-block` option like this:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "hydrogel-based drug delivery" | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ISOAbbreviation PubDate/Year Volume Issue MedlinePgn \
> -block ArticleId -if ArticleId@IdType -equals doi -doi ArticleId
33424262	El-Masry	SM	Saudi Pharm J	2020	28	12	1704-1718	https://doi.org/10.1016%2Fj.jsps.2020.10.016
33398321	Chen	W	J Mater Chem B	2021	https://doi.org/10.1039%2Fd0tb02384a
33396629	Dehshahri	A	Gels	2020	7	1	https://doi.org/10.3390%2Fgels7010002
33387892	Amiri	M	Adv Colloid Interface Sci	2020	288	102316	https://doi.org/10.1016%2Fj.cis.2020.102316
33378390	Kloepping	KC	PLoS One	2020	15	12	e0244540	https://doi.org/10.1371%2Fjournal.pone.0244540
33359482	Agarwal	P	Eur J Pharm Sci	2021	158	105689	https://doi.org/10.1016%2Fj.ejps.2020.105689
...
...
```
_tested on 2021.01.27, EDirect 14.4, total count was 436._


There is a lot going on with the last line of code that extracts out the DOIs: `-block ArticleId -if ArticleId@IdType -equals doi -doi ArticleId`. Let's look at part of a PubMed XML file to help interpret what is going on here:

```console
...
...
<ArticleIdList>
        <ArticleId IdType="pubmed">17630804</ArticleId>
        <ArticleId IdType="doi">10.1021/jo071035l</ArticleId>
</ArticleIdList>
...
...
```

The `-block` option limits the extraction to a particular section of the XML, in this case the `ArticleId` tags. The `@` defines the desired IdType `doi` element attribute. Finally, the `-doi` is an `xtract` string option that prefixes https://doi.org/ before the extracted ArticleId doi. There is a more thorough explanation of `-block` and extracting out the DOIs with the `-block` option in the [NLM Insider's Guide to Accessing NLM Data Part 4](https://dataguide.nlm.nih.gov/classes/edirect-for-pubmed/samplecode4.html#output-a-list-of-pmids-and-corresponding-dois) and [Entrez Programming Utilities Help Manual](https://www.ncbi.nlm.nih.gov/books/NBK179288/).


Similarly to the above script, we can specify particular fields to query within PubMed. The below script searches for "ionic liquids" in the MeSH term field (`[MESH]`) and "imidazolium" in all fields. Note that the internal quotes are escaped (`\`), which is sometimes necessary for the query to be interpreted correctly when using phrases.


```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "\"ionic liquids\"[MESH] AND imidazolium" | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ISOAbbreviation PubDate/Year Volume Issue MedlinePgn \
> -block ArticleId -if ArticleId@IdType -equals doi -doi ArticleId
33396149	Hu	LX	Ecotoxicol Environ Saf	2021	208	111629	https://doi.org/10.1016%2Fj.ecoenv.2020.111629
33346267	Kaur	M	Phys Chem Chem Phys	2021	23	1	320-328	https://doi.org/10.1039%2Fd0cp04513f
33253998	Tashakkori	P	J Chromatogr A	2021	1635	461741	https://doi.org/10.1016%2Fj.chroma.2020.461741
33142384	Ren	YM	Zhonghua Lao Dong Wei Sheng Zhi Ye Bing Za Zhi	2020	38	10	767-769	https://doi.org/10.3760%2Fcma.j.cn121094-20191010-00483
33135708	Kumar	S	Phys Chem Chem Phys	2020	22	43	25255-25263	https://doi.org/10.1039%2Fd0cp04014b
32822985	Zuo	L	J Chromatogr A	2020	1628	461446	https://doi.org/10.1016%2Fj.chroma.2020.461446
32711338	Zunita	M	Bioresour Technol	2020	315	123864	https://doi.org/10.1016%2Fj.biortech.2020.123864
...
...
```
_tested on 2021.01.27, EDirect 14.4, total count was 1000._


### Calculate the Most Frequent Journal Titles For a PubMed Search

The below script uses `esearch` to query PubMed for "Artificial Intelligence" in the `[MESH]` field and "drug discovery" in the `[ALL]` field. The records are then retrieved as XML format using the `efetch` function, followed by extracting out the journal names (`IsoAbbreviation`) using `xtract`. The `xtract` results are then piped to the EDirect alias function `sort-uniq-count-rank`, which sorts the data by highest frequency:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "\"Artificial Intelligence\"[MESH] AND \"drug discovery\"[ALL]" | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element ISOAbbreviation | \
> sort-uniq-count-rank
169	J Chem Inf Model
53	BMC Bioinformatics
49	PLoS One
40	Bioinformatics
39	Methods Mol Biol
33	Mol Pharm
32	Molecules
29	Sci Rep
28	Drug Discov Today
28	J Comput Aided Mol Des
24	J Med Chem
23	Expert Opin Drug Discov
23	Int J Mol Sci
19	Curr Top Med Chem
18	Mol Inform
17	Future Med Chem
16	Nucleic Acids Res
15	Nature
15	PLoS Comput Biol
14	IEEE/ACM Trans Comput Biol Bioinform
...
...
```
_tested on 2021.01.27, EDirect 14.4._

### Calculate The Frequency of Author Publications for a University Department in PubMed

The below script uses `esearch` to query PubMed for ("university of alabama" AND tuscaloosa) in the affiliation field (`[AFFL]`). Tuscaloosa was added to limit the number of retrieved records associated with The University of Alabama at Birmingham and The University of Alabama at Huntsville. Another approach could have been to use the NOT operator: `"(university of alabama[AFFL]) NOT (birmingham[AFFL] OR huntsville[AFFL])"`. However, the latter approach may eliminate any collaborative articles with these institutions (affiliation searches are challenging!). Next, the results were retrieved as XML using `efetch`, followed by piping these results to `xtract` to extract out the publication year (`PubDate/Year`) and sort by frequency with `sort-uniq-count-rank`. Note that a conditional statement was used in the `xtract` pattern to only extract results from articles if the affiliation contains both `chemistry` and `tuscaloosa`. The thought here was that this would limit the results (mostly) to author publications from The University of Alabama (Tuscaloosa) Department of Chemistry:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "(university of alabama[AFFL] AND tuscaloosa[AFFL])" | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -if Affiliation -contains chemistry -and Affiliation -contains tuscaloosa -element PubDate/Year | \
> sort-uniq-count-rank
65	2015
64	2020
59	2017
53	2018
49	2019
45	2016
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
...
...
```

_tested on 2021.01.27, EDirect 14.4._

If instead we want to know individual Author numbers in PubMed instead of total publications by year, we can change the `xtract` pattern:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "(university of alabama[AFFL] AND tuscaloosa[AFFL])" | \
> efetch -format xml | \
> xtract -pattern Author -if Affiliation -contains chemistry -and Affiliation -contains tuscaloosa -element LastName Initials | \
> sort-uniq-count-rank
108	Dixon	DA
53	Vasiliu	M
34	Vincent	JB
32	Rogers	RD
22	Bowman	MK
20	Fang	Z
14	Grant	DJ
14	Thanthiriwatte	KS
13	Cassady	CJ
12	Frantom	PA
11	Chen	M
11	Kelley	SP
11	Shamshina	JL
10	Kispert	LD
10	Metzger	RM
10	Papish	ET
9	Gerlach	DL
9	Li	S
9	Timkovich	R
8	Matus	MH
...
...
```
_tested on 2021.01.27, EDirect 14.4._


Let's take a closer look at the conditional `xtract` pattern specifying to extract data only if the affiliation contains chemistry and tuscaloosa:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "(university of alabama[AFFL] AND tuscaloosa[AFFL])" | \
> efetch -format xml | \
> xtract -pattern Author -if Affiliation -contains chemistry -and Affiliation -contains tuscaloosa -element LastName Initials Affiliation
Rowe	SJ	Department of Chemistry and Biochemistry, University of Alabama, Tuscaloosa, Alabama, USA.
Mecaskey	RJ	Department of Chemistry and Biochemistry, University of Alabama, Tuscaloosa, Alabama, USA.
Nasef	M	Department of Chemistry and Biochemistry, University of Alabama, Tuscaloosa, Alabama, USA.
Talton	RC	Department of Chemistry and Biochemistry, University of Alabama, Tuscaloosa, Alabama, USA.
Sharkey	RE	Department of Chemistry and Biochemistry, University of Alabama, Tuscaloosa, Alabama, USA.
Halliday	JC	Department of Chemistry and Biochemistry, University of Alabama, Tuscaloosa, Alabama, USA.
...
...
```
_tested on 2021.01.27, EDirect 14.4._


With a quick look at the ~1000 results, it seemed like we extracted out the intended data, however, I did notice some false positive results. One example was an article from The University of Alabama (Tuscaloosa) Department of Biological Sciences with an external collaborator having "Chemistry" in the Institution name. Other errors could be what we unintentionally excluded such as any records that do not have Tuscaloosa in the affiliation field (i.e., only a partial address or zip code). These type of affiliation searches are tricky, so test often and think through the results carefully.


### Retrieve Cites and Cited References in PubMed

The `elink` function can retrieve associated cites and cited references for PubMed records. Cites are the available references in the article (i.e. bibliography list) and cited are references to the article. Not all PubMed articles have associated citation reference data. The available reference data are from the [NIH Open Citation Collection Dataset](https://pubmed.ncbi.nlm.nih.gov/31600197/).

To retrieve the number of cites for a PubMed article, we can use the `elink` function, followed by `xtract` to extract out the Count element:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "29978703[PMID]" | \
> elink -cites | \
> xtract -pattern ENTREZ_DIRECT -element Count
11
```
_tested on 2021.01.27, EDirect 14.4._


Add `efetch` to your script if you want to retrieve the records:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "29978703[PMID]" | \
> elink -cites | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ISOAbbreviation PubDate/Year Volume Issue MedlinePgn \
> -block ArticleId -if ArticleId@IdType -equals doi -doi ArticleId
29382051	Stefanachi	A	Molecules	2018	23	2	https://doi.org/10.3390%2Fmolecules23020250
27709885	Stempel	E	Acc Chem Res	2016	49	11	2390-2402	https://doi.org/10.1021%2Facs.accounts.6b00265
26661053	James	MJ	Chemistry	2016	22	9	2856-81	https://doi.org/10.1002%2Fchem.201503835
26313158	Liu	BY	Org Lett	2015	17	17	4380-3	https://doi.org/10.1021%2Facs.orglett.5b02230
22969063	Han	X	Angew Chem Int Ed Engl	2012	51	41	10390-3	https://doi.org/10.1002%2Fanie.201205238
18620434	Martin	R	Acc Chem Res	2008	41	11	1461-73	https://doi.org/10.1021%2Far800036s
...
...
```

_tested on 2021.01.27, EDirect 14.4._

Getting the cited records only requires changing `-cites` to `-cited`:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "29978703[PMID]" | \
> elink -cited | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
> Author/Initials ISOAbbreviation PubDate/Year Volume Issue MedlinePgn \
> -block ArticleId -if ArticleId@IdType -equals doi -doi ArticleId
32537619	Fernandes	RA	Chem Commun (Camb)	2020	56	61	8569-8590	https://doi.org/10.1039%2Fd0cc02659j
32317969	Lautié	E	Front Pharmacol	2020	11	397	https://doi.org/10.3389%2Ffphar.2020.00397
30707497	Ivanova	OA	Chem Rec	2019	https://doi.org/10.1002%2Ftcr.201800166
30259622	Tymann	D	Angew Chem Int Ed Engl	2018	57	47	15553-15557	https://doi.org/10.1002%2Fanie.201808578
```
_tested on 2021.01.27, EDirect 14.4._

We can answer some interesting questions with the NIH Open Citation Collection Data. For example, I noticed that the PubMed XML records for articles in *J Cheminform* contain a reference list for articles in PubMed. So, theoretically, if we query PubMed for *J Cheminform*, extract out all of the references, and sort these by frequency, we should get the most cited references in *J Cheminform* article bibliographies (caveat: in the available PubMed citation data).

In the below script, the `xtract` pattern creates a new line for each extracted reference citation PMID from the ArticleId with pubmed attribute field:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "J Cheminform[JOUR]" | \
> efetch -format xml | \
> xtract -pattern Reference -if ArticleId@IdType -equals pubmed -element ArticleId | \
> sort-uniq-count-rank | \
> head -n 20
90	20426451
83	21982300
80	21948594
65	12653513
47	11259830
40	10592235
38	16796559
38	27899562
37	26400175
36	8709122
33	17154509
33	19498078
30	16381955
29	21059682
29	23343401
28	15667143
28	24214965
27	17932057
27	21425294
27	22587354
```
_tested on 2021.01.27, EDirect 14.4._

Note that when quickly viewing all of the sorted results (~10,000 lines), I did see maybe a 100 or so entries with two PMIDs per line or a DOI and a PMID. Since we specifically defined the pubmed IdType attribute, it is not exactly clear to me yet why there would be some extra data in there. Perhaps it is a mistake or inconsistency in the *J Cheminform* PubMed XML records. 


We can take a quick look at the top 10 cited references using a for loop::

```console
user@computer:~$ for refs in \
>     "20426451" \
>     "21982300" \
>     "21948594" \
>     "12653513" \
>     "11259830" \
>     "10592235" \
>     "16796559" \
>     "27899562" \
>     "26400175" \
>     "8709122"
> do
>      esearch -email name@xx.edu -db pubmed -query "$refs[PMID]" |
>      efetch -format xml |
>      xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName \
>      Author/Initials ArticleTitle ISOAbbreviation PubDate/Year Volume Issue MedlinePgn \
>      -block ArticleId -if ArticleId@IdType -equals doi -doi ArticleId    
>      sleep 1 
> done
20426451	Rogers	D	Extended-connectivity fingerprints.	J Chem Inf Model	2010	50	5	742-54	https://doi.org/10.1021%2Fci100050t
21982300	O'Boyle	NM	Open Babel: An open chemical toolbox.	J Cheminform	2011	3	33	https://doi.org/10.1186%2F1758-2946-3-33
21948594	Gaulton	A	ChEMBL: a large-scale bioactivity database for drug discovery.	Nucleic Acids Res	2012	40	Database issue	D1100-7https://doi.org/10.1093%2Fnar%2Fgkr777
12653513	Steinbeck	C	The Chemistry Development Kit (CDK): an open-source Java library for Chemo- and Bioinformatics.	J Chem Inf Comput Sci	43	2	493-500	https://doi.org/10.1021%2Fci025584y
11259830	Lipinski	CA	Experimental and computational approaches to estimate solubility and permeability in drug discovery and development settings.	Adv Drug Deliv Rev	2001	46	1-3	3-26	https://doi.org/10.1016%2Fs0169-409x%2800%2900129-0
10592235	Berman	HM	The Protein Data Bank.	Nucleic Acids Res	2000	28	1	235-42	https://doi.org/10.1093%2Fnar%2F28.1.235
16796559	Steinbeck	C	Recent developments of the chemistry development kit (CDK) - an open-source java library for chemo- and bioinformatics.Curr Pharm Des	2006	12	17	2111-20	https://doi.org/10.2174%2F138161206777585274
27899562	Gaulton	A	The ChEMBL database in 2017.	Nucleic Acids Res	2017	45	D1	D945-D954	https://doi.org/10.1093%2Fnar%2Fgkw1074
26400175	Kim	S	PubChem Substance and Compound databases.	Nucleic Acids Res	2016	44	D1	D1202-13	https://doi.org/10.1093%2Fnar%2Fgkv951
8709122	Bemis	GW	The properties of known drugs. 1. Molecular frameworks.J Med Chem	1996	39	15	2887-93	https://doi.org/10.1021%2Fjm9602928
```

Another interesting question would be what is the most cited Journal in *J Cheminform* articles (in the available PubMed citation data)? In the below script, we take a similar approach to above, but instead of extracting out the PMIDs, we extract out the Citation element. The line `cut -d "." -f 1` deletes any data after the Journal abbreviation (e.g., "Drug Discov Today. 2006 Dec;11(23-24):1046-53" becomes "Drug Discov Today"). 
```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "J Cheminform[JOUR]" | \
> efetch -format xml | \
> xtract -pattern Reference -element Citation | \
> cut -d "." -f 1 | \
> sort-uniq-count-rank
2274	J Chem Inf Model
1268	J Cheminform
1167	Nucleic Acids Res
930	J Med Chem
620	Bioinformatics
576	J Chem Inf Comput Sci
425	J Comput Aided Mol Des
381	BMC Bioinformatics
351	Drug Discov Today
252	Mol Inform
247	J Comput Chem
236	PLoS One
222	Nature
215	Nat Rev Drug Discov
202	Proc Natl Acad Sci U S A
181	Science
148	Anal Chem
147	Proteins
140	J Mol Graph Model
...
...
```
_tested on 2021.01.27, EDirect 14.4._

Note that there is some inconsistency in the citation formats results here as well that would need to be evaluated and cleaned up for a more thorough analysis. For example, some of the extracted Citations included author names and article titles, so the `cut` command deleting everything after the first `.` does not suffice for those data entries.


### Number of Records in PubMed by Create Date

Here is an interesting script to retrieve the count of PubMed records by create date (`[CRDT]`) for each month of 2020. Since there are over 100,000 records added to PubMed every month, a strategy using `efetch` likely would not work (i.e., trying to retrieve 500,000+ records would take a long time).

```console

user@computer:~$ for date in \
>     "2020/01" \
>     "2020/02" \
>     "2020/03" \
>     "2020/04" \
>     "2020/05" \
>     "2020/06" 
> do
>     esearch -email name@xx.edu -db pubmed -query "$date[CRDT]" |
>     xtract -pattern ENTREZ_DIRECT -lbl "$date" -element Count
>     sleep 1 
> done
2020/01	108863
2020/02	107561
2020/03	106386
2020/04	124575
2020/05	121324
2020/06	124664
```
_tested on 2021.01.27, EDirect 14.4._



### Number of Records in PubMed that are Also freely Available in PubMed Central

Let's say we wanted to know how many articles in *J Chem Inf Model* (indexed in PubMed) are available freely in PubMed Central. We can first get a count for *J Chem Inf Model* records in PubMed by querying PubMed in the Journal field (`[JOUR]`), followed by retrieving the records, extracting out the PubDate, and then sorting by frequency:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "J Chem Inf Model[JOUR]" | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element PubDate | \
> cut -d " " -f 1 | \
> sort-uniq-count-rank | \
> sort -k2,2
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
490	2019
612	2020
65	2021
```
_tested on 2021.01.27, EDirect 14.4._


In the above script, the line `cut -d " " -f 1` deletes any data appearing after the year and `sort -k2,2` sorts the data by the second column. Next, we can add `elink` into our script to find the linked records in PubMed Central (`pmc`) from the Entrez link `pubmed_pmc`:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "J Chem Inf Model[JOUR]" | \
> elink -target pmc -name pubmed_pmc | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element PubDate | \
> cut -d " " -f 1 | \
> sort-uniq-count-rank | \
> sort -k2,2
1	2005
3	2006
7	2007
14	2008
31	2009
26	2010
62	2011
38	2012
55	2013
59	2014
38	2015
32	2016
39	2017
43	2018
57	2019
51	2020
```

_tested on 2021.01.27, EDirect 14.4._

Note that if you have a query returning tens of thousands of results, you would likely want to use a strategy without `efetch`, such as adding a date into your `esearch` query, followed by extracting out the count element from the XML.


