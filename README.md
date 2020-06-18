# EDirectChemInfo

This repository contains Entrez Direct (EDirect, an NCBI tool) Unix scripts for programmatically obtaining data from various NCBI databases. Other EDirect resources and guides exist (referenced below). This EDirectChemInfo repository differs in that the focus is on obtaining chemical information, cheminformatics data, and chemical structure <--> bioactivity <--> document  relationship links. There are not many PubChem EDirect examples available, so hopefully this repository proves useful. I have also added some tips, step-wise directions, and code output examples to help you get started.

Please note that this EDirectChemInfo repository is not affiliated with NCBI. You should contact [NCBI](https://www.ncbi.nlm.nih.gov/books/NBK179288/#_chapter6_For_More_Information_) for specific questions related to EDirect. This repository was created to accompany library instruction at The University of Alabama. With that in mind, please feel free to open a GitHub Issue or contact me directly with comments/questions if you think there is something I can help you with. In addition, if this repository has been a useful resource for you, please do let me know as this type of feedback can help prioritize my time.

Vincent Scalfani\
Science and Engineering Librarian\
The University of Alabama\
[UA Libraries Directory](https://www.lib.ua.edu/#/staffdir?liaison=1&search=scalfani)

## Contents

* [What is EDirect?](https://github.com/vfscalfani/EDirectChemInfo/blob/master/01_EDirect_Intro.md)
  * [Installation Tips](https://github.com/vfscalfani/EDirectChemInfo/blob/master/01_EDirect_Intro.md#installation-tips)
  * [Usage Tips](https://github.com/vfscalfani/EDirectChemInfo/blob/master/01_EDirect_Intro.md#usage-tips)
  * [EDirect Function Help](https://github.com/vfscalfani/EDirectChemInfo/blob/master/01_EDirect_Intro.md#e-utility-application-help)
* [Available Databases, Data Fields, and Data Structures](https://github.com/vfscalfani/EDirectChemInfo/blob/master/02_EDirect_Data_Fields_Structure.md)
* [PubChem <--> PubMed EDirect Recipes](https://github.com/vfscalfani/EDirectChemInfo/blob/master/03_EDirect_PubChem_PubMed_Recipes.md)
* [PubChem EDirect Recipes](https://github.com/vfscalfani/EDirectChemInfo/blob/master/04_EDirect_PubChem_Recipes.md)
* [PubMed EDirect Recipes](https://github.com/vfscalfani/EDirectChemInfo/blob/master/05_EDirect_PubMed_Recipes.md)
* [Combining EDirect Results with Chemical Depiction and Plotting](https://github.com/vfscalfani/EDirectChemInfo/blob/master/06_EDirect_Combining_Tools.md)

## References

1. [NCBI Documentation for Entrez Direct: E-utilities on the UNIX Command Line](https://www.ncbi.nlm.nih.gov/books/NBK179288/)
2. [NIH NLM The Insider's Guide to Accessing NLM Data](https://dataguide.nlm.nih.gov/)
3. [NCBI EDirect Cookbook](https://github.com/NCBI-Hackathons/EDirectCookbook)
4. [Computational Genomics Manual: NCBI EDirect](https://github.com/linsalrob/ComputationalGenomicsManual/blob/master/Databases/NCBI_Edirect.md)
5. [Entrez Link Descriptions](https://eutils.ncbi.nlm.nih.gov/entrez/query/static/entrezlinks.html)
6. [Software Carpentry: The Unix Shell](https://swcarpentry.github.io/shell-novice/)
7. [Opening up connectivity between documents, structures and bioactivity by Christopher Southan](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC7136548/)
