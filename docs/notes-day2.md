# Day 2 - Domain controller and domain corp.lab

## What I built
- Cloned the sysprepped template into DC01
- DC01: name DC01, static IP 10.10.10.10/24, gateway 10.10.10.1, DNS 127.0.0.1
- Installed AD DS role, created a new forest: corp.lab (NetBIOS CORP)
- DNS installed on the DC

## Verification
- Get-ADDomain: DNSRoot corp.lab, NetBIOS CORP
- Resolve-DnsName corp.lab -> 10.10.10.10
- Services ADWS, KDC, Netlogon, DNS all Running
- Internet still working (NAT via host)

## What I learned
- A domain controller is also the DNS server for its domain (points DNS to itself)
- Creating a forest = the first domain in a new Active Directory
- NetBIOS name is the short login name (CORP\user); DNS name is corp.lab
