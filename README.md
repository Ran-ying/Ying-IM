# Ying-IM
点对点加密通讯软件

## 初步设想

1. 客户端通过wss与服务器建立加密连接  
2. 客服端产生公钥和私钥，向服务器注册公钥  
3. 服务器维护公钥表  
4. 客户端请求目标客户端公钥  
5. 客户端采用混合加密方式，通过目标客户端公钥加密对称加密密钥，通过对称加密密钥加密要传递的信息  
6. 目标客户端从服务器接收并读取信息  

### 加密方式

**客户端注册**  

客户端提供：  
1. 客户端 ID  
2. 客户端 Username  
3. 客户端 Password  
4. 客户端 Public Key  
5. 客户端 Private Key  

客户端向服务器端发送：  
1. 客户端 ID  
2. 客户端 Username  
3. (MD5加密) 客户端 Password + salt  
4. 客户端 Public Key  
5. (客户端 Password + salt 对称加密) 客户端 Private Key  

**客户端登陆**  

客户端向服务器发送：  
1. 客户端 Username  
2. (MD5加密) 客户端 Password + salt  

服务器端处理后向客户端发送：  
1. (客户端 Password + salt 对称加密) 客户端 Private Key  

客户端动作：  
1. 使用“客户端 Password + salt”对称解密客户端 Private Key  

**客户端发送信息**  

1. 客户端向服务器端请求 目标用户 ID  
2. 服务器端向客户端回发 目标用户 Public Key  
3. 客户端产生对称加密密钥，加密将要传输的信息  
4. 客户端使用 目标用户 Public Key 加密对称加密密钥  
5. 客户端向服务器端回发 3 & 4  

**客户端接收信息**  

服务器端与客户端建立连接后：  
1. 检索所有未发送的且Messkey为当前客户端ID的 Message 并发送  

客户端接收后动作：  
1. 使用 客户端 Private Key 解密 Message 的非对称加密部分，取得 Message 的 对称加密密钥  
2. 使用 对称加密密钥 解密 Message 的对称加密部分  
