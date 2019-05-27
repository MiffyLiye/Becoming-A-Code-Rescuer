# Kubernetes入门

Produced by 王韬 Wang, Tao

---

## 课程安排

* 为什么使用kubernetes
* 常用功能的理论与实践

---

## 为什么使用kubernetes

----

### 时代趋势

* 社会发展变快，对市场需求的快速反应越来越重要
* 工具越来越成熟，个人/小团队能把握的复杂度越来越高

----

### 制造业生产流程

* 手工作坊
* 流水线生产
* 单元生产

----

### 软件开发流程

* ...
* 瀑布式
* 敏捷，精益，DevOps

----

### 应用架构

* ...
* 分层
* 微服务

----

### 部署结构

* 物理机
* 虚拟机
* 容器平台

----

### 团队结构

* functional team
* feature team

---

* 微服务化和容器化提高了运维复杂度
* DevOps需要运维复杂度降低

----

### 降低运维复杂度
### 容器编排工具 kubernetes 

----

## kubernetes的能帮助实现

* 服务发现
* 水平扩展
* 部署升级
* 配置管理
* 健康检查/自动恢复
* 统一的运行环境<sup>*</sup>
* DevOps/NoOps<sup>*</sup>
* ...

---

## 常用功能的理论与实践

---

## kubernetes集群架构

* Control Plane (master)
* Worker nodes

---

## 搭建Kubernetes集群

* Docker for Mac
* kubeadm
* 阿里云托管k8s
* ...

----

#### Docker for Mac

```sh
$ brew cask install docker
```

```
Docker => Peference => Kubernetes => Enable Kubernetes
```

----

### 查看集群节点状态

```sh
$ kubectl get nodes
```

```
NAME                 STATUS   ROLES    AGE   VERSION
docker-for-desktop   Ready    master   91d   v1.10.11
```

```sh
$ kubectl describe node docker-for-desktop
```

```
Name:               docker-for-desktop
Roles:              master
...
```

---

## Pod

* Pod是k8s的基本调度单元，同一个Pod内的Container共享同一个Linux namespace，具有相同的IP
* 不同Pod内的Container使用不同的Linux namespace，具有不同的IP

----

### 创建Pod

```sh
$ kubectl create deployment nginx --image=nginx:alpine
```
```
deployment.apps/nginx created
```
```sh
$ kubectl get pods
```
```
NAME                     READY   STATUS    RESTARTS   AGE
nginx-5dbbff858d-dsklv   1/1     Running   0          5s
```
```sh
$ kubectl describe pod nginx-5dbbff858d-dsklv
```
```
Name:           nginx-5dbbff858d-dsklv
Namespace:      default
...
```

----

### 访问Pod

```sh
$  kubectl port-forward pod/nginx-5dbbff858d-dsklv 80:80
```
```
Forwarding from 127.0.0.1:80 -> 80
Forwarding from [::1]:80 -> 80
Handling connection for 80
```
```sh
$ curl http://localhost
```
```
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
<style>
    body {
        width: 35em;
        margin: 0 auto;
        font-family: Tahoma, Verdana, Arial, sans-serif;
    }
</style>
</head>
<body>
<h1>Welcome to nginx!</h1>
<p>If you see this page, the nginx web server is successfully installed and
working. Further configuration is required.</p>

<p>For online documentation and support please refer to
<a href="http://nginx.org/">nginx.org</a>.<br/>
Commercial support is available at
<a href="http://nginx.com/">nginx.com</a>.</p>

<p><em>Thank you for using nginx.</em></p>
</body>
</html>
```

---

## Service

提供相同服务的一组Pod的单一入口

----

### 创建Service

```sh
$ kubectl expose deployment nginx --port=80 --type=LoadBalancer
```
```sh
$ curl http://localhost
```
```sh
$ kubectl get services
```
```
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP        91d
nginx        LoadBalancer   10.104.157.219   localhost     80:30935/TCP   6m
```

----

### 删除Pod和Service

```sh
$ kubectl delete deployment nginx
$ kubectl delete service nginx
```

---

## 常用kubernetes对象

* Pod<sup>*</sup>
* <span style="color:blue">Service</span>
* Ingress<sup>*</sup>
* ReplicaSet<sup>*</sup>
* <span style="color:blue">Deployment</span>
* <span style="color:blue">StatefulSet</span>
* ConfigMap<sup>*</sup>
* <span style="color:blue">Secret</span>

----

## 其他

* Namespace
* Volume
* Job
* CronJob
* DaemonSet
* RBAC
* ...


---

## Pod

* 推荐每个Container运行一个进程
* 通过Pod组合多个Container的多个进程

* Pod内Container共享IP和端口
* Pod内Container的文件系统相互隔离

----

### 通过YAML创建Pod

`nginx-pod.yaml`:
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  namespace: default
  labels:
    app: nginx
spec:
  containers:
    - name: nginx
      image: nginx:alpine
      ports:
        - containerPort: 80
          protocol: TCP
