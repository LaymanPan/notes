# K8s笔记

![image-20210630205645815](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210630205645815.png)

![image-20210630210009368](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210630210009368.png)

## 组件

APISERVER: 所有服务的访问统一入口

ControllerManager: 维持副本期望数目

Scheduler: 负责介绍任务，选择合适的节点分配任务

ETCD: 键值对数据库 储存K8S集群所有重要信息（持久化）

Kubelet: 直接跟容器引擎交互实现容器的生命周期管理

COREDONS：可以为集群中的SVC创建一个域名IP对应关系解析

DASHBOARD：给　K8S集群提供一个B/S结构访问体系

INGRESS CONTROLLER：官方只能实现四层代理， INGRESS 可以实现7层代理

FEDERATION：提供一个可以跨集群中心多K8S统一管理功能

PROMETHEUS：提供K8S集群的监控能力

ELK：提供K8S集群日志统一分析接入平台

## 环境搭建方式-kubeadm

官方地址：

https://kubernetes.io/docs/reference/setup-tools/kubeadm/kubeadm/ 

https://kubernetes.io/docs/setup/production-environment/tools/kubeadm/install-kubeadm/ 

## 部署环境要求

```shell
# 安装前提
（1）一台或多台机器，操作系统CentOS 7.x-86_x64
（2）硬件配置：内存2GB或2G+，CPU 2核或CPU 2核+；
（3）集群内各个机器之间能相互通信；
（4）集群内各个机器可以访问外网，需要拉取镜像；(可选)
（5）禁止swap分区；

# 关闭防火墙
[root@192 ~]# systemctl stop firewalld
[root@192 ~]# systemctl disable firewalld

# 关闭selinux
sed -i 's/enforcing/disabled/' /etc/selinux/config  #永久
setenforce 0  #临时

# 关闭swap
sed -ri 's/.*swap.*/#&/' /etc/fstab #永久
swapoff -a #临时

# 在master添加hosts
cat >> /etc/hosts << EOF
192.168.172.134 k8smaster
192.168.172.135 k8snode
EOF

# 设置网桥参数
cat > /etc/sysctl.d/k8s.conf << EOF
net.bridge.bridge-nf-call-ip6tables = 1
net.bridge.bridge-nf-call-iptables = 1
EOF
sysctl --system  #生效

# 时间同步
yum install ntpdate -y
ntpdate time.windows.com
```

## 安装具体步骤

> 配置Docker

```shell
所有服务器节点安装 Docker/kubeadm/kubelet/kubectl
Kubernetes 默认容器运行环境是Docker，因此首先需要安装Docker

# 安装 Docker 
更新docker的yum源
yum install wget -y
wget https://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo -O /etc/yum.repos.d/docker-ce.repo
安装指定版本的docker：
yum install docker-ce-19.03.13 -y

# 配置加速器加速下载 （https://cr.console.aliyun.com/）
/etc/docker/daemon.json
{
"registry-mirrors": ["https://registry.docker-cn.com"]
}
https://gg3gwnry.mirror.aliyuncs.com
systemctl enable docker.service

# 搭建：kubeadm、kubelet、kubectl
cat > /etc/yum.repos.d/kubernetes.repo << EOF
[kubernetes]
name=Kubernetes
baseurl=https://mirrors.aliyun.com/kubernetes/yum/repos/kubernetes-el7-x86_64
enabled=1
gpgcheck=0
repo_gpgcheck=0
gpgkey=https://mirrors.aliyun.com/kubernetes/yum/doc/yum-key.gpg https://mirrors.aliyun.com/kubernetes/yum/doc/rpm-package-key.gpg
EOF

#安装 kubeadm，kubelet 和 kubectl 

```

> 配置K8s

```shell
[root@k8sMaster ~]# yum install kubelet-1.19.4 kubeadm-1.19.4 kubectl-1.19.4 -y

[root@k8sMaster ~]# systemctl enable kubelet.service

[root@k8sMaster ~]# yum list installed | grep kubelet
kubelet.x86_64                  1.19.4-0                       @kubernetes      
[root@k8sMaster ~]# yum list installed | grep kubeadm
kubeadm.x86_64                  1.19.4-0                       @kubernetes      
[root@k8sMaster ~]# yum list installed | grep kubectl
kubectl.x86_64                  1.19.4-0                       @kubernetes      

```

**重启Centos**

**部署K8s主节点**

