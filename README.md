# 用Helm安装chartmuseum

## 来源

https://chartmuseum.com/

https://helm.sh/docs/topics/chart_repository/

https://github.com/helm/chartmuseum

https://hub.helm.sh/charts/stable/chartmuseum

> 其他类似产品 例如：JFrog Artifactory  见 https://hub.helm.sh/charts/jfrog/artifactory

## 前提

1. 安装 Kubernetes（忽略）
2. 安装Helm   （忽略）

## 安装

追加repo，执行

```shell
helm repo add stable https://kubernetes-charts.storage.googleapis.com/
```

安装

```shell
helm install stable/chartmuseum --set env.open.DISABLE_API=false --version 2.13.0 --generate-name
```

> 参数细节见  https://hub.helm.sh/charts/stable/chartmuseum

得到

```shell
NAME: chartmuseum-1592489993
LAST DEPLOYED: Thu Jun 18 22:20:02 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
NOTES:
** Please be patient while the chart is being deployed **

Get the ChartMuseum URL by running:

  export POD_NAME=$(kubectl get pods --namespace default -l "app=chartmuseum" -l "release=chartmuseum-1592489993" -o jsonpath="{.items[0].metadata.name}")
  echo http://127.0.0.1:8080/
  kubectl port-forward $POD_NAME 8080:8080 --namespace default
```

执行

```shell
export POD_NAME=$(kubectl get pods --namespace default -l "app=chartmuseum" -l "release=chartmuseum-1592489993" -o jsonpath="{.items[0].metadata.name}")
echo http://127.0.0.1:8080/
kubectl port-forward $POD_NAME 8080:8080 --namespace default
```

> 如果遇到 error: unable to forward port because pod is not running. Current status=Pending  则需要手动拉取镜像，执行
>
> ```shell
>  docker pull chartmuseum/chartmuseum:v0.12.0
> ```

打开浏览器 访问 http://127.0.0.1:8080/ 得到

![homepage](https://raw.githubusercontent.com/zknyy/helm-chartmuseum-install/master/homepage.png)

## 设置

执行

```shell
helm repo add chartmuseum http://localhost:8080
```

得到

```
"chartmuseum" has been added to your repositories
```



## 安装插件

> 来源  https://github.com/chartmuseum/helm-push

执行

```shell
helm plugin install https://github.com/chartmuseum/helm-push.git
```

得到

```
https://github.com/chartmuseum/helm-push/releases/download/v0.8.1/helm-push_0.8.1_darwin_amd64.tar.gz
Installed plugin: push
```







## 打包Chart

克隆 https://github.com/zknyy/helm-hello-world 到本地，进入目录

执行检查

```shell
helm lint --strict helloworld-chart
```

得到

```
==> Linting helloworld-chart
[INFO] Chart.yaml: icon is recommended

1 chart(s) linted, 0 chart(s) failed
```

执行打包 【可以不用打包】

```shell
helm package helloworld-chart 
```

得到

```shell
Successfully packaged chart and saved it to: /Users/demo/git/helm-hello-world/helloworld-chart-0.1.0.tgz
```

## 上传

```shell
helm push ./helloworld-chart/ chartmuseum
```

得到

```
Pushing helloworld-chart-0.1.0.tgz to chartmuseum...
Done.
```

## 搜索

先更新，执行：

```
helm repo update
```

得到

```
Hang tight while we grab the latest from your chart repositories...
...Successfully got an update from the "chartmuseum" chart repository
...Successfully got an update from the "ingress-nginx" chart repository
...Successfully got an update from the "monocular" chart repository
...Successfully got an update from the "apphub" chart repository
...Successfully got an update from the "stable" chart repository
Update Complete. ⎈ Happy Helming!⎈ 
```

执行搜索

```shell
helm search repo helloworld-chart
```

得到

```
NAME                        	CHART VERSION	APP VERSION	DESCRIPTION                
chartmuseum/helloworld-chart	0.1.0        	1.16.0     	A Helm chart for Kubernetes
```

