# 部署文档

## Helm 方式安装

```bash
kubectl create ns postgres

helm -n postgres list

helm repo add postgres-quiz http://yunwei361.github.io/postgres-quiz/

helm -n postgres install postgres15 postgres-quiz/postgres15
```





## Manifests 方式安装

```bash
kubectl create ns postgres

# 部署Postgres15
kubectl create -f secret.yaml
kubectl create -f sts.yaml
kubectl create -f svc.yaml


# 部署初始化任务
kubectl create -f init-configmap.yaml
kubectl create -f init-job.yaml
# 查看日志
kubectl -n postgres get jobs
kubectl -n postgres logs $(kubectl -n postgres get pods -l job-name=$(kubectl -n postgres get jobs -o jsonpath='{.items[0].metadata.name}') -o name)
# 删除初始化任务
kubectl -n postgres delete jobs.batch postgres-data-init


# 部署备份任务
kubectl create -f backup-script-configmap.yaml
kubectl create -f backup-pvc.yaml
kubectl create -f backup-cronjob.yaml
# 查看任务状态
kubectl -n postgres get cronjobs
kubectl -n postgres get jobs -w
# 查看日志
kubectl -n postgres logs $(kubectl -n postgres get pods -l job-name=$(kubectl -n postgres get jobs -o jsonpath='{.items[0].metadata.name}') -o name)
```


