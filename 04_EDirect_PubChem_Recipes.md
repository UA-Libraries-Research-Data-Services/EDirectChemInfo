# PubChem EDirect Recipes

**Notes**

> 1. `user@computer:~$` represents an example terminal prompt name. Actual command/argument input is after the `$`.
> 2. Replace `name@xx.edu` with your email address.
> 3. `\` followed by `>` on the next line represents continued terminal input. You will need to delete the `>` symbol in order to run the scripts as a copy/paste into terminal.
> 4. You should validate your own EDirect scripts and results as there may be unintentional mistakes in these recipes. A convenient method is to compare your EDirect results to the NCBI Web interface search results: [https://www.ncbi.nlm.nih.gov/](https://www.ncbi.nlm.nih.gov/).

### PubChem Searching

**Description:** Search PubChem Compound via InChIKey and Retrieve Data

**Description:** Retrieve Pre-Computed Linked Similar Compounds

**Description:** Find Compounds with Specific Atom and Bond Attributes

**Description:** Find Number of Compounds by Create Date

We can use the Create Date field `[CDAT]` in PubChem Compound to search for compound records created in PubChem Compound on a specific date. The `esearch` results are then piped into `efetch` to retrieve the XML docsum compound records. Next, the `xtract` function is used to extract out the CreateDate. The extracted data is then piped into the EDirect alias function `sort-uniq-count-rank`, which sorts the data by highest frequency. Finally, I added an additional `sort` command, to sort by date (`-k2` for second column), instead of number of compounds.  

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

If we want to obtain the number of compounds in PubChem by create date over a longer period of time (e.g., several months to years), it probably does not make sense to use `efetch`, as the number of compounds will be hundreds of thousands or even likely millions. Trying to download all of the docsums for this many record likely won't work. As an alternative, we can use `esearch` in a for loop, and extract out the Count value from the `esearch` ENTREZ_DIRECT query XML summary. For example, if we wanted the number of compounds created in PubChem for 2019 by month:

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

Note that in the above for loop bash script, I added a sleep of one second between each `esearch` query in an effort to not overload the NCBI servers.


**Description** Find Related PubChem Substances (+ count)





