```shell
# --apiserver-advertise-address:本机服务器地址
[root@k8sMaster ~]# kubeadm init --apiserver-advertise-address=192.168.133.129 --image-repository registry.aliyuncs.com/google_containers --kubernetes-version v1.19.4 --service-cidr=10.96.0.0/12 --pod-network-cidr=10.244.0.0/16

W0627 13:48:46.747648   13950 configset.go:348] WARNING: kubeadm cannot validate component configs for API groups [kubelet.config.k8s.io kubeproxy.config.k8s.io]
[init] Using Kubernetes version: v1.19.4
[preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
[preflight] Pulling images required for setting up a Kubernetes cluster
[preflight] This might take a minute or two, depending on the speed of your internet connection
[preflight] You can also perform this action in beforehand using 'kubeadm config images pull'
[certs] Using certificateDir folder "/etc/kubernetes/pki"
[certs] Generating "ca" certificate and key
[certs] Generating "apiserver" certificate and key
[certs] apiserver serving cert is signed for DNS names [k8smaster kubernetes kubernetes.default kubernetes.default.svc kubernetes.default.svc.cluster.local] and IPs [10.96.0.1 192.168.133.129]
[certs] Generating "apiserver-kubelet-client" certificate and key
[certs] Generating "front-proxy-ca" certificate and key
[certs] Generating "front-proxy-client" certificate and key
[certs] Generating "etcd/ca" certificate and key
[certs] Generating "etcd/server" certificate and key
[certs] etcd/server serving cert is signed for DNS names [k8smaster localhost] and IPs [192.168.133.129 127.0.0.1 ::1]
[certs] Generating "etcd/peer" certificate and key
[certs] etcd/peer serving cert is signed for DNS names [k8smaster localhost] and IPs [192.168.133.129 127.0.0.1 ::1]
[certs] Generating "etcd/healthcheck-client" certificate and key
[certs] Generating "apiserver-etcd-client" certificate and key
[certs] Generating "sa" key and public key
[kubeconfig] Using kubeconfig folder "/etc/kubernetes"
[kubeconfig] Writing "admin.conf" kubeconfig file
[kubeconfig] Writing "kubelet.conf" kubeconfig file
[kubeconfig] Writing "controller-manager.conf" kubeconfig file
[kubeconfig] Writing "scheduler.conf" kubeconfig file
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Starting the kubelet
[control-plane] Using manifest folder "/etc/kubernetes/manifests"
[control-plane] Creating static Pod manifest for "kube-apiserver"
[control-plane] Creating static Pod manifest for "kube-controller-manager"
[control-plane] Creating static Pod manifest for "kube-scheduler"
[etcd] Creating static Pod manifest for local etcd in "/etc/kubernetes/manifests"
[wait-control-plane] Waiting for the kubelet to boot up the control plane as static Pods from directory "/etc/kubernetes/manifests". This can take up to 4m0s
[apiclient] All control plane components are healthy after 20.005148 seconds
[upload-config] Storing the configuration used in ConfigMap "kubeadm-config" in the "kube-system" Namespace
[kubelet] Creating a ConfigMap "kubelet-config-1.19" in namespace kube-system with the configuration for the kubelets in the cluster
[upload-certs] Skipping phase. Please see --upload-certs
[mark-control-plane] Marking the node k8smaster as control-plane by adding the label "node-role.kubernetes.io/master=''"
[mark-control-plane] Marking the node k8smaster as control-plane by adding the taints [node-role.kubernetes.io/master:NoSchedule]
[bootstrap-token] Using token: 4i5gxj.kmeck5n3jiq4vmcy
[bootstrap-token] Configuring bootstrap tokens, cluster-info ConfigMap, RBAC Roles
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to get nodes
[bootstrap-token] configured RBAC rules to allow Node Bootstrap tokens to post CSRs in order for nodes to get long term certificate credentials
[bootstrap-token] configured RBAC rules to allow the csrapprover controller automatically approve CSRs from a Node Bootstrap Token
[bootstrap-token] configured RBAC rules to allow certificate rotation for all node client certificates in the cluster
[bootstrap-token] Creating the "cluster-info" ConfigMap in the "kube-public" namespace
[kubelet-finalize] Updating "/etc/kubernetes/kubelet.conf" to point to a rotatable kubelet client certificate and key
[addons] Applied essential addon: CoreDNS
[addons] Applied essential addon: kube-proxy

Your Kubernetes control-plane has initialized successfully!

To start using your cluster, you need to run the following as a regular user:

  mkdir -p $HOME/.kube
  sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
  sudo chown $(id -u):$(id -g) $HOME/.kube/config

You should now deploy a pod network to the cluster.
Run "kubectl apply -f [podnetwork].yaml" with one of the options listed at:
  https://kubernetes.io/docs/concepts/cluster-administration/addons/

Then you can join any number of worker nodes by running the following on each as root:

kubeadm join 192.168.133.129:6443 --token 4i5gxj.kmeck5n3jiq4vmcy \
    --discovery-token-ca-cert-hash sha256:168a464b6a0bec26a0bf3978def3a67e563be5846416fbaf0e73024a9d771346 


# 执行完成后 发现下载了镜像
[root@k8sMaster ~]# docker images
REPOSITORY                                                        TAG        IMAGE ID       CREATED         SIZE
registry.aliyuncs.com/google_containers/kube-proxy                v1.19.4    635b36f4d89f   7 months ago    118MB
registry.aliyuncs.com/google_containers/kube-controller-manager   v1.19.4    4830ab618586   7 months ago    111MB
registry.aliyuncs.com/google_containers/kube-apiserver            v1.19.4    b15c6247777d   7 months ago    119MB
registry.aliyuncs.com/google_containers/kube-scheduler            v1.19.4    14cd22f7abe7   7 months ago    45.7MB
registry.aliyuncs.com/google_containers/etcd                      3.4.13-0   0369cf4303ff   10 months ago   253MB
registry.aliyuncs.com/google_containers/coredns                   1.7.0      bfe3a36ebd25   12 months ago   45.2MB
registry.aliyuncs.com/google_containers/pause                     3.2        80d28bedfe5d   16 months ago   683kB

说明：
service-cidr 的选取不能和PodCIDR及本机网络有重叠或者冲突，一般可以选择一个本机网络和PodCIDR都没有用到的私网地址段，比如PODCIDR使用10.244.0.0/16, 那么service cidr可以选择10.96.0.0/12，网络无重叠冲突即可；

# 接着在master上执行
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
# 运行kubectl get nodes 获取节点信息
[root@k8sMaster ~]# kubectl get nodes
NAME        STATUS     ROLES    AGE     VERSION
k8smaster   NotReady   master   6m41s   v1.19.4

```

