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

## Elasticsearch

### Multi-match (match multiple fields) query with field boosts and a date range filter, with results limited to first 30 hits, using curl
Elasticsearch is running locally or is piped to `localhost:9200`.
```bash
curl --location --request GET 'http://localhost:9200/index_name/_search' \
--header 'Content-Type: application/json' \
--data-raw '{
    "from": 0,
    "size": 30,
    "query": {
        "bool": {
            "must": [
                {
                    "multi_match": {
                        "query": "[query string]",
                        "fields": [
                            "a_field^4",
                            "another_field^2",
                            "a_field_with_subfield.subfield^0.5",
                            "array_field"
                        ]
                    }
                },
                {
                    "bool": {
                        "should": [
                            {
                                "bool": {
                                    "must": [
                                        {
                                            "range": {
                                                "some_period.startDate": { "lt": "2021-08-01" }
                                            }
                                        },
                                        {
                                            "range": {
                                                "some_period.endDate": { "gt": "2020-08-01" }
                                            }
                                        }
                                    ]
                                }
                            }
                        ]
                    }
                }
            ]
        }

    }
}'
```

### Pipe all traffic to local Elasticsearch to remote Elasticsearch
```bash
ssh -L 9200:127.0.0.1:9200 [username]@[hostname]
```

### Pipe all traffic to localhost:9201 to remote Elasticsearch
```bash
ssh -L 9201:localhost:9200 [username]@[hostname] -N
```

### Related to earlier, once pipe is open, copy entire index to local Elasticsearch instance (reindex)
Requires that the docker container that is running Elasticsearch is exposed at port 9201
```bash
curl -H "Content-Type: application/json" \
-d '{"source": { "remote": { "host": "http://host.docker.internal:9201"}, "index": "index_name" }, "dest": { "index": "index_name"}}' http://127.0.0.1:9200/_reindex
```
