## Required
- A repo on GitHub (can be this one)
- VM with Java and BCG downloaded installed
- https://rtyley.github.io/bfg-repo-cleaner/

## Steps
- Create a few new commits and push them one by one. They should contain
  (avoid using exactly the same names, as they will be removed also from that file):
```
username = 'rafal-user'
password = 'Im_Eating_Hawaiian_Pizza'
site = 'localhost'
```
- PANIC! **Correct entries in the current version, commit it and push. (BFG is not changing the HEAD**)
- PANIC! The secrets are also in the past commits! We need to remove them!
- Clone the repo using –-mirror option (the project files are not present in the mirrored repo)
```
git clone https://github.com/rafalkkk/security-credentials-demo.git –-mirror
```
- Prepare password file containing strings that should be removed. Put there the userName and password
```
rafal-user==>REMOVED
ImEatingHawaiPizza==>REMOVED
localhost==>REMOVED
```
- Run the command:
```
java -jar .\bfg-1.14.0.jar --replace-text .\passwords.txt security-credentials-demo.git
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
git push
```
- Refresh the page on GitHub and show that the userName and password disappeared from the repo history
- (Optional) show logs of BFG

## Links
- https://dev.to/toureholder/removing-sensitive-data-from-your-git-history-with-bfg-4ni4
- https://til.simonwillison.net/git/rewrite-repo-remove-secrets
