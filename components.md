# Kubernetes核心组件

## Master组件

运行于Master上的组件，做出集群的全局决定，观察并响应集群事件。Master组件可以运行于集群中的任意节点上，高可用场景下存在multi-master的情况。

### kube-apiserver

提供Kubernates API。

### etcd

Kubernates的backing store，所有集群数据都保存在这里。

对它需要有完善的备份方案。


### kube-controller-manager

运行controller，集群中处理例行任务的后台线程。 

逻辑上说，每个controller应该在独立的进程内，不过为了降低复杂度，所有controller都被编译运行于同一个进程里。

包括：

* Node Controller: 负责在node宕机时做出观察和响应
* Replication Controller: 负责保持每个pods的副本数正确
* Endpoints Controller: Populates the Endpoints object
* Service Account & Token Controller: 为新的namespace创造默认账号和API token

### kube-scheduler

kube-scheduler查看新建的还没有分配node的pods，并选择node运行它们

### DNS

所有Kubernates 集群都需要Cluster DNS，很多例子都依赖它，它是一个DNS server，主要服务Kubernates 服务的DNS记录。

被Kubernetes启动的容器自动包含了这个DNS服务器

### User interface

kube-ui提供只读的集群状态概览。


## 节点组件

运行在每个节点上，维护运行的pod并提供Kubernates运行环境。

### kubelet

主要的节点agent。它查看被分配给自己的pods并

* Mount pod需要的volume
* 下载pod的secrets
* 通过docker运行pod容器
* 定期检查容器的健康
* 向系统其他部分汇报pod的状态

### kube-proxy

通过维护网络规则进行服务抽象化。

### docker

用来运行容器

### supervisord

用来保持kubelet和docker运行的监测和系统控制轻量进程

### fluentd

提供集群级别日志的后台进程

