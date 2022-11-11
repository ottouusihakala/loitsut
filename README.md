# Useful Magic "Spells"

## Git

### Remove local branches that have been merged and deleted in remote
```
git branch -vv | grep ': gone]'|  grep -v "\*" | awk '{ print $1; }' | xargs -r git branch -d
```