---
layout: post
title: ubunut
comments: true
permalink: "ubuntu-commands"
---

__fix apt-get update “Hash Sum mismatch”__
sudo rm -rf /var/lib/apt/lists/*
sudo apt-get update
sudo apt-get cleand

__rsync__
#push
rsync ssl*uni* amdeb:/home/shaobo