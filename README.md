# DCCM_Calculation
This repository describes how to calculate the Dynamical Cross-Correlation Matrix from the simulated XTC data.

# Step 1
Extract the XTC of interest from the simulated data (GROMACS) using the following command:
```
gmx trjconv -f mdnoPBC.xtc -b <start_time_in_ps> -e <end_time_in_ps> -o <output.xtc> -skip 10
```
