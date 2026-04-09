# AKS - Primeiros Passos

## 1. Conectar no Cluster AKS

Baixe as credenciais do cluster para o `kubectl`:

```bash
az aks get-credentials \
  --resource-group <RESOURCE_GROUP> \
  --name <AKS_NAME>
```

Isso faz:
- Baixa o kubeconfig do AKS
- Mescla no `~/.kube/config`
- Permite que o `kubectl` fale com o cluster

Valide a conexão:

```bash
kubectl get nodes
```

> Se listar os nós, está conectado.

### Trabalhando com múltiplos clusters

```bash
kubectl config get-contexts        # Lista contextos
kubectl config current-context     # Mostra o contexto atual
kubectl config use-context <nome>  # Troca de contexto
```

### Erro comum de permissão

Se der erro de permissão:
- Você precisa ter pelo menos **Azure RBAC Reader/Contributor** no AKS
- Certifique-se de estar logado: `az login`

---

## 2. Hello World no AKS

### Passo 1 – Verificar conexão

```bash
kubectl get nodes
```

### Passo 2 – Criar um Deployment

```bash
kubectl create deployment hello-world --image=nginx
```

Verifique:

```bash
kubectl get pods
kubectl get deployments
```

> Espere o pod ficar `Running`.

### Passo 3 – Expor a aplicação

```bash
kubectl expose deployment hello-world \
  --type=LoadBalancer \
  --port=80
```

### Passo 4 – Obter o IP público

```bash
kubectl get svc hello-world
```

Saída esperada:

```
NAME          TYPE           CLUSTER-IP     EXTERNAL-IP      PORT(S)
hello-world   LoadBalancer   10.x.x.x       20.xxx.xxx.xxx   80:xxxxx/TCP
```

Abra o `EXTERNAL-IP` no navegador para ver a página padrão do nginx.

### Alternativa rápida (sem IP público)

```bash
kubectl port-forward deployment/hello-world 8080:80
```

Acesse: `http://localhost:8080`

### Limpeza

```bash
kubectl delete deployment hello-world
kubectl delete svc hello-world
```

---

## 3. Troubleshooting do LoadBalancer

Quando o `EXTERNAL-IP` fica em `<pending>`:

```
NAME          TYPE           CLUSTER-IP     EXTERNAL-IP   PORT(S)        AGE
hello-world   LoadBalancer   10.0.184.254   <pending>     80:31859/TCP   17s
```

Isso é normal — o Azure leva de **30s a 5 minutos** para criar o Load Balancer.

### Acompanhar em tempo real

```bash
kubectl get svc hello-world -w
```

### Verificar eventos do Service

```bash
kubectl describe svc hello-world
```

Procure por eventos como:
- `Ensuring load balancer`
- `Creating load balancer`

### Causas comuns se ficar muito tempo em `<pending>`

| Causa | Solução |
|-------|---------|
| Cluster privado | Usar `kubectl port-forward` ou acesso via VPN/Bastion |
| Falta de permissão para criar Public IP | Verificar RBAC no resource group `MC_*` |
| Quota de IPs públicos estourada | Verificar quotas no portal Azure |
| Subnet errada | Verificar no resource group gerenciado (`MC_*`) |

### Solução rápida para testar

```bash
kubectl port-forward svc/hello-world 8080:80
```

Acesse: `http://localhost:8080`

---

## 4. Descobrir configurações do seu AKS

### AKS é público ou privado?

```bash
az aks show \
  --resource-group <RESOURCE_GROUP> \
  --name <AKS_NAME> \
  --query "apiServerAccessProfile.enablePrivateCluster" \
  -o tsv
```

| Resultado | Significado |
|-----------|-------------|
| `true` | AKS **privado** — API acessível somente dentro da VNET |
| `false` ou vazio | AKS **público** — API acessível pela internet |

### Tipo de rede: Azure CNI ou kubenet?

```bash
az aks show \
  --resource-group <RESOURCE_GROUP> \
  --name <AKS_NAME> \
  --query "networkProfile.networkPlugin" \
  -o tsv
```

| Resultado | Significado |
|-----------|-------------|
| `azure` | Azure CNI |
| `kubenet` | Kubenet |

### Diferença entre Azure CNI e Kubenet

| Item | Azure CNI | Kubenet |
|------|-----------|---------|
| IP do Pod | IP da VNET | IP interno |
| Integração com rede | Alta | Mais simples |
| Consumo de IPs | Mais | Menos |
| Uso comum | Ambientes corporativos | Labs / simples |

### Verificar pelo Kubernetes (sem Azure CLI)

```bash
kubectl get nodes -o wide
```

- IPs da VNET → tende a ser **Azure CNI**
- IPs NAT → tende a ser **kubenet**

---

## 5. Criar um Namespace

### Via linha de comando

```bash
kubectl create namespace meu-namespace
```

Verifique:

```bash
kubectl get namespaces
```

### Via YAML (recomendado para versionamento)

Gerar o YAML:

```bash
kubectl create namespace meu-namespace --dry-run=client -o yaml > namespace.yaml
```

Conteúdo gerado:

```yaml
apiVersion: v1
kind: Namespace
metadata:
  name: meu-namespace
```

Aplicar:

```bash
kubectl apply -f namespace.yaml
```

### Usar o namespace nos comandos

```bash
# Por comando
kubectl get pods -n meu-namespace

# Definir como padrão
kubectl config set-context --current --namespace=meu-namespace

# Confirmar
kubectl config view --minify | grep namespace
```

### Dicas sobre namespaces

- `default` existe, mas evite usar em ambientes reais
- Namespace **não isola rede** — serve para organização e RBAC
- Recursos como `Node` e `Namespace` são **cluster-wide**
