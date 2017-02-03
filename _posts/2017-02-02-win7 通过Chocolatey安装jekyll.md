---
layout: post
title:  "win7 通过Chocolatey安装jekyll"
date:   2017-02-02 12:06:08 +0800
categories: jekyll
---

* content
{:toc}
本文介绍win7下通过Chocolatey安装jekyll

### 安装
powershell 3.0 内部安装Chocolatey需要先安装powershell 3.0
- 下载  [Windows6.1-KB2506143-x64.msu](https://www.microsoft.com/en-us/download/confirmation.aspx?id=34595) 安装powershell 3.0
- 通过powershell 3.0 安装 Chocolatey
- 通过Chocolatey安装ruby
- 通过ruby的gem安装bundler
```
C:\>gem install --local C:\rubygems-update-2.6.7.gem
C:\>update_rubygems --no-ri --no-rdoc
```

### refrence
>[SSL CERTIFICATE UPDATES](http://guides.rubygems.org/ssl-certificate-update)
