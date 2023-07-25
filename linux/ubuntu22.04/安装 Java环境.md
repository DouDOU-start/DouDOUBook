# 在 Ubuntu22.04 系统上安装 Java环境

### 1. 添加 OpenJDK 的 PPA。OpenJDK 团队提供了一个专门的 PPA，可以方便地安装更高版本的 OpenJDK。运行以下命令添加这个 PPA：

``` shell 
sudo add-apt-repository ppa:openjdk-r/ppa
```

### 2. 添加了 PPA 之后，更新包列表，使用以下命令：

```shell
sudo apt update
```

### 3. 可以通过 apt 来安装 OpenJDK 17，使用以下命令：

```shell
sudo apt install openjdk-17-jdk
```

### 4. 安装完成后，通过运行以下命令来验证安装：

```shell
java -version

# 如果 Java 已经成功安装，这个命令会输出你已安装的 Java 版本信息。
```

### 5. 如果你的系统中有多个版本的 Java
你可能需要使用 `update-alternatives` 命令来管理和选择默认的 Java 版本。例如，你可以运行以下命令来配置默认的 Java 和 javac 版本：

```shell
sudo update-alternatives --config java
sudo update-alternatives --config javac

#然后按照提示选择你希望设为默认的版本即可。
```

