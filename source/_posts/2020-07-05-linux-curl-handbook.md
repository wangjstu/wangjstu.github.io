---
title: Linux curl handbook

tags:
- Linux
categories:
- Linux
toc: true
---

## 简介
Linux命令`curl` 可以被轻松地应用于 web 调试中，它们的好兄弟 wget 也是如此，或者也可以试试更潮的 httpie。本文罗列介绍`curl`相关知识点，以便更方便的使用。

## linux curl
本文使用的curl版本是：

````XSHELL
[wangjstu@wangjstu ~]$ curl --version
curl 7.29.0 (x86_64-redhat-linux-gnu) libcurl/7.29.0 NSS/3.28.4 zlib/1.2.7 libidn/1.28 libssh2/1.4.3
Protocols: dict file ftp ftps gopher http https imap imaps ldap ldaps pop3 pop3s rtsp scp sftp smtp smtps telnet tftp 
Features: AsynchDNS GSS-Negotiate IDN IPv6 Largefile NTLM NTLM_WB SSL libz unix-sockets
````

### Linux curl 帮助文档：
````XSHELL
[wangjstu@wangjstu ~]$ curl --help
Usage: curl [options...] <url>
//语法：curl [options] [url]
Options: (H) means HTTP/HTTPS only, (F) means FTP only
//PS: 以下Options中，后面是 (H) 表示只给 HTTP/HTTPS 使用, (F) 表示只给 FTP 使用
     --anyauth       Pick "any" authentication method (H)
     //--anyauth     选择 "any" 认证方法 (HTTP/HTTPS可用)
 -a, --append        Append to target file when uploading (F/SFTP)
 //-a, --append		上传文件时，附加到目标文件 (FTP/SFTP可用)
     --basic         Use HTTP Basic Authentication (H)
     //--basic		使用HTTP基础认证（参见文章末尾参考:HTTP Authentication）(HTTP/HTTPS可用)
     --cacert FILE   CA certificate to verify peer against (SSL)
     //--cacert FILE  CA 证书，用于每次请求认证 (SSL)
     --capath DIR    CA directory to verify peer against (SSL)
     //--capath DIR  CA 证书目录，用于每次请求认证 (SSL)
 -E, --cert CERT[:PASSWD] Client certificate file and password (SSL)
 //-E, --cert CERT[:PASSWD] 客户端证书文件及密码 (SSL)
     --cert-type TYPE Certificate file type (DER/PEM/ENG) (SSL)
     //--cert-type 证书文件类型 (DER/PEM/ENG) (SSL)
     --ciphers LIST  SSL ciphers to use (SSL)
     //--ciphers LIST  SSL 秘钥 (SSL)
     --compressed    Request compressed response (using deflate or gzip)
     //--compressed    请求压缩响应 (使用 deflate 或 gzip)
 -K, --config FILE   Specify which config file to read
 //-K, --config FILE  指定配置文件
     --connect-timeout SECONDS  Maximum time allowed for connection
     //--connect-timeout SECONDS  连接超时设置
 -C, --continue-at OFFSET  Resumed transfer offset
 //-C, --continue-at OFFSET 断点续传请求传输偏移量
 -b, --cookie STRING/FILE  String or file to read cookies from (H)
 //-b, --cookie STRING/FILE  设置请求头中Cookies，内容为后面传的字符串或从文件中读取的内容 (HTTP/HTTPS可用)
 -c, --cookie-jar FILE  Write cookies to this file after operation (H)
 //-c, --cookie-jar FILE  请求完成后，返回头中 Cookies 值写入的文件位置 (HTTP/HTTPS可用)
     --create-dirs   Create necessary local directory hierarchy
     //--create-dirs   创建必要的本地目录层次结构
     --crlf          Convert LF to CRLF in upload
     //--crlf        在上传时将 LF 转写为 CRLF
     --crlfile FILE  Get a CRL list in PEM format from the given file
     //--crlfile FILE  从指定的文件获得PEM格式CRL列表
 -d, --data DATA     HTTP POST data (H)
 //-d, --data DATA     http post请求数据 (HTTP/HTTPS可用)
     --data-ascii DATA  HTTP POST ASCII data (H)
     //--data-ascii DATA  ASCII编码HTTP POST数据 (HTTP/HTTPS可用)
     --data-binary DATA  HTTP POST binary data (H)
     //--data-binary DATA  binary 编码HTTP POST数据 (HTTP/HTTPS可用)
     --data-urlencode DATA  HTTP POST data url encoded (H)
     //--data-urlencode DATA  url 编码 HTTP POST 数据 (HTTP/HTTPS可用)
     --delegation STRING GSS-API delegation permission
     //--delegation STRING GSS-API 授权权限
     --digest        Use HTTP Digest Authentication (H)
     //--digest        使用HTTP Digest身份验证（参见文章末尾参考:HTTP Authentication）(HTTP/HTTPS可用)
     --disable-eprt  Inhibit using EPRT or LPRT (F)
     //--disable-eprt  禁用EPRT或LPRT (FTP可用)
     --disable-epsv  Inhibit using EPSV (F)
     //--disable-epsv  禁用EPSV (FTP可用)
 -D, --dump-header FILE  Write the headers to this file
 //-D, --dump-header FILE  将请求返回头信息写入指定的文件
     --egd-file FILE  EGD socket path for random data (SSL)
     //--egd-file FILE  为随机数据(SSL)设置EGD socket路径
     --engine ENGINGE  Crypto engine (SSL). "--engine list" for list
     //--engine ENGINGE  SSL加密引擎类型，使用"--engine list"获取可用类型 
 -f, --fail          Fail silently (no output at all) on HTTP errors (H)
 //-f, --fail        连接失败时不显示HTTP错误详细信息，以curl错误显示 (HTTP/HTTPS可用)
 -F, --form CONTENT  Specify HTTP multipart POST data (H)
 //-F, --form CONTENT  模拟 HTTP 表单数据提交（multipart POST） (HTTP/HTTPS可用)
     --form-string STRING  Specify HTTP multipart POST data (H)
     //--form-string STRING  模拟 HTTP 表单数据提交 (HTTP/HTTPS可用)
     --ftp-account DATA  Account data string (F)
     //--ftp-account DATA  ftp帐户数据提交 (F)
     --ftp-alternative-to-user COMMAND  String to replace "USER [name]" (F)
     //--ftp-alternative-to-user COMMAND  指定替换 "USER [name]" 的字符串 (F)
     --ftp-create-dirs  Create the remote dirs if not present (F)
     //--ftp-create-dirs   如果远程不存相关目录，在则创建远程对应目录 (F)
     --ftp-method [MULTICWD/NOCWD/SINGLECWD] Control CWD usage (F)
     //--ftp-method [MULTICWD/NOCWD/SINGLECWD] 控制 CWD (F)
     --ftp-pasv      Use PASV/EPSV instead of PORT (F)
     //--ftp-pasv     使用 PASV/EPSV 替换 PORT (F)
 -P, --ftp-port ADR  Use PORT with given address instead of PASV (F)
 //-P, --ftp-port ADR  使用指定 PORT 及地址替换 PASV (F).使用端口地址，而不是使用PASV.
     --ftp-skip-pasv-ip Skip the IP address for PASV (F)
     //--ftp-skip-pasv-ip 跳过 PASV 的IP地址 (F)
     --ftp-pret      Send PRET before PASV (for drftpd) (F)
     //--ftp-pret      在 PASV 之前发送 PRET (drftpd) (F)
     --ftp-ssl-ccc   Send CCC after authenticating (F)
     //--ftp-ssl-ccc   认证之后发送 CCC (F)
     --ftp-ssl-ccc-mode ACTIVE/PASSIVE  Set CCC mode (F)
     //--ftp-ssl-ccc-mode  ACTIVE/PASSIVE  设置 CCC 模式 (F)
     --ftp-ssl-control Require SSL/TLS for ftp login, clear for transfer (F)
     //--ftp-ssl-control  登录时需要 SSL/TLS (F)
 -G, --get           Send the -d data with a HTTP GET (H)
 //-G, --get           使用 HTTP GET 方法发送 -d 数据  (HTTP/HTTPS可用)
 -g, --globoff       Disable URL sequences and ranges using {} and []
 //-g, --globoff       禁用的 URL 队列 及范围使用 {} 和 []
 -H, --header LINE   Custom header to pass to server (H)
 //-H, --header LINE   http请求服务端的自定义请求头 (HTTP/HTTPS可用)
 -I, --head          Show document info only
 //-I, --head          仅显示返回响应文档头
 -h, --help          This help text
 //-h, --help          显示帮助文档
     --hostpubmd5 MD5  Hex encoded MD5 string of the host public key. (SSH)
     //--hostpubmd5 MD5  将主机公钥的MD5字符串转为十六进制编码。（SSH）
 -0, --http1.0       Use HTTP 1.0 (H)
 //-0, --http1.0       使用 HTTP 1.0 (H)
     --ignore-content-length  Ignore the HTTP Content-Length header
     //--ignore-content-length  忽略 HTTP Content-Length 头
 -i, --include       Include protocol headers in the output (H/F)
 //-i, --include       在输出中包含响应协议头 (H/F)
 -k, --insecure      Allow connections to SSL sites without certs (H)
 //-k, --insecure      允许连接到 SSL 站点，而不使用证书 (H)
     --interface INTERFACE  Specify network interface/address to use
     //--interface INTERFACE  指定网络接口／地址
 -4, --ipv4          Resolve name to IPv4 address
 //-4, --ipv4          将域名解析为 IPv4 地址
 -6, --ipv6          Resolve name to IPv6 address
 -6, --ipv6          将域名解析为 IPv6 地址
 -j, --junk-session-cookies Ignore session cookies read from file (H)
 //-j, --junk-session-cookies 忽略从文件读取的会话cookie
     --keepalive-time SECONDS  Interval between keepalive probes
     //--keepalive-time SECONDS  keepalive 包间隔
     --key KEY       Private key file name (SSL/SSH)
     //--key KEY       私钥文件名 (SSL/SSH)
     --key-type TYPE Private key file type (DER/PEM/ENG) (SSL)
     //--key-type TYPE   私钥文件类型 (DER/PEM/ENG) (SSL)
     --krb LEVEL     Enable Kerberos with specified security level (F)
     //--krb LEVEL     启用指定安全级别的 Kerberos (F)
     --libcurl FILE  Dump libcurl equivalent code of this command line
     //--libcurl FILE  转储此命令行的libcurl等效代码
     --limit-rate RATE  Limit transfer speed to this rate
     //--limit-rate RATE  限制传输速度
 -l, --list-only     List only names of an FTP directory (F)
 //-l, --list-only     只列出FTP目录的名称 (FTP)
     --local-port RANGE  Force use of these local port numbers
     //--local-port RANGE  强制使用的本地端口号
 -L, --location      Follow redirects (H)
 //-L, --location      跟踪重定向 (H)
     --location-trusted like --location and send auth to other hosts (H)
     //--location-trusted  类似 --location 并发送验证信息到其它主机 (H)
 -M, --manual        Display the full manual
 //-M, --manual        显示详细帮助文档
     --mail-from FROM  Mail from this address
     //--mail-from FROM  设置邮件发送源地址
     --mail-rcpt TO  Mail to this receiver(s)
     //--mail-rcpt TO  设置邮件收件人(可用多人)
     --mail-auth AUTH  Originator address of the original email
     //--mail-auth AUTH  原始电子邮件的发起人地址
     --max-filesize BYTES  Maximum file size to download (H/F)
     //--max-filesize BYTES  下载文件大小上限 (H/F)
     --max-redirs NUM  Maximum number of redirects allowed (H)
     //--max-redirs NUM  最大重定向数 (H)
 -m, --max-time SECONDS  Maximum time allowed for the transfer
 //-m, --max-time SECONDS  允许的最多传输时间
     --metalink      Process given URLs as metalink XML file
     //--metalink      将给定的URL作为metalink xml文件处理
     --negotiate     Use HTTP Negotiate Authentication (H)
     //--negotiate    使用HTTP协商身份验证（参见文章末尾参考:HTTP Authentication）(HTTP/HTTPS可用)
 -n, --netrc         Must read .netrc for user name and password
 //-n, --netrc         必须从 .netrc 文件读取用户名和密码
     --netrc-optional Use either .netrc or URL; overrides -n
     //--netrc-optional 使用 .netrc 或 URL; 将重写 -n 参数
     --netrc-file FILE  Set up the netrc filename to use
     //--netrc-file FILE  设置要使用的 netrc 文件名
 -N, --no-buffer     Disable buffering of the output stream
 //-N, --no-buffer     禁用输出流的缓存
     --no-keepalive  Disable keepalive use on the connection
     //--no-keepalive  禁用 connection 的 keepalive
     --no-sessionid  Disable SSL session-ID reusing (SSL)
     //--no-sessionid  禁止重复使用 SSL session-ID (SSL)
     --noproxy       List of hosts which do not use proxy
     //--noproxy       不使用代理的主机列表
     --ntlm          Use HTTP NTLM authentication (H)
     //--ntlm          使用HTTP NTLM验证（参见文章末尾参考:HTTP Authentication）(HTTP/HTTPS可用)
 -o, --output FILE   Write output to <file> instead of stdout
 //-o, --output FILE   将输出写入文件，而非 stdout
     --pass PASS     Pass phrase for the private key (SSL/SSH)
     //--pass PASS     传递给私钥的短语 (SSL/SSH)
     --post301       Do not switch to GET after following a 301 redirect (H)
     //--post301       在 301 重定向后不要切换为 GET 请求 (HTTP/HTTPS可用)
     --post302       Do not switch to GET after following a 302 redirect (H)
     //--post302       在 302 重定向后不要切换为 GET 请求 (HTTP/HTTPS可用)
     --post303       Do not switch to GET after following a 303 redirect (H)
     //--post303       在 303 重定向后不要切换为 GET 请求 (HTTP/HTTPS可用)
 -#, --progress-bar  Display transfer progress as a progress bar
 //-#, --progress-bar  以进度条显示传输进度
     --proto PROTOCOLS  Enable/disable specified protocols
     //--proto PROTOCOLS  启用/禁用 指定的协议
     --proto-redir PROTOCOLS  Enable/disable specified protocols on redirect
     //--proto-redir PROTOCOLS  在重定向上 启用/禁用 指定的协议
 -x, --proxy [PROTOCOL://]HOST[:PORT] Use proxy on given port
 //-x, --proxy [PROTOCOL://]HOST[:PORT]  指定代理端口及地址
     --proxy-anyauth Pick "any" proxy authentication method (H)
     //--proxy-anyauth 在代理上使用 "any" 认证方法 (HTTP/HTTPS可用)
     --proxy-basic   Use Basic authentication on the proxy (H)
     //--proxy-basic   在代理上使用 Basic 认证  (HTTP/HTTPS可用)
     --proxy-digest  Use Digest authentication on the proxy (H)
     //--proxy-digest  在代理上使用 Digest 认证 (HTTP/HTTPS可用)
     --proxy-negotiate Use Negotiate authentication on the proxy (H)
     //--proxy-negotiate 在代理上使用 Negotiate 认证 (HTTP/HTTPS可用)
     --proxy-ntlm    Use NTLM authentication on the proxy (H)
     //--proxy-ntlm    在代理上使用 NTLM 认证 (HTTP/HTTPS可用)
 -U, --proxy-user USER[:PASSWORD]  Proxy user and password
 //-U, --proxy-user USER[:PASSWORD]  代理用户名及密码
     --proxy1.0 HOST[:PORT]  Use HTTP/1.0 proxy on given port
     //--proxy1.0 HOST[:PORT]  使用指定地址及端口的HTTP/1.0的代理
 -p, --proxytunnel   Operate through a HTTP proxy tunnel (using CONNECT)
 //-p, --proxytunnel   使用HTTP代理隧道 (用于 CONNECT)
     --pubkey KEY    Public key file name (SSH)
     //--pubkey KEY    指定请求的公钥文件名
 -Q, --quote CMD     Send command(s) to server before transfer (F/SFTP)
 //-Q, --quote CMD     在传输开始前向服务器发送命令 (F/SFTP)
     --random-file FILE  File for reading random data from (SSL)
     //--random-file FILE  设置读取随机数据的文件 (SSL)
 -r, --range RANGE   Retrieve only the bytes within a range
 //-r, --range RANGE   仅检索范围内的字节
     --raw           Do HTTP "raw", without any transfer decoding (H)
     //--raw           使用原始HTTP传输，而不使用编码 (HTTP/HTTPS可用)
 -e, --referer       Referer URL (H)
 //-e, --referer       Referer URL (HTTP/HTTPS可用)
 -J, --remote-header-name Use the header-provided filename (H)
 //-J, --remote-header-name 从远程文件读取头信息 (HTTP/HTTPS可用)
 -O, --remote-name   Write output to a file named as the remote file
 //-O, --remote-name   请求远程文件时，将请求返回内容以远程文件名保存到本地，如果没有则报错
     --remote-name-all Use the remote file name for all URLs
     //--remote-name-all  将请求的URL设置为请求远程文件名
 -R, --remote-time   Set the remote file's time on the local output
 //-R, --remote-time   将远程文件的时间设置在本地输出上
 -X, --request COMMAND  Specify request command to use
 //-X, --request COMMAND  使用指定的请求方法(GET POST等)
     --resolve HOST:PORT:ADDRESS  Force resolve of HOST:PORT to ADDRESS
     //--resolve HOST:PORT:ADDRESS  强制设置请求头中的HOST为对应的ip地址+端口
     --retry NUM   Retry request NUM times if transient problems occur
     //--retry NUM   出现问题时的重试次数
     --retry-delay SECONDS When retrying, wait this many seconds between each
     //--retry-delay SECONDS 重试时的间隔时长
     --retry-max-time SECONDS  Retry only within this period
     //--retry-max-time SECONDS  仅在指定时间段内重试
 -S, --show-error    Show error. With -s, make curl show errors when they occur
 //-S, --show-error    显示错误. 在选项 -s 中，当 curl 出现错误时将显示
 -s, --silent        Silent mode. Don't output anything
 //-s, --silent        Silent模式。不输出任务内容
     --socks4 HOST[:PORT]  SOCKS4 proxy on given host + port
     //--socks4 HOST[:PORT]  使用指定的 host + port 作为 SOCKS4 代理
     --socks4a HOST[:PORT]  SOCKS4a proxy on given host + port
     //--socks4a HOST[:PORT]  使用指定的 host + port 作为 socks4a 代理
     --socks5 HOST[:PORT]  SOCKS5 proxy on given host + port
     //--socks5 HOST[:PORT]  使用指定的 host + port 作为 socks5 代理
     --socks5-hostname HOST[:PORT] SOCKS5 proxy, pass host name to proxy
     //--socks5-hostname HOST[:PORT]  通过对应的hostname(地址+端口)实现SOCKS5代理
     --socks5-gssapi-service NAME  SOCKS5 proxy service name for gssapi
     //--socks5-gssapi-service NAME  SOCKS5代理GSSAPI服务名称
     --socks5-gssapi-nec  Compatibility with NEC SOCKS5 server
     //--socks5-gssapi-nec  与NEC Socks5服务器兼容
 -Y, --speed-limit RATE  Stop transfers below speed-limit for 'speed-time' secs
 //-Y, --speed-limit RATE  在指定限速情况下，指定相应时间之后停止传输
 -y, --speed-time SECONDS  Time for trig speed-limit abort. Defaults to 30
 //-y, --speed-time SECONDS  指定时间之后触发限速. 默认 30
     --ssl           Try SSL/TLS (FTP, IMAP, POP3, SMTP)
     //--ssl           尝试 SSL/TLS (FTP, IMAP, POP3, SMTP)
     --ssl-reqd      Require SSL/TLS (FTP, IMAP, POP3, SMTP)
     //--ssl-reqd      需要 SSL/TLS (FTP, IMAP, POP3, SMTP)
 -2, --sslv2         Use SSLv2 (SSL)
 //-2, --sslv2         使用 SSLv2 (SSL)
 -3, --sslv3         Use SSLv3 (SSL)
 //-3, --sslv3         使用 SSLv3 (SSL)
     --ssl-allow-beast Allow security flaw to improve interop (SSL)
     //--ssl-allow-beast 允许安全缺陷改进互操作 (SSL)
     --stderr FILE   Where to redirect stderr. - means stdout
     //--stderr FILE   重定向 stderr 的文件位置, 默认 stdout
     --tcp-nodelay   Use the TCP_NODELAY option
     //--tcp-nodelay   使用 TCP_NODELAY 选项
 -t, --telnet-option OPT=VAL  Set telnet option
 //-t, --telnet-option OPT=VAL  设置 telnet 选项
     --tftp-blksize VALUE  Set TFTP BLKSIZE option (must be >512)
     //--tftp-blksize VALUE  设备 TFTP BLKSIZE 选项 (必须 >512)
 -z, --time-cond TIME  Transfer based on a time condition
 //-z, --time-cond TIME  基于时间条件的传输
 -1, --tlsv1         Use => TLSv1 (SSL)
 //-1, --tlsv1         使用TLSv1 (SSL)
     --tlsv1.0       Use TLSv1.0 (SSL)
     //--tlsv1.0       使用TLSv1.0 (SSL)
     --tlsv1.1       Use TLSv1.1 (SSL)
     //--tlsv1.1       使用TLSv1.1 (SSL)
     --tlsv1.2       Use TLSv1.2 (SSL)
     //--tlsv1.2       使用TLSv1.2 (SSL)
     --trace FILE    Write a debug trace to the given file
     //--trace FILE    将 debug 信息写入指定的文件
     --trace-ascii FILE  Like --trace but without the hex output
     //--trace-ascii FILE  类似 --trace 但使用16进度输出
     --trace-time    Add time stamps to trace/verbose output
     //--trace-time    向 trace/verbose 输出添加时间戳
     --tr-encoding   Request compressed transfer encoding (H)
     //--tr-encoding   请求压缩传输编码 (HTTP/HTTPS可用)
 -T, --upload-file FILE  Transfer FILE to destination
 //-T, --upload-file FILE   将文件传输（上传）到目的地址
     --url URL       URL to work with
     //--url URL       指定所使用的 URL
 -B, --use-ascii     Use ASCII/text transfer
 //-B, --use-ascii     使用 ASCII/text 传输
 -u, --user USER[:PASSWORD]  Server user and password
 //-u, --user USER[:PASSWORD]  指定服务器认证用户名、密码
     --tlsuser USER  TLS username
     //--tlsuser USER  TLS 用户名
     --tlspassword STRING TLS password
     //--tlspassword STRING TLS 密码
     --tlsauthtype STRING  TLS authentication type (default SRP)
     //-tlsauthtype STRING  TLS 认证类型 (默认 SRP)
     --unix-socket FILE    Connect through this UNIX domain socket
     //--unix-socket FILE    指定 UNIX socket 域连接
 -A, --user-agent STRING  User-Agent to send to server (H)
 //-A, --user-agent STRING  设置要发送到服务器的 User-Agent (HTTP/HTTPS可用)
 -v, --verbose       Make the operation more talkative
 //-v, --verbose       显示详细操作信息
 -V, --version       Show version number and quit
 //-V, --version       显示版本号并退出
 -w, --write-out FORMAT  What to output after completion
 //-w, --write-out FORMAT  用于在一次完整且成功的操作后输出指定格式的内容到标准输出
     --xattr        Store metadata in extended file attributes
     //--xattr        将元数据存储在扩展文件属性中
 -q                 If used as the first parameter disables .curlrc
 //-q                 如果作为第一个参数, .curlrc 中的设置无效。 curl会在程序启动时会自动尝试读取用户家目录中的.curlrc文件。
````

## 命令速记[[译](https://curl.haxx.se/docs/manual.html)]

### 基础案例

* 请求个人blog主页
```SHELL
curl https://wangjstu.github.io/
```

* 请求funet的ftp的README文件
```SHELL
curl ftp://ftp.funet.fi/README
```

* 通过端口8000获取某主页
```SHELL
curl http://www.weirdserver.com:8000/
```

* 获取ftp站点的目录列表
```SHELL
curl ftp://ftp.funet.fi/
```

* 通过字典查询单词
```SHELL
//Aliases for ‘m’ are ‘match’ and ‘find’, and aliases for ‘d’ are ‘define’ and ‘lookup’
# 查询bash单词的含义
curl dict://dict.org/d:bash 
# 列出所有可用词典
curl dict://dict.org/show:db
# 在foldoc词典中查询bash单词的含义
curl dict://dict.org/d:bash:foldoc
# 查看curl的定义
curl dict://dict.org/m:curl
```

* 一次请求两个页面
```SHELL
curl ftp://cool.haxx.se/ http://www.weirdserver.com:8000/
```

* 获取ftps上文件
```SHELL
curl ftps://files.are.secure.com/secrets.txt
```

* 获取ftps上文件
```SHELL
#直接使用ftps协议
curl ftps://files.are.secure.com/secrets.txt
#使用ftp协议，并增加ssl选项
curl --ftp-ssl ftp://files.are.secure.com/secrets.txt
```

* 通过ssh协议(SFTP)结合账号名获取一个文件
```SHELL
curl -u username sftp://example.com/etc/issue
```

* 通过ssh协议(SCP)结合私钥获取一个文件
```SHELL
#密码不保护输入方式
curl -u username: --key ~/.ssh/id_rsa  scp://example.com/~/file.txt
#密码保护方式
curl -u username: --key ~/.ssh/id_rsa --pass private_key_password   scp://example.com/~/file.txt
```

* 访问已个IPV6站点下载文件
```SHELL
curl "http://[2001:1890:1112:1::20]/"
```

* 访问SMB站点下载文件
```SHELL
curl -u "domain\username:passwd" smb://server.example.com/share/file.txt
```

### 下载保存到文件(Download to a File)

* 访问某站点主页并将主页另存为到本地
```SHELL
curl -o thatpage.html http://www.netscape.com/
```

* 访问某站点主页并将主页内容按URL最后文件名报错在本地，如果没有文件名，则报错
```SHELL
curl -O http://www.netscape.com/index.html
#同时下载2个
curl -O www.haxx.se/index.html -O curl.haxx.se/download.html
```

### 使用普通认证(账号密码 Using Passwords)

* FTP
```SHELL
使用URL中传输账号名密码的格式请求认证
curl ftp://name:passwd@machine.domain:port/full/path/to/file
或者使用 -u 参数
curl -u name:passwd ftp://machine.domain:port/full/path/to/file
```

* FTPS
```SHELL
更加推荐通过使用FTP：//和--ftp-ssl选项完成。
```

* SFTP/SCP
```SHELL
 选项--key表示私钥，--pass表示密码，--pubkey表示公钥
```

* HTTP
```SHELL
支持以下两种方式:
    curl http://name:passwd@machine.domain/full/path/to/file
    curl -u name:passwd http://machine.domain/full/path/to/file
第一种方式会由于URL规范中不能包含用户名和密码，虽然curl支持，但是通过代理时无法正常使用。
HTTP提供了许多不同的身份验证方法，curl支持以下几种方法：Basic, Digest, NTLM and Negotiate (SPNEGO)。默认curl是Basic模式，可以使用--anyauth来与服务器选用一种更安全的方式。
```

* HTTPS
```SHELL
最常与私人证书一起使用：

curl -k https://www.thesitetoauthenticate.com/test -v –key key.pem –cacert ca.pem –cert client.pem

curl -X GET -H "Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpYXQiOjE1MjgwMTY5MjIsImlkIjowLCJuYmYiOjE1MjgwMTY5MjIsInVzZXJuYW1lIjoiYWRtaW4ifQ.LjxrK9DuAwAzUD8-9v43NzWBN7HXsSLfebw92DKd1JQ" -H "Content-Type: application/json" https://127.0.0.1:8081/v1/user --cacert conf/server.crt --cert conf/server.crt --key conf/server.key

```

### 代理(proxy)
curl支持HTTP及SOCKS代理，但是对FTP协议没有特别支持，不过可以HTTP及SOCKS的代理去向FTP传输文件。
```SHELL
curl -x my-proxy:888 ftp://ftp.leachsite.com/README   //代理名字叫my-proxy，端口是888
curl -u user:passwd -x my-proxy:888 http://www.get.this/        //使用需要账户的代理
curl -U user:passwd -x my-proxy:888 http://www.get.this/
curl --noproxy localhost,get.this -x my-proxy:888 http://www.get.this/   //本地请求不适用代理，可以用逗号分隔。使用--proxy1.0 (默认是用--proxy或-x)特指适用http1.0代理，--socks4 and --socks5特指使用SOCKS4 and SOCKS5代理。
curl -u "username@ftp.server Proxy-Username:Remote-Pass"  --ftp-account Proxy-Password --upload-file local-file ftp://my-ftp.proxy.server:21/remote/upload/path/  //ftp使用代理上传文件
```

### 断点续传(Ranges)
http 1.1支持断点续传，可以一部分一部分下载，curl使用 -r 参数。
```SHELL
curl -r 0-99 http://www.get.this/   //获取前一百个字节
curl -r -500 http://www.get.this/   //获取最后500个字节
curl -r 0-99 ftp://www.get.this/README  //获取ftp中前100个字节，FTP中必须传起始位置截止位置
```

### 上传文件(Uploading[FTP/FTPS/SFTP/SCP/HTTP])
```SHELL
curl -T - ftp://ftp.upload.com/myfile        //将标准输入的数据全部上传到ftp
curl -T uploadfile -u user:passwd ftp://ftp.upload.com/myfile       //上传文件名为uploadfile到ftp，在ftp上命名为myfile，并使用用户名和密码
curl -T uploadfile -u user:passwd ftp://ftp.upload.com/             //上传文件名为uploadfile到ftp，在ftp上名字同本地相同
curl -T localfile -a ftp://ftp.upload.com/remotefile                //上唇本地文件localfile，追加到ftp上名为remotefile的文件中
curl --proxytunnel -x proxy:port -T localfile ftp.upload.com        //使用允许上传文件的代理进行文件上传
curl -T file.txt -u "domain\username:passwd"  smb://server.example.com/share/       //SMB、SMBS上传文件
curl -T - http://www.upload.com/myfile          //http上传标准输入中的全部数据，这是PUT请求
```

### debug追踪(Verbose / Debug)
```SHELL
curl -v ftp://ftp.upload.com/       //-v参数可以用来查看curl请求失败时，server端返回的详细错误信息
curl --trace trace.txt www.haxx.se      //--trace or --trace-ascii可以获取到更详细的追踪信息，并保存到本地的trace.txt文件中
```

### 查看返回头信息(Detailed Information)
```SHELL
curl 可以使用 -I/--head 展示HTTP/FTP的头信息（仅展示头信息）。或使用-i/--include展示头信息及body。另外curl支持-D/--dump-header将body与头信息分开，头信息存储在单独制定的文件中，body返回展示。可以用来临时保存cookie等头信息。
curl --dump-header headers.txt curl.haxx.se   //头信息保存在headers.txt
```

### POST(HTTP)
```SHELL
-d 支持 application/x-www-form-urlencoded mime-type表单传输：
curl -d "name=Rafael%20Sagula&phone=3320780" http://www.where.com/guest.cgi //使用-d传输post参数，切记post参数要urlencode

-F 支持 multipart/form-data 类型，如上传文件：
curl -F "coolfiles=@fil1.gif;type=image/gif,fil2.txt,fil3.html"
  http://www.post.com/postit.cgi //上传文件，并使用@fil1.gif从文件中读取内容，后面的;type=<mime type> 指定文件类型，本例子上传了三个文件fil1.gif，fil2.txt，fil3.html。可以使用@前缀来制定提交的内容为一个文件，也可以使用<符号来提交文件中的内容。

另外-F支持多个字段传输，多文件传输：
curl -F "file=@cooltext.txt" -F "yourname=Daniel"
  -F "filedescription=Cool text file with cool text inside"
  http://www.post.com/postit.cgi
curl -F "pictures=@dog.gif,cat.gif" $URL
curl -F "docpicture=@dog.gif" -F "catpicture=@cat.gif" $URL

另外可以使用  --form-string
```

### Referrer
```SHELL
curl -e www.coolsite.com http://www.showme.com/   //curl可以使用-e参数指定http请求头中的referred值
```

### 客户端请求标志头信息(User Agent)
```SHELL
curl -A 'Mozilla/3.0 (Win95; I)' http://www.nationsbank.com/ //-A或者--user-agent标志请求的客户端的标志头信息

等同于 curl -H 'User-Agent: Mozilla/3.0 (Win95; I)' https://www.nationsbank.com/
```

### Cookies
```SHELL
HTTP是无状态的，为了解决这个问题，一般HTTP服务端返回cookie信息格式如下：Set-Cookie: sessionid=boo123; path="/foo";用于对客户端进行唯一标志状态。传输cookie可以使用以下方法：
curl -b "name=Daniel" www.sillypage.com
curl -b headersfile www.example.com  //其中headersfile来自 curl --dump-header headers www.example.com， 或者使用-c参数，将cookie写入一个txt文本 curl -c cookies.txt www.example.com
curl -b cookies.txt -c cookies.txt www.example.com //保存cookie，并及时使用cookie
curl -L -b empty.txt www.example.com //请求跟随服务器的重定向，并带上cookie。
```

### 进度信息(Progress Meter)
```SHELL
curl请求时候经常返回如下信息
% Total    %   Received   %   Xferd   Average Speed Dload  Average Speed Upload        Time Total   Time Current  Time Left    CURR.Speed
0  151M    0 38608    0     0   9406      0  4:41:43  0:00:04  4:41:39  9287
```
以上信息表示：
```SHELL
%             - 整体传输完成百分比
Total         - 整体传输的大小
%             - 下载完成百分比
Received      - 当前已下载的字节数
%             - 上传完成百分比
Xferd         - 当前已上传的字节数
Average Speed Dload         - 平均下载速度
Average Speed Upload        - 平均上传速度
Time Total    - 预计完成全部传输所需的时间
Time Current  - 当前所消耗的时间
Time Left     - 预计完成剩余传输所需的时间
Curr.Speed    - 最近5秒内平均传输速度(传输最开始5秒，该值是基于线路理论上的速度来计算的)
```

### 限速请求(Speed Limit)
```SHELL
curl -Y 3000 -y 60 www.far-away-site.com //如果速度小于300bytes/s，并持续1分钟，则终止curl
curl -m 1800 -Y 3000 -y 60 www.far-away-site.com  //前面的限制，另外加一个限制，在30分钟时终止请求
curl --limit-rate 10K www.far-away-site.com   //限制请求速度在10kb/s以下
curl --limit-rate 10240 www.far-away-site.com  //同上，限制请求速度在10kb/s以下
curl -T upload --limit-rate 1M ftp://uploadshereplease.com  //限制上传文件的速度在1Mb/s以下
```

### 配置信息(Config File)
curl 请求时会从本地home目录中的.curlrc （windows中是 _curlrc）中读取一些共用的配置信息，如proxy等:
```SHELL
# We want a 30 minute timeout:
-m 1800
#. .. and we use a proxy for all accesses:
proxy = proxy.our.domain.com:8080
# default url to get
url = "http://help.with.curl.com/curlhelp.html"
```
如果要阻止使用配置文件，使用-q参数：`curl -q www.thatsite.com`
另外可以使用-K/--config来读取一些配置从stdin中：`echo "user = user:passwd" | curl -K - http://that.secret.site.com`

### 头信息(Extra Headers)
可以使用-H参数传输头信息到Server端，如`curl -H "X-you-and-me: yes" www.love.com`
`curl -H "Host:" www.server.com` 会发出异常的头信息。

### FTP and Path Names
```SHELL
curl ftp://user:passwd@my.site.com/README //从ftp的服务主目录下载README，相对地址
curl ftp://user:passwd@my.site.com//README  //从ftp的服务器的根目录下下载README，绝对地址
```

### SFTP and SCP and Path Names
```SHELL
curl -u $USER sftp://home.example.com/~/.bashrc  //sftp: 和 scp: URLs显示的是绝对地址，下载home目录中的文件
```

### FTP and Firewalls
```SHELL
curl -P - ftp.download.com              //使用默认网卡下载,即为使用端口地址,而不是使用PASV 
curl -P le0 ftp.download.com            //通过网卡le0下载ftp文件
curl -P 192.168.0.10 ftp.download.com       //将本地IP置为 192.168.0.10 去下载
```

### Network Interface
```SHELL
curl --interface eth0:1 http://www.netscape.com/        //使用网卡eth0,端口1请求
curl --interface 192.168.1.10 http://www.netscape.com/      //使用网络地址 192.168.1.10 来请求
```

### HTTPS
安装了TLS(传输层安全)库之后，curl可以请求一般的https站点：
```SHELL
curl https://www.secure-site.com
```
另外，还支持客户端使用私有证书请求，但是请求中证书必须是PEM格式。如下是请求案例
```
curl -E /path/to/cert.pem:password https://secure.site.com/     //使用密码+pem证书请求。这类请求如果未填写密码，则在收数据前会收到提示，填写密码
curl -2 https://secure.site.com/      //部分站点需要特殊指定SSL或TLS版本， -3, -2 , -1 代表 SSLv3, SSLv2 , TLSv1
curl -k -v –key key.pem –cacert ca.pem –cert client.pem https://www.thesitetoauthenticate.com/test 
curl -k -v -4 --tlsv1.2 --cert client.pem --key key.pem https://www.thesitetoauthenticate.com/test
```
一般申请的证书都是PIE((Personal Information Exchange)，可以通过以下方式转换为pem：
```SHELL
1) 第一类：密码+证书
使用openssl转换：openssl pkcs12 -in abcd.pfx -out abcd.pem
根据提示输入口令及密码(Enter a passphrase and a password)，这类应用于密码+证书的。
2) 第二类：私钥、证书等三个客户端使用的文件：
openssl pkcs12 -in abcd.pfx -out ca.pem -cacerts -nokeys
openssl pkcs12 -in abcd.pfx -out client.pem -clcerts -nokeys
openssl pkcs12 -in abcd.pfx -out key.pem -nocerts
3) 使用方法:
curl -k https://www.thesitetoauthenticate.com/test -v –key key.pem –cacert ca.pem –cert client.pem:
```

### 持续传输(Resuming File Transfers)
```SHELL
curl -C - -o file ftp://ftp.server.com/path/file        //继续从ftp下载
curl -C - -T file ftp://ftp.server.com/path/file        //继续上传文件到ftp
curl -C - -o file http://www.server.com/        //从http服务端继续下载
```

### Time Conditions
If-Modified-Since与If-Unmodified-Since常用语静态文件缓存或断点续传下载等。先了解一下两者的区别：
* If-Unmodified-Since 
>HTTP协议中的 If-Unmodified-Since 消息头用于请求之中，使得当前请求成为条件式请求：只有当资源在指定的时间之后没有进行过修改的情况下，服务器才会返回请求的资源，或是接受 POST 或其他 non-safe 方法的请求。如果所请求的资源在指定的时间之后发生了修改，那么会返回 412 (Precondition Failed) 错误。

>常见的应用场景有两种：

>与 non-safe 方法如 POST 搭配使用，可以用来优化并发控制，例如在某些wiki应用中的做法：假如在原始副本获取之后，服务器上所存储的文档已经被修改，那么对其作出的编辑会被拒绝提交。
与含有 If-Range 消息头的范围请求搭配使用，用来确保新的请求片段来自于未经修改的文档。

* If-Modified-Since
>If-Modified-Since 是一个条件式请求首部，服务器只在所请求的资源在给定的日期时间之后对内容进行过修改的情况下才会将资源返回，状态码为 200  。如果请求的资源从那时起未经修改，那么返回一个不带有消息主体的  304  响应，而在 Last-Modified 首部中会带有上次修改时间。 不同于  If-Unmodified-Since, If-Modified-Since 只可以用在 GET 或 HEAD 请求中。

>当与 If-None-Match 一同出现时，它（If-Modified-Since）会被忽略掉，除非服务器不支持 If-None-Match。

>最常见的应用场景是来更新没有特定 ETag 标签的缓存实体。

CURL也支持：
```SHELL
curl -z local.html http://remote.server.com/remote.html     //如果服务器端文件更新则下载
curl -z -local.html http://remote.server.com/remote.html       //如果本地文件较新，则下载远端
curl -z "Jan 12 2012" http://remote.server.com/remote.html      //2012年1月12日后更新就下载
```

### DICT
```SHELL
别名m的意思是匹配（match)并查找(find)，而别名d是定义(define)并查找(lookup)的意思
curl dict://dict.org/m:curl
curl dict://dict.org/d:heisenbug:jargon
curl dict://dict.org/d:daniel:web1913
curl dict://dict.org/find:curl
curl dict://dict.org/show:db  //列出所有可用词典
curl dict://dict.org/show:strat
curl dict://dict.org/d:bash:foldoc      //在foldoc词典中查询bash单词的含义
```

### 轻量目录访问协议(LDAP)
linux上需要安装OpenLDAP库，windows上需要安装WinLDAP。LDAP默认用LDAPv3，当失败时有机制调用LDAPv2。
```SHELL
curl -B "ldap://ldap.frontec.se/o=frontec??sub?mail=*sth.frontec.se"
curl -u user:passwd "ldap://ldap.frontec.se/o=frontec??sub?mail=*"
curl "ldap://user:passwd@ldap.frontec.se/o=frontec??sub?mail=*"
curl --ntlm "ldap://user:passwd@ldap.frontec.se/o=frontec??sub?mail=*"
```

### 环境变量(Environment Variables)
curl会从环境变量中获取:http_proxy, HTTPS_PROXY, FTP_PROXY代理信息。可以通过ALL_PROXY设置所有都默认使用的代理，可以使用NO_PROXY设置不适用的代理。使用-x/--proxy 会覆盖这些环境变量。linux系统设置http/https proxy的方法，在文件 .bashrc 中添加，或者在/etc/bashrc或者/etc/profile中添加如下环境变量：
```SHELL
export http_proxy=proxy_addr:port
export ftp_proxy=proxy_addr:port
export https_proxy=proxy_addr:port
如:
export http_proxy="192.168.0.1:8080"
export no_proxy='a.test.com,127.0.0.1,2.2.2.2'
export http_proxy=http://easwy:123456@192.168.1.1:8080 //如果密码或用户名中有特殊字符，例如<，可以用 \< 来转义
```

### netrc
文件.netrc用于设置自动登录时所需要的帐号信息。下面是一个常用的"netrc"文件的内容：
```SHELL
machine somehost.com login username password passwd
default login username password passwd
```
curl支持-n/--netrc 和 --netrc-optional 命令参数指向使用netrc。

### 自定义输出(Custom Output)
-w/--write-out参数用于在一次完整且成功的操作后输出指定格式的内容到标准输出。
```

curl -w 'We downloaded %{size_download} bytes\n' www.baidu.com  //自定义输出下载了多大的文件
curl -s -m 10 -o /dev/null -w %{http_code} https://www.baidu.com   //取URL返回状态码
```
支持参数参见[该文档](https://ec.haxx.se/usingcurl-writeout.html)

### Kerberos FTP传输
```SHELL
curl --krb private ftp://krb4site.com -u username:fakepwd
```

### TELNET
```SHELL
curl telnet://remote.server.com       //向远端服务器访问，将stdin输入的数据传到远端，可以用-o将stdout输出到文件，-N/--no-buffer关闭输出
curl -tTTYPE=vt100 telnet://remote.server.com  //使用-t选项将选项传递给telnet协议协商。要告诉服务器我们使用vt100终端
```

### 持久连接(Persistent Connections)
在一个命令行中指定多个文件将使curl按指定的顺序依次传输所有文件。libcurl将尝试为传输使用持久连接，这样到同一主机的第二个传输就可以使用在前一个传输中已经启动并处于打开状态的连接。这大大减少了除了第一次传输之外的所有连接时间，并且更好地利用了网络。请注意，curl不能对在随后的curl调用中使用的传输使用持久连接。如果使用相同的主机，尝试在相同的命令行中填充尽可能多的url，因为这会使传输更快。如果使用HTTP代理进行文件传输，实际上所有传输都是持久的。

### 单命令多请求(Multiple Transfers With A Single Command Line)
```SHELL
curl -O http://url.com/file.txt ftp://ftp.com/moo.exe -o moo.jpg  //第一个请求使用-O将获取到的文件保存到本地，命名为file.txt，第二个请求用-o ，另存为moo.jpg
curl -T local1 ftp://ftp.com/moo.exe -T local2 ftp://ftp.com/moo2.txt //分别上传文件到两个ftp
```

### IPv6
```SHELL
curl -g http://[2001:1890:1112:1::20]/overview.html
curl -6 -d post 'http://2001:0:db8:1111:0:0:0:11:8091/'  //-4表示ipv4，-6表示ipv6
curl -g -6 'http://[fe80::3ad1:35ff:fe08:cd%eth0]:80/'  //telnet -6 fe80::3ad1:35ff:fe08:cd%eth0 80
curl --interface eth0 -g -6 'http://[2606:2800:220:1:248:1893:25c8:1946]:80/index.html' -H 'Host: www.example.com'
```

### Metalink
```SHELL
curl --metalink http://www.example.com/example.metalink
curl --metalink file://example.metalink
```

## 常见问题记

### [官方常见问题](https://curl.haxx.se/docs/faq.html)

### -F与-d区别
* 当form表单以enctype=multipart/form-data提交时，使用-F，常见场景是上传文件时,
```SHELL
<form action="submit.cgi" method="post" enctype="multipart/form-data">
   Name: <input type="text" name="person"><br>
   File: <input type="file" name="secret"><br>
   <input type="submit" value="Submit">
</form>

HTTP请求头如下:
POST /submit.cgi HTTP/1.1
Host: example.com
User-Agent: curl/7.46.0
Accept: */*
Content-Length: 313
Expect: 100-continue
Content-Type: multipart/form-data; boundary=------------------------d74496d66958873e
--------------------------d74496d66958873e
Content-Disposition: form-data; name="person"

anonymous
--------------------------d74496d66958873e
Content-Disposition: form-data; name="secret"; filename="file.txt"
Content-Type: text/plain

contents of the file
--------------------------d74496d66958873e--

使用案例：curl -F 'name=Dan'-F secret=@file.txt  -H 'Content-Type: multipart/magic' https://example.com
```


* 当form表单以application/x-www-form-urlencoded(默认)提交时，使用-d将'name=value'键值对进行URL encode，保证安全。当使用-d传输原数据或json格式时，记得修改Content-Type。

### PUT 请求如何发
```SHELL
curl -d "data to PUT" -X PUT http://example.com/new/resource/file
```

### http2 http3如何发请求
```SHELL
curl --http2 http://example.com/
curl --http3 https://example.com/
```

### curl HTTP cheat sheet
| Verbose              | Hide progress           | extra info        | Write output     | Timeout
|----------------------|-------------------------|-------------------|------------------|--------------
| -v                   | -s                      | -w "format"       | -O               | -m <secs>
| --trace-ascii <file> |                         |                   | -o <file>        |
| **POST**             | **multipart**           | **PUT**           | **HEAD**         | **custom**
| -d "string"          | -F name=value           | -T <file>         | -I               | -X "METHOD"
| -d @file             | -F name=@file           |                   |                  |
| **Basic auth**       | **read cookies**        | **write cookies** | **send cookies** | **user-agent**
| -u user:password     | -b <file>               | -c <file>         | -b "c=1; d=2"    | -A "string"
| **Use proxy**        | **Headers, add/remove** | **follow redirs** | **gzip**         | **insecure**
| -x <host:port>       | -H "name: value"        | -L                | --compressed     | -k
|                      | -H "name:"              |                   |                  |



----
## 参考文档:
* [curl 文档主页](https://curl.haxx.se/docs/manpage.html)
* [curl 帮助指南](https://curl.haxx.se/docs/manual.html)
* [HTTP Authentication](https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication)
* [HTTP HEADERS](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/If-Modified-Since)
* [usingcurl-writeout](https://ec.haxx.se/usingcurl-writeout.html)
* [HTTP Scripting](https://curl.haxx.se/docs/httpscripting.html)
