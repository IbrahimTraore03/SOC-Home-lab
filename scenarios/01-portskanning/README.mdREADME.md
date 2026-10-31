# Scenario 1 – Portskanning

MITRE ATT&CK: T1046 Network Service Discovery
Datum: 31 oktober 2026
Status: Del 1 klar (attack och analys). Del 2 (åtgärd och egen regel) pågår.

## Sammanfattning

Kali (10.10.10.30) portskannade Windows-maskinen (10.10.10.20) med Nmap. Attacken gav inga larm i Wazuh och inga händelser i Windows egna loggar. Luckan sitter i endpointens loggning, inte i SIEM-systemet.

## Attack

```bash
sudo nmap -sS -T4 10.10.10.20 -oN ~/scenarios/01/nmap.txt
```

En SYN-skanning mot de 1 000 vanligaste portarna. Det är ofta en angripares första steg för att hitta tjänster att angripa.

## Detektion

I Wazuh gav en sökning på 10.10.10.30 under de senaste 15 minuterna inga träffar.

![Ingen träff i Wazuh](wazuh-tomt.png)

På Windows-maskinen fanns inga brandväggshändelser (Event ID 5152 och 5157) under samma period:

```powershell
Get-WinEvent -FilterHashtable @{LogName='Security'; Id=5152,5157; StartTime=(Get-Date).AddMinutes(-30)} -ErrorAction SilentlyContinue | Measure-Object
# Count: 0
```

## Analys

Windows-brandväggen blockerade skanningen, men Windows loggar inte blockerad trafik som standard. Eftersom loggen aldrig skapades kunde Wazuh inte se något. En angripare kan alltså kartlägga en standardinstallerad Windows-maskin utan att det märks.

Inga larm betyder inte att ingen attack har skett.

## Åtgärd

Loggning av blockerad trafik slås på med auditpol, endast failure för att undvika brus:

```powershell
auditpol /set /subcategory:"Filtering Platform Packet Drop" /failure:enable
auditpol /set /subcategory:"Filtering Platform Connection" /failure:enable
```

Därefter ska Wazuh-agenten skicka händelserna vidare och en egen regel larma när en IP-adress träffar många portar på kort tid. Resultatet läggs till här när del 2 är klar.
