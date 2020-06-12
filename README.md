# EDirectChemInfo

This repository contains Entrez Direct (EDirect, an NCBI tool) UNIX scripts for programmatically obtaining data from various NCBI databases. Other EDirect resources and guides exist (referenced below). This EDirectChemInfo repository differs in that the focus is on obtaining chemical information, cheminformatics data, and chemical structure <--> document relationship links. There are not many PubChem EDirect examples available, so hopefully this repository proves useful. I have also added some tips, step-wise directions, and code output examples to help you get started.

## Useful Resources

1. [NCBI Documentation for Entrez Direct: E-utilities on the UNIX Command Line](https://www.ncbi.nlm.nih.gov/books/NBK179288/)
2. [NIH NLM The Insider's Guide to Accessing NLM Data](https://dataguide.nlm.nih.gov/)
3. [NCBI EDirect Cookbook](https://github.com/NCBI-Hackathons/EDirectCookbook)
4. [Computational Genomics Manual: NCBI EDirect](https://github.com/linsalrob/ComputationalGenomicsManual/blob/master/Databases/NCBI_Edirect.md)
5. [Entrez Link Descriptions](https://eutils.ncbi.nlm.nih.gov/entrez/query/static/entrezlinks.html)
6. [Software Carpentry: The Unix Shell](https://swcarpentry.github.io/shell-novice/)

## What is EDirect?

EDirect is a UNIX command line tool from NCBI that allows programmatic retrieval of chemical/biological data and literature references from NCBI databases. EDirect reduces the barrier to accessing NCBI data programmatically; that is, with a basic knowledge of UNIX shell, it is straightforward to obtain and compile your own custom dataset, often with a single line of code. Moreover, you can input data retrieved from EDirect directly into other UNIX tools for quick viewing and analysis ([Pipeline (Unix)](https://en.wikipedia.org/wiki/Pipeline_(Unix)).

## Installation Tips

Follow the installation instructions from NCBI: [Entrez Direct: E-utilities on the UNIX Command Line](https://www.ncbi.nlm.nih.gov/books/NBK179288/). There are several different ways to install EDirect. I used option 3 (EDirect v13.7) with `wget` on my Linux Ubuntu 18.04 workstation. If you are using Windows, NCBI mentions that you can use the Cygwin UNIX emulator. Another option for Windows users is to setup a Linux virtual machine. There are many tutorials for setting up virtual machines. For example, here is one for installing [Ubuntu on VirtualBox](https://askubuntu.com/questions/142549/how-to-install-ubuntu-on-virtualbox). When testing the installation of EDirect v13.7 in a virtual Ubuntu 20.04 LTS machine, I was not able to get my VirtualBox network settings configured correctly to use the `curl` or `wget` EDirect installation methods, however the fourth option with the perl script worked fine for installation of EDirect in the Ubuntu virtual machine.

## Usage Tip

NCBI has specific data usage policies and disclaimers:

* [NCBI Website and Data Usage Policies and Disclaimers](https://www.ncbi.nlm.nih.gov/home/about/policies/)
* [Entrez Programming Utilities Help](https://www.ncbi.nlm.nih.gov/books/NBK25501/)

If you do not follow NCBI's usage policies (e.g., no more than 3 requests per second), NCBI may block your IP address. So be cautious and follow good programming practices of testing and adding sleep delays, particularly if executing multiple sequential calls in a loop. Moreover, it is always a good idea to include your email address in the requests so that NCBI can contact you if necessary. You can add your email address within each query like this:

```console
user@computer:~$ eutility -email name@xx.edu -arg input
```
The "eutility" here is a place holder for one of the actual applications like `einfo` or `esearch`, and `-arg input` is a placeholder for e-utility arguments like `-db pubmed` or `-query "food allergies"`.

## Getting Help within e-utility applications

I generally refer to the official [Entrez Programming Utilities Help](https://www.ncbi.nlm.nih.gov/books/NBK25501/) or the [NIH NLM E-Utilities Documentation](https://dataguide.nlm.nih.gov/eutilities/utilities.html), however for a quick reference or reminder of the proper syntax, the `-help` option is very useful:

```console

user@computer:~$ einfo -help

```

produces:

```console
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

## Getting Familiar with Available Data Fields and Data Structures







