# 使用 Docker安装 Minio

1. **拉取镜像**
    ```shell
    docker pull minio/minio
    ```

2. **创建 Docker volume**
   ```shell
   docker volume create minio-data
   ```

3. **运行 Minio容器**
   ```shell
   docker run -itd \
   -p 9000:9000 \
   -p 9001:9001 \
   -v minio-data:/data \
   -e "MINIO_ROOT_USER=minio" \
   -e "MINIO_ROOT_PASSWORD=minio@8888" \
   --restart=always \
   --name minio \
   minio/minio \
   server /data \
   --address ":9000" \
   --console-address ":9001" 

   # 控制台访问地址：http://127.0.0.1:9001/login
   ```