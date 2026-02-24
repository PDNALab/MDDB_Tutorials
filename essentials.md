1) To take the screenshot from a specifc frame
```bash
mwf run -i screenshot \
  -dir <path_to_system_folder> \
  -top <path_to_raw_folder>/topology.prmtop \
  -md replica_00 \
  <path_to_raw_folder>/trajectory.00.dcd \
  -inp <path_to_system_folder>/inputs.yaml -ns \
  -fit \
  --screenshot_frame <frame_no>
  ```