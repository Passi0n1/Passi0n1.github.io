> 这个攻击方法很早就看到了，但是之前没时间去看具体的方式和原理，所以一直搁置了，刚好周末有空记录一下

## 导致漏洞的原因

Algorithm confusion vulnerabilities typically arise due to flawed implementation of JWT libraries. Although the actual verification process differs depending on the algorithm used, many libraries provide a single, algorithm-agnostic method for verifying signatures. These methods rely on the alg parameter in the token's header to determine the type of verification they should perform.

漏洞的原因有两处，这两处需要同时存在才可以实现这个攻击:
1、代码本身可以支持 HS 256 和 RS256 两种加解密的方法
2、代码默认将验证 JWT的公钥写死为 RS256 的公钥 


```
function verify(token, secretOrPublicKey){
    algorithm = token.getAlgHeader();
    if(algorithm == "RS256"){
        // Use the provided key as an RSA public key
    } else if (algorithm == "HS256"){
        // Use the provided key as an HMAC secret key
    }
}
```

```
publicKey = <public-key-of-server>;
token = request.getCookie("session");
verify(token, publicKey);
```

这样一来黑客可以将 rs 256 公钥作为 hs256 加密的 secret  key 用来加密签名。而后端代码页晕了吧唧的用 rs 256 公钥作为 secret key 去解密。

当然，这里的算法不固定，HS 256 和 RS256 只是加密方式的代指。

![image](https://github.com/user-attachments/assets/cdbde5f9-0b84-457f-bf24-5e5bcbe8c06a)
