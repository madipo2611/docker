Следующие файлы писал я при деплое своего проекта на go:

drone.yml - Авто деплой приложения go на сервер через drone ci при коммите изменений в gitea

Dockerfile - сборка приложения go в докер контейнер

Остальные файлы примеры запуска/деплоя тестовых приложений, выполняемых при обучении.

kubernetes/kind-config-test.yml - конфиг запуска класстера kubernetes kind состоящий из 1 главного мастер-узла и 2 рабочих узла.

```
mark@docker:~$ kind create cluster --config kind-config-test.yml -n multi-node
Creating cluster "multi-node" ...
 ✓ Ensuring node image (kindest/node:v1.35.0) 🖼
 ✓ Preparing nodes 📦 📦 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
 ✓ Joining worker nodes 🚜
Set kubectl context to "kind-multi-node"
You can now use your cluster with:

kubectl cluster-info --context kind-multi-node

Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/
mark@docker:~$ kubectl get nodes
NAME                       STATUS     ROLES           AGE   VERSION
multi-node-control-plane   Ready      control-plane   23s   v1.35.0
multi-node-worker          NotReady   <none>          12s   v1.35.0
multi-node-worker2         NotReady   <none>          12s   v1.35.0
mark@docker:~$ docker ps
CONTAINER ID   IMAGE                  COMMAND                  CREATED          STATUS          PORTS                       NAMES
3e4e5037068b   kindest/node:v1.35.0   "/usr/local/bin/entr…"   55 seconds ago   Up 52 seconds                               multi-node-worker
eaf293785f5e   kindest/node:v1.35.0   "/usr/local/bin/entr…"   55 seconds ago   Up 52 seconds                               multi-node-worker2
d7ec78ddfb16   kindest/node:v1.35.0   "/usr/local/bin/entr…"   55 seconds ago   Up 52 seconds   127.0.0.1:38801->6443/tcp   multi-node-control-plane
mark@docker:~$
```
