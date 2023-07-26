# 使用 Docker安装 MySQL

如果你希望 MySQL 容器的数据被持久化存储，你需要使用 Docker 的 volumes。volumes 可以用来存储和持久化 Docker 容器的数据，即使容器被删除，volumes 中的数据也不会丢失。

1. **创建 Docker volume**：

    首先，创建一个 Docker volume 来存储 MySQL 的数据。运行以下命令：

    ```bash
    sudo docker volume create mysql-data
    ```

    这将创建一个名为 `mysql-data` 的 volume。你可以选择任何你喜欢的名字。

2. **运行 MySQL 容器**：

    然后，使用 `-v` 或 `--mount` 选项运行 MySQL 容器，将刚才创建的 volume 挂载到容器的 `/var/lib/mysql` 目录。这是 MySQL 默认的数据目录。运行以下命令：

    ```bash
    sudo docker run --name mysql --restart=always -v mysql-data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -p 3306:3306 -d mysql
    ```

    或者你也可以使用 `--mount` 选项，这更清晰地显示了 volume 的源和目标：

    ```bash
    sudo docker run --name some-mysql --mount source=mysql-data,target=/var/lib/mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -p 3306:3306 -d mysql
    ```

    这里，`mysql-data:/var/lib/mysql` 或 `source=mysql-data,target=/var/lib/mysql` 将 `mysql-data` volume 挂载到容器的 `/var/lib/mysql` 目录。

现在，你运行的 MySQL 容器的数据将被存储在 `mysql-data` volume 中，即使你删除了容器，数据也会被保留。如果你想在新的 MySQL 容器中使用这些数据，只需将 volume 挂载到新容器的 `/var/lib/mysql` 目录即可。