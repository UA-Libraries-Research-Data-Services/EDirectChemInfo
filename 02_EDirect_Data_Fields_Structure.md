# Available EDirect Databases, Data Fields, and Data Structures

**Notes**

> 1. `user@computer:~$` represents an example terminal prompt name. Actual command/argument input is after the `$`.
> 2. Replace `name@xx.edu` with your email address.
> 3. `\` followed by `>` on the next line represents continued terminal input. You will need to delete the `>` symbol in order to run the scripts as a copy/paste into terminal.
> 4. You should validate your own EDirect scripts and results as there may be unintentional mistakes in these recipes. A convenient method is to compare your EDirect results to the NCBI Web interface search results: [https://www.ncbi.nlm.nih.gov/](https://www.ncbi.nlm.nih.gov/).

We can view available Entrez databases, data fields, and links (connected records) with the EDirect `einfo` function. To retrieve a list of all databases, use the `-dbs` argument:

```console

user@computer:~$ einfo -email name@xx.edu -dbs
annotinfo
assembly
biocollections
bioproject
biosample
biosystems
blastdbinfo
books
cdd
clinvar
dbvar
gap
gapplus
gds
gene
genome
geoprofiles
grasp
gtr
homologene
ipg
medgen
mesh
ncbisearch
nlmcatalog
nuccore
nucleotide
omim
orgtrack
pcassay
pccompound
pcsubstance
pmc
popset
protein
proteinclusters
protfam
pubmed
seqannot
snp
sra
structure
taxonomy

```

## PubChem Compound EDirect Fields, Links, and Data

This EDirectChemInfo repository focuses on searching the PubChem Compound, PubMed, and PubChem BioAssay databases. So let's take a closer look at these three databases, starting with the PubChem Compound (`-db pccompound`) database. The `einfo` arguments `-fields` and `-links` provide information about the available data fields and linked information, respectively:

```console

user@computer:~$ einfo -email name@xx.edu -db pccompound -fields
AC	ActiveAidCount
ACC	AtomChiralCount
ACDC	AtomChiralDefCount
ACUC	AtomChiralUndefCount
ALL	All Fields
BCC	BondChiralCount
BCDC	BondChiralDefCount
BCUC	BondChiralUndefCount
CDAT	CreateDate
CPLX	Complexity
CSYN	CompleteSynonym
CUC	CovalentUnitCount
DCNT	DepositorCount
DCSY	DepositorCompleteSynonym
DSYN	DepositorSynonym
ELMT	Element
EMAS	ExactMass
FILT	Filter
HAC	HeavyAtomCount
HBAC	HydrogenBondAcceptorCount
HBDC	HydrogenBondDonorCount
IAC	IsotopeAtomCount
IKEY	InChIKey
INCH	InChI
MMAS	MonoisotopicMass
MSHT	MeSHTerm
MW	MolecularWeight
PAID	PharmActionID
PHMA	PharmAction
RBC	RotatableBondCount
SID	SubstanceID
SRCC	SourceCategory
SRC	SourceName
STID	StructureID
SYNO	Synonym
TAC	TotalAidCount
TFC	TotalFormalCharge
TPSA	TPSA
UID	CompoundID
UPAC	IUPACName
XLGP	XLogP

