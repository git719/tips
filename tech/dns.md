---
type: reference
---
## DNS
Useful DNS bits.

### Speed Up Initial Record Creation
To speed this up initial record creation within a zone, update the **negative caching TTL** value in the SOA record for that zone:

```bash
Format:
    [authority-domain] [domain-of-zone-admin]
    [zone-serial-number] [refresh-time] [retry-time]
    [expire-time] [negative caching TTL]
Example:
    ns.example.net. hostmaster.example.com. 1
    7200 900 1209600 86400
```

### Google DNS Flush cache
<https://developers.google.com/speed/public-dns/cache>

### Improving DNS Perfomance
- Use a DNS service that uses Anycast for their servers
- Make sure the DNS provider has servers distributed globally (and that perform well)
- Try to avoid long CNAME chains (try to avoid them at all if possible)
- Use a long Time To Live (TTL) on your records so they can be cached by the ISPs and users

### NextDNS on OPNsense
Use the [NextDNS CLI client (DoH Proxy) installer](https://github.com/nextdns/nextdns/wiki/Installer):

```bash
sh -c 'sh -c "$(curl -sL https://nextdns.io/install)"'
```
and follow the prompts.

Ensure all other DNS resolvers are **NOT Enabled**

Uncheck **DHCP DNS Server Override**

Add additional interfaces to listen on:
```bash
nextdns stop 
vi /usr/local/etc/nextdns.conf  # Adjust lines
setup-router false
listen localhost:53
listen 10.10.1.1:53  # Sample 3 additions
listen 10.10.2.1:53
listen 10.10.3.1:53
nextdns start
```

### PowerShell DNS Command Commands
```bash
# Show A record
Get-DnsServerResourceRecord -ComputerName ns1.mydomain.com -ZoneName mydomain.com -Name myhost

# Show PTR
Get-DnsServerResourceRecord -ComputerName ns1.mydomain.com -ZoneName "10.in-addr.arpa" -Node "8.8.10" -RRType "PTR"
Get-DnsServerResourceRecord -ComputerName ns1.mydomain.com -ZoneName "10.10.in-addr.arpa" -Node "8.8" -RRType "PTR"
Get-DnsServerResourceRecord -ComputerName ns1.mydomain.com -ZoneName "8.10.10.in-addr.arpa" -Node "8" -RRType "PTR"

# Show TXT
Get-DnsServerResourceRecord -ComputerName ns1.mydomain.com -ZoneName "mydomain.com" -RRType "TXT" -Name _acme-challenge

# Create A
Add-DnsServerResourceRecordA -ComputerName ns1.mydomain.com -TimeToLive 00:05:00 -ZoneName mydomain.com -Name host1 -IPv4Address 10.10.8.8 [-CreatePtr] (Creates it in the 10.in-addr.arpa zone)
  Use -Name "." to update the apex domain

# CREATE PTR
Add-DnsServerResourceRecordPtr -ComputerName ns1.mydomain.com -TimeToLive 00:05:00 -ZoneName "10.in-addr.arpa" -Name "8" -PtrDomainName "host1.mydomain.com"

# CREATE CNAME
Add-DnsServerResourceRecordCName -ComputerName ns1.mydomain.com -TimeToLive 00:05:00 -ZoneName mydomain.com -Name host1 -HostNameAlias "srv1.contoso.com"

# CREATE TXT
Add-DnsServerResourceRecord -Txt -ComputerName ns1.mydomain.com -TimeToLive 00:05:00 -ZoneName mydomain.com -Name "." -DescriptiveText "value=SomeText"

# REMOVE A 
Remove-DnsServerResourceRecord -ComputerName ns1.mydomain.com -ZoneName "mydomain.com" -RRType "A" -Name "host1" -Force

# REMOVE CNAME
Remove-DnsServerResourceRecord -ComputerName ns1.mydomain.com -ZoneName "mydomain.com" -RRType "CNAME" -Name "host1" -Force

# REMOVE PRT
Remove-DnsServerResourceRecord -ComputerName ns1.mydomain.com -ZoneName "10.in-addr.arpa" -RRType "PTR" -Name "8.8.10" -Force
# You should also remove other PTRs if they exist

# REMOVE TXT
Remove-DnsServerResourceRecord -ComputerName ns1.mydomain.com -ZoneName "mydomain.com" -RRType "TXT" -Name "."
```

### PowerShell DNS Scripts
Four PowerShell scripts verify, create, delete, and upsert A and PTR records in zones hosted on Active Directory DNS servers. Each reads a `FQDN,IP` CSV file and needs DNS admin rights. They live in the scripts repo:

- [dns_chk.ps1](https://github.com/queone/scripts/blob/main/dns_chk.ps1): verify that each A and PTR record matches the file
- [dns_add.ps1](https://github.com/queone/scripts/blob/main/dns_add.ps1): create the A and PTR records
- [dns_del.ps1](https://github.com/queone/scripts/blob/main/dns_del.ps1): delete the A record and any PTR records
- [dns_upsert.ps1](https://github.com/queone/scripts/blob/main/dns_upsert.ps1): delete then recreate the records