> node节点加入到master中

```shell
[root@K8sNode ~]# kubeadm join 192.168.133.129:6443 --token 4i5gxj.kmeck5n3jiq4vmcy \
>     --discovery-token-ca-cert-hash sha256:168a464b6a0bec26a0bf3978def3a67e563be5846416fbaf0e73024a9d771346

[preflight] Running pre-flight checks
	[WARNING IsDockerSystemdCheck]: detected "cgroupfs" as the Docker cgroup driver. The recommended driver is "systemd". Please follow the guide at https://kubernetes.io/docs/setup/cri/
	[WARNING Hostname]: hostname "k8snode" could not be reached
	[WARNING Hostname]: hostname "k8snode": lookup k8snode on 192.168.133.2:53: no such host
[preflight] Reading configuration from the cluster...
[preflight] FYI: You can look at this config file with 'kubectl -n kube-system get cm kubeadm-config -oyaml'
[kubelet-start] Writing kubelet configuration to file "/var/lib/kubelet/config.yaml"
[kubelet-start] Writing kubelet environment file with flags to file "/var/lib/kubelet/kubeadm-flags.env"
[kubelet-start] Starting the kubelet
[kubelet-start] Waiting for the kubelet to perform the TLS Bootstrap...

This node has joined the cluster:
* Certificate signing request was sent to apiserver and a response was received.
* The Kubelet was informed of the new secure connection details.

Run 'kubectl get nodes' on the control-plane to see this node join the cluster.

# 运行kubectl get nodes 获取节点状态
[root@k8sMaster ~]# kubectl get nodes
NAME        STATUS     ROLES    AGE     VERSION
k8smaster   NotReady   master   11m     v1.19.4
k8snode     NotReady   <none>   2m15s   v1.19.4

```

![image-20210627140101735](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210627140101735.png)

> 部署网络插件

