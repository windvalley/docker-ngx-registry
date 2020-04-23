# Deployment

1. clone项目并进入目录
```bash
git clone git@...
cd docker-ngx-registry
```
2. 将`hub.sre.im`解析到宿主机IP上, 或者绑定host

3. 自签证书(或使用权威颁发的证书)
```bash
# 注意Common Name要填hub.sre.im
openssl req \
    -newkey rsa:4096 -nodes -sha256 -keyout auth/hub.sre.im.key \
    -x509 -days 365 -out auth/hub.sre.im.crt
```

4. 生成basic认证需要的用户名和密码
```bash
docker container run --rm \
    --entrypoint htpasswd registry -Bbn sre.im 123456 \
    > auth/nginx.htpasswd
```

5. 启动和关闭
```bash
# 启动
docker-compose up -d
# 关闭
docker-compose down
```

# Test

```bash
# 拉取官方镜像到本地;
# 实际上docker hub上的镜像名称python:3.8, 本质上是docker.io/library/python:3.8, 缺省忽略了前缀而已.
docker image pull python:3.8

# 登录到私有镜像仓库
docker login hub.sre.im -usre.im -p123456 或 docker login hub.sre.im
# 退出登录的命令
docker logout hub.sre.im

# 上传镜像到私有镜像仓库hub.sre.im
docker image tag python:3.8 hub.sre.im/python:3.8
docker image push $_

# 从私有镜像仓库拉取镜像
docker image pull hub.sre.im/python:3.8

# 查看私有镜像仓库有哪些镜像
curl -L --cacert /public/hub.sre.im.crt -u sre.im:123456 https://hub.sre.im/v2/_catalog

# 查看镜像python有哪些tag
curl -L --cacert /public/hub.sre.im.crt -u sre.im:123456 https://hub.sre.im/v2/python/tags/list

# 查看registry其他的rest api
https://docs.docker.com/registry/spec/api/
```

# Ref

https://docs.docker.com/registry/

https://docs.docker.com/registry/spec/api/

