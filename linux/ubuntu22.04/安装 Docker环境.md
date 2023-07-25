# 在 Ubuntu22.04 系统上安装 Docker环境

### 1. 更新软件包列表：

```shell
sudo apt-get update
```

### 2. 安装必要的一些软件包：

Docker 需要 `apt-transport-https`，`ca-certificates`，`curl`，`software-properties-common`，`lsb-release` 这些软件包，以确保下载软件包时使用 HTTPS，并管理第三方软件源。使用以下命令安装这些软件包：

```shell
sudo apt-get install apt-transport-https ca-certificates curl software-properties-common lsb-release
```

### 3. 添加 Docker 的官方 GPG 密钥：

你可以使用以下命令下载并添加 Docker 的官方 GPG 密钥：

```shell
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
```
### 4. 添加 Docker 的 APT 源：

接下来，需要向你的 APT 源添加 Docker 的官方存储库。这将使你能够从 Docker 官方安装 Docker:

```shell
sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
```

### 5. 更新软件包列表：

```shell
sudo apt-get update
```

### 6. 安装 Docker CE：

现在，你可以安装 Docker CE（社区版）了：

```shell
sudo apt-get install docker-ce
```

### 7. 验证 Docker 安装：

安装完成后，你可以运行以下命令来验证 Docker 是否正确安装：

```shell
sudo docker run hello-world

# 如果 Docker 安装正确，你应该能看到一个消息，说 Docker 正在下载 `hello-world` 镜像，并输出一个欢迎消息。
```