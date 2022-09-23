# Ressources

[PowerSploit](https://github.com/PowerShellMafia/PowerSploit) <- listes d'outil contre une cible windows
-> [powerview](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
[Documentation powersploit0](https://powersploit.readthedocs.io/)

# Info
## SAM
Base de données des utilisateurs locaux appelée **SAM** (_Security Accounts Manager_). Il y a la liste des **noms d’utilisateurs**, et le **hash de leur mot de passe**, appelé **hash NT**. Ce hash donne les même droits que le mdp.

# Enumeration
## SMB enumération
```bash
enum4linux -a <ip>
```


# Exploitation (Gaining Access)
_Ressource_ : [PowerSploit](https://github.com/PowerShellMafia/PowerSploit) <- listes d'outil contre une cible windows
-> [powerview](https://github.com/PowerShellMafia/PowerSploit/tree/master/Recon)
[Documentation powersploit0](https://powersploit.readthedocs.io/)

## Accès à un service winrm
```bash
gem install evil-winrm
evil-winrm -i 10.10.10.233 -u <username> -p <password>
```
# Reconnaissance
## Afficher les groupes d'un utilisateur
```powershell
net user <utilisateur>
```
Info sur les groupes https://learn.microsoft.com/en-us/windows-server/identity/ad-ds/manage/understand-security-groups

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

## Winpeas
[winpeas.exe](https://github.com/carlospolop/PEASS-ng/releases/download/20220529/winPEASx64.exe)

# Privilege Escalation
# Command & Control (Persistence/Maintaining Access)
# Clearing Tracks (usually not necessary in CTF’s, but good practice)


