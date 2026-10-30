# 03 – Kali-maskinen

Angriparen är Kali Linux 2026.2, installerad från installer-ISO med 2 vCPU, 4 GB RAM och 40 GB disk. Maskinen har två nätverkskort: NAT som bara användes under installation och uppdatering, och LAB med fast IP 10.10.10.30. Efter uppdateringen kopplades NAT-kortet ur så att Kali bara når labbnätet. Då kan en felskriven IP-adress i en skanning aldrig träffa något utanför labbet.

Verktyg för attackscenarierna är Nmap 7.991 och Hydra 9.7, som ingår i Kalis standardinstallation.

## Nätverk

Den fasta adressen sattes med NetworkManager. never-default gör att LAB-kortet aldrig blir väg ut mot internet.

```bash
sudo nmcli con add type ethernet ifname eth1 con-name LAB ipv4.method manual ipv4.addresses 10.10.10.30/24 ipv4.never-default yes ipv6.method disabled
sudo nmcli con up LAB
```

På Windows-maskinen öppnades ping endast från labbnätet:

```powershell
New-NetFirewallRule -DisplayName "Lab ICMP" -Protocol ICMPv4 -IcmpType 8 -RemoteAddress 10.10.10.0/24 -Action Allow
```

## Isoleringstest

Från Kali svarar Wazuh-servern och Windows-maskinen, medan internet, hemroutern och värddatorns riktiga adress inte går att nå. Labbet är alltså isolerat från hemnätverket.

Snapshot efter den här fasen: kali-clean-isolated.

## Problem och lösningar

- Ping till Windows gav först Destination Host Unreachable. Windows-VM:en var pausad. Det felmeddelandet betyder att ingen maskin svarar på adressen, till skillnad från en brandvägg som bara tyst släpper paketen.
- Därefter tidsgränsade pingen. Det var Windows-brandväggen, som löstes med en ICMP-regel begränsad till labbnätet.
- Kopiera och klistra in slutade fungera i Kali efter skärmlåset. Lärdom: i Linux-terminaler klistrar man in med Ctrl+Shift+V.
