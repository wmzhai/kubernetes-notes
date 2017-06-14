# k8s对象

k8s对象是k8s系统中持久化的实体。k8s使用这些实体来表述集群的状态。特别低，它们可以描述

* 正在容器化运行的应用是什么
* 应用相关的资源
* 应用相关的策略

k8s对象是一个**意愿的描述**，一旦你创建这个对象，k8s系统就会持续保证这个对象的存在。你通过创建对象来告诉k8s系统你希望系统工作的**desired state**

处理k8s对象，包括创建，修改和删除，都需要通过k8s api。 可以通过kubectl来调用api，也可以直接在程序里面调用，也可以通过一些客户端库来调用。


## 对象Spec和Status

每个k8s对象都包含2个内嵌的对象字段来控制对象的配置：object spec和object status。

* **期望状态spec** 你提供对象**desired state**的描述，表明你希望对象所具备的特征。
* **实际状态status** 对象实际的状态**actual state**，由k8s系统提供和更新。

任何时候k8s的control plane都积极地管理对象的实际状态来吻合你提供的期望状态。


## 描述k8s对象

当你创建一个k8s对象时，你必须提供这个对象的描述spec以表明他的期望状态**disired state**和一些对象的基本信息。当你使用k8s api创建对象时，必须在请求body里包含这些信息的JSON格式(常常是.ymal文件)。 kubectl把这些信息转化成JSON来请求API。

下面是一个k8s deployment的ymal文件

```
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nginx-deployment
spec:
  replicas: 3
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.7.9
        ports:
        - containerPort: 80
```

下面通过kubectl create指令来创建这个deployment

```
kubectl create -f nginx-deployment.yaml --record
```

在yaml文件里，你需要创建下面的值

* apiVersion 什么版本的Kubernetes API 
* kind 什么类型的对象
* metadata 用来唯一确认(uniquely identify)这个对象的数据，包括一个**name** 和可选的**namespace**
* spec 不同类型的spec的具体格式都不相同