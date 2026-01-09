# infra-gitops

Repositório GitOps central para gerenciamento de aplicações no Amazon EKS utilizando ArgoCD.

## 📋 Sobre o Projeto

Este repositório contém as configurações GitOps para orquestração e gerenciamento automatizado de microserviços em um cluster Kubernetes (EKS) através do ArgoCD. O projeto implementa práticas de Infrastructure as Code (IaC) e Continuous Deployment (CD) para garantir que o estado desejado das aplicações seja mantido automaticamente.

## 🏗️ Estrutura do Projeto

```
infra-gitops/
├── argocd/
│   ├── applications/          # Definições das aplicações ArgoCD
│   │   ├── ms-orchestrator.yaml
│   │   ├── ms-order.yaml
│   │   ├── ms-payment.yaml
│   │   └── ms-production.yaml
│   └── apps-ingress/          # Configurações de Ingress
│       └── ingress.yaml
└── README.md
```

## 🚀 Microserviços Gerenciados

O projeto gerencia os seguintes microserviços:

| Microserviço | Repositório | Namespace | Path |
|--------------|-------------|-----------|------|
| **ms-orchestrator** | [Grupo-202-FIAP/ms-orchestrator](https://github.com/Grupo-202-FIAP/ms-orchestrator) | default | infra/k8s |
| **ms-order** | [Grupo-202-FIAP/ms-order](https://github.com/Grupo-202-FIAP/ms-order) | default | infra/k8s |
| **ms-payment** | [Grupo-202-FIAP/ms-payment](https://github.com/Grupo-202-FIAP/ms-payment) | default | infra/k8s |
| **ms-production** | [Grupo-202-FIAP/ms-production](https://github.com/Grupo-202-FIAP/ms-production) | default | infra/k8s |

## ⚙️ Configuração

### Pré-requisitos

- Cluster Kubernetes (EKS) configurado e acessível
- ArgoCD instalado e configurado no cluster
- Acesso ao cluster via `kubectl`
- Permissões adequadas para criar/atualizar recursos no namespace `argocd` e `default`

### Política de Sincronização

Todas as aplicações estão configuradas com sincronização automática habilitada:

- **Automated Sync**: Ativado - sincroniza automaticamente quando há mudanças no repositório
- **Prune**: Ativado - remove recursos que não estão mais no repositório
- **Self-Heal**: Ativado - restaura automaticamente o estado desejado se houver drift

## 🌐 Ingress

O projeto inclui uma configuração de Ingress utilizando AWS Application Load Balancer (ALB) para rotear o tráfego HTTP para os microserviços:

- **Path `/order`** → `ms-order` service (porta 80)
- **Path `/payment`** → `ms-payment` service (porta 80)
- **Path `/production`** → `ms-production` service (porta 80)
- **Path `/orchestrator`** → `ms-orchestrator` service (porta 80)

### Configurações do Ingress

- **Ingress Class**: `alb`
- **Scheme**: `internet-facing`
- **Target Type**: `ip`

## 📦 Como Usar

### Aplicar Configurações no ArgoCD

1. Clone este repositório:
```bash
git clone <url-do-repositorio>
cd infra-gitops
```

2. Aplique as aplicações ArgoCD:
```bash
kubectl apply -f argocd/applications/
```

3. Aplique a configuração de Ingress:
```bash
kubectl apply -f argocd/apps-ingress/
```

### Verificar Status das Aplicações

Você pode verificar o status das aplicações através da interface web do ArgoCD ou via CLI:

```bash
# Listar todas as aplicações
argocd app list

# Ver detalhes de uma aplicação específica
argocd app get ms-order

# Verificar status de sincronização
argocd app sync ms-order
```

## ➕ Adicionando uma Nova Aplicação

Para adicionar um novo microserviço ao GitOps:

1. Crie um novo arquivo em `argocd/applications/` seguindo o padrão:

```yaml
apiVersion: argoproj.io/v1alpha1
kind: Application
metadata:
  name: ms-nome-do-servico
  namespace: argocd
spec:
  project: default
  source:
    repoURL: https://github.com/Grupo-202-FIAP/ms-nome-do-servico
    targetRevision: main
    path: infra/k8s
  destination:
    server: https://kubernetes.default.svc
    namespace: default
  syncPolicy:
    automated:
      prune: true
      selfHeal: true
```

2. Se necessário, adicione uma rota no Ingress em `argocd/apps-ingress/ingress.yaml`

3. Aplique as mudanças:
```bash
kubectl apply -f argocd/applications/ms-nome-do-servico.yaml
```

## 🔧 Manutenção

### Atualizar uma Aplicação

As aplicações são sincronizadas automaticamente quando há mudanças nos repositórios de origem. Para forçar uma sincronização manual:

```bash
argocd app sync <nome-da-aplicacao>
```

### Remover uma Aplicação

```bash
kubectl delete application <nome-da-aplicacao> -n argocd
```

## 📚 Recursos Adicionais

- [Documentação do ArgoCD](https://argo-cd.readthedocs.io/)
- [AWS Load Balancer Controller](https://kubernetes-sigs.github.io/aws-load-balancer-controller/)
- [Kubernetes Ingress](https://kubernetes.io/docs/concepts/services-networking/ingress/)

## 👥 Contribuindo

1. Faça um fork do projeto
2. Crie uma branch para sua feature (`git checkout -b feature/nova-aplicacao`)
3. Commit suas mudanças (`git commit -m 'Adiciona nova aplicação'`)
4. Push para a branch (`git push origin feature/nova-aplicacao`)
5. Abra um Pull Request

## 📝 Licença

Este projeto faz parte do trabalho do Grupo 202 - FIAP.
