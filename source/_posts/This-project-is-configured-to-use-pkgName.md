---
title: This project is configured to use pkgName
date: 2023-04-11 22:17:18
tags:
---


在用pnpm install时报错`This project is configured to use pkgName`
删除用户根目录package.json，因为里面包含
```json
{
 "packageManager": "yarn@..."
}
```
