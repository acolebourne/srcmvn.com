---
layout: post
title: "Removing One File from a Git Commit"
date: 2012-11-07 11:47
tags: git

---
As often happens, I committed `composer.lock` when I had not actually
intended to do so. [Igor](twitter.com/igorwesome) broke out the
following *after* I went through a whole lot of stupid things on my
own.

Thanks Igor. :)

    git reset HEAD~1 filename && git commit --amend