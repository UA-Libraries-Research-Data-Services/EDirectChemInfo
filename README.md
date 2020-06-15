# EDirectChemInfo

This repository contains Entrez Direct (EDirect, an NCBI tool) Unix scripts for programmatically obtaining data from various NCBI databases. Other EDirect resources and guides exist (referenced below). This EDirectChemInfo repository differs in that the focus is on obtaining chemical information, cheminformatics data, and chemical structure <--> document relationship links. There are not many PubChem EDirect examples available, so hopefully this repository proves useful. I have also added some tips, step-wise directions, and code output examples to help you get started.

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

Follow the installation instructions from NCBI: [Entrez Direct: E-utilities on the UNIX Command Line](https://www.ncbi.nlm.nih.gov/books/NBK179288/). There are several different ways to install EDirect. I used option 3 (EDirect v13.7) with `wget` in Gnome Terminal on my Linux Ubuntu 18.04 workstation. If you are using Windows, NCBI mentions that you can use the Cygwin Unix emulator. Another option for Windows users is to setup a Linux virtual machine. There are many tutorials for setting up virtual machines. For example, here is one for installing [Ubuntu on VirtualBox](https://askubuntu.com/questions/142549/how-to-install-ubuntu-on-virtualbox). When installing EDirect in a virtual machine, you may need to customize the VirtualBox network settings in order to use the `curl` or `wget` EDirect installation methods. In my testing on a Ubuntu 20.04 virtual machine, the fourth installation option for EDirect (using the longer perl script) worked fine with the standard VirtualBox network settings.

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

I generally refer to the official [Entrez Programming Utilities Help](https://www.ncbi.nlm.nih.gov/books/NBK25501/) or the [NIH NLM E-Utilities Documentation](https://dataguide.nlm.nih.gov/eutilities/utilities.html), however for a quick reference or reminder of the proper syntax, the `-help` option is useful. Here is an example with with `einfo` application:

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

Before getting too far, it is helpful to understand the available indexed search fields, related links, and returned data format. We can see the available indexed fields and links (connected records) with the `einfo` application. For example, if we are interested in the PubChem Compound (pccompound) database:

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
Great, so now we have an understanding about what kind of data is available, next let's take a look at a PubChem Compound record using the `esearch` and `efetch` applications:

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

We searched PubChem for the Compound Identifier 512323 using `esearch`, and the NCBI Entrez server returned a summary of the search results. The WebEnV and QueryKey specify the location of the search results on the NCBI server. In order to actually retrieve the data, we can pipe (|) the `esearch` results directly into the `efetch` application:

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

`efetch` returned the CID record data as document summary XML format (for other formats see `efetch -help`). This is great, however, we might want to parse the data also, and extract out a few specific data points. We can accomplish this with the EDirect application `xtract`. For example, to extract out only the IsomericSmiles, CID, InChIKey, MolecularFormula, and Molecular Weight:

```console

esearch -email name@xx.edu -db pccompound -query 512323[UID] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey MolecularFormula MolecularWeight
C1[C@@H]([C@H](O[C@H]1N2C=C(C(=O)NC2=O)C3=CC=CS3)CO)O	512323	PCDQBRGMSMVLDZ-IQJOONFLSA-N	C13H14N2O5S	310.330

```

In a similar fashion, let's preview the available PubMed database indexed fields, links, and data structure...



## PubChem and PubChem --> PubMed EDirect Recipes


## PubMed and PubMed --> PubChem EDirect Recipes








