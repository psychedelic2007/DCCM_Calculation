# DCCM_Calculation
This repository describes how to calculate the Dynamical Cross-Correlation Matrix from the simulated XTC data.

# Step 1
Extract the XTC of interest from the simulated data (GROMACS) using the following command:
```
gmx trjconv -f mdnoPBC.xtc -b <start_time_in_ps> -e <end_time_in_ps> -o <output.xtc> -skip 10
```
Here we use the gmx command "trjconv" to extract the final XTC file.
**-b** - Define the time in ps of the first frame to read from the trajectory
**-e** - Define the time in ps of the last frame to read from the trajectory
**-o** - Define the output. Make sure the extension remains **".xtc"**
**-skip** - This step is optional. Skip defines to only write the corresponding frame to the output xtc. Here 5 frames.

# Step 2
We will use Bio3D package from R to calculate the Dynamic Cross Correlation Matrix. But some pre-installation steps are required. The main tool that we requires is CATDCD. It is used to trasfer the XTC file into DCD format as Bio3D doesnt recognise the XTC format.

## Step a)
Download the CatDCD package from [here](http://www.ks.uiuc.edu/Development/MDTools/catdcd/)

## Steb b)
Once you have downloaded the package then extract it in your choice of folder.

## Step c
Paste your extracted XTC file in the working directory of CATDCD folder.

## Step d)
Once you have pasted your XTC file in the required directory then run the following command:
```
./catdcd -o <output.dcd> -xtc <output.xtc>
```
**-o** - Defines the output file in the DCD format
**-xtc** - Defines the XTC extracted in Step 1.

## Step e)
Once you have the required DCD format file, then proceed with installing Bio3D package. Visit this [link](http://thegrantlab.org/bio3d/tutorials/installing-bio3d) or type the following command in your IDE or R software:
```
install.packages("bio3d", dependencies=TRUE)
```

# Step 3
a) Once you have succesfully installed the Bio3D package and have successfully converted your XTC file into DCD format then load the Bio3D library in R as follows:
```
library(bio3d)
```
b) Load your dcd file by specifying the path of the dcd file:
```
dcdfile <- "/path/to/dcd/file/last10ns.dcd"
```
c) Read the dcd file:
```
dcd <- read.dcd(dcdfile)
```
**The file output.dcd is first assigned to the pbject dcdfile. The function *read.dcd()* then processes the input trajectory file and returns its output to the new object *dcd* which can be checked via the following command:**
```
print(dcd)
```
d) Then a PDB file needs to be read to determine the atom correspondace. Type the following command:
```
pdbfile <- "/path/to/pad/file/file.pdb"
```
Read the file by typing:
```
pdb <- read.pdb(pdbfile)
```
**The simulation initial structure *file.pdb* is first assigned to the object *pdbfile*. The function *read.pdb()* the processes the PDB structure and returns its output to the new object *pdb*. This object can be checked by the following command:**
```
print(pdb)
```
e) Then the frames in the trajectory will be superposed. The interesting atoms will be first identified by using the function **atom.select()**. Type the following command:
```
ca.inds <- atom.select(pdb, elety = "CA")
```
**Here, all the C-alpha atoms are selected. The returned object *ca.inds* is a list containing atom and xyz numeric indices that we can now use to superpose all frames of the trajectory on the selected indices. For this, the *fit.xyz()* function will be used:**
```
xyz <- fit.xyz(fixed = pdb$xyz, mobile = dcd, fixed.inds = ca.inds$xyz, mobile.inds = ca.inds$xyz)
```
**The above command performs the actual superposition and stores the new coordinates in the matrix object *xyz*.**

f) The function **dccm()** will be used to calculate the cross-correlation matrices and this function will return a matrix of all atom-wise cross-correlations. The command is:
```
cij <- dccm(xyz[, ca.inds$xyz])
```
**The function *dccm()* calculates the DCCM using the above object *xyz* as the input and returns its output to the new object *cij*. The DCCM can be visualized by using the command:**
```
plot(cij)
```

## For more information regrading the Dynamic Cross-Correlation Matrix please follow the following paper:
**[Chapter Two - A beginner's guide to molecular dynamics simulations and the identification of cross-correlation networks for enzyme engineering](https://www.sciencedirect.com/science/article/pii/S0076687920301488?via%3Dihub)**
Haoran Yu, Paul A. Dalby
Methods in Enzymology, 643:15-49, 2020.
DOI: [https://doi.org/10.1016/bs.mie.2020.04.020](https://doi.org/10.1016/bs.mie.2020.04.020)
