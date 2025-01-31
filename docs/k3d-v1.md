# Docker e nosso projeto

### Build da imagem
````
docker build -t felipementel/test-scalar:1.0 .
````
### Login no docker registry
````
docker login -u felipementel -p <pasword>
````
### push da imagem no docker hub
````
docker push felipementel/test-scalar:1.0
````
### Execução do container local
````
docker container run --rm --name test-scalar -p 8000:80 -p 8001:81 -e ASPNETCORE_HTTP_PORTS=80 -e ASPNETCORE_HTTPS_PORTS=81 -e ASPNETCORE_ENVIRONMENT=Development felipementel/test-scalar:1.0
````

# Kubernetes

## Para nosso exemplo, estamos utilizando o K3D
````
https://k3d.io/stable/
````

## Criar cluster
````
k3d cluster create canal-deploy
````

## Excluir cluster
````
k3d cluster delete canal-deploy
````

## Criar namespace
````
kubectl create namespace aplicacao-canal-deploy
````

## Configs

### Caso tenha criado mais de um cluster, aprenda como apontar para o cluster que vai receber os comandos
````
kubectl config set-context canal-deploy --cluster=k3d-canal-deploy
````

### Infos do Cluster
````
kubectl cluster-info
````
### Api Version
````
kubectl api-versions
````
### Listar todos os nodes
````
kubectl get nodes --all-namespaces 
````
### Listar todos os serviços
````
kubectl get services --all-namespaces
````

### Aplicar manifesto de deployment

#### dry-run (apenas testa)
````
kubectl apply --dry-run=client -f ./TestScalar/deployment.yml --namespace aplicacao-canal-deploy
````

#### Aplicando no cluster
````
kubectl apply -f ./TestScalar/deployment.yml --namespace aplicacao-canal-deploy
````
#### Port-forward - Devido ao LoadBalancer
````
kubectl port-forward svc/canal-deploy-service -n aplicacao-canal-deploy 8001:8000
````

### Listar Pods
````
kubectl get pods -n aplicacao-canal-deploy
````

## Logs
````
kubectl logs canal-deploy-deployment-688fd7fb48-9fmsk --namespace aplicacao-canal-deploy
````
````
kubectl describe deployment -n aplicacao-canal-deploy
````
````
kubectl logs --follow test-scalar-canal-deploy
````

## Delete 
````
kubectl delete service canal-deploy-service --namespace aplicacao-canal-deploy
````
````
kubectl delete deployments canal-deploy-deployment --namespace aplicacao-canal-deploy
````




kubectl apply -f ./TestScalar/pod.yml -n aplicacao-canal-deploy

kubectl delete pods test-scalar-canal-deploy-1

kubectl get pods --show-labels -n aplicacao-canal-deploy



 # Simplificando a forma de escalar

kubectl logs --follow --tail 0 --selector="app=canal-deploy-deployment" -n canal-deploy --max-log-requests 8


kubectl scale --replicas=3 -f ./TestScalar/pod-1.yml --namespace canal-deploy



