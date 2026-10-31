# 02 – Windows-maskinen

Offret är Windows 11 Enterprise (utvärderingsversion) med 2 vCPU, 4 GB RAM, 60 GB disk och virtuellt TPM. Maskinen har två nätverkskort: NAT för internet och LAB med fast IP 10.10.10.20 utan gateway. Nätet var urkopplat under installationen så att jag kunde skapa ett lokalt konto i stället för ett Microsoft-konto. VMware Tools är installerat för urklipp och tidssynk.

## Sysmon

Sysmon 15.22 från Sysinternals med Olaf Hartongs sysmon-modular (Balanced), som taggar händelser med MITRE ATT&CK. Jag kontrollerade att Sysmon64.exe var signerad av Microsoft innan installationen.

```powershell
.\Sysmon64.exe -accepteula -i sysmonconfig.xml
```

## Wazuh-agent

Agent 4.14.9, samma version som servern, registrerad som windows11-victim mot 10.10.10.10. Agenten läser bara Application, Security och System som standard, så jag lade till Sysmon- och PowerShell-loggen i ossec.conf:

```xml
<localfile>
  <location>Microsoft-Windows-Sysmon/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
<localfile>
  <location>Microsoft-Windows-PowerShell/Operational</location>
  <log_format>eventchannel</log_format>
</localfile>
```

## Loggning

Windows loggar inte allt som standard. Via gpedit slog jag på PowerShell Script Block Logging (4104) och kommandorad i processhändelser (4688). Resten slogs på med auditpol:

```powershell
auditpol /set /subcategory:"Process Creation" /success:enable
auditpol /set /subcategory:"Other Object Access Events" /success:enable /failure:enable
auditpol /set /subcategory:"User Account Management" /success:enable /failure:enable
auditpol /set /subcategory:"Security Group Management" /success:enable
auditpol /set /subcategory:"Logon" /success:enable /failure:enable
```

Snapshot efter den här fasen: windows-baseline-logging. Jag återställer till den efter varje attackscenario.

## Problem och lösningar

- Klockan i VM:en gick åtta timmar fel. Löstes med tidssynk mot värden via VMware Tools. Rätt tid är avgörande för tidslinjer i incidentrapporter.
- Länken till sysmon-modular gav 404 eftersom konfigurationen har flyttats till GitHub Releases.
- Agenten hamnade först på värddatorn av misstag. Den avinstallerades direkt. Lärdom: kontrollera alltid vilken maskin prompten tillhör.
- En tyst msiexec-installation misslyckades utan felmeddelande. Löstes genom att köra den med -Wait och logg som administratör.