```shell
# 地址已经被墙
wget https://raw.githubusercontent.com/coreos/flannel/master/Documentation/kube-flannel.yml

# 下载文件后
# 在master机上执行
[root@k8sMaster ~]# kubectl apply -f kube-flannel.yml 
podsecuritypolicy.policy/psp.flannel.unprivileged created
clusterrole.rbac.authorization.k8s.io/flannel created
clusterrolebinding.rbac.authorization.k8s.io/flannel created
serviceaccount/flannel created
configmap/kube-flannel-cfg created
daemonset.apps/kube-flannel-ds created

# kubectl get nodes 获取节点状态
NAME        STATUS   ROLES    AGE   VERSION
k8smaster   Ready    master   23m   v1.19.4
k8snode     Ready    <none>   14m   v1.19.4

# 查看运行时容器pod(一个pod容器运行多个docker容器)
[root@k8sMaster ~]# kubectl get pods -n kube-system
NAME                                READY   STATUS    RESTARTS   AGE
coredns-6d56c8448f-dkpb8            1/1     Running   0          24m
coredns-6d56c8448f-rjfcq            1/1     Running   0          24m
etcd-k8smaster                   1/1     Running   0          24m
kube-apiserver-k8smaster            1/1     Running   0          24m
kube-controller-manager-k8smaster   	1/1     Running   0          24m
kube-flannel-ds-jccwf              1/1     Running   0          92s
kube-flannel-ds-kvrh9              1/1     Running   0          92s
kube-proxy-6plmj                  1/1     Running   0          24m
kube-proxy-7wbjw                  1/1     Running   0          15m
kube-scheduler-k8smaster            1/1     Running   0          24m

```

## 实战：在K8s集群中部署一个Nginx

```shell
# 拉取nginx镜像 创建nginx 容器
[root@k8sMaster ~]# kubectl create deployment nginx --image=nginx
deployment.apps/nginx created

# 查看控制器
[root@k8sMaster ~]# kubectl get deployment
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           14m


# 查看pod
[root@k8sMaster ~]# kubectl get pod
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6799fc88d8-zjjm8   1/1     Running   0          88s

# 暴露服务器端口
[root@k8sMaster ~]# kubectl expose deployment nginx --port=80 --type=NodePort
service/nginx exposed

# 安装net-tools
[root@k8sMaster ~]# yum install net-tools -y
Loaded plugins: fastestmirror
Loading mirror speeds from cached hostfile
 * base: mirrors.huaweicloud.com
 * extras: mirrors.huaweicloud.com
 * updates: mirrors.huaweicloud.com
base                                                                        | 3.6 kB  00:00:00     
docker-ce-stable                                                            | 3.5 kB  00:00:00     
extras                                                                      | 2.9 kB  00:00:00     
kubernetes                                                                  | 1.4 kB  00:00:00     
updates                                                                     | 2.9 kB  00:00:00     
Resolving Dependencies
--> Running transaction check
---> Package net-tools.x86_64 0:2.0-0.25.20131004git.el7 will be installed
--> Finished Dependency Resolution

Dependencies Resolved

===================================================================================================
 Package              Arch              Version                              Repository       Size
===================================================================================================
Installing:
 net-tools            x86_64            2.0-0.25.20131004git.el7             base            306 k

Transaction Summary
===================================================================================================
Install  1 Package

Total download size: 306 k
Installed size: 917 k
Downloading packages:
net-tools-2.0-0.25.20131004git.el7.x86_64.rpm                               | 306 kB  00:00:00     
Running transaction check
Running transaction test
Transaction test succeeded
Running transaction
  Installing : net-tools-2.0-0.25.20131004git.el7.x86_64                                       1/1 
  Verifying  : net-tools-2.0-0.25.20131004git.el7.x86_64                                       1/1 

Installed:
  net-tools.x86_64 0:2.0-0.25.20131004git.el7                                                      

Complete!

```

**在node服务器上查看nginx镜像和容器**

![image-20210627203408563](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210627203408563.png)

**页面访问nginx**

![image-20210627204118117](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210627204118117.png)

## 实战：在K8s集群上部署Tomcat

```shell
# 创建deploy
[root@k8sMaster ~]# kubectl create deployment tomcat --image=tomcat
deployment.apps/tomcat created

# 暴露端口
[root@k8sMaster ~]# kubectl expose deployment tomcat --port=8080 --type=NodePort
service/tomcat exposed
```

![image-20210627205513656](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210627205513656.png)

## 指令

