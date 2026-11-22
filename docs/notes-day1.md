# Day 1 - Host setup and Windows Server template

## What I built
- Private lab network "labnet" in virt-manager/KVM: 10.10.10.0/24, NAT, no DHCP, gateway 10.10.10.1
- Windows Server 2022 template VM: 2 vCPU, 3 GB RAM, 40 GB SATA disk, e1000e NIC, BIOS
- SPICE guest tools (copy/paste), Paris time zone, all Windows updates

## Snapshots
| Snapshot | State |
| --- | --- |
| before-sysprep | Updated, SPICE installed, static IP - restore point |
| sysprepped | Generalized with sysprep /generalize /oobe /shutdown - clone only, never start |

## Problem I solved: VM had no internet
- VM reached the gateway (10.10.10.1) but not the internet.
- Checked hop by hop: VM IP OK, gateway OK, host internet OK, ip_forward=1, firewalld zones OK.
- Root cause: Docker on the host sets the iptables FORWARD policy to DROP, which drops KVM NAT traffic.
- Fix: allowed virbr10 traffic in the DOCKER-USER chain.

## Lesson learned the hard way
- Starting the template after sysprep consumes it (169.254.x.x address = sysprep erased network settings, which is expected).
- Fix: run sysprep again and never start the template - only clone it.

## What I learned
- How a NAT virtual network gives VMs internet through the host
- Why servers need static IPs
- Why Kerberos needs correct time on every machine
- Why sysprep is needed before cloning Windows (unique SID per machine)
