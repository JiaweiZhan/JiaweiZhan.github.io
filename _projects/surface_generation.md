---
layout: project
title: 'Surface and Interface Generation'
# date: 1 Jul 2019
# image: /assets/img/projects/hy-push-state.svg
# screenshot: /assets/img/projects/hy-push-state.svg
screenshot:
  src:       /assets/img/projects/hyde-v2@0,25x.jpg
  srcset:
    1920w:   /assets/img/projects/hyde-v2.jpg
    960w:    /assets/img/projects/hyde-v2@0,5x.jpg
    480w:    /assets/img/projects/hyde-v2@0,25x.jpg
links:
  - title: Papers
    url: https://aip.scitation.org/doi/full/10.1063/5.0010615
  - title: News
    url: /news/2020-06-26-Ogre-published/
caption: Cleave slabs and predict optimized geometric structure of interfaces
description: >
  Driven by the idea of predicting how two slabs would construct a interface in
  the real world, we are working on (a) cleaving slabs from bulk structure, (b) carry out quantum-chemistry calculation to obtain surface energies and predict crystal habits as well as (c) predicting
  the optimized geometric structure of interfaces. 
accent_color: '#4fb1ba'
accent_image:
  background: 'linear-gradient(to bottom,#193747 0%,#233e4c 30%,#3c929e 50%,#d5d5d4 70%,#cdccc8 100%)'
  overlay:    true
author: false
---

> Surface and Interface Generation
{:.lead}
***Surface Generation*** is a very essential demand for pharmaceuticals, organic electronics,
and energetic materials. Polymorphism, which is the capability of a molecule to crystallize into multiple forms,
expands the design space and increases the opportunities for surface property optimization. 
Computer modeling of organic surfaces can promote rational molecular crystal habit design and solid form selection 
for pharmaceuticals, can lead to improved performance of organic electronic devices, and can help tune the sensitivity of energetic materials.

Furthermore, two surfaces stacked together could ***generate interfaces***, which is the prerequisite of heterojunctions' construction and analyzing.
Therefore, we want to build an open-source tools that can be used not only by scientific researchers, but also by industry engineers, 
to model and analyze the construction of interfaces in a visualized and efficient way. Basically, we divide ***surface generation*** into two part
- Generate surface slab models from bulk molecular crystal structures and numerically predict the crystal habits based on that
- Predict the optimized geometric structure of interfaces using Machine
    Learning or Monte-Carlo Method.

Currently, we have developed the first part and summarize our
effots in one [paper](/news/2020-06-26-Ogre-published/). As for the second
part, we have made great progess in using Graphic Neural Networks (GNN) and
force field to predict the distance between two adjacent slabs in c direction, which can be found [here](https://drive.google.com/file/d/17LXl33MuGLrYIKm3qkBWU3Bs0lkgDojR/view). 
However, there is still a long way to go before the goal we want to achieve. So we will continue to develop the project.

In the following part, I will introduce how to use our open-source code
**Ogre** to implement slab generation.
# Ogre 
<img src="/assets/img/logo.png" alt="logo" align="bottom">

Ogre is written in Python and interfaces with the FHI-aims code to calculate surface energies at the level of density functional theory (DFT). 
The input of Ogre is the geometry of the bulk molecular crystal. The surface is cleaved from the bulk structure with the molecules on the surface kept intact. 
A slab model is constructed according to the user specifications for the number of molecular layers and the length of the vacuum region. 
Ogre automatically identifies all symmetrically unique surfaces for the user-specified Miller indices and detects all possible surface terminations. 
Ogre includes utilities, OgreSWAMP, to analyze the surface energy convergence and Wulff shape of the molecular crystal. 

The data that support the findings of this study are available upon reasonable request. The Ogre code is available for [download](https://www.noamarom.com/software/ogre/).
## Installation
```bash
conda create -n ogre python=3.6
conda activate ogre
python setup.py install
```
This code might crash if you use the newest version of pymatgen, so please use the version specified in setup.py
## Example
All configurations are stored in the .config file.

Ogre is for cleaving the surfaces, ogreSWAMP is for plotting surface energy convergence plots and Wulff diagram.

ogre.config:


```
[io]
structure_path = ./structures/relaxed_structures/TETCEN.cif
structure_name = TETCEN_test
format = FHI
; Format can be FHI, VASP or CIF
[methods]
cleave_option = 1
;0: cleave a single surface, 1: cleave surfaces for surface energy calculations
[parameters]
layers = 1-6
; Layers could be specified as combination of start-end or separate numbers by space
vacuum_size = 40
highest_index = 3
; Only needed in cleave_option = 1， ignored in cleave_option = 0
supercell_size = None
miller_index = 3 3 3
; Only needed in cleave_option = 0, ignored in cleave_option = 1
desired_num_of_molecules_oneLayer = 0
; Set to 0 if you don't want to change one layer structure. Default is 0 (highly recommended)
```


ogreSWAMP.config:
```
[io]
scf_path = example/SCF
structure_path = structures/relaxed_structures/ASPIRIN.cif
structure_name = aspirin
[methods]
fitting_method = 0
; 0: linear method, 1: Boettger method.
[Wulff]
Wulff_plot = True
; Wehther to plot the Wulff diagram
projected_direction = 1 1 1
; projected_direction for Wulff diagram
[convergence]
threshhold = 0.35
consecutive_step = 2
; If for N consecutive steps, the difference is smaller than threshhold, then it is converged.
```

To run ogre
```bash
python runOgre.py --filename ogre.config
```
To launch DFT calculations, use scripts under scripts/
```bash
#Launch PBE+TS, please modify the setting in the script, same for the following scripts.
python AimsBatchCAlc.py

#Extract the data from PBE+TS and store in .json file
python AimsExtractor.py

#Launch PBE+MBD
python MBDBatchCalc.py

#Store data in .json file
python MBDExtract.py
```
To run ogreSWAMP
```bash
python runOgreSWAMP.py --filename ogreSWAMP.config
```
