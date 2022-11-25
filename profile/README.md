This organization maintains repositories of Nimble packages based on certain modules from the Nim standard library with the associated original commit history. For the reasoning behind this, see https://github.com/nim-lang/RFCs/issues/473.

<details>
<summary>Info about applying commit history</summary>

## How to create new repo from Nim module with commit history

Based on [this stackoverflow](https://stackoverflow.com/a/11426261/10633874)

Taking asyncftpclient as an example:

```bash
git clone https://github.com/nim-lang/Nim # cannot be shallow!
mkdir asyncftpclient
cd asyncftpclient
git init
cd ../nim
# create patch file:
git log --pretty=email --patch-with-stat --reverse --full-index --binary -m --first-parent -- lib/pure/asyncftpclient.nim > ../asyncftpclient/latest.patch
cd ../asyncftpclient
# replace lib/pure/ path with src/ path in the patch file:
sed -i 's=lib/pure/=src/=g' latest.patch
# apply patch:
git am -3 --committer-date-is-author-date < latest.patch
# remove patch file:
rm latest.patch
```

## Apply new commits to module from Nim to repo

We do the same thing, except with a revision range for the `git log` command, and also we may have to deal with merge conflicts. Assume the last commit we added from Nim has commit hash `1234abcd`.

```bash
cd nim # also consider running git fetch --unshallow
# create patch file for commits after 1234abcd:
git log --pretty=email --patch-with-stat --reverse --full-index --binary -m --first-parent -- lib/pure/asyncftpclient.nim 1234abcd..HEAD > ../asyncftpclient/latest.patch
cd ../asyncftpclient
# replace lib/pure/ path with src/ path in the patch file:
sed -i 's=lib/pure/=src/=g' latest.patch
# apply patch:
git am -3 --committer-date-is-author-date < latest.patch
# remove patch file:
rm latest.patch
```

Merge conflicts can happen in the "apply patch" stage. The `-3` option considers a 3-way merge, but this might not be possible for all commits. In such cases, manually adapt as much of the commit that is failing to merge, and then do `git add` and `git am --continue`. The commit will still be credited to the original commit authors.

</details>
