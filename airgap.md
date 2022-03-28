
# 离线安装

## 手动部署
## k3s 软件包事先下载

- https://github.com/rancher/k3s/releases
- http://mirror.rancher.cn 国内

```
.
├── k3s
├── k3s-airgap-images-amd64.tar
├── k3s-images.txt
└── k3s-install.sh
```
k3s文件放到 /usr/local/bin/k3s下

将 tar 文件放在images目录下，例如：
```
sudo mkdir -p /var/lib/rancher/k3s/agent/images/
sudo cp ./k3s-airgap-images-$ARCH.tar /var/lib/rancher/k3s/agent/images/
```

### 配置安装
```
server:
    INSTALL_K3S_SKIP_DOWNLOAD=true ./k3s-install.sh  // 主要是这个

systemctl status k3s    //服务运行状态
journalctl -u k3s -f    //查看日志

agent:
    INSTALL_K3S_SKIP_DOWNLOAD=true K3S_URL=https://myserver:6443 K3S_TOKEN=mynodetoken ./install.sh
```


---

netstat -tnlp //查看端口情况

尝试一下添加deployment。使用官方提供的yaml template
```
kubectl create -f https://kubernetes.io/docs/user-guide/nginx-deployment.yaml
```
通过k3s kubectl get deployment和k3s kubectl get pods查看创建的pods


### 添加node
```
查看node
k3s kubectl get node

查看token
cat /var/lib/rancher/k3s/server/node-token

k3s agent -s ${YOUR_SERVER_IP} -t ${NODE_TOKEN}
```

---
配置私有镜像:
```
mkdir -p /etc/rancher/k3s/
/etc/rancher/k3s/registries.yaml 

mirrors:
  docker.io:
    endpoint:
      - "http://mycustomreg.com:5000"
configs:
  "mycustomreg:5000":
    auth:
      username: xxxxxx # 这是私有镜像仓库的用户名
      password: xxxxxx # 这是私有镜像仓库的密码
```



参考文档：

https://abser.top/index.php/2019/11/28/k3s-by-rancher-%c2%b7-%e8%af%ad%e9%9b%80/

https://www.yuque.com/dimension/shu93f/k3sbyrancher

[K3S生产案例分享：逾百台工控机的应用实践](https://www.freesion.com/article/4741181333/)