# socat:OpenSSL 证书哈希验证补丁

> 原文：<https://medium.com/hackernoon/socat-patch-for-openssl-certificate-hash-verification-f806982652db>

信任服务器上的特定证书(例如自签名证书，或者与不属于公共链的另一个 CA 的证书)的过程需要访问`/etc/ssl/certificates` (root 访问)或者您喜欢的命令行界面工具中的选项，以提供不同的证书存储。`socat`尚未提供此选项。

您也可能遇到这样的情况:证书已过期，但您希望只信任特定的过期证书，而不是完全禁用证书验证。

为了涵盖这些和类似的用例，我开发了一个补丁，通过使用 SHA256 哈希的证书锁定来实现另一个方向(见下面的[要点](https://gist.github.com/gdm85/db9c060c06e390cf6832346e787ec190))。

示例用法:

```
socat TCP-LISTEN:4443,reuseaddr,fork OPENSSL:example.com:8443,verify=0,verify-hash=654f3537fbff41fef5addf323dd01b7171dd14c54a5ae5b20f988e4c7a84c256
```

如果您希望在 SHA256 哈希验证之后使用常规验证，可以省略上面示例中的`verify=0`。

享受新的`--verify-hash`功能，如果它对你的`socat`用例有帮助，请在评论中告诉我。