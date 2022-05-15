## Apacheインストール
```
 153  dnf -y install httpd
 133  dnf -y install mod_ssl
```

## Apache設定
```
  233  echo 'mysv986.com' > /var/www/html/index.html
   81  cp -a /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf.org
   82  vi /etc/httpd/conf/httpd.conf
   83  diff /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf.org

[root@mysv986 ~]# diff /etc/httpd/conf/httpd.conf /etc/httpd/conf/httpd.conf.org
99d98
< ServerName www.mysv986.com:80
148,149c147
<     #Options Indexes FollowSymLinks
<     Options Includes ExecCGI FollowSymLinks
---
>     Options Indexes FollowSymLinks
156,157c154
<     #AllowOverride None
<     AllowOverride All
---
>     AllowOverride None
301d297
<     AddHandler cgi-script .cgi .pl
361,379d356
< 
< <VirtualHost *:80>
<     ServerName mysv986.com
<     ServerAlias www.mysv986.com
<     DocumentRoot "/var/www/html"
< 
<     <Directory "/var/www/html">
<         Options FollowSymLinks
<         AllowOverride None
<         Require all granted
<     </Directory>
< 
< RewriteEngine on
< RewriteCond %{SERVER_NAME} =www.mysv986.com [OR]
< RewriteCond %{SERVER_NAME} =mysv986.com
< RewriteRule ^ https://%{SERVER_NAME}%{REQUEST_URI} [END,NE,R=permanent]
< </VirtualHost>
```

## 不要な設定を消す
```
  485  mv -i /etc/httpd/conf.d/autoindex.conf /etc/httpd/conf.d/autoindex.conf.org
  486  mv -i /etc/httpd/conf.d/userdir.conf /etc/httpd/conf.d/userdir.conf.org
  487  mv -i /etc/httpd/conf.d/welcome.conf /etc/httpd/conf.d/welcome.conf.org

  170  systemctl start httpd
  171  systemctl enable httpd
   94  systemctl status httpd
```

## 証明書の取得
https://linux-svr.com/tips/SSL%E8%A8%BC%E6%98%8E%E6%9B%B8/33.php
```
       dnf -y install epel-release
  120  dnf -y install snapd
  121  systemctl enable --now snapd.socket
  105  ln -s /var/lib/snapd/snap /snap
  107  reboot
  108  snap install core
  108  snap install core
  109  snap refresh core
```

## certbotインストール
```
  112  rm -rf /usr/bin/certbot
  138  snap install --classic certbot
  140  ln -s /snap/bin/certbot /usr/bin/certbot
  141  ls -la /usr/bin/certbot
  142  ls -la /snap/bin/certbot
  143  certbot --version
  178  certbot --apache

[root@mysv986 ~]# certbot --apache
Saving debug log to /var/log/letsencrypt/letsencrypt.log
Enter email address (used for urgent renewal and security notices)
 (Enter 'c' to cancel): kuhataku@gmail.com

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Please read the Terms of Service at
https://letsencrypt.org/documents/LE-SA-v1.2-November-15-2017.pdf. You must
agree in order to register with the ACME server. Do you agree?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: Y

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Would you be willing, once your first certificate is successfully issued, to
share your email address with the Electronic Frontier Foundation, a founding
partner of the Let's Encrypt project and the non-profit organization that
develops Certbot? We'd like to send you email about our work encrypting the web,
EFF news, campaigns, and ways to support digital freedom.
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
(Y)es/(N)o: Y
Account registered.

Which names would you like to activate HTTPS for?
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
1: mysv986.com
2: www.mysv986.com
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
Select the appropriate numbers separated by commas and/or spaces, or leave input
blank to select all options shown (Enter 'c' to cancel): 2
Requesting a certificate for www.mysv986.com

Successfully received certificate.
Certificate is saved at: /etc/letsencrypt/live/www.mysv986.com/fullchain.pem
Key is saved at:         /etc/letsencrypt/live/www.mysv986.com/privkey.pem
This certificate expires on 2021-12-28.
These files will be updated when the certificate renews.
Certbot has set up a scheduled task to automatically renew this certificate in the background.

Deploying certificate
Successfully deployed certificate for www.mysv986.com to /etc/httpd/conf/httpd-le-ssl.conf
Congratulations! You have successfully enabled HTTPS on https://www.mysv986.com
We were unable to subscribe you the EFF mailing list because your e-mail address appears to be invalid. You can try again later by visiting https://act.eff.org.

- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
If you like Certbot, please consider supporting our work by:
 * Donating to ISRG / Let's Encrypt:   https://letsencrypt.org/donate
 * Donating to EFF:                    https://eff.org/donate-le
- - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - - -
```

