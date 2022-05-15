> 本質的な話ではないが、  
> CentOSはyumからdufに代わっているので  
> 変なエラー表示対策

```
    1  curl -fsSL https://rpm.nodesource.com/setup_lts.x | bash -
   76  sed -iBAK '/^failovermethod=/d' /etc/yum.repos.d/*.repo
   77  sudo sed '/^failovermethod=/d' /etc/yum.repos.d/*.repo
   78  dnf upgrade

    1  curl -fsSL https://rpm.nodesource.com/setup_lts.x | bash -
    3  yum install -y nodejs
    4  node -v
    5  npm -v
```