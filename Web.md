# 1. WEB


# Reconnaissance
## Action à réaliser 
   1. Générer une feuille de note web
   2. Ajouter l'ip / nom de domaine dans /etc/hosts
   2. Lancer en parralèlle les actions de la rubrique "Scanning"
   3. Regarder tous les liens du site
   4. Noter tous les sous domaines
   5. Ajouter tous les sous domaines à /etc/hosts
   6. Si site https, regarder les infos du certificat, nottament la rubrique Issuer qui peut fournir des infos
   7. Relever tout potentiel nom d'utilisateur
   8. Relever le CMS à l'aide de wappalyzer (lien en resssource)
   9. Relever le langage utilisé à l'aide de wappalyzer (lien en resssource)
   10. Relever les services et versions éventuellements

# Scanning & Enumeration
## Scanning 
### Recherche de Vhost
*Prérequis: Dictionnaires SecList - https://github.com/danielmiessler/SecLists.git*   
<code>
gobuster dir -u http://@ip_cible -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
</code>

### Recherche de répertoires
<code> 
gobuster dir -u http://@ip_cible/dir -w /usr/share/SecLists/Discovery/Web-Content/raft-small-directories.txt
</code>

### Recherche de fichiers
<code>
gobuster dir -u http://@ip_cible -w /usr/share/SecLists/Discovery/Web-Content/raft-small-files.txt
</code>

### Fuzz d'un paramètre
<code>
wfuzz --hh=22 -c -w /usr/share/dirb/wordlists/big.txt http://$target_url/action.php?FUZZ=random
</code>
Options:  
–hh taille de la réponse (quand il y a une erreur)<br/>
-c (Output with colors)<br/>
-w (Wordlist)<br/>
FUZZ (FUZZ keyword will be replaced by the word from the wordlist)

## Enumeration
### Recherche d'entrées utilisateur
Parcourir le site à la recherche d'élément modifiables par l'utilisateur. Ces éléments peuvent être:
- Champs de saisi utilisateur( formulaire, champs de recherche ...)
- Url
- En tête HTTP

# Exploitation (Gaining Access)
## Recherche de vulnérabilités
Pour chaque entrée utilisateur rechercher les éventuelles vulnérabilités:
### Injection SQL

### Injection XSS

### Server Side Template Injection


# Privilege Escalation
# Command & Control (Persistence/Maintaining Access)
# Clearing Tracks (usually not necessary in CTF’s, but good practice)

# Resources
https://www.wappalyzer.com/apps 

# Note
Ajouter LFI