## MongoDB インストール

> 最新版は、ダウンロードサイトから取得できるが、  
> 今回は version4.4 をインストール

```
   57  vi /etc/yum.repos.d/mongodb-org-4.4.repo
  135  cat /etc/yum.repos.d/mongodb-org-4.4.repo
   58  yum install -y mongodb-org
   60  mongod -version
   61  systemctl start mongod
   62  systemctl status mongod
   63  systemctl enable mongod
   64  mongo
```

```
作成するファイルは、以下の通り

[root@centos ~]# cat /etc/yum.repos.d/mongodb-org-4.4.repo
[mongodb-org-4.4]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/4.4/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-4.4.asc
```