Juniper JUNOS SLAX op script to assess a system's network interfaces and
determine which ones might be appropriate to run the rpf-check feature in
order to implement reverse path filtering to prevent spoofed addresses.

Syntax:

Assess/audit interfaces:  
op rpf-tool
  
Automatically apply to all eligible interfaces:  
op rpf-tool mode apply

Remove RPF from all interfaces:  
op rpf-tool mode remove
  
Scope operation to nominated interface:  
  op rpf-tool mode apply interface ge-0/0/0.0
  op rpf-tool mode remove interface ge-0/0/0.0

Also included:
- test configuration file extracts from Juniper Firefly
- test outputs
