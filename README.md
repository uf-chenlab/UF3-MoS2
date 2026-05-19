# UF3-MoS₂ Machine-Learned Interatomic Potential 

This repository provides a **UF3 machine-learned interatomic potential (MLIP) for MoS₂** developed to enable molecular-dynamics simulations of epitaxial growth.  
The potential is designed for use with **LAMMPS** via the **UF3 pair style**:https://docs.lammps.org/pair_uf3.html



## Repository Contents

- `MoS2.UF3`: UF3 potential file for MoS₂

- `pair_uf3.cpp`: UF3 pair style source file with a **one-line buffer-size modification** required to read long UF3 coefficient lines (>1024 characters). **The trivial patch is described below for completeness.**

- `prod_epitaxy.in`: Example LAMMPS input script for non-equilibrium epitaxial growth simulations

- `substrate_heated.data`: Example substrate data file used as input for growth simulations

- `Supplemental Material - UF3 MoS2.pdf` : Copy of the supplementary material accompanying the associated publication


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
 txtfilereader.set_bufsize(10240);  // REQUIRED MODIFICATION: increase the default 1024-character per line limit 
```

Then compile LAMMPS.

**This 'issue' has been addressed in the offical UF3 repository: https://github.com/uf3/uf3/issues/136** 


For convenience, a modified pair_uf3.cpp file with this change already applied is provided in this
repository. Users may either apply the one-line change manually or replace the UF3 pair style source file
before compilation.

---

## Citation
E. Bilgili, N. Taormina, R. Hennig, S. R. Phillpot, and Y. Chen, Machine-learned interatomic potential for predictive simulation of MoS2 epitaxy, Phys. Rev. Mater. 10, 054002 (2026). DOI: https://doi.org/10.1103/71xp-pjb6

