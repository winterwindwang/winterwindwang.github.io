---
layout: post
title: ubuntu配置gitlab的ssh
category: 技术
keywords: python, torch, yolo
tags: technical details
---

# Ubuntu中配置gitlab私域的ssh

1. 生成ssh连接的公私钥

   ```cmd
   ssh-keygen -t rsa -b 2048 -C "comment"
   # 将会输出以下为文本：
   Generating public/private rsa key pair.
   Enter file in which to save the key (/home/user/.ssh/id_rsa):
   # 注：如果生成多个密钥对，可以更改密钥的保存地址，如：/home/user/custom_file/id_rsa
   ```

2. 在保存密钥地址找到id_rsa.pub，并将其中内容复制到gitlab中的ssh密钥中。

3. 生成`~/.ssh/config`文件，并且配置私域地址

   ```cmd
   touch ~/.ssh/config  # 若无config文件，则先生成，若有则直接添加以下内容
   # User1 Account Identity
   Host gitlab.idrl.ac.cn
   Hostname gitlab.idrl.ac.cn
   User git
   Identity File ~/.ssh/id_rsa
   
   # User2 Account Identity
   Host gitlab.yourdomain.com
   Hostname gitlab.yourdomain.com
   User git
   Identity File /home/user/custom_file/id_rsa
   
   ```

