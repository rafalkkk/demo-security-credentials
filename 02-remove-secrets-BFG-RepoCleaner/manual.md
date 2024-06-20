## Required
- A repo on GitHub (can be this one)
- VM with Java and BCG downloaded installed
- (https://rtyley.github.io/bfg-repo-cleaner/)

## Steps
- Create a few new commits and push them one by one. They should contain:
```
username = 'topsecretuser'
password = 'ImEatingHawaiPizza'
site = 'TESTSITE'
```
- PANIC! Correct entries in the current version, commit it and push. (**BFG is not changing the HEAD**)
- PANIC! The secrets are also in the past commits! We need to remove them!
- Clone the repo using –-mirror option (the project files are not present in the mirrored repo)
```
git clone https://github.com/TESTUSERkkk/security-credentials-demo.git –mirror
```
- Prepare password file containing strings that should be removed. Put there the userName and password
```
topsecretuser==>TESTUSER
ImEatingHawaiPizza==>TESTPASS
TESTSITE==>TESTSITE
```
- Run the command:
```
java -jar C:\tmp\bfg-1.14.0.jar --replace-text c:\tmp\passwords.txt tmp-demo-01.git
```
- (Optional) To validate the change use commands like below:
```
git show HEAD
git show HEAD~1
```
- Approve the change by running:
```
git reflog expire --expire=now --all && git gc --prune=now –aggressive
```
- Push the code
```
Git push
```
- Refresh the page on GitHub and show that the userName and password disappeared from the repo history
- (Optional) show logs of BFG

## Links
- (https://dev.to/toureholder/removing-sensitive-data-from-your-git-history-with-bfg-4ni4)
- (https://til.simonwillison.net/git/rewrite-repo-remove-secrets)
