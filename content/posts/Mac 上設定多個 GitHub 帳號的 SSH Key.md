---
title: "Mac 上設定多個 GitHub 帳號的 SSH Key"
description: "在同一台電腦上使用多個 GitHub 帳號時，如何正確設定 SSH Key"
date: 2026-03-11T20:00:00+08:00
draft: false
tags:
  - Git
  - SSH
  - GitHub
categories:
  - Git
---

在同一台 Mac 上有多個 GitHub 帳號（例如工作用和個人用），push 時常常會遇到權限錯誤：

```
ERROR: Permission to xxx/repo.git denied to yyy.
```

這是因為 SSH agent 會優先送出第一把 key，GitHub 一認到帳號就不會再試其他 key 了。

## 解法：core.sshCommand

在次要帳號的 repo 裡設定 `core.sshCommand`，強制指定要用的 key：

```bash
git config --local core.sshCommand "ssh -i ~/.ssh/id_ed25519_secondary -o IdentitiesOnly=yes"
```

順便設定 commit 的作者資訊：

```bash
git config --local user.name "Your Name"
git config --local user.email "your-secondary-email@example.com"
```

這樣做的好處：

- remote URL 維持正常的 `git@github.com:...`，不需要改
- 設定只影響這個 repo，其他 repo 完全不受影響
- 不用動 `~/.ssh/config`

## 完整步驟

### 1. 產生第二把 SSH key

```bash
ssh-keygen -t ed25519 -C "your-secondary-email@example.com" -f ~/.ssh/id_ed25519_secondary
```

### 2. 把 public key 加到對應的 GitHub 帳號

登入次要的 GitHub 帳號，到 Settings → SSH and GPG keys → New SSH key，貼上：

```bash
cat ~/.ssh/id_ed25519_secondary.pub
```

### 3. 在 repo 裡設定

```bash
cd /path/to/secondary-account-repo
git config --local core.sshCommand "ssh -i ~/.ssh/id_ed25519_secondary -o IdentitiesOnly=yes"
git config --local user.name "Your Name"
git config --local user.email "your-secondary-email@example.com"
```

### 4. 驗證

```bash
git fetch origin
```

沒有報錯就代表設定成功了。

## 為什麼不用 SSH config Host 別名？

網路上常見的做法是在 `~/.ssh/config` 設定 Host 別名：

```
Host github.com-account-b
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_secondary
  IdentitiesOnly yes
```

但這樣 remote URL 不能用 GitHub 預設給的，要手動改成：

```bash
# 預設
git@github.com:account-b/repo.git
# 要改成
git@github.com-account-b:account-b/repo.git
```

每次 clone 都要記得改，比較麻煩。

## 為什麼 SSH agent 會搞混？

SSH agent 裡如果有多把 key，連線時會依序嘗試。但 GitHub 的行為是：**只要任何一把 key 匹配到某個帳號，就直接認證成功**，不管你實際要存取的是哪個帳號的 repo。

所以即使你在 `~/.ssh/config` 指定了 `IdentityFile`，如果 agent 裡有其他 key 且被優先送出，就會認錯帳號。

`core.sshCommand` 加上 `-o IdentitiesOnly=yes` 可以確保**只用指定的 key**，不受 agent 裡其他 key 的干擾。
