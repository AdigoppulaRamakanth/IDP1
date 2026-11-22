# Day 1 - Host setup and Windows Server template

## What I built
- Private lab network "labnet" in virt-manager/KVM: 10.10.10.0/24, NAT, no DHCP, gateway 10.10.10.1
- GitHub repo with .gitignore that blocks ISOs, VM disks and secrets
- Windows Server 2022 template VM: 2 vCPU, 3 GB RAM, 40 GB SATA disk, e1000e NIC, BIOS
- SPICE guest tools (copy/paste), Paris time zone, all Windows updates
- Internal snapshot "before-sysprep", then sysprep /generalize /oobe /shutdown

## Problem I solved: VM had no internet
| Test | Result |
| --- | --- |
| ipconfig in VM | IP 10.10.10.99 correct |
| ping 10.10.10.1 | Worked after full VM shutdown/start (network restart had unplugged the VM NIC) |
| ping 1.1.1.1 from Fedora | Worked |
| ip_forward / firewalld zones | OK |
| systemctl is-active docker | active |

Root cause: Docker sets the iptables FORWARD policy to DROP, which drops KVM NAT traffic.
Fix: allowed virbr10 traffic in the DOCKER-USER chain:
    sudo iptables -I DOCKER-USER -i virbr10 -j ACCEPT
    sudo iptables -I DOCKER-USER -o virbr10 -m conntrack --ctstate RELATED,ESTABLISHED -j ACCEPT

## What I learned
- How a NAT virtual network gives VMs internet through the host
- Why servers and domain controllers need static IPs
- Debugging network problems hop by hop: VM -> gateway -> internet -> DNS
- Why Kerberos needs correct time on every machine
- Why sysprep is needed before cloning Windows (unique SID per machine)