user@computer:~$ einfo -email name@xx.edu -db pccompound -links
pccompound_biosystems	BioSystems
pccompound_gene	Gene
pccompound_mesh	MeSH Keyword
pccompound_nuccore	Nucleotide Sequences
pccompound_omim	OMIM
pccompound_pcassay	BioAssays
pccompound_pcassay_active	BioAssays, Active
pccompound_pcassay_activityconcmicromolar	BioAssays, activity concentration at/below 1 uM
pccompound_pcassay_activityconcnanomolar	BioAssays, activity concentration at/below 1 nM
pccompound_pcassay_inactive	BioAssays, Inactive
pccompound_pcassay_probe	BioAssays, Probe
pccompound_pccompound	Similar Compounds
pccompound_pccompound_3d	Similar Conformers
pccompound_pccompound_mixture	Mixture/Component Compounds
pccompound_pccompound_parent	Parent Compound
pccompound_pccompound_parent_connectivity_pulldown	Same Parent, Connectivity
pccompound_pccompound_parent_isotopes_pulldown	Same Parent, Isotopes
pccompound_pccompound_parent_pulldown	Same Parent
pccompound_pccompound_parent_stereo_pulldown	Same Parent, Stereochemistry
pccompound_pccompound_parent_tautomer_pulldown	Same Parent, Any Tautomer
pccompound_pccompound_sameanytautomer_pulldown	Same, Any Tautomer
pccompound_pccompound_sameconnectivity_pulldown	Same, Connectivity
pccompound_pccompound_sameisotopic_pulldown	Same, Isotopes
pccompound_pccompound_samestereochem_pulldown	Same, Stereochemistry
pccompound_pcsubstance	PubChem Mixture Substances
pccompound_pcsubstance_same	PubChem Same Substances
pccompound_pmc	PMC Articles
pccompound_protein	Protein Sequences
pccompound_pubmed	PubMed Citations
pccompound_pubmed_mesh	PubMed (MeSH Keyword)
pccompound_pubmed_publisher	PubMed (Publisher)
pccompound_structure	Protein Structures
pccompound_taxonomy	Taxonomy

```
Now that we have an understanding about what kind of data is available in the PubChem Compound database, let's take a look at a PubChem Compound record using the `esearch` and `efetch` functions:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query 512323[UID]
<ENTREZ_DIRECT>
  <Db>pccompound</Db>
  <WebEnv>MCID...</WebEnv>
  <QueryKey>1</QueryKey>
  <Count>1</Count>
  <Step>1</Step>
  <Email>name@xx.edu</Email>
</ENTREZ_DIRECT>

```

We searched PubChem for the Compound Identifier 512323 using `esearch`, and the NCBI Entrez server returned a summary of the search results. The WebEnV and QueryKey specify the location of the search results on the NCBI server. In order to retrieve the data, we can pipe (`|`) the `esearch` results directly into the `efetch` function:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query 512323[UID] | \
> efetch -format docsum
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE DocumentSummarySet PUBLIC "-//NLM//DTD esummary pccompound 20170720//EN" "https://eutils.ncbi.nlm.nih.gov/eutils/dtd/20170720/esummary_pccompound.dtd">
<DocumentSummarySet status="OK">
  <DbBuild>Build210125-0720m.1</DbBuild>
  <DocumentSummary>
    <Id>512323</Id>
    <CID>512323</CID>
    <SourceCategoryList>
      <string>Chemical Vendors</string>
      <string>Governmental Organizations</string>
      <string>Subscription Services</string>
      <string>Curation Efforts</string>
      <string>Journal Publishers</string>
      <string>Research and Development</string>
      <string>Legacy Depositors</string>
    </SourceCategoryList>
    <CreateDate>2005/08/01 00:00</CreateDate>
    <SynonymList>
      <string>CHEMBL1791149</string>
      <string>89647-10-9</string>
      <string>Uridine, 2&apos;-deoxy-5-(2-thienyl)-</string>
      <string>5-(2&apos;-Thienyl)-2&apos;-beta-deoxyuridine</string>
      <string>SCHEMBL1635430</string>
      <string>5-thien-2-yl-2&apos;-deoxyuridine</string>
      <string>CTK2J2646</string>
      <string>5-(2-thienyl)-2&apos;-deoxyuridine</string>
      <string>5-(2&apos;-Thienyl)-2&apos;-deoxyuridine-</string>
      <string>BDBM50407986</string>
      <string>1-[(2R,4S,5R)-4-hydroxy-5-(hydroxymethyl)oxolan-2-yl]-5-thiophen-2-ylpyrimidine-2,4-dione</string>
      <string>1-[(2R,4S,5R)-4-hydroxy-5-(hydroxymethyl)tetrahydrofuran-2-yl]-5-(2-thienyl)pyrimidine-2,4-dione</string>
    </SynonymList>
    <IUPACName>1-[(2R,4S,5R)-4-hydroxy-5-(hydroxymethyl)oxolan-2-yl]-5-thiophen-2-ylpyrimidine-2,4-dione</IUPACName>
    <CanonicalSmiles>C1C(C(OC1N2C=C(C(=O)NC2=O)C3=CC=CS3)CO)O</CanonicalSmiles>
    <IsomericSmiles>C1[C@@H]([C@H](O[C@H]1N2C=C(C(=O)NC2=O)C3=CC=CS3)CO)O</IsomericSmiles>
    <RotatableBondCount>3</RotatableBondCount>
    <MolecularFormula>C13H14N2O5S</MolecularFormula>
    <MolecularWeight>310.330</MolecularWeight>
    <MolecularWeightSort>310330</MolecularWeightSort>
    <TotalFormalCharge>0</TotalFormalCharge>
    <XLogP>-0.2</XLogP>
    <HydrogenBondDonorCount>3</HydrogenBondDonorCount>
    <HydrogenBondAcceptorCount>6</HydrogenBondAcceptorCount>
    <Complexity>483.000</Complexity>
    <ComplexitySort>483000</ComplexitySort>
    <HeavyAtomCount>21</HeavyAtomCount>
    <AtomChiralCount>3</AtomChiralCount>
    <AtomChiralDefCount>3</AtomChiralDefCount>
    <AtomChiralUndefCount>0</AtomChiralUndefCount>
    <BondChiralCount>0</BondChiralCount>
    <BondChiralDefCount>0</BondChiralDefCount>
    <BondChiralUndefCount>0</BondChiralUndefCount>
    <IsotopeAtomCount>0</IsotopeAtomCount>
    <CovalentUnitCount>1</CovalentUnitCount>
    <TPSA>127</TPSA>
    <ActiveAidCount>1</ActiveAidCount>
    <TotalAidCount>37</TotalAidCount>
    <InChIKey>PCDQBRGMSMVLDZ-IQJOONFLSA-N</InChIKey>
    <ProbeAidCount>0</ProbeAidCount>
    <InChI>InChI=1S/C13H14N2O5S/c16-6-9-8(17)4-11(20-9)15-5-7(10-2-1-3-21-10)12(18)14-13(15)19/h1-3,5,8-9,11,16-17H,4,6H2,(H,14,18,19)/t8-,9+,11+/m0/s1</InChI>
  </DocumentSummary>
