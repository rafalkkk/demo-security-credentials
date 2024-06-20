## Required: 
----------------------------
- access to azure storage account
  - Create a storage account & copy the key
- access to empty repo with with at least initial commit (can be this one)
- configured git:
  - Generate a PAT if needed
  - Configure environment
``` bash
git config user.name ...
git config user.email ...
```

## Steps:
----------------------------
```bash
git clone https://github.com/rafalkkk/security-credentials-demo.git

# Add a demo.py file to the project , add & commit (initial commit, if file was already commited this step can be skipped):
Git add --all
Git commit –m  “adding file”

# Add a secret to the file, add & commit:
git add --all
git commit –m  “adding secret”

# Add something more (ie container) to a demo.py, add & commit
git add --all
git commit –m  “adding secret”

# Push the changes - it will fail
git push

# PANIC! Remove the secret, add  & commit
git add --all
git commit –m  “removing secret”

# Push the changes - it will fail again
git push

# we have error because the secret is still in commit history
git log –oneline

# Lets squash the recent 3 commits (remove of secret + add name + remove secret)
git rebase -i HEAD~3
```
In editor change the first lines:
```
 pick ... Add secret
 squash ... Add name
 squash ...  Remove secret
``` 
Save & Close. A new editor opens, for commit message. Enter something and save.
Run push again - it should suceed.
