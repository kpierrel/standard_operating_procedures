# Standard Operating Procedure:  
## Running Molecular Dynamic Simulations on your Local Machine

### 1. Purpose
- This SOP outlines the steps necessary to run Molecular Dynamic Simulations using OpenMM and OpenFF on a local HPC.

### 2. Procedure
#### 2.1 Set Up NSF Access Account
- Use the NSF Access [website](https://access-ci.org/get-started/for-graduate-students/) to begin setting up your account
- Your advisor should add you to their allocated server.
#### 2.2 Set Up your HPC Account
- Use the Pittsburgh Supercomputer [website](https://www.psc.edu/resources/bridges-2/user-guide/) to begin setting up your PSC account password
##### 2.2.1 Set Up an SSH Connection to your Local Machine's Terminal/Command Prompt
- Follow the instructions to establish an SSH connection [here](https://www.psc.edu/resources/bridges-2/user-guide#connecting-to-bridges-2)
#### 2.3 Downloading Necessary Software on Your Local Machine
##### 2.3.1 Install Visual Studio Code or any Integrated Development Environment (IDE)
- Follow the instructions to download VS Code [here](https://code.visualstudio.com/Download)
##### 2.3.2 Install Miniconda
- Follow the instructions to install Miniconda [here](https://www.anaconda.com/docs/getting-started/miniconda/install)
##### 2.3.3 Install Julia
- Follow the instructions to install Julia [here](https://julialang.org/downloads/)
##### 2.3.4 Install Packmol
- Follow the instructions to install Packmol [here](https://m3g.github.io/packmol/download.shtml)
##### 2.3.5 Install VMD
- Follow the instructions to install VMD [here](https://www.ks.uiuc.edu/Development/Download/download.cgi?PackageName=VMD)
#### 2.4 Downloading Necessary Software on the HPC
##### 2.4.1 Install OpenMM
- Check the cuda version that is able to run on the HPC's GPU
```bash
interact -p GPU -t 01:00:00 --gres=gpu:1
module avail cuda
nvidia-smi
```
- Follow the instructions to install OpenMM [here](https://docs.openmm.org/latest/userguide/application/01_getting_started.html#introduction)
    - Match the highest cuda version that is available with the GPU cuda version (cuda version 11.7.1 is what worked during Summer 2025)
##### 2.3.2 Install OpenFF
- Follow the instructions to install OpenFF [here](https://docs.openforcefield.org/projects/toolkit/en/latest/installation.html)

#### 2.2 Creating Folders for Files on your Local Machine
- Create a folder for the whole project (ideally with no spaces in the title)
    -  Example: *GPE_Transport_Properties_for_SIBs*
    -  The name of folders and files should be detailed enough for one to immediately remember what they were working on 1 year in the future
- Within the project folder:
    -  Create a folder for initial PDB files of all molecules
    -  Create a folder for Packmol Input Files
    -  Create a folder for Packmol Output Files
    -  Create a folder for Molecular Dynamic Output Files

#### 2.3 Creating Initial Configurations with Packmol on your Local Machine
- Form PDB files for molecules
    - Use PubChem to find SMILE string
        - Try Sigma Aldrich for specific chemicals found in papers
        - A short tutorial for the SMILES naming convention can be found [here](https://archive.epa.gov/med/med_archive_03/web/html/smiles.html)
        - If all else fails, one can use [this website](https://www.cheminfo.org/Chemistry/Cheminformatics/FormatConverter/index.html) to visualize how the molecule should look like and convert that image to a SMILES string or a PDB file
    - Use a SMILEs to PDB converter, download PDB file, and place in Initial PDB Folder
    - Small molecule SMILEs to PDB converter can be found [here](https://www.novoprolabs.com/tools/smiles2pdb)   
    - Polymer-sized molecule SMILEs to PDB converter can be found [here](https://cactus.nci.nih.gov/translate/)
- Open VS Code, open project folder, open Packmol Input Files folder
- Create a Packmol input file in the following format
    - Name the file in this format: *XXX.inp*
![Packmol Input File Example. This shows the spacing between molecules (tolerance), output file type, input file type, and the structure of the simulation box. The structure of the simulation box includes the initial molecule PDB file, how many atoms of each molecule should be in the box, the dimensions of the simulation box in Angstroms, and the end of the structure.](Packmol_input_file.png)
- Open Terminal/Command Prompt
```bash
    julia
```
```julia
    using Packmol
    input_file = "path_to_input_file/XXX.inp"
    run_packmol(input_file)
```
- Visualize the initial configuration box (the output PDB file from Packmol) using VMD 
- Within a text editor or IDE, open the initial configuration PDB file and write in the crystal structure of the simulation box at the top of the file after the last *REMARK* line and before the first *HETATM* line
    - eg. 40 Angstoms on each side and 90° Angles for a simulation box would be formatted as	*CRYST1   40.000   40.000   40.000  90.00  90.00  90.00 P 1           1*
- Copy all files from your local machine onto the HPC
    - Open Terminal/Command Prompt on your local machine
```bash
rsync -avz ~/GPE_Transport_Properties_for_SIBs/ username@bridges2.psc.edu:/jet/home/username/
```

#### 2.4 Performing Molecular Dynamic Simulations on the HPC
- Open a text editor on the HPC 
- Write a script for the specific molecular dynamic simulation using Sage 2.2.0
    - An example of the script using OpenMM and OpenFF for the simulation can be found at [this website](https://docs.openforcefield.org/en/latest/examples/openforcefield/openff-toolkit/SMIRNOFF_simulation/run_simulation.html)
    - Name the simulation script in the following format: *XXX.py*
    - Place script in the overall project folder on the HPC
- Open a text editor on the HPC 
- Write a SLURM batch script to run the simulation on the GPU
```bash
#!/bin/bash
#SBATCH -J XXX_transport
#SBATCH -N 1
#SBATCH -A chm240015p
#SBATCH -p GPU-shared
#SBATCH --gpus=v100-32:1
#SBATCH -n 4
#SBATCH -t 48:00:00
#SBATCH --time-min=24:00:00
#SBATCH --output=R-%x.%j.out
#SBATCH --error=R-%x.%j.err

source ~/.bashrc
module purge
module load cuda/11.7.1
micromamba activate openmm-sage

# Run the simulation
cd ~/GPE_Transport_Properties_for_SIBs
python XXX.py
```
    - Name the SLURM batch script in the following format: *XXX_transport.slurm*
- On the login node of the HPC, send the SLURM batch script to the scheduler on the HPC
```bash
sbatch XXX_transport.slurm
```