## apache再起動
```
  246  systemctl restart httpd
  247  systemctl status httpd
```

## mod_sslの設定
```
  152  cp -a /etc/httpd/conf.d/ssl.conf /etc/httpd/conf.d/ssl.conf.org
  186  vi /etc/httpd/conf.d/ssl.conf
  215  diff /etc/httpd/conf.d/ssl.conf /etc/httpd/conf.d/ssl.conf.org 

[root@mysv986 ~]# diff /etc/httpd/conf.d/ssl.conf /etc/httpd/conf.d/ssl.conf.org 
26,28d25
< SSLRandomSeed startup file:/dev/urandom  256
< SSLRandomSeed connect builtin
< 
39,70d35
< SSLStrictSNIVHostCheck off
< 
< SSLProtocol -all +TLSv1.2 +TLSv1.3
< 
< SSLCipherSuite "ECDHE-ECDSA-AES128-GCM-SHA256 \
< ECDHE-ECDSA-AES256-GCM-SHA384 \
< ECDHE-ECDSA-AES128-SHA \
< ECDHE-ECDSA-AES256-SHA \
< ECDHE-ECDSA-AES128-SHA256 \
< ECDHE-ECDSA-AES256-SHA384 \
< ECDHE-RSA-AES128-GCM-SHA256 \
< ECDHE-RSA-AES256-GCM-SHA384 \
< ECDHE-RSA-AES128-SHA \
< ECDHE-RSA-AES256-SHA \
< ECDHE-RSA-AES128-SHA256 \
< ECDHE-RSA-AES256-SHA384 \
< DHE-RSA-AES128-GCM-SHA256 \
< DHE-RSA-AES256-GCM-SHA384 \
< DHE-RSA-AES128-SHA \
< DHE-RSA-AES256-SHA \
< DHE-RSA-AES128-SHA256 \
< DHE-RSA-AES256-SHA256 \
< EDH-RSA-DES-CBC3-SHA"
< 
< SSLHonorCipherOrder on
< SSLCompression off
< 
< SSLUseStapling On
< 
< SSLStaplingReturnResponderErrors off
< SSLStaplingCache shmcb:/run/httpd/stapling_cache(128000)
< 
78,79c43,44
< DocumentRoot "/var/www/html"
< ServerName www.example.com:443
---
> #DocumentRoot "/var/www/html"
> #ServerName www.example.com:443
237a203
> 

  188  apachectl restart
```

## 動作確認
```
  192  ping www.mysv986.com
  193  ping mysv986.com
  194  wget http://www.mysv986.com
  195  wget https://www.mysv986.com
```

## certbot自動更新
```
  251  certbot renew --dry-run
  252  certbot renew --renew-hook "systemctl restart httpd" --renew-hook "systemctl restart postfix" --renew-hook "systemctl restart dovecot" --renew-hook "service vsftpd restart" --renew-hook "systemctl restart webmin" -q >> /var/log/certbot-renew.log | mail -s "CERTBOT Renewals" root@mysv986.com < /var/log/certbot-renew.log
  252  certbot renew --renew-hook "systemctl restart httpd" --renew-hook "systemctl restart postfix" --renew-hook "systemctl restart dovecot" --renew-hook "service vsftpd restart" --renew-hook "systemctl restart webmin" -q >> /var/log/certbot-renew.log | mail -s "CERTBOT Renewals" root@mysv986.com < /var/log/certbot-renew.log
  258  reboot
```
  
## Webサーバーだけ使用する場合
```
  263  dnf -y install mailx
  252  certbot renew --renew-hook "systemctl restart httpd"  -q >> /var/log/certbot-renew.log | mail -s "CERTBOT Renewals" root@mysv986.com < /var/log/certbot-renew.log
  252  certbot renew --renew-hook "systemctl restart httpd"  -q >> /var/log/certbot-renew.log | mail -s "CERTBOT Renewals" root@mysv986.com < /var/log/certbot-renew.log
  258  reboot
```