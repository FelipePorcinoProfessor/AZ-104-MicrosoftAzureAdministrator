---
demo:
    title: 'Demonstração 06: Administrar o Gerenciamento de Tráfego de Rede'
    module: 'Administrar o Gerenciamento de Tráfego de Rede'
layout: default
---


# 06 - Administrar o Gerenciamento de Tráfego de Rede

## Configurar o Azure Load Balancer

Nesta demonstração, aprenderemos como criar um load balancer público.

**Observação:** Esta demonstração requer uma rede virtual com pelo menos uma sub-rede.

**Referência**: [Introdução rápida: Criar um Azure Load Balancer público para balancear VMs usando o portal do Azure](https://learn.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)

**Mostrar o recurso 'Balanceamento de carga — me ajude a escolher (Load balancing - help me choose)' no portal**

1. Acesse o portal do Azure.

1. Pesquise e selecione **Balanceamento de carga — me ajude a escolher (Load balancing - help me choose)**.

1. Use o assistente para percorrer diferentes cenários.

**Criar um Azure Load Balancer**

1. Permaneça no portal do Azure.

1. Pesquise e selecione **Azure Load Balancer**. **Criar (Create)** um Azure Load Balancer.

1. Na guia **Básicos (Basics)**, discuta **SKU**, **Tipo (Type)** e **Camada (Tier)**.

1. Na guia **Configuração de IP de frontend (Frontend IP configuration)**, discuta o uso de um endereço IP público.

1. Na guia **Pools de backend (Backend pools)**, selecione a Virtual Network (rede virtual) com o intervalo de endereços IP.

1. Na guia **Regras de entrada (Inbound rules)**, crie uma **regra de balanceamento (Load balancing rule)**. Discuta parâmetros como **Protocolo (Protocol)**, **Portas (Ports)**, **Sondas de integridade (Health probes)** e **Persistência de sessão (Session persistence)**.


## Configurar o Azure Application Gateway

Nesta demonstração, aprenderemos como criar um Azure Application Gateway.

**Observação**: Para simplificar, crie novas redes virtuais e sub-redes conforme avança na configuração.

**Referência**: [Introdução rápida: Direcionar tráfego da web com o Azure Application Gateway - portal do Azure](https://learn.microsoft.com/azure/application-gateway/quick-create-portal)

**Criar o Azure Application Gateway**

1. Acesse o portal do Azure.

1. Pesquise e selecione **Azure Application Gateway**.

1. **Criar (Create)** um novo gateway.

1. Na guia **Básicos (Basics)**, discuta **Camadas (Tiers)**, **Dimensionamento automático (Autoscaling)** e **Contagem de instâncias (Instance counts)**.

1. Na guia **Frontends (Frontends)**, discuta os tipos de endereço IP.

1. Na guia **Backends (Backends)**, discuta quando usar um pool de backend vazio.

1. Na guia **Configuração (Configuration)**, discuta as **regras de roteamento (routing rules)**. Compare com as **regras do Load Balancer (load balancer rules)**.

1. Explique que, após o gateway ser criado, você adicionaria **alvos de backend (backend targets)** e realizaria testes.
