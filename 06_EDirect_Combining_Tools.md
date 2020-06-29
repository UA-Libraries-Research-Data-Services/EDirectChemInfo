# Combining EDirect Results with Chemical Depiction and Plotting

**Notes**

> 1. `user@computer:~$` represents an example terminal prompt name. Actual command/argument input is after the `$`.
> 2. Replace `name@xx.edu` with your email address.
> 3. `\` followed by `>` on the next line represents continued terminal input. You will need to delete the `>` symbol in order to run the scripts as a copy/paste into terminal.
> 4. You should validate your own EDirect scripts and results as there may be unintentional mistakes in these recipes. A convenient method is to compare your EDirect results to the NCBI Web interface search results: [https://www.ncbi.nlm.nih.gov/](https://www.ncbi.nlm.nih.gov/).

## EDirect --> Chemical Depiction and Plots

It is possible to pipe EDirect results into chemical structure viewers as some cheminformatics toolkits can read chemical file formats (e.g., SMILES) directly from standard input.

### ChemAxon MarvinView Chemical Depiction

For [ChemAxon Marvin](https://chemaxon.com/products/marvin), we can pipe EDirect compiled SMILES directly into Marvin View (`mview`). Note that the `-` is the `mview` option to read structures from standard input.

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "132427739"[UID] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey IUPACName | \
> mview -

```

![mview01](/images/mview01.png)

If you have multiple molecules to display, you can use the `mview` standard input option `-` along with the `gridbag` option to display the molecules in a matrix:


```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "132427739"[UID] | \
> elink -target pccompound -name pccompound_pccompound | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID InChIKey IUPACName | \
> mview --gridbag -

```

![mview02](/images/mview02.png)

_tested with ChemAxon MarvinView version 19.27.0._

### Open Babel Chemical Depiction

