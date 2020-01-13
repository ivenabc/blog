
---
title: "certbot"
date: 2020-01-13
draft: false
author: "Iven"
---

certbot可以帮助你下载和管理let's encrypt的证书
<!--more-->
## 查看证书列表
可以查看已下载的证书列表
```
certbot certificates
```
> 可以通过--cert-name标志来指定一个特定的证书  
> 另外 certonly,certificates,renew,delete都可以加入这个参数来指定证书名字

## 生成证书
可以手动生成证书，证书下载的位置默认会放在`/etc/letsencrypt/live/grafana.hellobin.top/` grafana.hellobin.top 是你要指定生成的网站  
```
certbot certonly --cert-name grafana.hellobin.top
```




## 重新创建和更新现有证书
certonly和run都会帮你请求创建新的证书或者更新， certonly和run的区别是certonly仅仅只是获取证书，而run会帮你把它放在插件里并且帮你做好指定

另外 `--force-renewal`,`--duplicate`和 `--expand` 也可以更新现有证书

**--force-renewal**重新更新现有的证书，通过`-d`指定域名
```
cerbot --force-renewal -d hellobin.top,api.hellobin.top
```
也可以分开指定
```
cerbot --force-renewal -d hellobin.top -d api.hellobin.top
```

**--duplicate** 会单独创建一个证书，和之前的分开
**--expand**告诉Certbot用包含所有旧域和一个或多个其他新域的新证书更新现有证书。



## 更改证书的域

`--cert-name`通过使用`-d`或`--domains`标志指定新的域，该标志还可用于修改证书包含的域。例如现在有一个hellobin.top这个证书只包含了www.hellobint.top，如果想要加一个api.hellobin.top。可以运行下面命令进行更改：
```
certbot certonly --cert-name hellobin.top -d www.hellobin.top,api.hellobin.top
```
## 吊销证书

需要吊销证书，请使用revoke命令来这样做。请注意，该revoke命令采用证书路径（以结尾cert.pem），而不是证书名称或域。例:
```
certbot revoke --cert-path /etc/letsencrypt/live/CERTNAME/cert.pem
```
> 可以添加原因 --reason unspecified,keycompromise,affiliationchanged,superseded,cessationofoperation

此外，如果证书是通过--staging或--test-cert标志获得的测试证书，则必须将该标志传递给 revoke子命令。吊销证书（或用于其他证书管理任务）后，可以使用delete子命令从系统中删除证书的所有相关文件：
```bash
certbot delete --cert-name example.com
```

## 续订证书

`certbot renew` 会检测到已安装的证书，把将要过期的证书进行续订，由于renew仅更新即将到期的证书，因此可以根据需要频繁运行它-因为通常不会采取任何措施。  

有的时候你需要更新后重启nginx，则可以添加 钩子命令

```bash
certbot renew --pre-hook "service nginx stop" --post-hook "service nginx start"
```
> pre-hook 是更新前的钩子，post-hook是更新证书后的命令

如果仅在续订成功后才能运行，可以使用`--deploy-hook`
```bash
certbot renew --deploy-hook /path/to/deploy-hook-script
```