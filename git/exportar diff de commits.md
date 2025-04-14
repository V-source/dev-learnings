```bash

git rev-list --all | while read commit; do
  git show $commit > "diff_${commit:0:7}.patch"
done

```
