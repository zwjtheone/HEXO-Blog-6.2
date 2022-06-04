---
layout: post
title: CentOS安装Traivs步骤
date: 2022-05-30 12:0:00
tags:
- CentOS
- Traivs
---

[https://cloud.tencent.com/developer/article/1626159](https://cloud.tencent.com/developer/article/1626159)
```bash
sudo dnf install ruby
ruby --version
gem sources -l
yum install ruby-devel 
sudo dnf install redhat-rpm-config
gem install travis
```