
## 离线安装

### k3s 软件包事先下载

https://github.com/rancher/k3s/releases

[📎k3s.zip](https://lark-assets-prod-aliyun.oss-cn-hangzhou.aliyuncs.com/yuque/0/2019/zip/176280/1572440584708-204f21e3-4962-4769-b97e-7e9ec873e557.zip?OSSAccessKeyId=LTAIX2KDHwZymFhr&Expires=1590241656&Signature=b2idkfeld4jbVGv34wlv7xTyhmw%3D&response-content-disposition=attachment%3Bfilename*%3DUTF-8%27%27k3s.zip)

[📎k3s-airgap-images-amd64.tar](https://github.com/rancher/k3s/releases/download/v1.17.5%2Bk3s1/k3s-airgap-images-amd64.tar)

```
wget https://raw.githubusercontent.com/rancher/k3s/master/install.sh
unzip k3s.zip 
cp k3s /usr/local/bin/
chmod +x /usr/local/bin/k3s
mkdir -p /var/lib/rancher/k3s/agent/images/
cp k3s-airgap-images-amd64.tar  /var/lib/rancher/k3s/agent/images/
docker load -i k3s-airgap-images-amd64.tar
```
> 修改默认容器
```
vi /etc/systemd/system/k3s.service
ExecStart=/usr/bin/k3s \
server --docker\              //容器选择docker，替换默认的containerd
```

### [准备好 pause 镜像](README.md##pause)

### 配置安装
```
export INSTALL_K3S_SKIP_DOWNLOAD=true     //设置跳过下载k3s二进制文件
export INSTALL_K3S_BIN_DIR=/usr/bin       //设置k3s安装目录
./install.sh       //自动建立service服务及软连接  kubectl ctr  ....

systemctl status k3s    //服务运行状态
journalctl -u k3s -f    //查看日志
```
---

netstat -tnlp //查看端口情况

尝试一下添加deployment。使用官方提供的yaml template
```
k3s kubectl create -f https://kubernetes.io/docs/user-guide/nginx-deployment.yaml
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





参考文档：

https://abser.top/index.php/2019/11/28/k3s-by-rancher-%c2%b7-%e8%af%ad%e9%9b%80/

https://www.yuque.com/dimension/shu93f/k3sbyrancher