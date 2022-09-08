Les En-têtes HTTP fournissent beaucoup d'informations sur la cible et représente une surface d'attaque qui n'est pas à négliger.

Source: https://book.hacktricks.xyz/network-services-pentesting/pentesting-web/special-http-headers 

# En tête de sécurié
## CSP - Content security Policy

## X-Content-Type-Options
Si l'option nosnif est configurée, le navigateur client n'acceptera que le contenu respectant le type de donnée spécifié dans l'en-tête `Content-Type`.
Cela permet de limiter les effets d'injection xss

## X-Frame-Options
Permet de limiter l'intégration au sein d'iframe d'autres sites. Cette pratique permet de limiter les attaques de type clickjacking.
Il est recommandé de placer cette en-tête sur l'ensemble des documents.

## CORP - Cross-Origin Resource Policy 
Permet de limiter la liste des serveurs ayant accès aux ressources du site.

## COOP - Cross-Origin Opener Policy
Permet de spécifier qui peut ouvrir en pop up la ressource. 

## Cookie same site:
https://developer.mozilla.org/fr/docs/Web/HTTP/Headers/Set-Cookie/SameSite  
L'attribut SameSite de l'en-tête de réponse HTTP Set-Cookie vous permet de déclarer si vos cookies doivent être restreints au site visité, à des tiers, ou à des sous-domaines du site actuel.  
- Lax :  
Les cookies sont transférables depuis le site actuel vers des sites de niveaux inférieurs et seront envoyés lors de requêtes GET initialisées par des sites tiers. C'est la valeur par défaut des navigateurs les plus récents.

- Strict :  
Les cookies ne seront envoyés qu'avec les requêtes effectuées sur le domaine de même niveau, et ne seront pas envoyées sur les requêtes provenant de sites tiers.

# En-têtes à surveiller
## Redéfinir l'ip source
```http
X-Originating-IP: 127.0.0.1
X-Forwarded-For: 127.0.0.1
X-Forwarded: 127.0.0.1
Forwarded-For: 127.0.0.1
X-Forwarded-Host: 127.0.0.1
X-Remote-IP: 127.0.0.1
X-Remote-Addr: 127.0.0.1
X-ProxyUser-Ip: 127.0.0.1
X-Original-URL: 127.0.0.1
Client-IP: 127.0.0.1
X-Client-IP: 127.0.0.1
X-Host: 127.0.0.1
True-Client-IP: 127.0.0.1
Cluster-Client-IP: 127.0.0.1
X-ProxyUser-Ip: 127.0.0.1
Via: 1.0 fred, 1.1 127.0.0.1
Connection: close, X-Forwarded-For 
```

## Redéfinir la location
```http
X-Original-URL: /admin/console
X-Rewrite-URL: /admin/console
```

## HTTP Request Smuggling
```language
Content-Length: 30
Transfer-Encoding: chunked
```


## Cache Headers
- `X-Cache` in the response may have the value miss when the request wasn't cached and the value hit when it is cached
- `Cache-Control` indicates if a resource is being cached and when will be the next time the resource will be cached again: Cache-Control: public, max-age=1800
- `Vary` is often used in the response to indicate additional headers that are treated as part of the cache key even if they are normally unkeyed.
- `Age` defines the times in seconds the object has been in the proxy cache.
- `Server-Timing`: cdn-cache; desc=HIT also indicates that a resource was cached


## Server info
```http
Server: Apache/2.4.1 (Unix)
X-Powered-By: PHP/5.3.3
```

