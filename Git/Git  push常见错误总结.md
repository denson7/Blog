[TOC]
## git push 常见错误
```powershell
# 错误1 
# fatal: repository 'https://github.com/xxx/xxx.git/' not found
// 原因分析：可能是权限验证错误引起的，可以尝试卸载 Git 凭证管理器后重装，步骤如下：
$ git credential-manager uninstall # 卸载
$ git credential-manager install   # 安装

# 错误2
# fatal: unable to access 'https://github.com/xxx/xxx.git/': OpenSSL SSL_read: Connection was reset, errno 10054
// 原因分析：可能是 SSL 验证失败导致的，我们可以直接禁止 SSL 验证
$ git config --global http.sslVerify "false"
```

