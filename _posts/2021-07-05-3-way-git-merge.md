---
layout: post
title: 3-way git merge 
subtitle: What I usually do when I need to get some specific commits into my branch
tags: [git]
readtime: true
---

Some times you need to merge some specific commits into your branch

so here is what I usually do when I need this.

1. creating a file that has all the info about this hash (patch)
```bash
git format-patch -1 <commit_hash>
```
2. check the changes applying
```bash
git apply --check <changes.patch>
```
If the result of git apply returns nothing then you can apply it.
3. apply the commit
```bash
git am -s <changes.patch>
```
But If there are errors, then you have to apply it using 3-way merge
So instead of doing the previous step you need to do the following
```bash
git am -3 <changes.patch>
```
1. Check the log conflicts or search for it.
2. fix the conflicts and add them
```bash
git add <changed_fix_conflict_files>
```
3. continue the applying patch
```bash
git am --continue
```
