# SOC-hemmalabb – Wazuh, Sysmon & Kali

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
- [x] Fas 3 – Windows-maskinen ([setup/02-windows-sysmon-agent.md](setup/02-windows-sysmon-agent.md))
- [x] Fas 4 – Kali ([setup/03-kali.md](setup/03-kali.md))
- [ ] Fas 5 – Attackera och upptäck
  - [x] Scenario 1 – Portskanning ([rapport](scenarios/01-portskanning/README.md), egen regel [100100](detections/100100-portscan.xml))
  - [ ] Scenario 2 – Brute force
- [ ] Fas 6 – Dokumentation

## Nästa gång

Starta labbet i ordningen wazuh-server, Windows11-victim, Kali-attacker och vänta ett par minuter efter servern. Utgångslägen är windows-baseline-v3 och wazuh-rule-portscan. Nästa steg är scenario 2, brute force mot inloggningen.

Wazuh-servern har visat varningar om att processorn inte fick CPU-tid (rcu_preempt stalls) när alla tre VM:ar körs samtidigt. Om det händer igen stänger jag av side channel mitigations för servern.