```
```sh
$ kubectl apply -f nginx-pod.yaml
$ kubectl get pods
```

----

### 查看日志

```sh
$ kubectl port-forward pod/nginx 80:80
```
```sh
$ curl http://localhost
```
```sh
$ kubectl logs nginx
```
```
127.0.0.1 - - [22/May/2019:13:30:39 +0000] "GET / HTTP/1.1" 200 612 "-" "curl/7.64.1" "-"
```
```sh
$ kubectl logs nginx -c nginx
```

----

### 删除Pod

```sh
$ kubectl delete -f nginx-pod.yaml
```
```sh
$ kubectl delete pod nginx
```

---

## Label

```sh
$ kubectl get pods -L app
```
```
NAME    READY   STATUS    RESTARTS   AGE   APP
nginx   1/1     Running   0          1h    nginx
```
```sh
$ kubectl get pods -L app=nginx
```
```
NAME    READY   STATUS    RESTARTS   AGE   APP=NGINX
nginx   1/1     Running   0          1h    
```

----

### 通过label选择node
```sh
$ kubectl get nodes -L roles=master
```
```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx
  labels:
    app: nginx
spec:
  nodeSelector:
    roles: master
  containers:
    - name: nginx
      image: nginx:alpine
      ports:
        - containerPort: 80
          protocol: TCP
```

----

### 通过label选择Pod

```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: ClusterIP
  ports:
    - name: http
      port: 80
      targetPort: 80
      protocol: TCP
  selector:
    app: nginx
```

---

## Service

* 封装多个用于负载均衡的Pod，对外提供单一入口
* 封装单个Pod地址，隔离单个Pod地址的变化

----

### 创建Service

`nignx-service.yaml`
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: ClusterIP
  ports:
    - name: http
      port: 80
      targetPort: 80
      protocol: TCP
  selector:
    app: nginx
```
```sh
$ kubectl apply -f nginx-service.yaml
```

----

### 访问Service

```sh
$ kubectl run -it curl --image=radial/busyboxplus:curl
```
```sh
$ nslookup nginx
$ nslookup nginx.default.svc.cluster.local
$ curl http://nginx/
$ curl http://nginx.default.svc.cluster.local/
```

----

### LoadBalancer

* 传输层(L4)负载均衡

----

#### 创建LoadBalancer

具体配置依赖于云服务提供商
```yaml
apiVersion: v1
kind: Service
metadata:
  name: nginx
spec:
  type: LoadBalancer
  ports:
    - name: http
      port: 80
      targetPort: 80
      protocol: TCP
  selector:
    app: nginx
```
```sh
$ kubectl get services
```
```
NAME         TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)        AGE
kubernetes   ClusterIP      10.96.0.1        <none>        443/TCP        91d
nginx        LoadBalancer   10.104.157.219   localhost     80:30935/TCP   6m
```

---

## Ingress

* 应用层(L7)负载均衡
* 多个应用共用同一个IP

----

### 创建Ingress

具体配置依赖于云服务提供商
```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: nginx
spec:
  rules:
    - host: nginx.example.com
      http: 
        paths:
          - path: /
            backend:
              serviceName: nginx
              servicePort: 80
```

---

## ReplicaSet

* 用于确保特定类型的Pod处于运行中
* Pod数量不足时启动新Pod
* Pod数量过多时删除多余Pod

----

### 创建ReplicaSet

```yaml
apiVersion: apps/v1beta2
kind: ReplicaSet
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:alpine
          ports:
            - containerPort: 80
              protocol: TCP
```

----

### 删除ReplicaSet

```sh
$ kubectl delete replicaset nginx
```

---

## Deployment

用于部署和升级无状态应用

----

### 创建Deployment

`nginx-deployment.yaml`
```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:alpine
      env:
        - name: VERSION
          value: "1"
```
```sh
$ kubectl apply -f nginx-deployment.yaml
$ kubectl get replicasets
$ kubectl get pods
```

----

#### 升级Deployment

`nginx-deployment.yaml`
```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:alpine
      env:
        - name: VERSION
          value: "2"
```
```sh
$ kubectl apply -f nginx-deployment.yaml
$ kubectl get replicasets
$ kubectl get pods
```

----

### 升级策略

```yaml
apiVersion: apps/v1beta1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  minReadySeconds: 10
  strategy:
    type: RollingUpdate
    rollingUpdate:
      maxSurge: 1
      maxUnavailable: 0
  template:
    metadata:
      name: nginx
      labels:
        app: nginx
    spec:
      containers:
        - name: nginx
          image: nginx:alpine
      env:
        - name: VERSION
          value: "2"
```

---

## StatefulSet

用于部署和升级有状态应用

* StatefulSet -- Deployment
* Stateful -- Stateless
* Pet -- Cattle
* Database
* Consumer Daemon

----

### 创建StatefulSet

