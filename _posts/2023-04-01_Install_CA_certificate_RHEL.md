---
layout: post
title: "Install CA certificate on CentOS/RHEL"
author: "Xiping Hu"
header-style: text
tags:
  - Linux
---

1. Install ca-certificates
```bash
yum install ca-certificates
```

2. Enable the dynamic CA configuration feature
```bash
update-ca-trust enable
```

3. Put your CA.crt in `/etc/pki/ca-trust/source/anchors/`
```bash
vim /etc/pki/ca-trust/source/anchors/hxp-ca.crt
```

4. Update CA
```bash
update-ca-trust extract
```

5. Verify
```bash
curl https://speedtest.hxp.plus/
```

# References
<https://jermsmit.com/install-a-ca-certificate-on-red-hat-enterprise-linux/>