# Scanning & Enumeration
## Scan de port ouvert
Utilisation de masscan pour la découverte de ports :  
<code>
masscan -p1-65535,U:1-65535 $ip_address --rate="1000" -e tun0
</code>

## Scan des services
En fonction des ports découverts avec massscan, récupérer les informations liés aux services.  
<code>
nmap -sV -p $ports --script="banner,vulners,default" @ip
</code>

## Récupération manuelle des bannières
<code> netcat $ad_ip $port </code>

## Amelioration du shell
- Si python n'est pas présent 
<code> script /dev/null -c bash</code>