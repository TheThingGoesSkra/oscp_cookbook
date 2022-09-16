# Resources
# Reconnaissance
## Rechercher les utilisateurs connecté
dans powershell:
```powershell
qwinta
```
## Recherche de volume caché
These are popularly used to create disk snapshots to quickly
backup data inn order to recover from future data corruption or loss. 
```powershell
vssadmin list shadows
```
Si un tel volume est découvert il est possible d'y accéder en créant un lien symbolique.
```powershell
cmd /c mklink /d C:\VSS \\?\GLOBALROOT\Device\HarddiskVolumeShadowCopy1
```
Le volume sera alors accessible via C:\VSS.  
Certains fichier dans \Users\Administrator\AppData\Roaming\Microsoft\Credentials\ peuvent détenir des informations sensibles

# Exploitation (Gaining Access)
# Privilege Escalation
# Command & Control (Persistence/Maintaining Access)
# Clearing Tracks (usually not necessary in CTF’s, but good practice)


