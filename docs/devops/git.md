## Remove file from History

```bash
git filter-branch --force --index-filter "git rm --cached --ignore-unmatch private.key" --prune-empty --tag-name-filter cat -- --all

git push origin --force --all (master branch needs to be unprotected)

git push origin --force --tags
```