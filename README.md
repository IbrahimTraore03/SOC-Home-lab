# SOC-hemmalabb – Wazuh, Sysmon & Kali

Har min AI som skriver om det jag gör för att bli mer effektiv med tid. 

## Sammanfattning
Ett eget mini-SOC: Kali angriper en Windows-maskin, loggarna samlas i Wazuh (SIEM) och attackerna upptäcks och analyseras. *(Under uppbyggnad.)*

## Arkitektur
Tre VM:ar i ett isolerat host-only-nät (VMnet2, 10.10.10.0/24) i VMware Workstation Pro.

| Maskin | Roll | IP |
|---|---|---|
| Värddator | Webbläsare till dashboarden | 10.10.10.1 |
| wazuh-server | SIEM (Ubuntu 24.04 + Wazuh 4.14) | 10.10.10.10 |
| Windows 11 | Offer (Sysmon + Wazuh-agent) | 10.10.10.20 |
| Kali | Angripare | 10.10.10.30 |

## Verktyg
VMware Workstation Pro, Wazuh, Sysmon, Kali Linux, Atomic Red Team.

## Status
- [x] Fas 1 – Förberedelser
- [x] Fas 2 – SIEM-servern ([setup/01-wazuh-server.md](setup/01-wazuh-server.md))
- [ ] Fas 3 – Windows-maskinen
- [ ] Fas 4 – Kali
- [ ] Fas 5 – Attackera och upptäck
- [ ] Fas 6 – Dokumentation