</DocumentSummarySet>

```

`efetch` returned the CID record data as document summary XML format (for other formats see `efetch -help`). XML is useful, but we probably want to parse the data into a table for easier viewing and analysis. EDirect contains a function called `xtract` that can convert the Entrez XML data into tables. See `xtract -help` for more information. In brief, you will need to select a main XML heading tag to define the extract pattern and then specify the data you want to extract with the sub-heading tag names (elements). For example, in the above CID record data, we can set the pattern to DocumentSummary (the first main XML tag in this case), and then the elements to a few of the sub-heading tags we are interested such as IsomericSmiles, CID, InChIKey, MolecularFormula, and MolecularWeight:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query 512323[UID] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey MolecularFormula MolecularWeight
C1[C@@H]([C@H](O[C@H]1N2C=C(C(=O)NC2=O)C3=CC=CS3)CO)O	512323	PCDQBRGMSMVLDZ-IQJOONFLSA-N	C13H14N2O5S	310.330

```

## PubMed EDirect Fields, Links, and Data

Similarly to PubChem Compound, let's preview the available PubMed database indexed fields, links, and data structure:

```console

user@computer:~$ einfo -email name@xx.edu -db pubmed -fields
AFFL	Affiliation
ALL	All Fields
AUCL	Author Cluster ID
AUID	Author - Identifier
AUTH	Author
BOOK	Book
CDAT	Date - Completion
CNTY	Place of Publication
COIS	Conflict of Interest Statements
COLN	Author - Corporate
CRDT	Date - Create
DSO	DSO
ECNO	EC/RN Number
EDAT	Date - Entrez
ED	Editor
EID	Extended PMID
EPDT	Electronic Publication Date
FAUT	Author - First
FILT	Filter
FINV	Investigator - Full
FULL	Author - Full
GRNT	Grant Number
INVR	Investigator
ISBN	ISBN
ISS	Issue
JOUR	Journal
LANG	Language
LAUT	Author - Last
LID	Location ID
MAJR	MeSH Major Topic
MDAT	Date - Modification
MESH	MeSH Terms
MHDA	Date - MeSH
OTRM	Other Term
PAGE	Pagination
PAPX	Pharmacological Action
PDAT	Date - Publication
PID	Publisher ID
PPDT	Print Publication Date
PS	Subject - Personal Name
PTYP	Publication Type
PUBN	Publisher
SI	Secondary Source ID
SUBH	MeSH Subheading
SUBS	Supplementary Concept
TIAB	Title/Abstract
TITL	Title
TT	Transliterated Title
UID	UID
VOL	Volume
WORD	Text Word

user@computer:~$ einfo -email name@xx.edu -db pubmed -links
pubmed_assembly	Assembly
pubmed_bioproject	Project Links
pubmed_biosample	BioSample Links
pubmed_biosystems	BioSystem Links
pubmed_books_refs	Cited in Books
pubmed_cdd	Conserved Domain Links
pubmed_clinvar_calculated	ClinVar (calculated)
pubmed_clinvar	ClinVar
pubmed_dbvar	dbVar
pubmed_gap	dbGaP Links
pubmed_gds	GEO DataSet Links
pubmed_gene_bookrecords	Gene (from Bookshelf)
pubmed_gene_citedinomim	Gene (OMIM) Links
pubmed_gene	Gene Links
pubmed_gene_pmc_nucleotide	Gene (nucleotide/PMC)
pubmed_gene_rif	Gene (GeneRIF) Links
pubmed_genome	Genome Links
pubmed_geoprofiles	GEO Profile Links
pubmed_homologene	HomoloGene Links
pubmed_medgen_bookshelf_cited	MedGen (Bookshelf cited)
pubmed_medgen_genereviews	MedGen (GeneReviews)
pubmed_medgen	MedGen
pubmed_medgen_omim	MedGen (OMIM)
pubmed_nuccore	Nucleotide Links
pubmed_nuccore_refseq	Nucleotide (RefSeq) Links
pubmed_nuccore_weighted	Nucleotide (Weighted) Links
pubmed_omim_bookrecords	OMIM (from Bookshelf)
pubmed_omim_calculated	OMIM (calculated) Links
pubmed_omim_cited	OMIM (cited) Links
pubmed_pcassay	PubChem BioAssay
pubmed_pccompound_mesh	PubChem Compound (MeSH Keyword)
pubmed_pccompound	PubChem Compound
pubmed_pccompound_publisher	PubChem Compound (Publisher)
pubmed_pcsubstance_bookrecords	PubChem Substance (from Bookshelf)
pubmed_pcsubstance	PubChem Substance Links
pubmed_pcsubstance_publisher	PubChem Substance (Publisher)
pubmed_pmc_bookrecords	References in PMC for this Bookshelf citation
pubmed_pmc_embargo
pubmed_pmc_local
pubmed_pmc	PMC Links
pubmed_pmc_refs	Cited in PMC
pubmed_popset	PopSet Links
pubmed_probe	Probe Links
pubmed_proteinclusters	Protein Cluster Links
pubmed_protein	Protein Links
pubmed_protein_refseq	Protein (RefSeq) Links
pubmed_protein_weighted	Protein (Weighted) Links
pubmed_protfam	Protein Family Models
pubmed_pubmed_alsoviewed	Articles frequently viewed together
pubmed_pubmed_bookrecords	References for this Bookshelf citation
pubmed_pubmed_refs	References for PMC Articles
pubmed_pubmed	Similar articles
pubmed_snp_cited	SNP (Cited)
pubmed_snp	SNP Links
pubmed_sra	SRA Links
pubmed_structure	Structure Links
pubmed_taxonomy_entrez	Taxonomy via GenBank

```

