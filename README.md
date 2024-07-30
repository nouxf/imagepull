# imagepull
Download Image
* 通过github action 将国外镜像同步到国内的公有云仓库

**效果示例**

`docker pull k8s.gcr.io/kube-apiserver:v1.21.11`  

↓↓↓

`docker pull ${REG_URL}/${REG_REPO}/kube-apiserver:v1.21.11`

# 仓库配置
`在Git仓库中添加镜像仓库登录信息: 
[Settings]-->[Security]-->[Secrets]`

> **示例：** registry.cn-hangzhou.aliyuncs.com/public/alpine:3.15.0
- REG_URL: 仓库地址, 示例为：registry.cn-hangzhou.aliyuncs.com
- REG_USER: 仓库登录的用户名。
- REG_PWD: 登录仓库的密码。
- REG_REPO: 仓库名，自建的仓库名字，示例为 public。

# 使用方法
> **将镜像地址按行写入到image.list文件中，然后提交即可**
```shell
cat <<EOF >image.list
k8s.gcr.io/kube-apiserver:v1.21.11
k8s.gcr.io/kube-controller-manager:v1.21.11
k8s.gcr.io/kube-scheduler:v1.21.11
k8s.gcr.io/kube-proxy:v1.21.11
k8s.gcr.io/pause:3.4.1
k8s.gcr.io/etcd:3.4.13-0
k8s.gcr.io/coredns/coredns:v1.8.0
EOF
git commit -am "add image"
git push
```

# 下载和重Tag
**示例脚本**
```shell
# 修改成对应的镜像仓库地址
REG_URL="registry.cn-hangzhou.aliyuncs.com"
REG_REPO="public"
for image in $(<image.list);do 
    imagename=`echo ${image##*/} | awk -F@ '{print $1}'`
    pri_image="${REG_URL}/${REG_REPO}/${imagename}"
    # 1.下载镜像
    docker pull ${pri_image} | continue
    docker tag ${pri_image} ${image} | continue
done 
```
