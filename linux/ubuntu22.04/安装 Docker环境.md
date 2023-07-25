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

### 8. 非 root用户运行
如果你希望非 `root` 用户也能运行 Docker 命令，你需要将用户添加到 `docker` 用户组。这样做的结果是，被添加的用户拥有了对 Docker daemon 的权限，也就等同于获得了 root 权限，因为他们现在可以运行任何 Docker 容器。所以，只有你信任的用户才应该被添加到 `docker` 组。

这里是添加用户到 `docker` 组的步骤：

1. **创建 docker 组（如果它不存在）**：

    首先，你需要确认 `docker` 组是否存在。如果不存在，你可以使用以下命令来创建它：

    ```bash
    sudo groupadd docker
    ```

2. **将用户添加到 docker 组**：

    然后，你可以使用以下命令将用户添加到 `docker` 组。把 `username` 替换为你要添加的用户名：

    ```bash
    sudo usermod -aG docker username
    ```

3. **验证用户是否已经添加到 docker 组**：

    你可以使用以下命令来检查用户是否已经成功添加到 `docker` 组：

    ```bash
    groups username
    ```

    如果 `docker` 在输出的组列表中，那就表示用户已经被成功添加到 `docker` 组。

4. **重启 Docker 服务**：

    运行下面的命令来重启 Docker 服务：

    ```bash
    sudo systemctl restart docker
    ```

5. **注销并重新登录**：

    最后，你需要注销并重新登录用户，以便让新的组设置生效。之后，你就可以以非 root 用户身份运行 Docker 命令了。

请注意，添加用户到 `docker` 组等同于赋予他们 root 权限，因为他们可以在 Docker 容器中运行任何命令。只有你完全信任的用户才应被添加到 `docker` 组。

### 9. 在中国大陆的网络环境下，你可能会发现 Docker 的默认镜像仓库速度较慢。幸运的是，你可以设置 Docker 使用国内的镜像源，以提高拉取镜像的速度。

下面是如何设置 Docker 使用阿里云镜像源的步骤：

1. **登录阿里云 Docker 镜像服务**：

    首先，你需要有一个阿里云账号，并登录阿里云 Docker 镜像服务。你可以访问这个链接：https://cr.console.aliyun.com/ ，然后点击 "镜像加速器"。

2. **获取加速器地址**：

    在镜像加速器页面，你会看到一个专属加速器地址，如 `https://xxxxxx.mirror.aliyuncs.com`。请复制这个地址，稍后我们会用到。

3. **配置 Docker daemon**：

    在你的 Docker 宿主机上，打开或创建 Docker daemon 的配置文件。这个文件通常位于 `/etc/docker/daemon.json`。如果这个文件或目录不存在，你可以创建它：

    ```bash
    sudo mkdir -p /etc/docker
    sudo nano /etc/docker/daemon.json
    ```

    在 `daemon.json` 文件中，添加或修改 `registry-mirrors` 键，使用你刚刚复制的阿里云加速器地址。例如：

    ```json
    {
      "registry-mirrors": ["https://xxxxxx.mirror.aliyuncs.com"]
    }
    ```

    保存并关闭文件。

4. **重启 Docker daemon**：

    最后，你需要重启 Docker daemon 使设置生效：

    ```bash
    sudo systemctl daemon-reload
    sudo systemctl restart docker
    ```

这样，你的 Docker 就被设置为使用阿里云的镜像源了，拉取镜像的速度应该会有所提高。

除了阿里云，还有其他一些国内的 Docker 镜像源，如 网易 163 的镜像源、DaoCloud 的镜像源等，使用方法类似。请注意，使用这些镜像源需要遵守相应服务的使用条款和策略。