And here is an example PubMed article record in abstract form:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "17630804"[PMID] |\
> efetch -format abstract

1. J Org Chem. 2007 Aug 17;72(17):6621-3. Epub 2007 Jul 14.

Total synthesis and absolute configuration determination of (+)-bruguierol C.

Solorio DM(1), Jennings MP.

Author information: 
(1)Department of Chemistry, 500 Campus Drive, The University of Alabama,
Tuscaloosa, Alabama 35487-0336, USA.

The first total synthesis and absolute configuration of bruguierol C are
reported. The key step involved the diastereoselective capture of an in situ
generated oxocarbenium ion via an intramolecular Friedel-Crafts alkylation.

DOI: 10.1021/jo071035l 
PMID: 17630804  [Indexed for MEDLINE]

```

and the same record in XML format:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "17630804"[PMID] |\
> efetch -format xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE PubmedArticleSet PUBLIC "-//NLM//DTD PubMedArticle, 1st January 2019//EN" "https://dtd.nlm.nih.gov/ncbi/pubmed/out/pubmed_190101.dtd">
<PubmedArticleSet>
  <PubmedArticle>
    <MedlineCitation Status="MEDLINE" Owner="NLM">
      <PMID Version="1">17630804</PMID>
      <DateCompleted>
        <Year>2007</Year>
        <Month>10</Month>
        <Day>25</Day>
      </DateCompleted>
      <DateRevised>
        <Year>2007</Year>
        <Month>08</Month>
        <Day>10</Day>
      </DateRevised>
      <Article PubModel="Print-Electronic">
        <Journal>
          <ISSN IssnType="Print">0022-3263</ISSN>
          <JournalIssue CitedMedium="Print">
            <Volume>72</Volume>
            <Issue>17</Issue>
            <PubDate>
              <Year>2007</Year>
              <Month>Aug</Month>
              <Day>17</Day>
            </PubDate>
          </JournalIssue>
          <Title>The Journal of organic chemistry</Title>
          <ISOAbbreviation>J Org Chem</ISOAbbreviation>
        </Journal>
        <ArticleTitle>Total synthesis and absolute configuration determination of (+)-bruguierol C.</ArticleTitle>
        <Pagination>
          <MedlinePgn>6621-3</MedlinePgn>
        </Pagination>
        <Abstract>
          <AbstractText>The first total synthesis and absolute configuration of bruguierol C are reported. The key step involved the diastereoselective capture of an in situ generated oxocarbenium ion via an intramolecular Friedel-Crafts alkylation.</AbstractText>
        </Abstract>
        <AuthorList CompleteYN="Y">
          <Author ValidYN="Y">
            <LastName>Solorio</LastName>
            <ForeName>Dionicio Martinez</ForeName>
            <Initials>DM</Initials>
            <AffiliationInfo>
              <Affiliation>Department of Chemistry, 500 Campus Drive, The University of Alabama, Tuscaloosa, Alabama 35487-0336, USA.</Affiliation>
            </AffiliationInfo>
          </Author>
          <Author ValidYN="Y">
            <LastName>Jennings</LastName>
            <ForeName>Michael P</ForeName>
            <Initials>MP</Initials>
          </Author>
        </AuthorList>
        <Language>eng</Language>
        <PublicationTypeList>
          <PublicationType UI="D016428">Journal Article</PublicationType>
          <PublicationType UI="D013485">Research Support, Non-U.S. Gov't</PublicationType>
          <PublicationType UI="D013486">Research Support, U.S. Gov't, Non-P.H.S.</PublicationType>
        </PublicationTypeList>
        <ArticleDate DateType="Electronic">
          <Year>2007</Year>
          <Month>07</Month>
          <Day>14</Day>
        </ArticleDate>
      </Article>
      <MedlineJournalInfo>
        <Country>United States</Country>
        <MedlineTA>J Org Chem</MedlineTA>
        <NlmUniqueID>2985193R</NlmUniqueID>
        <ISSNLinking>0022-3263</ISSNLinking>
      </MedlineJournalInfo>
      <ChemicalList>
        <Chemical>
          <RegistryNumber>0</RegistryNumber>
          <NameOfSubstance UI="D006575">Heterocyclic Compounds, 3-Ring</NameOfSubstance>
        </Chemical>
        <Chemical>
          <RegistryNumber>0</RegistryNumber>
          <NameOfSubstance UI="C523709">bruguierol C</NameOfSubstance>
        </Chemical>
      </ChemicalList>
      <CitationSubset>IM</CitationSubset>
      <MeshHeadingList>
        <MeshHeading>
          <DescriptorName UI="D006575" MajorTopicYN="N">Heterocyclic Compounds, 3-Ring</DescriptorName>
          <QualifierName UI="Q000138" MajorTopicYN="N">chemical synthesis</QualifierName>
          <QualifierName UI="Q000737" MajorTopicYN="Y">chemistry</QualifierName>
        </MeshHeading>
        <MeshHeading>
          <DescriptorName UI="D009682" MajorTopicYN="N">Magnetic Resonance Spectroscopy</DescriptorName>
        </MeshHeading>
        <MeshHeading>
          <DescriptorName UI="D015394" MajorTopicYN="Y">Molecular Structure</DescriptorName>
        </MeshHeading>
        <MeshHeading>
          <DescriptorName UI="D021241" MajorTopicYN="N">Spectrometry, Mass, Electrospray Ionization</DescriptorName>
        </MeshHeading>
        <MeshHeading>
          <DescriptorName UI="D013055" MajorTopicYN="N">Spectrophotometry, Infrared</DescriptorName>
        </MeshHeading>
        <MeshHeading>
          <DescriptorName UI="D013237" MajorTopicYN="N">Stereoisomerism</DescriptorName>
        </MeshHeading>
      </MeshHeadingList>
    </MedlineCitation>
    <PubmedData>
      <History>
        <PubMedPubDate PubStatus="pubmed">
          <Year>2007</Year>
          <Month>7</Month>
          <Day>17</Day>
          <Hour>9</Hour>
          <Minute>0</Minute>
        </PubMedPubDate>
        <PubMedPubDate PubStatus="medline">
          <Year>2007</Year>
          <Month>10</Month>
          <Day>27</Day>
          <Hour>9</Hour>
          <Minute>0</Minute>
        </PubMedPubDate>
        <PubMedPubDate PubStatus="entrez">
          <Year>2007</Year>
          <Month>7</Month>
          <Day>17</Day>
          <Hour>9</Hour>
          <Minute>0</Minute>
        </PubMedPubDate>
      </History>
      <PublicationStatus>ppublish</PublicationStatus>
      <ArticleIdList>
        <ArticleId IdType="pubmed">17630804</ArticleId>
        <ArticleId IdType="doi">10.1021/jo071035l</ArticleId>
      </ArticleIdList>
    </PubmedData>
  </PubmedArticle>
</PubmedArticleSet>

```
The above returned XML PubMed record is hard to understand as it has many fields. We can use the `xtract -outline` argument to present a structured view of only the XML data tags:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "17630804"[PMID] | \
> efetch -format xml | \
> xtract -outline
PubmedArticle
  MedlineCitation
    PMID
    DateCompleted
      Year
      Month
      Day
    DateRevised
      Year
      Month
      Day
    Article
      Journal
        ISSN
        JournalIssue
          Volume
          Issue
          PubDate
            Year
            Month
            Day
        Title
        ISOAbbreviation
      ArticleTitle
      Pagination
        MedlinePgn
      Abstract
        AbstractText
      AuthorList
        Author
          LastName
          ForeName
          Initials
          AffiliationInfo
            Affiliation
        Author
          LastName
          ForeName
          Initials
      Language
      PublicationTypeList
        PublicationType
        PublicationType
        PublicationType
      ArticleDate
        Year
        Month
        Day
    MedlineJournalInfo
      Country
      MedlineTA
      NlmUniqueID
      ISSNLinking
    ChemicalList
      Chemical
        RegistryNumber
        NameOfSubstance
      Chemical
        RegistryNumber
        NameOfSubstance
    CitationSubset
    MeshHeadingList
      MeshHeading
        DescriptorName
        QualifierName
        QualifierName
      MeshHeading
        DescriptorName
      MeshHeading
        DescriptorName
      MeshHeading
        DescriptorName
      MeshHeading
        DescriptorName
      MeshHeading
        DescriptorName
  PubmedData
    History
      PubMedPubDate
        Year
        Month
        Day
        Hour
        Minute
      PubMedPubDate
        Year
        Month
        Day
        Hour
        Minute
      PubMedPubDate
        Year
        Month
        Day
        Hour
        Minute
    PublicationStatus
    ArticleIdList
      ArticleId
      ArticleId