```shell
# 在master 服务器上进行查看

# 查看节点
[root@k8sMaster ~]# kubectl get nodes
NAME        STATUS   ROLES    AGE     VERSION
k8smaster   Ready    master   7h11m   v1.19.4
k8snode     Ready    <none>   7h2m    v1.19.4

# kubectl get deployment(deploy)
[root@k8sMaster ~]# kubectl get deploy
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           19m
# kubectl get pod(s)
[root@k8sMaster ~]# kubectl get pods
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6799fc88d8-zjjm8   1/1     Running   0          20m
# kubectl get service(s)
[root@k8sMaster ~]# kubectl get service
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        6h47m
nginx        NodePort    10.103.29.41   <none>        80:31177/TCP   17m
# kubectl --help

# 删除指令
# 删除service
[root@k8sMaster ~]# kubectl get service
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)          AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP          7h7m
nginx        NodePort    10.103.29.41   <none>        80:31177/TCP     37m
tomcat       NodePort    10.98.63.188   <none>        8080:31046/TCP   7m52s
[root@k8sMaster ~]# kubectl delete service  tomcat 
service "tomcat" deleted
[root@k8sMaster ~]# kubectl get service
NAME         TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP   10.96.0.1      <none>        443/TCP        7h8m
nginx        NodePort    10.103.29.41   <none>        80:31177/TCP   37m
# 删除service后 pod 和 deploy 仍然存在

# 删除nginx的控制器
[root@k8sMaster ~]# kubectl delete deploy tomcat
deployment.apps "tomcat" deleted
[root@k8sMaster ~]# kubectl get deploy
NAME    READY   UP-TO-DATE   AVAILABLE   AGE
nginx   1/1     1            1           42m
[root@k8sMaster ~]# kubectl  get pod
NAME                     READY   STATUS    RESTARTS   AGE
nginx-6799fc88d8-zjjm8   1/1     Running   0          43m

# 删除pod
[root@k8sMaster ~]# kubectl  delete pod tomcat-7d987c7694-5nhmq
Error from server (NotFound): pods "tomcat-7d987c7694-5nhmq" not found

```



## 实战：部署Springboot项目

**生成JDK镜像**

```shell
# 具体的JDK版本和JAR 包根据实际上传
[Dockerfile]
FROM centos:latest
MAINTAINER ym
ADD jdk-8u181-linux-x64.tar.gz /usr/local/java
ENV JAVA_HOME /usr/local/java/jdk1.8.0_181
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV PATH $PATH:$JAVA_HOME/bin
CMD java -version

# 生成镜像
[root@k8sMaster jdk]# docker build -t jdk1.8 .

```

**生成项目镜像**

```shell
[Dockerfile]
FROM jdk1.8
MAINTAINER ym
ADD com-fline-screen-gongan-0.0.1-SNAPSHOT.jar /opt
RUN chmod +x /opt/com-fline-screen-gongan-0.0.1-SNAPSHOT.jar
CMD java -jar /opt/com-fline-screen-gongan-0.0.1-SNAPSHOT.jar

# 生成镜像
[root@k8sMaster project]# docker build -t screen .
```

**空运行测试**

```shell
# Yaml
# --dry-run 测试不真实启动 若要真实启动 去掉--dry-run
[root@k8sMaster project]# kubectl create deployment screen --image=screen --dry-run -o yaml
W0627 21:47:51.841570   55871 helpers.go:553] --dry-run is deprecated and can be replaced with --dry-run=client.
apiVersion: apps/v1
kind: Deployment
metadata:
  creationTimestamp: null
  labels:
    app: screen
  name: screen
spec:
  replicas: 1
  selector:
    matchLabels:
      app: screen
  strategy: {}
  template:
    metadata:
      creationTimestamp: null
      labels:
        app: screen
    spec:
      containers:
      - image: screen
        name: screen
        resources: {}
status: {}
# JSON
[root@k8sMaster project]# kubectl create deployment screen --image=screen --dry-run -o json
W0627 21:48:37.981095   56138 helpers.go:553] --dry-run is deprecated and can be replaced with --dry-run=client.
{
    "kind": "Deployment",
    "apiVersion": "apps/v1",
    "metadata": {
        "name": "screen",
        "creationTimestamp": null,
        "labels": {
            "app": "screen"
        }
    },
    "spec": {
        "replicas": 1,
        "selector": {
            "matchLabels": {
                "app": "screen"
            }
        },
        "template": {
            "metadata": {
                "creationTimestamp": null,
                "labels": {
                    "app": "screen"
                }
            },
            "spec": {
                "containers": [
                    {
                        "name": "screen",
                        "image": "screen",
                        "resources": {}
                    }
                ]
            }
        },
        "strategy": {}
    },
    "status": {}
}
```

