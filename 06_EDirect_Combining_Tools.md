# Combining EDirect Results with Chemical Depiction and Plotting (gnuplot)

**Notes**

> 1. `user@computer:~$` represents an example terminal prompt name. Actual command/argument input is after the `$`.
> 2. Replace `name@xx.edu` with your email address.
> 3. `\` followed by `>` on the next line represents continued terminal input. You will need to delete the `>` symbol in order to run the scripts as a copy/paste into terminal.
> 4. You should validate your own EDirect scripts and results as there may be unintentional mistakes in these recipes. A convenient method is to compare your EDirect results to the NCBI Web interface search results: [https://www.ncbi.nlm.nih.gov/](https://www.ncbi.nlm.nih.gov/).

## Chemical Depiction

**Description:** Pipe received SMILES from EDirect into a Chemical Structure Viewer.

It is possible to pipe EDirect results into chemical structure viewers as some cheminformatics toolkits can read chemical file formats (e.g., SMILES) directly from standard input.

### ChemAxon MarvinView

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

### Open Babel

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

This works for multiple molecules too:

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
```

You can save depictions in a more classic PNG file using Open Babel with either a single molecule:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "13586"[UID] | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID | \
> openbabel.obabel -ismi -O 13586.png
1 molecule converted
```

![openbabel01](/images/openbabel01.png)


or multiple molecules in a matrix:

```console

user@computer:~$ esearch -email name@xx.edu -db pccompound -query "132427739"[UID] | \
> elink -target pccompound -name pccompound_pccompound | \
> efetch -format docsum | \
> xtract -pattern DocumentSummary -element IsomericSmiles CID | \
> openbabel.obabel -ismi -O 132427739_similar.png -xp 1400
24 molecules converted

![openbabel02](/images/openbabel02.png)

_Tested with Open Babel v3.0.0 installed from Snap. I did receive a Font Configuration error when saving the PNG files, however, the conversion seemed to work fine._


**Description:** Pipe Data directly into gnuplot

...in progress, coming soon.







