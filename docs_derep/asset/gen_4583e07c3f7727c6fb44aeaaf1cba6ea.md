先把文本分析服务的外部访问入口建起来，用 LoadBalancer 类型的 Service 把内部 Pod 暴露出去。名字统一用 text-analysis-service，通过 label app=text-analysis 把流量分发到对应的 Pod，外部 80 端口进来后转发到容器 5000 端口。

[PRE0]
apiVersion: v1

kind: Service

metadata:

  name: text-analysis-service

spec:

  type: LoadBalancer # 设置负载均衡

  selector:

    app: text-analysis

  ports:

    - protocol: TCP

      port: 80 # Service的端口

      targetPort: 5000 # Pod的容器端口
配置文件就绪后，先把工作负载和服务一起推送到集群，相当于一次性把算力和入口都上线。

[PRE1]
kubectl apply -f deployment.yaml    # 部署Deployment

kubectl apply -f service.yaml       # 部署Service
上线后第一件事是确认状态，Pod 是否已就绪、Service 是否已经分配好地址，这样才能继续下一步。

[PRE2]
kubectl get pods                   # 查看Pod状态

kubectl get service                # 查看Service状态
流量稳定后需要自动扩缩容。下面这份 HPA 规则把伸缩目标锁定到 text-analysis-deployment，初始最少保留 3 个副本，峰值最多扩到 10 个副本，当 CPU 使用率接近 80% 时开始扩容。

[PRE3]
apiVersion: autoscaling/v1

kind: HorizontalPodAutoscaler

metadata:

  name: text-analysis-hpa

spec:

  scaleTargetRef:

    apiVersion: apps/v1

    kind: Deployment

    name: text-analysis-deployment

  minReplicas: 3

  maxReplicas: 10

  targetCPUUtilizationPercentage: 80    # CPU利用率阈值
规则确认无误就应用到集群，让控制器开始按负载自动调节副本数。

[PRE4]
kubectl apply -f hpa.yaml
[PRE5]