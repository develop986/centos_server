# OpenJDKと、Java関連ソフトインストール

## OpenJDK 17

```
# dnf -y install java-17-openjdk java-17-openjdk-devel 

# cat > /etc/profile.d/java.sh <<'EOF' 
export JAVA_HOME=$(dirname $(dirname $(readlink $(readlink $(which java)))))
export PATH=$PATH:$JAVA_HOME/bin
EOF

# source /etc/profile.d/java.sh

# java --version
```

## Javaバージョン切り替え

```
# alternatives --config java
# alternatives --config javac

# java --version
# javac --version
```

## Gradle

- https://gradle.org/install/
- https://services.gradle.org/distributions/

```
$ wget https://services.gradle.org/distributions/gradle-7.4.2-bin.zip
# mkdir /opt/gradle
# unzip -d /opt/gradle /home/centos/gradle-7.4.2-bin.zip
# ls /opt/gradle/gradle-7.4.2
LICENSE  NOTICE  README  bin  init.d  lib

# vi /etc/profile.d/gradle.sh
export PATH=$PATH:/opt/gradle/gradle-7.4.2/bin

# source /etc/profile
# gradle -v
```