**使用Yaml/Json文件运行**

```shell
# 生成文件
[root@k8sMaster project]# kubectl create deployment screen --image=screen --dry-run -o json > deploy.json
W0627 21:50:11.850215   56719 helpers.go:553] --dry-run is deprecated and can be replaced with --dry-run=client.
[root@k8sMaster project]# ll
total 48564
-rw-r--r-- 1 root root 49720615 Jun 17 17:05 com-fline-screen-gongan-0.0.1-SNAPSHOT.jar
-rw-r--r-- 1 root root      849 Jun 27 21:50 deploy.json
-rw-r--r-- 1 root root      202 Jun 27 21:44 Dockerfile
[root@k8sMaster project]# kubectl create deployment screen --image=screen --dry-run=client -o yaml > deploy.yaml
[root@k8sMaster project]# ll
total 48568
-rw-r--r-- 1 root root 49720615 Jun 17 17:05 com-fline-screen-gongan-0.0.1-SNAPSHOT.jar
-rw-r--r-- 1 root root      849 Jun 27 21:50 deploy.json
-rw-r--r-- 1 root root      390 Jun 27 21:50 deploy.yaml
-rw-r--r-- 1 root root      202 Jun 27 21:44 Dockerfile

# 文件方式部署
[root@k8sMaster project]# kubectl apply -f deploy.yaml 
deployment.apps/screen created

```



## 安装Dashboard

**获取yaml文件**

```shell
wget https://raw.githubusercontent.com/kubernetes/dashboard/v2.0.1/aio/deploy/recommended.yaml
```

**修改文件内容**

·![image-20210628141936360](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210628141936360.png)

**应用yaml文件**

```shell
[root@k8sMaster ~]# kubectl apply -f kubernetes-dashboard.yaml
```

![image-20210628142150971](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210628142150971.png)

**查看pod**

```shell
[root@k8sMaster ~]# kubectl get pod -n kubernetes-dashboard
NAME                                         READY   STATUS    RESTARTS   AGE
dashboard-metrics-scraper-7b59f7d4df-2bnx5   1/1     Running   0          2m14s
kubernetes-dashboard-665f4c5ff-jwd6g         1/1     Running   0          2m14s
```

**浏览器访问https://192.168.133.130:30001/#/overview?namespace=default**

![image-20210628142341517](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210628142341517.png)

![image-20210628142330668](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210628142330668.png)

**生成token所需指令**

```shell
[root@k8sMaster ~]# kubectl create serviceaccount dashboard-admin -n kube-system
serviceaccount/dashboard-admin created
[root@k8sMaster ~]# kubectl create clusterrolebinding dashboard-admin --clusterrole=cluster-admin --serviceaccount=kube-system:dashboard-admin
clusterrolebinding.rbac.authorization.k8s.io/dashboard-admin created
[root@k8sMaster ~]# kubectl describe secrets -n kube-system $(kubectl -n kube-system get secret | awk '/dashboard-admin/{print $1}')
Name:         dashboard-admin-token-nnr7l
Namespace:    kube-system
Labels:       <none>
Annotations:  kubernetes.io/service-account.name: dashboard-admin
              kubernetes.io/service-account.uid: 181eb317-5507-4e29-ad67-44a24b92ac11

Type:  kubernetes.io/service-account-token

Data
====
ca.crt:     1066 bytes
namespace:  11 bytes
token:      eyJhbGciOiJSUzI1NiIsImtpZCI6IjNXUzJuV2wxeFl5c0V3azNJaWtGc0hjUzZfczlOTGJEQk1yTW9heE5tckkifQ.eyJpc3MiOiJrdWJlcm5ldGVzL3NlcnZpY2VhY2NvdW50Iiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9uYW1lc3BhY2UiOiJrdWJlLXN5c3RlbSIsImt1YmVybmV0ZXMuaW8vc2VydmljZWFjY291bnQvc2VjcmV0Lm5hbWUiOiJkYXNoYm9hcmQtYWRtaW4tdG9rZW4tbm5yN2wiLCJrdWJlcm5ldGVzLmlvL3NlcnZpY2VhY2NvdW50L3NlcnZpY2UtYWNjb3VudC5uYW1lIjoiZGFzaGJvYXJkLWFkbWluIiwia3ViZXJuZXRlcy5pby9zZXJ2aWNlYWNjb3VudC9zZXJ2aWNlLWFjY291bnQudWlkIjoiMTgxZWIzMTctNTUwNy00ZTI5LWFkNjctNDRhMjRiOTJhYzExIiwic3ViIjoic3lzdGVtOnNlcnZpY2VhY2NvdW50Omt1YmUtc3lzdGVtOmRhc2hib2FyZC1hZG1pbiJ9.w94h8LWMzaU1qBVRAJY2UYvEaTWXoHDzvORkMiXB82mCpn9dzM8PMshGbNvDxesCIu1uggKh5n3UnA7VYaK-hTY66WC-96ow2Asptnm7N0v7sBASqF-_jbH5AEnCiUDySz-cuumCMGK4TYae7cIGzQPMonV0jgE73tXu5jM5jIylcwTsxKtjJZ55cyN2I7v6yUQbdIzQeAbx7eDoeLJ42Xvt3GNmO02v6BvjS_xOOIqHVBI_Y_D4ipXQ7kxNo6bYgFS8GLvDIl2FNYbyAZ7L05lr2eyMno-DGaVlIh0dl82liRI7h0g4LHYz_r_5cvYeFcdMf7XCw2eFJpaIl4BLmw

```



