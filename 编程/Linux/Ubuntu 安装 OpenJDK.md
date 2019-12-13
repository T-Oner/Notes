# Ubuntu 安装 OpenJDK



## 在 Ubuntu 下配置 openJDK

最新的Ubuntu 安装源默认已经没有 openJdk 了,需要自己手动添加仓库: 

1.oracle openjdk ppa source

```text
sudo add-apt-repository ppa:openjdk-r/ppa
sudo apt-get update
sudo apt-get install openjdk-7-jdk  // OpenJdk 7安装：
```

2. oracle java jdk ppa source

```text
sudo add-apt-repository ppa:webupd8team/java
sudo apt-get update
#JDK6 ：
sudo apt-get install oracle-java6-installer
#JDK 7：
sudo apt-get install oracle-java7-installer
#JDK 8:
sudo apt-get install oracle-java8-installer
```

如果安装成功之后还是不能用可能不有多个版本，选的不对

```text
sudo update-alternatives --config java
sudo update-alternatives --config javac
选出正确的版本
```

