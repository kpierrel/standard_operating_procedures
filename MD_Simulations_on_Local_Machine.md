# Standard Operating Procedure:  
## Running Molecular Dynamic Simulations on your Local Machine

### 1. Purpose
- This SOP outlines the steps necessary to run Molecular Dynamic Simulations using OpenMM and OpenFF on your local machine.

### 2. Procedure
##### 2.1 Downloading Necessary Software
###### 2.1.1 Install Visual Studio Code
- Follow the instructions to download VS Code [here](https://code.visualstudio.com/Download)
###### 2.1.2 Install Miniconda
- Follow the instructions to install Miniconda [here](https://www.anaconda.com/docs/getting-started/miniconda/install)
###### 2.1.3 Install Julia
- Follow the instructions to install Julia [here](https://julialang.org/downloads/)
###### 2.1.4 Install Packmol
- Follow the instructions to install Packmol [here](https://m3g.github.io/packmol/download.shtml)
###### 2.1.5 Install OpenMM
- Follow the instructions to install OpenMM [here](https://docs.openmm.org/latest/userguide/application/01_getting_started.html#introduction)
###### 2.1.6 Install OpenFF
- Follow the instructions to install OpenFF [here](https://docs.openforcefield.org/projects/toolkit/en/latest/installation.html)
###### 2.1.7 Install VMD
- Follow the instructions to install VMD [here](https://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD)

##### 2.2 Creating Folders for Files
- Create a folder for the whole project
- Within the project folder:
    -  Create a folder for initial PDB files of all molecules
    -  Create a folder for Packmol Input Files
    -  Create a folder for Packmol Output Files
    -  Create a folder for Molecular Dynamic Output Files

##### 2.3 Creating Initial Configurations with Packmol
- Form PDB files for molecules
    - Use PubChem to find SMILE string
    - Use a SMILEs to PDB converter, download PDB file, and place in Initial PDB Folder
    - Small molecule SMILEs to PDB converter can be found [here](https://www.novoprolabs.com/tools/smiles2pdb)   
    - Polymer-sized molecule SMILEs to PDB converter can be found [here](https://cactus.nci.nih.gov/translate/)
- Open VS Code, open project folder, open Packmol Input Files folder
- Create a Packmol input file in the following format
    - Name the file in this format: XXX.inp
![Packmol Input Example](Packmol_input_file.png)
- Open Terminal/Command Prompt
- Type Julia in the command line
- Once Julia is activated, type *Using Packmol*
- Type *input_file = "path to Packmol Input File"*
- Type *run_packmol(input_file)*
- Visualize the initial configuration box (the output PDB file from Packmol) using VMD 
- In VS Code, open the initial configuration PDB file and write in the crystal structure of the simulation box at the top of the file after the last *REMARK* line and before the first *HETATM* line
    - eg. 40 Angstoms and 90° Angles would be formatted as	*CRYST1   40.000   40.000   40.000  90.00  90.00  90.00 P 1           1*