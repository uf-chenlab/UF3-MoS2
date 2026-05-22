# UF3-MoS₂ Machine-Learned Interatomic Potential 

This repository provides a **UF3 machine-learned interatomic potential (MLIP) for MoS₂** developed to enable molecular-dynamics simulations of epitaxial growth. The potential is designed for use with **LAMMPS** via the **UF3 pair style**:https://docs.lammps.org/pair_uf3.html



## Repository Contents

- **`MoS2.UF3`**: UF3 potential file for MoS₂
- `pair_uf3.cpp`: UF3 pair style source file with a **one-line buffer-size modification** required to read long UF3 coefficient lines (>1024 characters). **The trivial patch is described below for completeness.**
- `prod_epitaxy.in`: Example LAMMPS input script for non-equilibrium epitaxial growth simulations
- `substrate_heated.data`: Example substrate data file used as input for growth simulations
- `Supplemental Material - UF3 MoS2.pdf` : Copy of the supplementary material accompanying the associated publication
- `MoS2-UF3.json`: Raw model file output during optimization using UF3Tools used to obtain the .UF3 potential files.
-  `MoS2_v2.UF3` — UF3 potential file for MoS₂; uniform-knot (uk) version with fixed replusion. Potential parameters are functionally identical to `MoS2.UF3`, with two improvements:

     1. **~60% faster.** The potential is tagged with `uk`, indicating uniform knot spacing. Uniform knots allow the active B-spline interval to be located via O(1) index arithmetic rather than O(n) linear search, accelerating every energy and force evaluation. Knot positions are stored at full double precision to ensure correct boundary behavior in LAMMPS. This yields even higher computational speed than the performance reported in the associated publication (see **Sec. III F**).
     2. **Spurious energy wells in the short-range repulsive region of 2B terms removed.** The original 2B potentials contained non-physical wells inside the repulsive wall (r ≲ 1.5 Å). As discussed in the associated publication, these arose from spline interpolation in regions absent from the DFT training set, leaving the splines unconstrained at unphysically short pair distances. These artifacts did not affect simulation stability or accuracy, since such extremely short-range configurations were not sampled during simulations performed in the associated publication. Nevertheless, `MoS2_v2.UF3` provides a cleaner and more physically faithful short-range repulsive representation. The potential parameters are unchanged relative to `MoS2.UF3` in the physically relevant regions; i.e., the two potentials are functionally identical for **most** practical simulations.

<img width="1364" height="455" alt="image" src="https://github.com/user-attachments/assets/bb57bb45-7d39-474b-aa47-bbae3d0851c5" />
*2B potential terms for S–S, S–Mo, and Mo–Mo. Parameters are identical `MoS2.UF3` in the physically relevant region.*


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


For convenience, a modified pair_uf3.cpp file with this change already applied is provided in this repository. Users may either apply the one-line change manually or replace the UF3 pair style source file before compilation.

---

## Citation
E. Bilgili, N. Taormina, R. Hennig, S. R. Phillpot, and Y. Chen, Machine-learned interatomic potential for predictive simulation of MoS2 epitaxy, Phys. Rev. Mater. 10, 054002 (2026). DOI: https://doi.org/10.1103/71xp-pjb6

