---
layout: post
title: "Fix for invalid format error when using Git on Windows"
subtitle: ''
author: "Xiping Hu"
header-style: text
tags:
- Windows
- SSH
- Git
---

If you encounter this kind of error when using Git on Windows:

```powershell
PS C:\Users\hxp\Notes> git push origin master
Load key "/c/Users/hxp/credentials/ssh/hxp/id_rsa": invalid format
git@github.com: Permission denied (publickey).
fatal: Could not read from remote repository.
```

and if the ssh command on windows works fine and can read ssh key then login to your server.

This is beacuse Git is using bundled ssh command instead of system's ssh.

Try to set Environment variable `GIT_SSH` to `C:\Windows\System32\OpenSSH\ssh.exe`.
