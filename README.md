# EDirectChemInfo

This repository contains Entrez Direct (EDirect, an NCBI tool) Unix scripts for programmatically obtaining data from various NCBI databases. Other EDirect resources and guides exist (referenced below). This EDirectChemInfo repository differs in that the focus is on obtaining chemical information, cheminformatics data, and chemical structure <--> document relationship links. There are not many PubChem EDirect examples available, so hopefully this repository proves useful. I have also added some tips, step-wise directions, and code output examples to help you get started.

Please note that this EDirectChemInfo repository is not affiliated with NCBI. You should contact [NCBI](https://www.ncbi.nlm.nih.gov/books/NBK179288/#_chapter6_For_More_Information_) for specific questions related to EDirect. This repository was created to accompany library instruction at The University of Alabama. With that in mind, please feel free to open a GitHub New Issue with comments/questions if you think there is something I can help you with. Alternatively, you can contact me directly:

Vincent Scalfani\
Science and Engineering Librarian\
The University of Alabama\
[UA Libraries Directory](https://www.lib.ua.edu/#/staffdir?liaison=1&search=scalfani)

## Useful Resources

1. [NCBI Documentation for Entrez Direct: E-utilities on the UNIX Command Line](https://www.ncbi.nlm.nih.gov/books/NBK179288/)
2. [NIH NLM The Insider's Guide to Accessing NLM Data](https://dataguide.nlm.nih.gov/)
3. [NCBI EDirect Cookbook](https://github.com/NCBI-Hackathons/EDirectCookbook)
4. [Computational Genomics Manual: NCBI EDirect](https://github.com/linsalrob/ComputationalGenomicsManual/blob/master/Databases/NCBI_Edirect.md)
5. [Entrez Link Descriptions](https://eutils.ncbi.nlm.nih.gov/entrez/query/static/entrezlinks.html)
6. [Software Carpentry: The Unix Shell](https://swcarpentry.github.io/shell-novice/)

## What is EDirect?

EDirect is a Unix command line tool from NCBI that allows programmatic retrieval of chemical/biological data and literature references from NCBI databases. EDirect reduces the barrier to accessing NCBI data programmatically; that is, with a basic knowledge of the Unix shell (e.g., bash), it is straightforward to obtain and format your own custom dataset, often with a single line of code. Moreover, you can input data retrieved from EDirect into other Unix tools for quick viewing and analysis ([Pipeline (Unix)](https://en.wikipedia.org/wiki/Pipeline_(Unix)).

## Installation Tips

Follow the installation instructions from NCBI: [Entrez Direct: E-utilities on the UNIX Command Line](https://www.ncbi.nlm.nih.gov/books/NBK179288/). There are several different methods to install EDirect. I used option 3 (EDirect v13.7) with `wget` in Gnome Terminal on a Linux Ubuntu 18.04 workstation. If you are using Windows, NCBI mentions that you can use the Cygwin Unix emulator. Another option for Windows users is to setup a Linux virtual machine. There are many tutorials for setting up virtual machines. For example, here is one for installing [Ubuntu on VirtualBox](https://askubuntu.com/questions/142549/how-to-install-ubuntu-on-virtualbox). When installing EDirect in a virtual machine, you may need to customize the VirtualBox network settings in order to use the `curl` or `wget` EDirect installation methods. In my testing on an Ubuntu 20.04 virtual machine, the fourth installation option for EDirect (using the longer perl script) worked fine with the standard VirtualBox network settings.

## Usage Tip

NCBI has specific data usage policies and disclaimers:

* [NCBI Website and Data Usage Policies and Disclaimers](https://www.ncbi.nlm.nih.gov/home/about/policies/)
* [Entrez Programming Utilities Help](https://www.ncbi.nlm.nih.gov/books/NBK25501/)

If you do not follow NCBI's usage policies (e.g., no more than 3 requests per second), NCBI may block your IP address. So be cautious and follow good programming practices of testing and adding sleep delays, particularly if executing multiple sequential calls in a loop. Moreover, it is always a good idea to include your email address in the requests so that NCBI can contact you if necessary. You can add your email address within each query like this:

```console

user@computer:~$ eutility -email name@xx.edu -arg input

```
Replace name@xx.edu with your email address. The `eutility` is a place holder for one of the actual applications like `einfo` or `esearch`, and `-arg input` is a placeholder for e-utility argument(s) like `-db pccompound` or `-db pubmed -query "food allergies"`.

## Getting Help within an e-utility application

I generally refer to the official [Entrez Programming Utilities Help Document](https://www.ncbi.nlm.nih.gov/books/NBK25501/) or the [NIH NLM E-Utilities Documentation](https://dataguide.nlm.nih.gov/eutilities/utilities.html), however for a quick reference or reminder of the proper syntax, the `-help` option is useful. Here is an example with the `einfo` application:

```console

user@computer:~$ einfo -help
einfo 13.7

Database Selection

  -db        Database name
  -dbs       Get all database names

Data Summaries

  -fields    Print field names
  -links     Print link names

Field Example

  <Field>
    <Name>ALL</Name>
    <FullName>All Fields</FullName>
    <Description>All terms from all searchable fields</Description>
    <TermCount>138982028</TermCount>
    <IsDate>N</IsDate>
    <IsNumerical>N</IsNumerical>
    <SingleToken>N</SingleToken>
    <Hierarchy>N</Hierarchy>
    <IsHidden>N</IsHidden>
    <IsTruncatable>Y</IsTruncatable>
    <IsRangable>N</IsRangable>
  </Field>

Link Example

  <Link>
    <Name>pubmed_protein</Name>
    <Menu>Protein Links</Menu>
    <Description>Published protein sequences</Description>
    <DbTo>protein</DbTo>
  </Link>
  <Link>
    <Name>pubmed_protein_refseq</Name>
    <Menu>Protein (RefSeq) Links</Menu>
    <Description>Link to Protein RefSeqs</Description>
    <DbTo>protein</DbTo>
  </Link>

```

## Understanding the Available Data Fields and Data Structures

Before getting too far, it is helpful to understand the available indexed search fields, related links, and returned data format. We can see the available fields and links (connected records) with the `einfo` application. For example, if we are interested in the PubChem Compound (pccompound) database:

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
SRC	SourceName
SRCC	SourceCategory
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
Now that we have an understanding about what kind of data is available in the PubChem Compound database, let's take a look at a PubChem Compound record using the `esearch` and `efetch` applications:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query 512323[UID]
<ENTREZ_DIRECT>
  <Db>pccompound</Db>
  <WebEnv>NCID_1...</WebEnv>
  <QueryKey>1</QueryKey>
  <Count>1</Count>
  <Step>1</Step>
  <Email>name@xx.edu</Email>
</ENTREZ_DIRECT>

```

We searched PubChem for the Compound Identifier 512323 using `esearch`, and the NCBI Entrez server returned a summary of the search results. The WebEnV and QueryKey specify the location of the search results on the NCBI server. In order to retrieve the data, we can pipe (|) the `esearch` results directly into the `efetch` application:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query 512323[UID] | \
> efetch -format docsum
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE DocumentSummarySet PUBLIC "-//NLM//DTD esummary pccompound 20170720//EN" "https://eutils.ncbi.nlm.nih.gov/eutils/dtd/20170720/esummary_pccompound.dtd">

<DocumentSummarySet status="OK">
<DbBuild>Build200610-1135m.1</DbBuild>

<DocumentSummary><Id>512323</Id>
	<CID>512323</CID>
	<SourceNameList>
	</SourceNameList>
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
		<string>1-[(2R,4S,5R)-4-hydroxy-5-(hydroxymethyl)tetrahydrofuran-2-yl]-5-(2-thienyl)pyrimidine-2,4-dione</string>
	</SynonymList>
	<MeSHHeadingList>
	</MeSHHeadingList>
	<MeSHTermList>
	</MeSHTermList>
	<PharmActionList>
	</PharmActionList>
	<CommentList>
	</CommentList>
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
	<SubstanceIDList>
	</SubstanceIDList>
	<TPSA>127</TPSA>
	<ActiveAidCount>1</ActiveAidCount>
	<TotalAidCount>37</TotalAidCount>
	<InChIKey>PCDQBRGMSMVLDZ-IQJOONFLSA-N</InChIKey>
	<ProbeAidCount>0</ProbeAidCount>
	<InChI>InChI=1S/C13H14N2O5S/c16-6-9-8(17)4-11(20-9)15-5-7(10-2-1-3-21-10)12(18)14-13(15)19/h1-3,5,8-9,11,16-17H,4,6H2,(H,14,18,19)/t8-,9+,11+/m0/s1</InChI>
</DocumentSummary>

</DocumentSummarySet>
```

`efetch` returned the CID record data as document summary XML format (for other formats see `efetch -help`). XML is certainly useful, but we probably want to parse the data into a table for easier viewing and data analysis. EDirect contains a utility called `xtract` that can convert the Entrez XML data into tables. See `xtract -help` for more information. In brief, you will need to select a main XML heading tag to define the extract pattern and then specify the data you want to extract with the sub-heading tag names. For example, in the above CID record data, we can set the pattern to DocumentSummary (the first main XML tag in this case), and then the elements to a few of the sub-heading tags we are interested such as IsomericSmiles, CID, InChIKey, MolecularFormula, and MolecularWeight:

```console

esearch -email name@xx.edu -db pccompound -query 512323[UID] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey MolecularFormula MolecularWeight
C1[C@@H]([C@H](O[C@H]1N2C=C(C(=O)NC2=O)C3=CC=CS3)CO)O	512323	PCDQBRGMSMVLDZ-IQJOONFLSA-N	C13H14N2O5S	310.330

```

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
ED	Editor
EDAT	Date - Entrez
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
pubmed_clinvar	ClinVar
pubmed_clinvar_calculated	ClinVar (calculated)
pubmed_dbvar	dbVar
pubmed_gap	dbGaP Links
pubmed_gds	GEO DataSet Links
pubmed_gene	Gene Links
pubmed_gene_bookrecords	Gene (from Bookshelf)
pubmed_gene_citedinomim	Gene (OMIM) Links
pubmed_gene_pmc_nucleotide	Gene (nucleotide/PMC)
pubmed_gene_rif	Gene (GeneRIF) Links
pubmed_genome	Genome Links
pubmed_geoprofiles	GEO Profile Links
pubmed_homologene	HomoloGene Links
pubmed_medgen	MedGen
pubmed_medgen_bookshelf_cited	MedGen (Bookshelf cited)
pubmed_medgen_genereviews	MedGen (GeneReviews)
pubmed_medgen_omim	MedGen (OMIM)
pubmed_nuccore	Nucleotide Links
pubmed_nuccore_refseq	Nucleotide (RefSeq) Links
pubmed_nuccore_weighted	Nucleotide (Weighted) Links
pubmed_omim_bookrecords	OMIM (from Bookshelf)
pubmed_omim_calculated	OMIM (calculated) Links
pubmed_omim_cited	OMIM (cited) Links
pubmed_pcassay	PubChem BioAssay
pubmed_pccompound	PubChem Compound
pubmed_pccompound_mesh	PubChem Compound (MeSH Keyword)
pubmed_pccompound_publisher	PubChem Compound (Publisher)
pubmed_pcsubstance	PubChem Substance Links
pubmed_pcsubstance_bookrecords	PubChem Substance (from Bookshelf)
pubmed_pcsubstance_publisher	PubChem Substance (Publisher)
pubmed_pmc	PMC Links
pubmed_pmc_bookrecords	References in PMC for this Bookshelf citation
pubmed_pmc_embargo	_
pubmed_pmc_local	_
pubmed_pmc_refs	Cited in PMC
pubmed_popset	PopSet Links
pubmed_probe	Probe Links
pubmed_protein	Protein Links
pubmed_proteinclusters	Protein Cluster Links
pubmed_protein_refseq	Protein (RefSeq) Links
pubmed_protein_weighted	Protein (Weighted) Links
pubmed_pubmed	Similar articles
pubmed_pubmed_alsoviewed	Articles frequently viewed together
pubmed_pubmed_bookrecords	References for this Bookshelf citation
pubmed_pubmed_refs	References for PMC Articles
pubmed_snp	SNP Links
pubmed_snp_cited	SNP (Cited)
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
<?xml version="1.0" ?>
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
<ISOAbbreviation>J. Org. Chem.</ISOAbbreviation>
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

The above returned XML PubMed record is hard to understand as it has many fields and does not contain tabbed indenting (at least not as presented in my terminal). Thankfully, we can use the `xtract -outline` argument to present a structured view of the data tags:

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


## PubChem <--> PubMed EDirect Recipes

### Retrieve Associated PubMed References from a PubChem Compound Search

### Retrieve Associated PubMed References from a PubChem Substance Data Source Depositor

### Retrieve Associated PubChem Compounds from a PubMed Search

### Retrieve Associated PubChem Compounds from a list of PubMed IDs (i.e., keep track of the individual links)


## PubChem EDirect Recipes

### Search PubChem Compound via InChIKey and Retrieve Data

### Find Compounds with Specific Atom and Bond Attributes

### Find Compounds Deposited on a Specific Date

### Retrieve Pre-Computed Linked Similar Compounds

### Find Related PubChem Substances (+ count)


## PubMed EDirect Recipes

### Retrieve Cited and Citing References (+ count) for a PubMed Article

### Retrieve Related References and Filter For Dissertation References

### Calculate Most Frequent Journal Title For a PubMed Search

### Calculate the Frequency of Researcher Publications in PubMed by Year

### Calculate Most Frequent Affiliated Collaborations


## Combining EDirect Results with Chemical Depiction (MarvinView) and Plotting (gnuplot)





