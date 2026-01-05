# MDDB Tutorials
This repository contains the tutorials about MDDB.


# Instructions for the users that are going to provide the data to be uploaded to the MDDB

1) A 'raw' folder containing trajectories, parameter file, and a structure file (pdb).

2) A `inputs.yaml` which contains information about the simulations.  
   A. [Template inputs.yaml](template_inputs.yaml)  
   B. [Example inputs.yaml](example_inputs.yaml) (For a REMD which is having 30 trajectories)

# Instructions for the users at the Perez Lab that are going to upload the data to the MDDB (Florida Node)

1) Navigate to `/orange/alberto.perezant-mddb/MDDB` and create a folder relevant to the simulation that is going to be uploaded, which contains the `raw` folder and `inputs.yaml` file:

```bash
/orange/alberto.perezant-mddb/MDDB/
└── <system_name>/
    ├── raw/
    └── inputs.yaml
```
2) Run the workflow to do the analysis for trajectories provided. Please refer to [mwf run -h] (mwf_run_h.yaml) to have an idea about the options that you can have. Example slurm script to run the workflow. (This slurm refers to a system which is having 30 trajectories. So please edit as you need.)

```bash
#!/bin/bash
#SBATCH --job-name=MDDB
#SBATCH --output=MDDB.out
#SBATCH --error=MDDB.err
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --mail-user=<useid>>@gmail.com
#SBATCH --partition=hpg-b200
#SBATCH --nodes=1
#SBATCH --ntasks=4
#SBATCH --ntasks-per-node=4
#SBATCH --gpus-per-task=1
#SBATCH --cpus-per-task=1
#SBATCH --mem-per-cpu=80000mb
#SBATCH --time=04:00:00
#SBATCH --qos=alberto.perezant

ml conda
conda activate /orange/alberto.perezant/imesh.ranaweera/.conda/envs/mwf_env

for i in $(seq -w 0 29)
do
  echo "Running MDDB workflow for replica_$i"
  mwf run -e clusters energies pockets tmscore \
    -dir <path_to_system_directory> \
    -top <path_to_topology_file> \
    -md replica_$i <path_to_structure.pdb_file> \
    <path_to_raw_folder>/trajectory.$i.dcd \
    -inp <path_to_inputs.yaml_file> -ns \
    -fit \
done
```