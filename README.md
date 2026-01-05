# MDDB_Tutorials
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
└── <simulation_name>/
    ├── raw/
    └── inputs.yaml
```
2) 