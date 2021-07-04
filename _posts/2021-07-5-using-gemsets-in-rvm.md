---
layout: post
title: Creating and using Gemsets in RVM
# subtitle: What I usually do when I need to get some specific commits into my branch
tags: [rvm, ruby, jruby]
readtime: true
---

I've started to use RVM Gemsets in every project I work on, it has a lot of advantages, one of them is:

If you are working on multiple projects with different ruby versions so it's very helpful here because once you change the directory to this folder it changes the ruby version and use the specified Gemset by just doing simple `cd` command.

Here is how to create `.ruby-version` and `.ruby-gemset` files in your project
```bash
rvm --create --ruby-version use <RUBY | JRUBY VERSION>@<GEMSET-NAME>
```

Example
```bash
rvm --create --ruby-version use ruby-2.7.0@my-project-gemset
```