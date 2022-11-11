# Useful Magic "Spells"

## Git

### Remove local branches that have been merged and deleted in remote
```
git branch -vv | grep ': gone]'|  grep -v "\*" | awk '{ print $1; }' | xargs -r git branch -d
```

## OpenSSL

### Generate certificate request
```
openssl req -nodes -sha256 -newkey rsa:4096 -keyout ./[key file name].key -nodes -out ./[your certificate name].csr -subj \
"/C=FI/ST=[State or County]/L=[City]/O=[Organisation]/OU=[Owner or responsible person/entity]/CN=[hostname]/emailAddress=[Contact email address]"
```
