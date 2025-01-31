k3d cluster create canal-deploy

k3d cluster delete canal-deploy

kubectl config set-context --cluster=Canal-DEPLOY

kubectl cluster-info

kubectl api-versions

kubectl get nodes

docker build -t felipementel/test-scalar:1.0 .

docker push felipementel/test-scalar:1.0

kubectl logs --follow test-scalar-canal-deploy



kubectl apply --dry-run=client -f ./TestScalar/deployment.yml
kubectl apply -f ./TestScalar/pod.yml -n canal-deploy

kubectl delete pods test-scalar-canal-deploy-1

kubectl get pods --show-labels -n canal-deploy

 # Simplificando a forma de escalar

kubectl logs --follow --tail 0 --selector="app=canal-deploy-deployment" -n canal-deploy --max-log-requests 8


kubectl scale --replicas=3 -f ./TestScalar/pod-1.yml --namespace canal-deploy
