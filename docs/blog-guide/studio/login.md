---
title: 登录与授权
parent: 写作台
nav_order: 1
---

# 登录与授权

写作台需要一枚 GitHub 令牌（Fine-grained Personal Access Token）。

## 怎么拿

1. GitHub → Settings → Developer settings → **Fine-grained tokens** → Generate new token
2. Repository access 只勾 `yen-chiu/yen-chiu.github.io`
3. Permissions → Repository permissions → **Contents: Read and write**
4. 生成后复制，粘进写作台首页的输入框

权限只给 `Contents`，别的都不用给。它只能读写这一个仓库的文件。

## 存在哪

存在浏览器本地（localStorage），只在你这一台设备上。
换设备、换浏览器，要重新贴一次。

## 会过期

Fine-grained token 最长 90 天。

过期时写作台会退回登录页，提示「令牌无效或已过期（401）」。
**本地草稿不会丢**——重新生成一个令牌贴进去，草稿还在。

## 几个注意事项

- 令牌等同于仓库的写权限，不要贴到聊天、截图、公开的地方
- 公用电脑用完点「退出」；退出会一并清掉本地草稿
- 如果是在网吧或者别人的手机上，用无痕窗口，关掉就没了
