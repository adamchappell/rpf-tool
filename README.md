# rpf-tool

Juniper JUNOS SLAX op script to assess a system's network interfaces and
determine which ones might be appropriate to run the rpf-check feature in
order to implement reverse path filtering to prevent spoofed addresses.

Tool checks interface to ensure it is free of routing protocol that might cause
asymmetry and reports. Additionally, tool can apply and remove RPF either to
all eligible interfaces, or on a one-by-one basis.

[Background information and motivation] (http://blog.packetsource.net/2015/04/24/preventing-ddos-using-junipers-slax-on-mx-960)

## Example usage

Assess/audit interfaces to check for suitability of RPF:

	adamc@VirtualSRX> op rpf-tool 
	Interface RPF analysis/configuration tool
	   Flags: + eligible, - ineligible, * running, ! excluded
	  Interface      Instance     Description              Address/Config
	! ge-0/0/0.0                  CST 10M FOOB1/CPE/12345  
	+ ge-0/0/1.1                  CST 10M FOOB1/CPE/10001  inet 10.0.1.1/24 
	+ ge-0/0/1.2                  CST 10M FOOB1/CPE/10002  inet6 fd00:0001::1/64 
	+ ge-0/0/1.3                  CST 10M FOOB1/CPE/10003  inet 10.0.3.1/24 inet6 fd00:0003::1/64 
	+ ge-0/0/1.4                  CST 10M FOOB1/CPE/10004  inet 10.0.4.1/24 inet 10.1.4.1/24 inet 10.2.4.1/24 inet6 fd00:0004::1/64 inet6 fd00:0104::1/64 inet6 fd00:0204::1/64 
	+ ge-0/0/1.5     VPN-1        CST 10M FOOB1/CPE/10005  inet 10.0.5.1/24 
	+ ge-0/0/1.6     VPN-1        CST 10M FOOB1/CPE/10006  inet6 fd00:0006::1/64 
	+ ge-0/0/1.7     VPN-1        CST 10M FOOB1/CPE/10007  inet 10.0.7.1/24 inet6 fd00:0007::1/64 
	+ ge-0/0/1.8     VPN-1        CST 10M FOOB1/CPE/10008  inet 10.0.8.1/24 inet 10.1.8.1/24 inet 10.2.8.1/24 inet6 fd00:0008::1/64 inet6 fd00:0108::1/64 inet6 fd00:0208::1/64 

Automatically apply RPF to all eligible interfaces:  

	adamc@VirtualSRX> op rpf-tool mode apply     
	Applying RPF inet configuration to interface ge-0/0/1.1
	Applying RPF inet6 configuration to interface ge-0/0/1.2
	Applying RPF inet configuration to interface ge-0/0/1.3
	Applying RPF inet6 configuration to interface ge-0/0/1.3
	Applying RPF inet configuration to interface ge-0/0/1.4
	Applying RPF inet6 configuration to interface ge-0/0/1.4
	Applying RPF inet configuration to interface ge-0/0/1.5
	Applying RPF inet6 configuration to interface ge-0/0/1.6
	Applying RPF inet configuration to interface ge-0/0/1.7
	Applying RPF inet6 configuration to interface ge-0/0/1.7
	Applying RPF inet configuration to interface ge-0/0/1.8
	Applying RPF inet6 configuration to interface ge-0/0/1.8
	Applying RPF inet6 configuration to interface ge-0/0/1.18
	Applying RPF inet6 configuration to interface ge-0/0/1.22
	Apply RPF to all interfaces: 10 interface(s) affected: 6 inet, 8 inet6

	adamc@VirtualSRX> show system commit 
	0   2014-03-01 18:36:27 UTC by adamc via junoscript
	    Apply RPF to all interfaces: 10 interface(s) affected: 6 inet, 8 inet6

Remove RPF from all interfaces:  

	adamc@VirtualSRX> op rpf-tool mode remove 
	Removing RPF inet configuration from interface ge-0/0/1.1
	Removing RPF inet6 configuration from interface ge-0/0/1.2
	Removing RPF inet configuration from interface ge-0/0/1.3
	Removing RPF inet6 configuration from interface ge-0/0/1.3
	Removing RPF inet configuration from interface ge-0/0/1.4
	Removing RPF inet6 configuration from interface ge-0/0/1.4
	Removing RPF inet configuration from interface ge-0/0/1.5
	Removing RPF inet6 configuration from interface ge-0/0/1.6
	Removing RPF inet configuration from interface ge-0/0/1.7
	Removing RPF inet6 configuration from interface ge-0/0/1.7
	Removing RPF inet configuration from interface ge-0/0/1.8
	Removing RPF inet6 configuration from interface ge-0/0/1.8
	Removing RPF inet6 configuration from interface ge-0/0/1.18
	Removing RPF inet6 configuration from interface ge-0/0/1.22
	Remove RPF from all interfaces: 10 interface(s) affected: 6 inet, 8 inet6

Scope operation to nominated interface:  

	adamc@VirtualSRX> op rpf-tool mode apply interface ge-0/0/1.1 
	Applying RPF inet configuration to interface ge-0/0/1.1
	Apply RPF to interface: ge-0/0/1.1: 1 inet, 0 inet6

	adamc@VirtualSRX> op rpf-tool mode apply interface ge-0/0/0.0  
	Interface ge-0/0/0.0 specifically excluded by local-policy-exclusions
	Apply RPF to interface: ge-0/0/0.0: 0 inet, 0 inet6
	No affected interfaces: configuration not applied

Enforce exclusions by including the text *{ no-rpf }* in the description

## Caveats

* Interfaces carrying multi-hop EBGP not currently detected.
* Interfaces carrying IBGP not detected, but typically these also
  run an IGP which is detected.
* Be aware of Juniper PR 873709: disabling RPF on an interface will
  break forwarding on other interfaces that share a common value for
  ifl index && 65536. Remedy: remove RPF and re-apply on affected interface

## Other documents:
- test configuration file from Juniper Firefly
- test outputs