```

The above structured output makes it easier to view the XML formatting and determine which data elements we are interested in extracting out with `xtract`, such as the PMID, Author/LastName, Author/Initials, ISOAbbreviation, ArticleTitle, PubDate, Volume, Issue, and MedlinePgn:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "17630804"[PMID] | \
> efetch -format xml | \
> xtract -pattern PubmedArticle -element MedlineCitation/PMID -first Author/LastName Author/Initials \
> ISOAbbreviation ArticleTitle PubDate/Year Volume Issue MedlinePgn
17630804	Solorio	DM	J. Org. Chem.	Total synthesis and absolute configuration determination of (+)-bruguierol C.	2007	72	17	6621-3

```
Note that in the above `xtract` argument, the element selection is specified with `-first`, so that only the first occurrence is extracted (e.g., first Author).

## PubChem BioAssay EDirect Fields, Links, and Data

Lastly, let's take a look at the PubChem BioAssay database indexed fields, links, and data structure:

```console

user@computer:~$ einfo -email name@xx.edu -db pcassay -fields
AC	Active Sid Count
ACMD	Activity Outcome Method
ACMT	Assay Comment
ADES	Assay Description
ALL	All Fields
ANAM	Assay Name
APRJ	Assay Project
APRL	Assay Protocol
ASRD	Assay Source ID
BSID	BioSystems ID
CCMT	Categorized Comment
CCT	Categorized Comment Title
CELL	Cell Line
CSNM	Current Source Name
DDAT	Deposit Date
DTMD	Detection Method
FILT	Filter
GBAC	GenBank Accession
GRN	Grant Number
GSYM	Gene Symbol
HDAT	Hold Until Date
JNAM	Journal Name
MDAT	Modify Date
NARD	Nucleic Acid Reagent ID
NSAM	Number of Sids With Activity Concentration micromolar
NSAN	Number of Sids With Activity Concentration nanomolar
ORGN	Organism
PCC	Probe Cid Count
PIGI	Pig GI
PSC	Probe Sid Count
PTGI	Protein Target GI
PTN	Protein Target Name
RTGI	RNA Target GI
SNME	Source Name
SRCC	Source Category
SYNT	Synonym Tested
TCNT	Target Count
TSC	Total Sid Count
TXNM	Taxonomy Name
UID	Assay ID
UPAC	UniProt Accession

user@computer:~$ einfo -email name@xx.edu -db pcassay -links
pcassay_books_probe	MLP Chemical Probe Report
pcassay_cdd_protein_target	Conserved Domains (Full) via Protein Target
pcassay_gene_rnai	RNAi Target, Tested
pcassay_gene_rnai_active	RNAi Target, Active
pcassay_gene_target	Gene Target
pcassay_nuccore	Nucleotide
pcassay_nuccore_rna_target	Nucleotide RNA Target
pcassay_omim	OMIM
pcassay_pcassay_activityneighbor_list	Related BioAssays, by Activity Overlap (List)
pcassay_pcassay_assay_project	Related Assay Projects
pcassay_pcassay_common_gene_list	Related BioAssays, by Common Active Gene (List)
pcassay_pcassay_gene_interaction_list	Related BioAssays, by Gene Interaction (List)
pcassay_pcassay_neighbor_list	Related BioAssays, by Depositor (List)
pcassay_pcassay_same_assay_project_list	Related BioAssays, by Same Project (List)
pcassay_pcassay_same_publication_list	Related BioAssays, by Same Publication (List)
pcassay_pcassay_similar_publication_list	Related BioAssays, by Similar Publication (List)
pcassay_pcassay_targetneighbor_list	Related BioAssays, by Target Similarity (List)
pcassay_pccompound	Compounds
pcassay_pccompound_active	Compounds, Active
pcassay_pccompound_activityconcmicromolar	Compounds, activity concentration at/below 1 uM
pcassay_pccompound_activityconcnanomolar	Compounds, activity concentration at/below 1 nM
pcassay_pccompound_inactive	Compounds, Inactive
pcassay_pccompound_probe	Compounds, Probe
pcassay_pcsubstance	Substances
pcassay_pcsubstance_active	Substances, Active
pcassay_pcsubstance_activityconcmicromolar	Substances, activity concentration at/below 1 uM
pcassay_pcsubstance_activityconcnanomolar	Substances, activity concentration at/below 1 nM
pcassay_pcsubstance_inactive	Substances, Inactive
pcassay_pcsubstance_probe	Substances, Probe
pcassay_pmc	PMC Articles
pcassay_probe	Nucleic acid reagent
pcassay_protein_target	Protein Target
pcassay_protein_target_pig	Protein Target, Identical Sequence
pcassay_pubmed	PubMed Citations
pcassay_sparcle_target	Target Functional Class
pcassay_structure	Protein Structures
pcassay_taxonomy	Taxonomy
```

