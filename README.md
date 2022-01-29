# - 使用于Shadowrocket（小火箭）的代理规则

基本语法：“规则类型，内容，代理模式，选项(可选)”

类型包括：DOMAIN、DOMAIN-SUFFIX、DOMAIN-KEYWORD、IP-CIDR、GEOIP。

内容一般是：域名、IP地址或地址段、关键字

代理模式：DIRECT(直连)、Proxy(代理)、Reject（拦截/拒绝）

选项条件：见下文。

规则结尾：

>#其他未匹配到规则的走代理
>
>FINAL,PROXY

>#其他未匹配到规则的走直连
>
>FINAL,DIRECT

**基于域名的规则**

>`DOMAIN,www.apple.com,Proxy`

如果与请求的域名完全相同，则匹配规则。

----------------------------

**域名后缀**

`DOMAIN-SUFFIX,apple.com,Proxy`
如果与请求的域名后缀相同，则规则匹配。例如：google.com 匹配 `www.google.com`，`mail.google.com` 和 `google.com`，但并没有匹配`content-google.com`。

**域名关键字**

>`DOMAIN-KEYWORD,google,Proxy`

如果请求的域包含关键字，则规则匹配。

----------------------------

**基于域名的规则选项**
force-remote-dns

带有force-remote-dns 选项时主机名返回Fake IP并反向查出真正的域名是什么，避免本地的DNS查询动作。

意思就是用远程DNS解析域名，解决本地DNS污染问题。

>DOMAIN,www.google.com,Proxy,force-remote-dns
>
>DOMAIN-SUFFIX,google.com,Proxy,force-remote-dns
>
>DOMAIN-KEYWORD,google,Proxy,force-remote-dns

----------------------------

**基于IP的规则**
IP-CIDR

>IP-CIDR,192.168.0.0/16,DIRECT
>
>IP-CIDR,10.0.0.0/8,DIRECT
>
>IP-CIDR,172.16.0.0/12,DIRECT
>
>IP-CIDR,127.0.0.1/8,DIRECT

如果请求的IP地址匹配指定范围，则规则匹配。

----------------------------

**GEOIP**

>GEOIP,US,DIRECT
>
>GEOIP,CN,DIRECT

**如果 GeoIP 测试结果符合指定的国家/地区代码，则规则匹配。

----------------------------

**基于 IP 的规则选项**

no-resolve

>GEOIP,US,DIRECT,no-resolve
>
>IP-CIDR,172.16.0.0/12,DIRECT,no-resolve

当遇到 GEOIP 或 IP-CIDR 规则时，Shadowrocket 会发送 DNS 问题，以检查请求的主机名是否为域名。您可以选择 “no-resolve” 选项来跳过具有域名的请求的此规则。

注意：如果某些域名无法由本地 DNS 服务器解决，请确保在与该域匹配的规则前面没有基于 IP 的规则。否则由于 DNS 错误，规则测试将失败。您也可以使用 “no-resolve” 来解决问题。

意思就是上面用域名匹配规则DNS服务器无法解析的时候，下面用IP匹配规则，出现冲突的时候用 “no-resolve” 跳过这条IP规则
