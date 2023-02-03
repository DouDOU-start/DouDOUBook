# Git配置SSH-Key

**概要**

本地生成公钥，将本地公钥配置到远程github，这个公钥相当于本地和远程github的连接桥梁。

**配置**

1. 本地配置

   ```shell
   # 检查用户名和邮箱是否配置
   $ git config --global --list
   
   # 如未配置，则执行以下命令进行配置
   $ git config --global user.name "你的用户名"
   $ git config --global user.email "你的邮箱"
   
   # 执行以下命令生成密钥，需进行3-4次确认，直接回车即可：
   $ ssh-keygen -t rsa -C "你的邮箱"
   
   成功后会在~/.ssh/路径下生成两个名为id_rsa和id_rsa.pub的文件
   ```

2. 添加公钥到远程仓库（github）

   - 打开你的github，进入配置页：Settings -- SSH and GPG keys
   - 选择New SSH key 添加本地生成的ssh密钥，密钥为之前生成id_rsa.pub文件的内容，拷贝到key下面的输入框，并为这个key定义一个Title（用于区分不同主机），最后Add SSH key。

3. 测试是否配置成功

   ```shell
   # 用ssh连接git：
   $ ssh -T git@github.com
   
   # 配置成功结果
   # Hi xxxx! You've successfully authenticated, but GitHub does not provide shell access.
   ```

​	