## 1. 下载

```
git clone -b cicd https://gitlab.servicechain.newtouch.com/tt-chain/ttchain.git
```

账号: zhiwei.li@newtouch.com	密码：lzw980618



## 2. 使用docker编译项目,打包

```
cd ttchain/

sh dockerize.sh
```



## 3. 上传docker

```
docker login https://registry.onecloud.newtouch.com
admin  Accp1234

docker push registry.onecloud.newtouch.com/ttc/ttchain:latest

docker push registry.onecloud.newtouch.com/ttc/substrate-front:59.80.34.156

```



## 4. 运行docker

```
cd ttchain/docker
#服务器1
docker-compose -f docker-compose1.yml up

#服务器2
docker-compose -f docker-compose2.yml up

#服务器3
docker-compose -f docker-compose3.yml up
```

