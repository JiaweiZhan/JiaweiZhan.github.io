---
layout: project
title: 'Berkeley GW--VASP interface'
# date: 1 Jul 2020
image: /assets/img/projects/VASP_BGW.jpg
screenshot: /assets/img/projects/VASP_BGW.jpg
# screenshot:
#   src:       /assets/img/projects/hyde-v2@0,25x.jpg
#   srcset:
#     1920w:   /assets/img/projects/hyde-v2.jpg
#     960w:    /assets/img/projects/hyde-v2@0,5x.jpg
#     480w:    /assets/img/projects/hyde-v2@0,25x.jpg
# links:
#   - title: Papers
#     url: https://aip.scitation.org/doi/full/10.1063/5.0010615
#   - title: News
#     url: /news/2020-06-26-Ogre-published/
caption: Expand the scale of systems that Hefei-Non-adiabatic Molecular Dynamics (HEFEI-NAMD) could analyze with the help of Berkeley GW.
description: >
    Ab initio GW calculation by VASP would take lots of computational cost,
    which further limits the number of atoms that the simulated cluster could
    contain. Therefore, in order to expand the scale of systems that Hefei-NAMD
    could analyze, we want to replace VASP GW with Berkeley GW, which is very
    famous for its large scale parallel algorithm and fast GW calculation.
accent_color: '#4fb1ba'
accent_image:
  background: 'linear-gradient(to bottom,#193747 0%,#233e4c 30%,#3c929e 50%,#d5d5d4 70%,#cdccc8 100%)'
  overlay:    true
author: false
---

