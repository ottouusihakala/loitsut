# Useful Magic "Spells"

## Git

### Remove local branches that have been merged and deleted in remote
```bash
git branch -vv | grep ': gone]'|  grep -v "\*" | awk '{ print $1; }' | xargs -r git branch -d
```

## OpenSSL

### Generate certificate request
```bash
openssl req -nodes -sha256 -newkey rsa:4096 -keyout ./[key file name].key -nodes -out ./[your certificate name].csr -subj \
"/C=FI/ST=[State or County]/L=[City]/O=[Organisation]/OU=[Owner or responsible person/entity]/CN=[hostname]/emailAddress=[Contact email address]"
```

## Curl related

### Remove new lines from GraphQL query, send it as part of a POST request to a GraphQL endpoint requiring authentication, process the response to a prettified format and read in less
```bash
tr -d '\n' < [GraphQL file name].gql | sudo curl  -H 'content-type: application/json'  --cert /path/to/client/certificate.crt --key /path/to/client/certificate.key https://[hostname]/graphql/endpoint   -d @- |json_pp|less
```

### Same but write into a file

```bash
tr -d '\n' < [GraphQL file name].gql | sudo curl  -H 'content-type: application/json'  --cert /path/to/client/certificate.crt --key /path/to/client/certificate.key https://[hostname]/graphql/endpoint   -d @- |json_pp > [file name].json
```

## Log reading

### Search Docker container logs for lines with specific content, select a field out of it, show only unique lines and sort
Example content line, an API endpoint string: `'s/.&searchText=([^&]+)./\1/'`
```bash
sudo docker logs [container name]|grep endpoint/path|sed -E '[content]'|awk '{print $[field index, ex. 7]}'|uniq -c|sort -n
```

