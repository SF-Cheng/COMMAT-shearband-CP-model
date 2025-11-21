# Crystal Plasticity Modeling of Orientation Evolution Within Shear Bands 

This repository contains all computational scripts and EBSD data used in the article:

**Crystal Plasticity Modeling of Orientation Evolution Within Shear Bands: Application to Grain-Oriented Silicon Steel**

The repository includes:
- The UDM–SDB crystal plasticity code used to simulate orientation evolution.
- EBSD datasets corresponding to the figures shown in the manuscript.

## 📂 Repository Structure

Many subroutines in this project follow the same numerical structure as the classic crystal plasticity UMAT developed by **Huang.** To avoid redundancy, such subroutines are *not* described in detail in this README; please refer to Huang’s paper **A User-Material Subroutine Incorporating Single Crystal Plasticity in the ABAQUS Finite Element Program” for their full documentation.**

```
├── Code_UDM-SDB/                  # UDM–SDB crystal plasticity model
│   │
│   ├── Code_UDM-SDB.vfproj        # Intel Fortran project file (open this file)
│   ├── Code_UDM-SDB.sln           # Visual Studio solution 
│   │
│   ├── F1.txt                     # Input file1 (initial orientations)
│   ├── F2.txt                     # Input file2 (target strain components to be output)
│   ├── F3.txt                     # Input file3 state variables to be output)
│   │
│   ├── PROGRAM MAIN.for           # Main program
│   ├── SUBROUTINE UMAT.for        # Core user-material subroutine (UDM–SDB constitutive model)
│   ├── SUBROUTINE EULERMILLER.for # Convert Euler angles to Miller indices
│   ├── SUBROUTINE SM1toMILLER.for # Update Miller indices during deformation
│   ├── SUBROUTINE L_StoG.for      # Transform the displacement-gradient tensor from the shear-band frame to the global coordinate system
│   ├── SUBROUTINE LtoDSDR.for     # Decompose the displacement-gradient tensor into deformation rate and rigid-body rotation components
│   ├── SUBROUTINE ROTtensor.for   # Rotate a second-order tensor between different coordinate systems
│   ├── others.for                 # uxiliary routines for loop-iteration counting
│   │            
│   └── Subroutines_from_Huang's UMAT
│       ├── SUBROUTINE CROSS.for
│       ├── SUBROUTINE GSLPINIT.for
│       ├── SUBROUTINE ITERATION.for
│       ├── SUBROUTINE LATENTHARDEN.for   # latent hardening law 
│       ├── SUBROUTINE LUBKSB.for
│       ├── SUBROUTINE LUDCMP.for
│       ├── SUBROUTINE ROTATION.for
│       ├── SUBROUTINE SLIPSYS.for
│       └── SUBROUTINE STRAINRATE.for

│
├── EBSD_data/                       # EBSD experimental datasets used in the paper
│   │ 
│   ├── Grain_A_initial.rar          # EBSD data of grain A before cold rolling (corresponds to Fig. 5a)
│   ├── Grain_A_0.43_matrix.rar      # Matrix orientation data of grain A after 43% cold rolling (corresponds to Fig. 6a)
│   ├── Intraband_volume_SB-A.rar    # Intraband EBSD data of SB-A (corresponds to Fig. 8a)
│   │
│   ├── Grain_B_initial.rar          # EBSD data of grain B before cold rolling (corresponds to Fig. 5b)
│   ├── Grain_B_0.43_matrix.rar      # Matrix orientation data of grain B after 43% cold rolling (corresponds to Fig. 6b)
│   ├── Intraband_volume_SB-B.rar    # Intraband EBSD data of SB-B (corresponds to Fig. 8b)
│   │
│   ├── Grain_C_initial.rar          # EBSD data of grain C before cold rolling (corresponds to Fig. 5c)
│   ├── Grain_C_0.43_matrix.rar      # Matrix orientation data of grain C after 43% cold rolling (corresponds to Fig. 6c)
│   └── Intraband_volume_SB-C.rar    # Intraband EBSD data of SB-C (corresponds to Fig. 8c)  
│
└── README.md
```

## ▶️ How to Run

### **1. Software Requirements**

- Visual Studio (2019 or later)
- Intel Fortran Compiler (e.g., Intel OneAPI or Intel Parallel Studio)
- Windows OS (Linux not tested)

### **2. Compilation**

Open this file in Visual Studio:

**Code_UDM-SDB.vfproj**

Then:

- Set build mode to **Release x64**
- Build the project

### **3. Running a Simulation**

#### 3.1 Input Initial Orientations
List all initial Euler angles in the order **(φ1, Φ, φ2)** and place them in `F1.txt`, **one orientation (one set of Euler angles) per line**. The last line must be **720 720 720** as the termination flag.

#### 3.2 Specifying the strains to output
Enter the specified strains (in true-strain form) into `F2.txt` , with one strain value per line. The last line must be **–1**, which serves as the termination flag.

#### 3.3 Specifying the state variables to output
Enter into `F3.txt` the IDs of the state variables you wish to output.In this work, the calculations require the Miller indices (IDs **501–509**) and the slip rates of all slip systems (IDs **325–348**). For example, to output only the Miller indices, the content of `F3.txt` should be:

```text
501
509
```
#### 3.4 Modifying Key Parameters
Four key parameters must be specified in `PROGRAM_MAIN.for`:

- **limit** — located at line 92.
  This value must be greater than or equal to the maximum strain specified in `F2.txt`.

- **ANGSB** — located at line 149.
  This value is the angle between the shear band and the rolling direction (RD). In this work, a shear band of **30°** relative to nagetive RD is adopted, so **ANGSB = 150°** .

- **SSPS_con** — located at line 150.
 This coefficient determines the incremental strain step within a shear band. A value of **0.001** is recommended to ensure numerical stability and convergence.

- **Step_SB** — located at line 151.
  This is the critical strain (true strain) at which the shear band forms. For example, if the shear band initiates at a **35%** reduction, the corresponding true strain is approximately **0.43**, so this parameter should be set to **430**(=0.43/0.001).

#### 3.5 Output data

All output data are stored in the automatically generated file `Out_data.txt`.  
The number of rows in this file equals the product of the number of initial orientations and the number of specified strains, and the number of columns equals the number of selected state variables.

## 📬 Contact
If you have any questions regarding the code or data, please contact:

Sifei Cheng  
Email: Sifei_Cheng@163.com

