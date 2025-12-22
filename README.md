# UF3-MoS₂ Machine Learned Interatomic Potential 

This repository provides a **UF3 machine-learned interatomic potential (MLIP) for MoS₂** developed to enable non-equilibrium molecular-dynamics simulations of epitaxial growth.  
The potential is designed for use with **LAMMPS** via the **UF3 pair style**:  
https://docs.lammps.org/pair_uf3.html



## Repository Contents

- `MoS2.UF3`  
  UF3 potential file for MoS₂

- `pair_uf3.cpp`  
 UF3 pair style source file with a **one-line buffer-size modification** required to read long UF3 coefficient lines (>1024 characters). **The trivial patch is described below for completeness.**

- `prod_epitaxy.in`  
  Example LAMMPS input script for non-equilibrium epitaxial growth simulations

- `substrate_heated.data`  
  Example substrate data file used as input for growth simulations

- `SI.docx`  
  Supplementary Information document accompanying the publication


---
## UF3 pair_style modification 

Due to the large cutoff distances and dense knot spacing used in this model, the UF3 potential file contains
coefficient lines that exceed the default 1024-character limit used by LAMMPS via the `TextFileReader` object when parsing UF3
potential files. Therefore, to use the UF3 potential, a **small modification to the UF3 pair style in LAMMPS is required**. 


Before compiling LAMMPS, update the UF3 pair style source file:

```
src/ML-UF3/pair_uf3.cpp
```

Add the following line after the `TextFileReader` is constructed (should be at line 261) :

```cpp
 txtfilereader.set_bufsize(10240)  // REQUIRED MODIFICATION: increase the default 1024-character per line limit 
```

Then compile LAMMPS.

**This 'issue' has been addressed in the offical UF3 repository: https://github.com/uf3/uf3/issues/136** 


For convenience, a modified pair_uf3.cpp file with this change already applied is provided in this
repository. Users may either apply the one-line change manually or replace the UF3 pair style source file
before compilation.

---

## Citation

The associated work has been submitted for journal publication. A preprint manuscript is available here:

E. Bilgili, N. Taormina, R. Hennig, S. R. Phillpot, Y. Chen  
*Ultra-Fast Machine-Learned Interatomic Potential for MoS₂ Enabling  
Non-Equilibrium Molecular-Dynamics Simulation of Epitaxial Growth*  
https://doi.org/10.48550/arXiv.2512.15952