One really cool feature of using [Open Babel](https://github.com/openbabel) is the ability to display molecules as ASCII figures directly in the terminal. Below, we pipe the results to Open Babel using the standard input smiles format, `-ismi`, and then output in ascii format, `-oascii`. The `-xh 10` is a resizing option.

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "13586"[UID] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles | \
> openbabel.obabel -ismi -oascii -xh 10
                   __                                                          
                  __ \__                                                       
                _/  \__ \_                                                     
             __/       \_ \_                                                   
 O  ________/            \_                                                    
         /                                                                     
        /                                                                      
   \    |                                                                      
    \  /                                                                       
     \/                                                                        
1 molecule converted
```

This works for multiple molecules too!

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "132427739"[UID] | \
> elink -target pccompound -name pccompound_pccompound | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles | \
> openbabel.obabel -ismi -oascii -xh 10
               \                                                               
              ____                                                             
                |                                                              
         __  O_Si__                                                            
      O\  \__\  |                                                              
       |\N /  \                                                                
    __O|  \__/                                                                 
 /__/      \ OH                                                                
|  \                                                                           
/|  /                                                                          
             /                                                                 
    /       |                                                                  
 \_ |    O ____                                                                
   \__O___  |____                                                              
__Si_O   N \\/   \                                                             
     \      /|  /                                                              
   |  \___   __N                                                               
                \__O    ____                                                   
               O /  ___\/  \\                                                  
                /      /|  /                                                   
                       __   __                                                 
                         \_/                                                   
                          |    /                                               
                          |    |                                               
  /____               O __Si__/                                                
 |/   \              |    |    |                                               
 /     \  O\\     ____   _|    \                                               
\  ___ |   ||     /   \_/  \__                                                 
 \____/     \\_N \     /                                                       
  \   |     _/    |   /                                                        
 ___                                                                           
/  \/                                                                          
|| |\                                                                          
 \__                                                                           
    \_O    ___                                                                 
       |_N/  \   /                                                             
      O|/ _\__\_/                                                              
       / /_/ O_Si__                                                            
         \_                                                                    
           \O   |                                                              
           /                                                                   
       _Si/                                                                    
        \ |_                                                                   
   O____O | \                                                                  
__|Si_ |  |     ____                                                           
  /     \_\ __  |  |                                                           
 /  |   /  \____N__|                                                           
             \O|  |  /  \_                                                     
                  \O/\___ \                                                    
                   \   | ||                                                    
              \_ /                                                             
 ___            \                                                              
/  \         O_Si_|                                                            
 |__\    \   \  \ |                                                            
   /\_O  \__\  _\                                                              
      __N /  \/  \                                                             
  \    / /   |                                                                 
  |   O  \__/                                                                  
   \   ___\                                                                    
   ____/        

...
24 molecules converted
```

You can save depictions in a more classic PNG file using Open Babel with either a single molecule:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "13586"[UID] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID | \
> openbabel.obabel -ismi -O 13586.png
1 molecule converted
```

![obabel01](/images/obabel01.png)


or multiple molecules in a matrix:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "132427739"[UID] | \
> elink -target pccompound -name pccompound_pccompound | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID | \
> openbabel.obabel -ismi -O 132427739_similar.png -xp 1400
24 molecules converted
```

![obabel02](/images/obabel02.png)

_Tested with Open Babel v3.0.0 installed from Snap. I did receive a Font Configuration error when saving the PNG files, however, the conversion seemed to work fine._

### gnuplot Data plotting

[gnuplot](http://www.gnuplot.info/) is a command-line graphing program that allows plotting data from standard input. In gnuplot, there is an option called "dumb terminal" that creates plots using ASCII characters directly in the terminal window, which is convenient for initial analysis of compiled EDirect data. For example, here is some data related to the number of *J Cheminform* articles indexed in PubMed by publication date:

```console

user@computer:~$  esearch -email name@xx.edu -db pubmed -query "J Cheminform[JOUR]" | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element PubDate | \
> cut -d " " -f 1 | \
> sort-uniq-count-rank | \
> sort -k2
22	2009
12	2010
54	2011
39	2012
52	2013
71	2014
78	2015
71	2016
67	2017
68	2018
56	2019
```
We can pipe this data directly to gnuplot. In the below script, `set term dumb` is the gnuplot option to create an ASCII plot, `-` sets the data input to standard input instead of a file, `using 2:1` sets the second column as the x-axis, and the first column as the y-axis, `with boxes` creates a box plot, and `notitle` removes the plot legend:

```console

user@computer:~$ esearch -email name@xx.edu -db pubmed -query "J Cheminform[JOUR]" | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element PubDate | \
> cut -d " " -f 1 | \
> sort-uniq-count-rank | \
> sort -k2 | \
> gnuplot -e "set term dumb; plot '-' using 2:1 with boxes notitle"

                                                                               
  80 +---------------------------------------------------------------------+   
     |           +          +           +  *******  +          +           |   
     |                                     *     *                         |   
  70 |-+                             *******     *******    *******      +-|   
     |                               *     *     *     ******     *        |   
     |                               *     *     *     *    *     *        |   
  60 |-+                             *     *     *     *    *     *      +-|   
     |              ******           *     *     *     *    *     *******  |   
     |              *    *     *******     *     *     *    *     *     *  |   
  50 |-+            *    *     *     *     *     *     *    *     *     *+-|   
     |              *    *     *     *     *     *     *    *     *     *  |   
  40 |-+            *    *     *     *     *     *     *    *     *     *+-|   
     |              *    *******     *     *     *     *    *     *     *  |   
     |              *    *     *     *     *     *     *    *     *     *  |   
  30 |-+            *    *     *     *     *     *     *    *     *     *+-|   
     |              *    *     *     *     *     *     *    *     *     *  |   
     |              *    *     *     *     *     *     *    *     *     *  |   
  20 |-+*******     *    *     *     *     *     *     *    *     *     *+-|   
     |  *     *     *    *     *     *     *     *     *    *     *     *  |   
     |  *     *******    *  +  *     *  +  *     *  +  *    *  +  *     *  |   
  10 +---------------------------------------------------------------------+   
    2008        2010       2012        2014        2016       2018        2020 

```

_Tested with gnuplot-x11 5.2.8._


