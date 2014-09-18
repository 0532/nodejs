# Crypto 加密模块

使用require('crypto')来访问这个模块。


crypto 模块需要node 所运行的运行支持OpenSSL，该模块为使用安全证书实现HTTPS 安全网络以及HTTP 连
接提供了支持。
模块同样为OpenSSL 的hash、hmac、cipher、decipher、sign 以及verify 方法提供一层包装（以方便在Node 中
使用）。


**crypto.createCredentials(details)**


建立一个证书对象，参数detail 是由键值对组成的字典。


key : 一个字符串，包含PEM 编码的私钥


cert: 一个字符串，包含PEM 编码的证书


ca : 一个包含PEM
编码的、可信任的数字中心认证证书的字符串或者字符串列表


如果参数details 中没有'ca' ， 那么node.js 将缺省使用
http://mxr.mozilla.org/mozilla/source/security/nss/lib/ckfw/builtins/certdata.txt 中给出的可信任的公钥。


**crypto.createHash(algorithm)**


通过参数algorithm 指定算法建立并且返回一个哈希对象，可以用来产生哈希摘要。
algorithm 参数依赖于node 运行平台上OpenSSL 所支持的有效算法。例如sha1,md5,sha256,sha512等，在最近发
布的版本中, openssl list-message-digest-algorithms 将显示有效的算法摘要。


**hash.update(data)**


使用data 更新哈希表。当以流的方式接受新数据时（数据可能被为分多次接收），可多次调用此方法。


**hash.digest(encoding='binary')**


计算所有传递来数据的哈希摘要。编码可以是'hex','binary'或者'base64'。


**crypto.createHmac(algorithm, key)**


通过指定算法(algorithm)和密钥(key)建立并返回一个加密的hmac 对象。
和createhash 函数一样，参数algorithm 的选择依赖于node 运行平台上OpenSSL 所支持的有效算法，key 是要使
用的HMAC 私钥。


**hmac.update(data)**


更新指定数据(参数data)的hmac 的内容，当以流的方式接收新数据(参数data)时，可多次调用此方法。


**hmac.digest(encoding='binary')**


计算所有传递来数据的hmac 摘要。编码可以是'hex','binary'或者'base64' 。


**crypto.createCipher(algorithm, key)**


通过指定算法(algorithm )和密钥(key)建立并返回一个cipher 对象
算法参数的内容依赖于OPENSSL 所支持的有效算法，例如aes192等等。OpenSSL 的list-cipher-algorithms 将显
示有效的cipher 算法。


**cipher.update(data, input_encoding='binary', output_encoding='binary')**


更新参数data 所代表的cipher ， input_encodin 是初始数据的编码， 编码可以是'utf8','ascii' 或者'binary' 。
output_encoding 参数指定了加密数据的输出编码，编码可以是'binary','base64'或者'hex'。
返回加密后的内容，当以流的方式接收新数据时，可多次调用此方法。


**cipher.final(output_encoding='binary')**


返回剩余的已加密内容，output_eocoding 可以是'binary','ascii','utf8'中的一个。


**crypto.createDecipher(algorithm, key)**


通过参数algorithm 和key 建立并返回一个decipher 对象。这是前面cipher 对象的一个镜像.


**decipher.update(data, input_encoding='binary', output_encoding='binary')**


更新参数data 所代表的decipher ，input_encoding 是初始数据的编码，编码可以是'binary','base64'或者'hex'。
output_encoding 参数指定了已解密的铭文的输出编码，编码可以是'binary','base64'或者'hext'


**decipher.final(output_encoding='binary')**


返回其余解密后的文本。参数output_encoding 是'binary', 'ascii' or 'utf8'中的一个.


**crypto.createSign(algorithm)**


通过参数algorithm 建立并返回一个signing 对象。根据当前openSSL 版本,penssl 的list-public-key-algorithms 将
显示singning 的有效算法.例如'RSA-SHA256'.


**signer.update(data)**


用参数data 更新signer 对象，当以流的方式接收新数据时，可多次调用此方法。


**signer.sign(private_key, output_format='binary')**


计算所有signer 里已经更新的数据的签名。private_key 是一个字符串，包含用于签名的PEM 编码的私钥。
返回用output_format 指定编码的签名，编码可以是'binary', 'hex' or 'base64'


**crypto.createVerify(algorithm)**


通过指定algorithm 建立并返回一个verification 对象.这是上面signing 对象的一个镜像。


**verifier.update(data)**


用新数据(参数data)更新verifyer 对象，当以流的方式接收新数据时，可多次调用此方法。


**verifier.verify(public_key, signature, signature_format='binary')**


用包含有PEM 编码的公钥，即参数public_key 来验证电子签名数据。参数signature 是先前用此数据计算的签
名，参数signature_format 可以是'binary', 'hex' or 'base64'.
根据签名和公钥对数据的验证结果返回true 或false。
