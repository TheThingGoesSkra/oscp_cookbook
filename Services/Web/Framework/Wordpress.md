# Scanning & Enumeration
# Enumération de page
wpscan permet de d'énumérer les pages, à la recherche de vulnérabilités.
```console
wpscan --url https://phoenix.htb --clear-cache --enumerate at,ap --disable-tls-
checks --random-user-agent --plugins-detection passive --plugins-version-
detection passive
```
Pour chaque plugin installés il faut regardé, s'ils ont des vulnérabilités connues.
```console
searchsploit nom_plugin
```
ou par recherche web

## Recherche d'utilisateur
```console
wpscan --ulr https://phoenix.htb -e u --disable-tls-checks
```
L'id 1 est celui de l'administrateur wordpress.

# Exploitation (Gaining Access)
# Privilege Escalation