# Ubuntu16.04 安装Minikube


## 启动VT-X/AMD-v虚拟化

如果是WMware，则需要在处理器选项里面选择，默认是不选的


## 安装VirtualBox

```
echo "deb http://download.virtualbox.org/virtualbox/debian xenial contrib" | sudo tee /etc/apt/sources.list.d/virtualbox.list
```

添加pub key

```
wget -q https://www.virtualbox.org/download/oracle_vbox_2016.asc -O- | sudo apt-key add -
```

如下安装，安装过程中会自动安装qt和sdl
```
apt update
apt install virtualbox-5.1 -y
apt install dkms
VBoxManage --version
```

## 安装kubectl


```
curl -LO https://storage.googleapis.com/kubernetes-release/release/$(curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt)/bin/linux/amd64/kubectl
chmod +x ./kubectl
mv ./kubectl /usr/local/bin/kubectl
kubectl version
``` 

安装完以后执行如下代码，发现cluster还没有正常配置

```
root@ubuntu:~# kubectl cluster-info
The connection to the server localhost:8080 was refused - did you specify the right host or port?
```

## 安装Minikube

```
curl -Lo minikube https://storage.googleapis.com/minikube/releases/v0.19.1/minikube-linux-amd64 && chmod +x minikube && sudo mv minikube /usr/local/bin/
minikube version
``` 

## 运行Minikube

```
root@ubuntu:~# minikube start
Starting local Kubernetes v1.6.4 cluster...
Starting VM...
Downloading Minikube ISO
 89.51 MB / 89.51 MB [==============================================] 100.00% 0s
Moving files into cluster...
Setting up certs...
Starting cluster components...
Connecting to cluster...
Setting up kubeconfig...
Kubectl is now configured to use the cluster.

root@ubuntu:~# kubectl run hello-minikube --image=gcr.io/google_containers/echoserver:1.4 --port=8080
deployment "hello-minikube" created

root@ubuntu:~# kubectl expose deployment hello-minikube --type=NodePort
service "hello-minikube" exposed

root@ubuntu:~# kubectl get pod
NAME                             READY     STATUS              RESTARTS   AGE
hello-minikube-938614450-0nkjl   0/1       ContainerCreating   0          39s
```

直到

```
root@ubuntu:~# kubectl get pod
NAME                             READY     STATUS    RESTARTS   AGE
hello-minikube-938614450-0nkjl   1/1       Running   0          1m
```

然后 
```
root@ubuntu:~# curl $(minikube service hello-minikube --url)
CLIENT VALUES:
client_address=172.17.0.1
command=GET
real path=/
query=nil
request_version=1.1
request_uri=http://192.168.99.100:8080/

SERVER VALUES:
server_version=nginx: 1.10.0 - lua: 10001

HEADERS RECEIVED:
accept=*/*
host=192.168.99.100:31264
user-agent=curl/7.47.0
BODY:
```


这时运行

```
root@ubuntu:~# kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.
```

## 停止minikube

```
root@ubuntu:~# minikube stop
Stopping local Kubernetes cluster...
Machine stopped.
root@ubuntu:~# kubectl get pod
Unable to connect to the server: dial tcp 192.168.99.100:8443: getsockopt: no route to host
```

## 再启动minikube


```
root@ubuntu:~# minikube start
Starting local Kubernetes v1.6.4 cluster...
Starting VM...
Moving files into cluster...
Setting up certs...
Starting cluster components...
Connecting to cluster...
Setting up kubeconfig...
Kubectl is now configured to use the cluster.

root@ubuntu:~# kubectl get pod
NAME                             READY     STATUS    RESTARTS   AGE
hello-minikube-938614450-0nkjl   1/1       Running   0          6m

root@ubuntu:~# kubectl cluster-info
Kubernetes master is running at https://192.168.99.100:8443

To further debug and diagnose cluster problems, use 'kubectl cluster-info dump'.

root@ubuntu:~# curl $(minikube service hello-minikube --url)
CLIENT VALUES:
client_address=172.17.0.1
command=GET
real path=/
query=nil
request_version=1.1
request_uri=http://192.168.99.100:8080/

SERVER VALUES:
server_version=nginx: 1.10.0 - lua: 10001

HEADERS RECEIVED:
accept=*/*
host=192.168.99.100:31264
user-agent=curl/7.47.0
BODY:
-no body in request-
```

## 安装Docker

```
sudo apt-key adv --keyserver hkp://p80.pool.sks-keyservers.net:80 --recv-keys 58118E89F3A912897C070ADBF76221572C52609D
echo "deb https://mirrors.tuna.tsinghua.edu.cn/docker/apt/repo ubuntu-xenial main" | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt-get update
sudo apt-get install docker-engine -y
```

## Installing kubelet and kubeadm

```
apt-get update && apt-get install -y apt-transport-https
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | apt-key add -
cat <<EOF >/etc/apt/sources.list.d/kubernetes.list
deb http://apt.kubernetes.io/ kubernetes-xenial main
EOF
apt-get update
apt-get install -y kubelet kubeadm kubernetes-cni
```


