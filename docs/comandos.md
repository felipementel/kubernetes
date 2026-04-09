# Kubectl - Comandos Essenciais

## Inspeção

| Comando | Descrição |
|---------|-----------|
| `kubectl get pods` | Lista pods |
| `kubectl get svc` | Lista services |
| `kubectl get deploy` | Lista deployments |
| `kubectl get nodes` | Lista nodes |
| `kubectl get all` | Lista os principais recursos do namespace |

## Detalhes

| Comando | Descrição |
|---------|-----------|
| `kubectl describe pod <nome>` | Mostra detalhes e eventos de um pod |
| `kubectl describe node <nome>` | Mostra detalhes de um node |

## Aplicação de Manifests

| Comando | Descrição |
|---------|-----------|
| `kubectl apply -f arquivo.yaml` | Cria ou atualiza recursos declarativamente |
| `kubectl delete -f arquivo.yaml` | Remove os recursos definidos no manifesto |

## Logs e Troubleshooting

| Comando | Descrição |
|---------|-----------|
| `kubectl logs <pod>` | Mostra logs do pod |
| `kubectl logs -f <pod>` | Acompanha logs em tempo real |
| `kubectl exec -it <pod> -- sh` | Entra no container do pod |
| `kubectl port-forward pod/<pod> 8080:80` | Encaminha porta local para o pod |

## Escala e Rollout

| Comando | Descrição |
|---------|-----------|
| `kubectl scale deploy <nome> --replicas=3` | Altera número de réplicas |
| `kubectl rollout status deploy/<nome>` | Mostra status do rollout |
| `kubectl rollout history deploy/<nome>` | Mostra histórico de versões |
| `kubectl rollout undo deploy/<nome>` | Faz rollback |

## Organização por Namespace

| Comando | Descrição |
|---------|-----------|
| `kubectl get pods -n <namespace>` | Lista pods de um namespace |
| `kubectl config set-context --current --namespace=<namespace>` | Define namespace padrão no contexto atual |

## Contexto e Cluster

| Comando | Descrição |
|---------|-----------|
| `kubectl config get-contexts` | Lista contextos |
| `kubectl config use-context <contexto>` | Troca de cluster/contexto |
| `kubectl cluster-info` | Mostra informações do cluster |

## Arquivos YAML

| Comando | Descrição |
|---------|-----------|
| `kubectl create deployment nginx --image=nginx --dry-run=client -o yaml` | Gera YAML sem aplicar |
| `kubectl edit deploy <nome>` | Edita recurso direto no cluster |

## Comandos Essenciais (Destaques)

```
kubectl get
kubectl describe
kubectl apply
kubectl delete
kubectl logs
kubectl exec
kubectl scale
kubectl rollout
kubectl port-forward
```

## Exemplo de Fluxo Básico

```bash
# 1. Aplicar manifesto
kubectl apply -f app.yaml

# 2. Ver pods
kubectl get pods

# 3. Inspecionar deployment
kubectl describe deploy minha-api

# 4. Ver logs
kubectl logs -f pod/minha-api-abc123

# 5. Escalar
kubectl scale deploy minha-api --replicas=3

# 6. Ver rollout
kubectl rollout status deploy/minha-api
```