[Hefei-NAMD](http://staff.ustc.edu.cn/~zhaojin/code.html) is an ab initio nonadiabatic molecular dynamics program to 
investigate the ultrafast excited carrier dynamics in real and momentum space, 
energy and time scale. Hefei-NAMD would take the screened coulomb interaction
W<sub>GG'</sub> and the difference of DFT_gap and GW_gap as input, construct
Bethe–Salpeter equation and finally carry out time dependent evolution.
Nowadays, there are mainly two version of Hefei-NAMD:  
- hefei-NAMD, developed by [Dr. Qijing Zhang](https://github.com/QijingZheng),
    is wroten in Fortran
- paraNAMD, developed by [Xiang Jiang](http://home.ustc.edu.cn/~jxiang/), supports parallel computing and is wroten in C++. **(This project is based on paraNAMD)**

Previously, Hefei-NAMD obtain required quasiparticles information and screened
coulomb interaction frm [VASP](https://www.vasp.at/), which is a widely used quantum-chemistry calculation
package based on Density Funtional Theory (DFT). VASP carries out a
full-frequency GW calculation to obtain the quasiparticles' bandstructure,
which is very accurate but more time-consuming.

[Berkeley GW](https://berkeleygw.org/), however, is very famous for its large-scale parallel algorithm. 
The package can be used to compute the electronic and optical properties of a 
wide variety of material systems from bulk semiconductors and metals to 
nanostructured materials and molecules. The package scales to 10,000's of CPUs 
and can be used to study systems containing up to 100's of atoms.

Therefore, this project would combine VASP and Berkeley GW, harvest each advantages
, accelerate GW calculation required by Hefei-NAMD and finally makes Hefei-NAMD
more applicable in excited carrier molecular dynamics simulation.

# Computational Process
## Run structure optimized on primitive cell
```bash
cd PrimitiveCell/struc_opt
qsub sub_vasp
```
**INCAR**:
```
General
  SYSTEM      = WS2 monolayer primitive_cell
  NPAR        = 4
  PREC        = Accurate
  ISIF        = 2
  NSW         = 2000
  EDIFFG      = -1E-5
  IBRION      = 2
  ENCUT       = 400
  ISPIN       = 1
  ISTART      = 0
  ICHARG      = 2
Dos Related values
  LPLANE      = .TRUE.
  ISMEAR      = 0
  SIGMA       = 0.05
Electronic relaxation
  ALGO        = Normal
  NELMIN      = 3
  NELM        = 200
  EDIFF       = 1E-8
Dipole Correction
#  LDIPOL      = .TRUE.
#  IDIPOL      = 3
#  DIPOL       = 0.5 0.5 0.5
Writing Flag
  LWAVE       = .FALSE.
  LCHARG      = .FALSE.
# VDW-DF
#  LUSE_VDW    = .TRUE.
#  AGGAC       = 0.0
#  GGA         = RE
```
## Run SCF, which includes an intact GW calculation (DFT1, DFT2, G0W0)
***WILL BE REPLACED BY Berkeley GW***
```bash
cd ../SCF/DFT1
qsub sub_vasp

cp WAVECAR ../dft2/
qsub sub_vasp

cp WAVECAR ../g0w0/
qsub sub_vasp
cp ${HOME}/NKPTSan ../g0w0/
```
**INCAR** for dft1 and dft2:
```
General
  SYSTEM      = WS2 monolayer primitive cell
  NPAR        = 4
  PREC        = Accurate
  NSW         = 0
  ENCUT       = 400 eV
  ISPIN       = 1
  ISTART      = 0 # 1 for dft2
  ICHARG      = 2 # 0 for dft2
Dos Related values
  LPLANE      = .TRUE.
  ISMEAR      = 0
  SIGMA       = 0.05
  LORBIT      = 11
  # LOPTICS = .TRUE. for dft2
Electronic relaxation
  ALGO        = Normal # Exact for dft2
  NELMIN      = 6 # comment for dft2
  NELM        = 300 # 1 for dft2
  EDIFF       = 1E-9 # comment for dft2
  # NBANDS = 192 for dft2
Dipole Correction
#  LDIPOL      = .TRUE.
#  IDIPOL      = 3
#  DIPOL       = 0.5 0.5 0.5
Writing Flag
  LWAVE       = .TRUE.
  LCHARG      = .TRUE.
#VDW-D2
# VDW-DF
#  LUSE_VDW    = .TRUE.
#  AGGAC       = 0.0
#  GGA         = RE
```
**INCAR** for single-point GW calculation:
```
General
  SYSTEM      = WS2 monolayer primitive cell
#  ISYM        = 2
#  NPAR        = 4
  PREC        = Accurate
  NSW         = 0
  ENCUT       = 400 eV
  ISPIN       = 1
  ISTART      = 1
  ICHARG      = 0
Dos Related values
  LPLANE      = .TRUE.
  ISMEAR      = 0
  SIGMA       = 0.05
Electronic relaxation
#  IALGO       = 38
  ALGO        = GW0
  NELM        = 1
  NBANDS      = 192
  NBANDSGW    = 192
  NOMEGA      = 40
Dipole Correction
#  LDIPOL      = .TRUE.
#  IDIPOL      = 3
#  DIPOL       = 0.5 0.5 0.5
Writing Flag
  LWAVE       = .TRUE.
  LCHARG      = .TRUE.
# LWANNIER90=.TRUE.
#VDW-DF
#  LUSE_VDW    = .TRUE.
#  AGGAC       = 0.0
#  GGA         = RE
```
## RUN structure on SuperCell to take more kpoints into account
```bash
cd ../SuperCell/struc_opt
qsub sub_vasp
```
**INCAR** is the same as PrimitiveCell's struc_opt **INCAR**.

## RUN Molecular Dynamics in NVT and NVE ensemble respectively
run in NVT ensemble:
```bash
cp CONTCAR ../100k/MD_NVT/POSCAR
cd ../100k/MD_NVT
qsub sub_vasp
```
**INCAR** for NVT:
```
SYSTEM = WS2 monolayer 1x2

# electronic degrees
ENCUT = 400
LREAL = A                      # real space projection
PREC  = Normal                 # chose Low only after tests
EDIFF = 1E-5                   # do not use default (too large drift)
#ISMEAR = -1 ; SIGMA = 0.172   # Fermi smearing: 2000 K 0.086 10-3
ISMEAR = 0 ; SIGMA = 0.1       # Gaussian smearing
ALGO = Very Fast               # recommended for MD (fall back ALGO = Fast)
MAXMIX = 40                    # reuse mixer from one MD step to next
NCORE= 4                       # one orbital on 4 cores
ISYM = 0                       # no symmetry
NELMIN = 6                     # minimum steps per time step, avoid breaking after 2 steps

# MD
IBRION = 0                     #molecular dynamics
NSW = 5000
NWRITE = 0
LWAVE = F ; LCHARG = F         #Don't write WAVECAR or CHGCAR
TEBEG = 100                   #Start temperature
TEEND = 100                   #Final temperature

# canonic (Nose) MD with XDATCAR updated every 50 steps
#SMASS = 0 ; NBLOCK = 50 ; POTIM = 1.5

# micro canonical MD with temperature scaling every 50 steps
# good for equlibration but usually better to use Nose thermostat
SMASS = -1 ; NBLOCK = 5 ; POTIM = 1.0
```
Check whether temperature converge at the target point:
```bash
python script/temp.py
```
run in NVE ensemble:
```bash
cp CONTCAR ../100k/MD_NVE/POSCAR
cd ../100k/MD_NVE
qsub sub_vasp
```
**INCAR** for NVE:
```
SYSTEM = WS2 monolayer 1x2

# electronic degrees
ENCUT = 400
LREAL = A                      # real space projection
PREC  = Low                    # chose Low only after tests
EDIFF = 1E-5                   # do not use default (too large drift)
#ISMEAR = -1 ; SIGMA = 0.172   # Fermi smearing: 2000 K 0.086 10-3
ISMEAR = 0 ; SIGMA = 0.1       # Gaussian smearing
ALGO = Fast                    # recommended for MD (fall back ALGO = Fast)
MAXMIX = 40                    # reuse mixer from one MD step to next
NCORE= 4                       # one orbital on 4 cores
ISYM = 0                       # no symmetry
NELMIN = 4                     # minimum steps per time step, avoid breaking after 2 steps

# MD
IBRION = 0                     #molecular dynamics
NSW = 12000
NWRITE = 0
LWAVE = F ; LCHARG = F         #Don't write WAVECAR or CHGCAR
TEBEG = 100                   #Start temperature
TEEND = 100                   #Final temperature

# canonic (Nose) MD with XDATCAR updated every 50 steps
#SMASS = 0 ; NBLOCK = 50 ; POTIM = 1.5

# micro canonical MD with temperature scaling every 50 steps
# good for equlibration but usually better to use Nose thermostat
# SMASS = -1 ; NBLOCK = 5 ; POTIM = 1.0

# canonic MD
SMASS = -3 ; NBLOCK = 1 ; POTIM = 1.0
```
## Electronic Calculation for Each Geometry in NVE Trajectory
Obtain each geometry from NVE's **XDATCAR** file
```bash
cp XDATCAR ../NAMD/
python script/xdat2pos.py
```
**INCAR** for electronic calculation:
```
General
  SYSTEM      = WS2 monolayer 1x2
  ISYM        = -1
  NPAR        = 4
  PREC        = Med
  NSW         = 0
  ENCUT       = 400 eV
  #NGX = 38; NGY = 42; NGZ = 162
  #NGX = 42; NGY = 50; NGZ = 182
  ISPIN       = 1
  ISTART      = 0
  ICHARG      = 1
Dos Related values
  LPLANE      = .TRUE.
  ISMEAR      = 0
  SIGMA       = 0.05
  LORBIT      = 11
Electronic relaxation
#  IALGO       = 38
  ALGO        = Fast
  NELMIN      = 4
  NELM        = 100
  EDIFF       = 1E-6
Writing Flag
  LWAVE       = .TRUE.
  LCHARG      = .TRUE.
```

## Finally, Carry out NAMD:
Generate **inicon**:
```bash
python  script/random_int.py
```
**input** for NAMD:
```
vaspver = 2           // VASP version 5.2.x or 5.4.x
igamma = 0            // VASP is in Gamma version or not
mode = 7              // calculation mode
ldiag = 0             // diagnalize Hamiltion matrix or not: 1 or 0 or ...
initc = 0             // initial conditions: 0-on original basis   1-on diagnal basis  3-on singlet/triplet basis
lphase = 0            // turn on/off phase correction
igsen = 0             // ground state energy option
rundir = ../run1          // directory for all SCFs

potim = 1.0           // fs  ion step time
temp = 100            // temperature
nelm = 1000           // electron intervals per ions step
nsw = 3000            // No. of structures in rundir
namdtime = 2500       // total steps of ions
nsample = 50         // No. of samples(initial ion structures)
ntraj = 20000         // trajectories for hopping
nrun = 5              // digital numbers in rundir (e.g. 00123 for nrun = 5)

// basis region
spin = 0              // 0-up 1-down
num_of_kpoints = 0    // No. of k-points for NAMD, 0 for all
kpoints = 1           // 1, 2, 3 ...    list all k-points, if No. of k-points = 0, kpoints = 1
bmax = 334 334        // band max for spin up/down
bmin = 325 325        // band min for spin up/down

// exciton
ispin = 1             // ispin = 1 or 2, calculate spin down if ispin = 2 OR spin up = spin down if ispin = 1
highmem = 1           // 0 or 1, usually highmem = 1 if machine memory is enough
epsl = -1.0           // epsl = -1 ( < 0), use gw; OR epsl > 0 use a dielectric constant = epsl
is_w_diagonal = 1     // only use diagonal W(1) OR not(0)
// directory for GW calculation
wpotdir = /data/alsaidi/graduation_project/WS2_NAMD/PrimitiveCell/SCF/g0w0/
gapdiff = -1.0        // gap_GW - gap_DFT. if < 0, NAMD will calculate automatically
ngf = 2               // FFT for charge density matrix, usually ngf = 2
nkptv = 9 9 1         // kpoints grid for BSE (KPOINTS-mesh in rundir)
nkptv_GW = 3 3 1      // kpoints grid for GW  (KPOINTS-mesh from wpotdir transfer)

idege = 1             // usually 1
iexpot = 2
iscpot = 1
ienergy = 0

cmax = 53 53        // conduction/valence bands region for spin up/down
cmin = 53 53
vmax = 52 52
vmin = 52 52
```
run **NAMD**:
```bash
qsub sub_namd
```
