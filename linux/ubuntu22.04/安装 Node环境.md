# 在 Ubuntu22.04 系统上安装 Node环境

在 Ubuntu 22.04 上安装 Node.js，你可以选择从 Ubuntu 的默认仓库安装，或者从 NodeSource 仓库（Node.js 的官方仓库）安装。下面是这两种方法的步骤：

## 方法一：从 Ubuntu 默认仓库安装

### 1. 更新你的包列表，使用以下命令：

```shell
sudo apt update
```

### 2. 安装 Node.js，使用以下命令：

```shell
sudo apt install nodejs
```

### 3. 验证 Node.js 是否已经安装成功：

```shell
nodejs --version
```

这个方法会安装比较旧的 Node.js 版本。如果你需要最新版本的 Node.js，你可以考虑以下的第二种方法。

## 方法二：从 NodeSource 仓库安装

### 1. 首先，你需要在终端中安装 `curl`：

```shell
sudo apt install curl
```

### 2. 使用 curl 从 NodeSource 设置你的 Node.js 源。以下命令将设置为最新的 LTS 版本：

```shell
curl -sL https://deb.nodesource.com/setup_lts.x | sudo -E bash -
```

如果你想安装最新的当前版本，你可以使用下面的命令：

```shell
curl -sL https://deb.nodesource.com/setup_current.x | sudo -E bash -
```

### 3. 然后，你就可以安装 Node.js 了：

```shell
sudo apt install nodejs
```

### 4. 验证 Node.js 是否已经安装成功：

```shell
node --version
```

## 安装指定版本
如果你想在 Ubuntu 22.04 上安装特定版本的 Node.js，可以使用 Node Version Manager (nvm)。这是一个在单个用户的 Unix 系统上管理 Node.js 版本的工具。以下是安装和使用 nvm 的步骤：

### 1. 下载并安装 nvm。你可以使用 curl 或 wget。这里是一个使用 curl 的例子：

```shell
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.38.0/install.sh | bash
```

注意，上面的 URL 是 nvm v0.38.0 的安装脚本，你可能需要根据实际情况修改为最新版本的 URL。
官网地址：https://github.com/nvm-sh/nvm

### 2. 安装脚本会将 nvm 的相关代码添加到你的 `~/.bash_profile`, `~/.zshrc`, `~/.profile`, 或 `~/.bashrc` 文件中。你可以通过运行以下命令来使它立即生效：

```shell
source ~/.bashrc
```

### 3. 安装完成后，你可以通过以下命令来验证 nvm 是否已经安装成功：

```shell
nvm --version
```

### 4. 现在，你可以使用 nvm 来安装你想要的 Node.js 版本。例如，要安装 Node.js v14.17.0，你可以运行：

```shell
nvm install 14.17.0
```

### 5. 你可以使用 nvm 切换不同版本的 Node.js。例如，要切换到 v14.17.0，你可以运行：

```shell
nvm use 14.17.0
```

### 6. 最后，你可以通过以下命令来验证你的 Node.js 版本：

```bash
node --version

# 如果 Node.js 已经成功安装，这个命令会输出你当前使用的 Node.js 版本。
```

