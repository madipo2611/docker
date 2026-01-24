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
kubernetes/test-app - запуск тестового сайта в kubernetes с nginx
```
mark@docker:~$ nano test-app.yml
mark@docker:~$ kind create cluster --config test-app.yml --name=test
Creating cluster "test" ...
 ✓ Ensuring node image (kindest/node:v1.35.0) 🖼
 ✓ Preparing nodes 📦 📦 📦
 ✓ Writing configuration 📜
 ✓ Starting control-plane 🕹️
 ✓ Installing CNI 🔌
 ✓ Installing StorageClass 💾
 ✓ Joining worker nodes 🚜
Set kubectl context to "kind-test"
You can now use your cluster with:

kubectl cluster-info --context kind-test

Not sure what to do next? 😅  Check out https://kind.sigs.k8s.io/docs/user/quick-start/

mark@docker:~$ kubectl get nodes
NAME                 STATUS   ROLES           AGE   VERSION
test-control-plane   Ready    control-plane   34s   v1.35.0
test-worker          Ready    <none>          23s   v1.35.0
test-worker2         Ready    <none>          23s   v1.35.0
mark@docker:~$ mkdir -p test/files
mark@docker:~$ nano test/files/index.html
mark@docker:~$ nano test/files/default
mark@docker:~$ nano test/Dockerfile
mark@docker:~$ cd test
mark@docker:~/test$ docker build -t nginx-test:01 .
[+] Building 19.7s (9/9) FINISHED                                                                                                                                                                 docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                                        0.0s
 => => transferring dockerfile: 263B                                                                                                                                                                        0.0s
 => [internal] load metadata for docker.io/library/ubuntu:24.04                                                                                                                                             1.5s
 => [internal] load .dockerignore                                                                                                                                                                           0.0s
 => => transferring context: 2B                                                                                                                                                                             0.0s
 => [1/4] FROM docker.io/library/ubuntu:24.04@sha256:cd1dba651b3080c3686ecf4e3c4220f026b521fb76978881737d24f200828b2b                                                                                       0.1s
 => => resolve docker.io/library/ubuntu:24.04@sha256:cd1dba651b3080c3686ecf4e3c4220f026b521fb76978881737d24f200828b2b                                                                                       0.0s
 => [internal] load build context                                                                                                                                                                           0.0s
 => => transferring context: 456B                                                                                                                                                                           0.0s
 => [2/4] RUN apt -y update && apt -y install nginx                                                                                                                                                        14.4s
 => [3/4] COPY files/default /etc/nginx/sites-available/default                                                                                                                                             0.1s
 => [4/4] COPY files/index.html /usr/share/nginx/html/index.html                                                                                                                                            0.0s
 => exporting to image                                                                                                                                                                                      3.4s
 => => exporting layers                                                                                                                                                                                     2.8s
 => => exporting manifest sha256:bbbd4be087c666f9241dc3e4b523057cc66f4f5e2444f639bb28920f3d327e02                                                                                                           0.0s
 => => exporting config sha256:713db6f2b787c6cdf60d5ad5a72bc9c57490f4620e30ce20d8d0802cb611c921                                                                                                             0.0s
 => => exporting attestation manifest sha256:e9c033fca17057c9f76e6beced69eea89f05df4786576d6ad3e2548592d5d7a6                                                                                               0.0s
 => => exporting manifest list sha256:4df22a3bff4abae23bc873355d7930f3862c147e3c23bca615f94633f082039f                                                                                                      0.0s
 => => naming to docker.io/library/nginx-test:01                                                                                                                                                            0.0s
 => => unpacking to docker.io/library/nginx-test:01                                                                                                                                                         0.5s
mark@docker:~/test$ kind load docker-image nginx-test:01 --name test
Image: "nginx-test:01" with ID "sha256:4df22a3bff4abae23bc873355d7930f3862c147e3c23bca615f94633f082039f" not yet present on node "test-worker", loading...
Image: "nginx-test:01" with ID "sha256:4df22a3bff4abae23bc873355d7930f3862c147e3c23bca615f94633f082039f" not yet present on node "test-worker2", loading...
Image: "nginx-test:01" with ID "sha256:4df22a3bff4abae23bc873355d7930f3862c147e3c23bca615f94633f082039f" not yet present on node "test-control-plane", loading...
mark@docker:~$ nano nginx-deployment.yml

mark@docker:~$ kubectl apply -f nginx-deployment.yml
deployment.apps/test-nginx created
service/test-nginx-service created
mark@docker:~$ kubectl get pod
NAME                          READY   STATUS    RESTARTS   AGE
test-nginx-5977b6f9d8-s6cm8   1/1     Running   0          13s
mark@docker:~$ kubectl port-forward service/test-nginx-service 32700:80
Forwarding from [::1]:32700 -> 80
Handling connection for 32700



mark@docker:~$ curl -i localhost:32700
HTTP/1.1 200 OK
Server: nginx/1.24.0 (Ubuntu)
Date: Sat, 24 Jan 2026 09:05:04 GMT
Content-Type: text/html
Content-Length: 172
Last-Modified: Sat, 24 Jan 2026 08:53:51 GMT
Connection: keep-alive
ETag: "6974889f-ac"
Accept-Ranges: bytes

<html>
 <head>
  <title>Test-deployment</title>
 </head>
 <body>
  <h1>Nginx test</h1>
  <p>This Nginx pod is running in kind Kubernetes cluster</p></div>
 </body>
</html>
```