And here is an example PubChem BioAssay record in docsum XML:

```console

user@computer:~$ esearch -email name@xx.edu -db pcassay -query "1236573"[UID] | \
> efetch -format docsum
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE DocumentSummarySet PUBLIC "-//NLM//DTD esummary pcassay 20161116//EN" "https://eutils.ncbi.nlm.nih.gov/eutils/dtd/20161116/esummary_pcassay.dtd">

<DocumentSummarySet status="OK">
<DbBuild>Build200617-0952.1</DbBuild>

<DocumentSummary><Id>1236573</Id>
	<AssayName>Cytotoxicity against human NCI/ADR cells</AssayName>
	<CellLine></CellLine>
	<AssayDescription>Title: Progress Toward the Development of Noscapine and Derivatives as Anticancer Agents.  Abstract: Many nitrogen-moiety containing alkaloids derived from plant origins are bioactive and play a significant role in human health and emerging medicine. Noscapine, a phthalideisoquinoline alkaloid derived from Papaver somniferum, has been used as a cough suppressant since the mid 1950s, illustrating a good safety profile. Noscapine has since been discovered to arrest cells at mitosis, albeit with moderately weak activity. Immunofluorescence staining of microtubules after 24 h of noscapine exposure at 20  inverted question markM elucidated chromosomal abnormalities and the inability of chromosomes to complete congression to the equatorial plane for proper mitotic separation ( Proc. Natl. Acad. Sci. U. S. A. 1998 , 95 , 1601 - 1606 ). A number of noscapine analogues possessing various modifications have been described within the literature and have shown significantly improved antiprolific profiles for a large variety of cancer cell lines. Several semisynthetic antimitotic alkaloids are emerging as possible candidates as novel anticancer therapies. This perspective discusses the advancing understanding of noscapine and related analogues in the fight against malignant disease. </AssayDescription>
	<AssaySourceID>1506980</AssaySourceID>
	<SourceNameList>
		<string>ChEMBL</string>
	</SourceNameList>
	<CurrentSourceName>ChEMBL</CurrentSourceName>
	<DetectionMethod></DetectionMethod>
	<ActiveSidCount>1</ActiveSidCount>
	<ActivityOutcomeMethod>Confirmatory</ActivityOutcomeMethod>
	<TotalSidCount>1</TotalSidCount>
	<OnHold>No</OnHold>
	<ModifyDate>2018/10/08 00:00</ModifyDate>
	<DepositDate>2016/12/22 00:00</DepositDate>
	<HoldUntilDate>1/01/01 00:00</HoldUntilDate>
	<AID>1236573</AID>
	<ProbeSidCount>0</ProbeSidCount>
	<TargetCount>0</TargetCount>
	<NumberofSidsWithActivityConcmicromolar>1</NumberofSidsWithActivityConcmicromolar>
	<NumberofSidsWithActivityConcnanomolar>1</NumberofSidsWithActivityConcnanomolar>
	<ProteinTargetList>
	</ProteinTargetList>
</DocumentSummary>

</DocumentSummarySet>
```
Similarly to the PubChem Compound and PubMed data, we can extract out specific data using the `xtract` function such as the AID, CurrentSourceName, AssayName, ActiveSidCount, and TargetCount:

```console

user@computer:~$ esearch -email name@xx.edu -db pcassay -query "1236573"[UID] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element AID CurrentSourceName AssayName ActiveSidCount TargetCount
1236573	ChEMBL	Cytotoxicity against human NCI/ADR cells	1	0
```

