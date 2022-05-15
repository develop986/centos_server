# CentOS Stream release 8

## SSHで外部接続出来るようにする
```
  218  cp ~/ダウンロード/private_key.txt /home/kuhataku/.ssh/authorized_keys
  219  chmod 600 .ssh/authorized_keys
  221  rm .ssh/known_hosts*
  223  ssh -i ~/.ssh/authorized_keys centos@mysv986.com
```

## アップデート
```
    1  dnf -y update
```

## 設定情報の履歴を取れるようにする
```
    5  dnf -y install git epel-release
    6  dnf -y install etckeeper
    7  etckeeper init
    8  etckeeper commit "1st commit"
```

## 日本設定
```
    9  timedatectl set-timezone Asia/Tokyo
   10  timedatectl status
   11  date && chronyc -a makestep && date
   20  localectl set-locale LANG=ja_JP.utf8
   21  localectl set-keymap jp106
   22  localectl
```

## ホスト名変更
```
   18  hostnamectl set-hostname mysv986.com
```

## 自動アップデート設定
```
   12  dnf -y install dnf-automatic dnf-utils
   13  cp -a /etc/dnf/automatic.conf{,.org}
   14  sed -i -e 's/^apply_updates = no/apply_updates = yes/' /etc/dnf/automatic.conf
   15  diff /etc/dnf/automatic.conf{,.org}
   16  systemctl enable dnf-automatic.timer
   17  systemctl start dnf-automatic.timer
```

## 基本ソフトのインストール
```
   23  dnf -y install bash-completion
   29  dnf -y group install base "Development tools"
   30  dnf repoquery --installonly
  449  dnf -y groupinstall development
  450  dnf -y groupinstall network-tools
```

## 不要パッケージ無効化
```
  454  systemctl disable atd
  455  systemctl disable kdump
  456  systemctl disable mdmonitor
```

## 再起動
```
   26  reboot
```

## SELINUX無効化
```
   24  setenforce 0
   25  cp -a /etc/selinux/config{,.org}
   26  sed -i -e 's/^SELINUX=enforcing/SELINUX=disabled/' /etc/selinux/config
   27  diff /etc/selinux/config{,.org}
```

## SWAP設定
```
   31  dd if=/dev/zero of=/swapfile bs=1M count=1024
   32  chmod 600 /swapfile
   33  mkswap /swapfile
   34  swapon /swapfile
   40  sed -i '$ a /swapfile                                 swap                    swap    defaults        0 0' /etc/fstab
   41  free -m
   42  reboot
   43  free -m
``` 

## ファイアウォール構築
```
  152  dnf -y install firewalld
  153  systemctl enable --now firewalld.service
  151  systemctl status firewalld
  154  firewall-cmd --state
  155  firewall-cmd --get-active-zones
  156  firewall-cmd --list-all --zone=public --permanent
  157  firewall-cmd --get-services
  158  firewall-cmd --add-service=http --zone=public --permanent
  159  firewall-cmd --add-service=https --zone=public --permanent
  160  firewall-cmd --add-service=smtp --zone=public --permanent
  160  firewall-cmd --add-service=smtps --zone=public --permanent
  160  firewall-cmd --add-service=imaps --zone=public --permanent
  161  firewall-cmd --reload
  163  firewall-cmd --list-all --zone=public --permanent
```

## 接続用アカウント作成
```
  201  userdel -r remoteadmin
  202  useradd remoteadmin
  206  passwd remoteadmin
   80  cp -a /etc/sudoers /etc/sudoers.org
   81  visudo
   82  diff /etc/sudoers /etc/sudoers.org

[root@mysv986 ~]# diff /etc/sudoers /etc/sudoers.org
122d121
< remoteadmin	ALL=(ALL)	NOPASSWD: ALL

   83  reboot
```

## 不要かもしれない転送設定
```
   61  vi /etc/postfix/gmail
   62  cat /etc/postfix/gmail

[root@mysv986 ~]# cat /etc/postfix/gmail
[smtp.gmail.com]:587 kuhataku@gmail.com:rglheoqurozxxohx

   63  chmod 600 /etc/postfix/gmail
   64  postmap /etc/postfix/gmail

  135  cp -p /etc/postfix/main.cf /etc/postfix/main.cf.org
  136  vi /etc/postfix/main.cf
  137  diff /etc/postfix/main.cf /etc/postfix/main.cf.org

[root@mysv986 ~]# diff /etc/postfix/main.cf /etc/postfix/main.cf.org
739,746d738
< 
< relayhost = [smtp.gmail.com]:587
< smtp_sasl_auth_enable = yes
< smtp_sasl_password_maps = hash:/etc/postfix/gmail
< smtp_sasl_security_options = noanonymous
< smtp_sasl_tls_security_options = noanonymous
< smtp_sasl_mechanism_filter = plain
< smtp_use_tls = yes
```

## メール起動
```
   67  systemctl start postfix
   68  systemctl reload postfix
   69  systemctl enable --now postfix
```

## 不要かもしれない転送設定の確認
```
   70  echo test | mail -s "test mail" kuhataku@gmail.com

   71  cp -a /etc/aliases{,.org}
   72  vi /etc/aliases
   73  diff /etc/aliases{,.org}

[root@mysv986 ~]# diff /etc/aliases{,.org}
98,101d97
< 
< centos:		root
< remoteadmin:	root
< root:		kuhataku@gmail.com

   74  newaliases
   58  echo "test mail" | mail root
   59  echo "test mail" | mail centos
   60  echo "test mail" | mail remoteadmin
```

## Web 管理コンソールを利用する
```
  165  systemctl enable --now cockpit.socket

https://mysv986.com:9090/

DELの現状のfirefoxやcromeでは「replaceAll が不明」と表示されてしまうため
Debian11をインストールする時に本物のfirefoxをインストールし検証する

```