`nginx-statefulset.yaml`
```yaml
apiVersion: apps/v1beta1
kind: StatefulSet
metadata:
  name: nginx
spec:
  serviceName: nginx
  replicas: 2
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:alpine
        ports:
        - name: http
          containerPort: 80
```
```
$ kubectl apply -f nginx-statefulset.yaml
```

---

## ConfigMap

用于提供配置

----

### Command/Args配置方式

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: explorer
spec:
  selector:
    matchLabels:
      app: explorer
  serviceName: "explorer"
  replicas: 1
  template:
    metadata:
      labels:
        app: explorer
    spec:
      containers:
        - name: explorer
          image: ubuntu:18.04
          command: ["tail"]
          args: ["-f", "/dev/null"]
```

----

### 环境变量配置方式

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: explorer
spec:
  selector:
    matchLabels:
      app: explorer
  serviceName: "explorer"
  replicas: 1
  template:
    metadata:
      labels:
        app: explorer
    spec:
      containers:
        - name: explorer
          image: ubuntu:18.04
          env:
            - name: VARIABLE
              value: example
          command: ["sh"]
          args:
            - -ec
            - |
              echo $VARIABLE
              tail -f /dev/null
```

----

### ConfigMap配置方式

* 可以用于Pod定义与配置解耦
* 多个环境使用同一份Pod定义，不同的ConfigMap

----

### 使用ConfigMap作为环境变量

```sh
kubectl create configmap example-config --from-literal=config=example
```
```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: explorer
spec:
  selector:
    matchLabels:
      app: explorer
  serviceName: "explorer"
  replicas: 1
  template:
    metadata:
      labels:
        app: explorer
    spec:
      containers:
        - name: explorer
          image: ubuntu:18.04
          env:
            - name: VARIABLE
              valueFrom:
                configMapKeyRef:
                  name: example-config
                  key: config
          command: ["sh"]
          args:
            - -ec
            - |
              echo $VARIABLE
              tail -f /dev/null
```

----

### 使用ConfigMap作为配置文件

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: ingress-config
data:
  default.conf: |
    server {
      listen 80 default_server;
      listen [::]:80 default_server;
      return 204;
    }
```

----

### 使用ConfigMap作为配置文件

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: explorer
spec:
  selector:
    matchLabels:
      app: explorer
  serviceName: "explorer"
  replicas: 1
  template:
    metadata:
      labels:
        app: explorer
    spec:
      volumes:
        - name: config
          configMap:
            name: ingress-config
      containers:
        - name: explorer
          image: ubuntu:18.04
          volumeMounts:
            - name: config
              mountPath: /data
              readOnly: true
          command: ["sh"]
          args:
            - -ec
            - |
              cat /data/default.conf
              tail -f /dev/null
```
---

## Secret

与ConfigMap类似，用于配置敏感信息

----

#### 创建Secret

```sh
kubectl create secret generic example-secret --from-literal=config=example --from-file=tls.key
```

----

### 使用Secret作为环境变量

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: explorer
spec:
  selector:
    matchLabels:
      app: explorer
  serviceName: "explorer"
  replicas: 1
  template:
    metadata:
      labels:
        app: explorer
    spec:
      containers:
        - name: explorer
          image: ubuntu:18.04
          env:
            - name: VARIABLE
              valueFrom:
                secretKeyRef:
                  name: example-secret
                  key: config
          command: ["sh"]
          args:
            - -ec
            - |
              echo $VARIABLE
              tail -f /dev/null
```

----

### 使用Secret作为配置文件

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
  name: explorer
spec:
  selector:
    matchLabels:
      app: explorer
  serviceName: "explorer"
  replicas: 1
  template:
    metadata:
      labels:
        app: explorer
    spec:
      volumes:
        - name: secret
          secret:
            secretName: ingress-secret
      containers:
        - name: explorer
          image: ubuntu:18.04
          volumeMounts:
            - name: secret
              mountPath: /data
              readOnly: true
          command: ["sh"]
          args:
            - -ec
            - |
              cat /data/tls.key
              tail -f /dev/null
```

---

## Namespace

用于资源分组与隔离


----

### 查看Namespace

```sh
$ kubectl get namespaces
```
```
NAME          STATUS   AGE
default       Active   91d
docker        Active   91d
kube-public   Active   91d
kube-system   Active   91d
```
```sh
$ kubectl get pods --namespace kube-system
```
```
NAME                                         READY   STATUS    RESTARTS   AGE
etcd-docker-for-desktop                      1/1     Running   0          91d
kube-apiserver-docker-for-desktop            1/1     Running   0          91d
kube-controller-manager-docker-for-desktop   1/1     Running   0          91d
kube-dns-86f4d74b45-dkrvb                    3/3     Running   0          91d
kube-proxy-jf4hn                             1/1     Running   0          91d
kube-scheduler-docker-for-desktop            1/1     Running   0          91d
```

----

### 创建namespace

`uat.yaml`:
```yaml
apiVersion: v1
kind: Namespace
metadata: 
  name: uat
```
```sh
$ kubectl apply -f uat.yaml
```

---

## 问题诊断

