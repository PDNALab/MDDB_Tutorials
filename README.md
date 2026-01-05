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
2) Run the workflow to do the analysis for trajectories provided. Please refer to [mwf run -h](mwf_run_h.yaml) to have an idea about the options that you can have. Example slurm script to run the workflow. (This slurm refers to a system which is having 30 trajectories. So please edit as you need.)

```bash
#!/bin/bash
#SBATCH --job-name=MDDB
#SBATCH --output=MDDB.out
#SBATCH --error=MDDB.err
#SBATCH --mail-type=BEGIN,END,FAIL
#SBATCH --mail-user=<userid>>@gmail.com
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

After running the analysis the folder should look like this 
```bash
/orange/alberto.perezant-mddb/MDDB/
└── <system_name>/
    ├── raw/
    └── inputs.yaml
    └── replica_00
    └── ..
    └── replica_29
    └── topology.prmtop
    └── <metedata>.json
```

3) Move the files to pubperez1 machine. (Do not upload the raw folder! upload the rest) (do cd ../ before running the following commad)

```bash
rsync -avP -e 'ssh -J <userid>@hpg.rc.ufl.edu' --exclude 'raw/' <system_folder_name>/ perez@pubperez1:/pubapps/perez/mddb/data/<system_folder_name>/
```

4) Uplaod the files to MDDB (To perform this step you should login to the pubperez1 machine and navigate to the directory `/pubapps/perez/mddb/data` which is having the data to be uploaded)

4.1) Load the data to the florida node without publishing.
podman run --rm --network data_network -v /pubapps/perez/mddb/data:/data:Z localhost/loader_image load /data/<system_foder_name>

4.2) publish to the main node 
podman run --rm --network data_network -v /pubapps/perez/mddb/data:/data:Z localhost/loader_image publish <accessionID>

5) If you want to remove all the data for a specific accessionID use the following command (Use with caution!!)

podman run --rm --network data_network -v /pubapps/perez/mddb/data:/data:Z localhost/loader_image delete <accessionID> -y 



