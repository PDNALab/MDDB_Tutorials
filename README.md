# MDDB Tutorials
This repository contains tutorials for MDDB.


# Instructions for the users that are going to provide the data to be uploaded to the MDDB

The following data must be provided for each system to the uploaders to have your data included in the MDDB Florida Node.

1) A 'raw' folder containing the trajectories, parameter file.

2) An `inputs.yaml` file containing simulation information.  
   A. [Template inputs.yaml](template_inputs.yaml)  
   B. [Example inputs.yaml](example_inputs.yaml) (for a REMD system with 30 trajectories)

The folder structure for each system should look like this and the folder name `<pdbid>_<program>_<forcefield>`. Use simple letters for folder name.
```bash
<pdbid>_<program>_<forcefield>/
    ├── raw/
    └── inputs.yaml
```

# Instructions for Perez Lab users uploading data to MDDB (Florida Node)

1) Navigate to `/orange/alberto.perezant-mddb/MDDB` and create a folder following `<pdbid>_<program>_<forcefield>_<accession>`. Use simple letters for the rest except `accession`. This folder should contain the `raw` folder and `inputs.yaml` file:

```bash
/orange/alberto.perezant-mddb/MDDB/
└── <system_name>/
    ├── raw/
    └── inputs.yaml
```
2) Run the workflow to analyze the provided trajectories. Refer to [mwf run -h](mwf_run_h.yaml) for available options or activate the conda environment `conda activate /orange/alberto.perezant/imesh.ranaweera/.conda/envs/mwf_env` and run `mwf run -h`. Example Slurm script (for a system with 30 trajectories; edit as needed):

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
    -fit 
done
```

After running the analysis, the folder structure should look like this:
```bash
/orange/alberto.perezant-mddb/MDDB/
└── <system_name>/
    ├── raw/
    └── inputs.yaml
    └── replica_00
    └── ..
    └── replica_29
    └── topology.prmtop
    └── mdf.screenshot.jpg
    └── <metedata>.json
```

3) Transfer files to the `pubperez1` machine (do not include the raw folder). First navigate to `/orange/alberto.perezant-mddb/MDDB`, then run:

```bash
rsync -avP -e 'ssh -J <userid>@hpg.rc.ufl.edu' --exclude 'raw/' <system_folder_name>/ perez@pubperez1:/pubapps/perez/mddb/data/<system_folder_name>/
```

4) Uplaod the files to MDDB. (Log in to the `pubperez1` machine and navigate to `/pubapps/perez/mddb/data`, which contains the data to be uploaded.)

4.1) Load data to the Florida node (without publishing):
```bash
podman run --rm --network data_network -v /pubapps/perez/mddb/data:/data:Z localhost/loader_image load /data/<system_foder_name>
```

4.2) Publish to the main node: 
```bash
podman run --rm --network data_network -v /pubapps/perez/mddb/data:/data:Z localhost/loader_image publish <accessionID>
```

5) Remove data for a specific accession ID (use with extreme caution!!):
```bash
podman run --rm --network data_network -v /pubapps/perez/mddb/data:/data:Z localhost/loader_image delete <accessionID> -y 
```

For more information about the workflow, visit the [MDDB Workflow documentation](https://mddb-workflow.readthedocs.io/en/latest/).
