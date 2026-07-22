****{  index=* source="investigation28_edr_multi_stage_intrusion.csv"
| sort timestamp
| table timestamp user hostname process_name command_line severity description   }  *****
