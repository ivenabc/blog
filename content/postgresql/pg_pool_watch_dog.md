---
title: "PGpool-II Watch dod(看门狗)"
date: 2019-11-15
author: "Iven"
draft: true
weight: 101
---

看门狗可以实现高可用性，可以实现故障转移功能。watch dog 通过相互交换信息来协调多个Pgpool-II节点。
watch dog可以对远程结点和本地结点进行监控，如果监控返回错误则认定Pgpool结点故障。watch dog有两张模式心跳和查询模式