## 安装汉化版本Dashboard





## Ingress暴露应用

> NodePort

因容器内的服务无法直接在外部进行访问，故可以通过--type=NodePort将宿主机器指定端口映射容器内端口，如若不指定宿主机的端口则会自动使用一个>30000的端口。

**使用方式一、命令行**

```shell
# --port: 容器间请求端口
# --target-port: 容器暴露端口
[root@k8sMaster ~]# kubectl expose deployment nginx01 --port=80 --target-port=80 --type=NodePort
```

**使用方式二、yaml文件中指定**

![image-20210628220749470](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210628220749470.png)

这种方式的不足：

1、一个端口只能供一个服务使用；

2、只能使用30000–32767之间的端口；

3、如果节点/虚拟机的IP地址发生变化，需要人工进行处理；



>Ingress Nginx

![image-20210628221114799](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210628221114799.png)

**使用Ingress Nginx的步骤**

1、部署Ingress Nginx；

2、配置Ingress Nginx规则；

**启动Ingress**

```shell
[root@k8sMaster ~]# kubectl apply -f ingress-deploy.yaml 
namespace/ingress-nginx created
serviceaccount/ingress-nginx created
configmap/ingress-nginx-controller created
clusterrole.rbac.authorization.k8s.io/ingress-nginx created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx created
role.rbac.authorization.k8s.io/ingress-nginx created
rolebinding.rbac.authorization.k8s.io/ingress-nginx created
service/ingress-nginx-controller-admission created
service/ingress-nginx-controller created
deployment.apps/ingress-nginx-controller created
validatingwebhookconfiguration.admissionregistration.k8s.io/ingress-nginx-admission created
serviceaccount/ingress-nginx-admission created
clusterrole.rbac.authorization.k8s.io/ingress-nginx-admission created
clusterrolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
role.rbac.authorization.k8s.io/ingress-nginx-admission created
rolebinding.rbac.authorization.k8s.io/ingress-nginx-admission created
job.batch/ingress-nginx-admission-create created
job.batch/ingress-nginx-admission-patch created
```

**查看Ingress的状态**

![image-20210628221752349](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210628221752349.png)



**启动Ingress Nginx规则**

```shell
# rule.yaml
[root@k8sMaster ~]# cat ingress-nginx-rule.yaml 
apiVersion: networking.k8s.io/v1 
kind: Ingress
metadata:
  name: k8s-ingress
spec:
  rules:
  - host: www.abc.com
    http:
      paths:
      - pathType: Prefix
        path: /
        backend:
          service:
            name: nginx01
            port: 
              number: 80
[root@k8sMaster ~]# kubectl apply -f ingress-nginx-rule.yaml 
```

**查看规则**

kubectl get ing

![image-20210628222028190](C:\Users\52606\AppData\Roaming\Typora\typora-user-images\image-20210628222028190.png)





## 组件详解



