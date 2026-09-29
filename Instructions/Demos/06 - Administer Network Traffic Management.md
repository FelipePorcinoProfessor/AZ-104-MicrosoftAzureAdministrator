---
demo:
    title: 'Demonstração 06: Administrar o Gerenciamento de Tráfego de Rede'
    module: 'Administrar o Gerenciamento de Tráfego de Rede'
layout: default
---


# 06 - Administrar o Gerenciamento de Tráfego de Rede

## Configure Azure Load Balancer

Nesta demonstração, aprenderemos como criar um public load balancer.

**Observação:** Esta demonstração requer uma virtual network com pelo menos uma subnet.

**Reference**: [Quickstart: Create a public load balancer to load balance VMs using the Azure portal](https://learn.microsoft.com/azure/load-balancer/quickstart-load-balancer-standard-public-portal)

**Mostrar o recurso help me choose do portal**

1. Acesse o Azure portal.

1. Pesquise por e selecione **Load balancing - help me choose**.

1. Use o assistente para percorrer diferentes cenários.

**Criar um load balancer**

1. Continue no Azure portal.

1. Pesquise por e selecione **Load balancer**. **Create** um load balancer.

1. Na guia **Basics**, discuta **SKU**, **Type** e **Tier**.

1. Na guia **Frontend IP configuration**, discuta o uso de um public IP address.

1. Na guia **Backend pools**, selecione a virtual network com o intervalo de endereços IP.

1. Na guia **Inbound rules**, crie uma load balancing rule. Discuta parâmetros como **Protocol**, **Ports**, **Health probes** e **Session persistence**.


## Configure Azure Application Gateway

Nesta demonstração, aprenderemos como criar um Azure Application Gateway.

**Observação**: Para simplificar, crie novas virtual networks e subnets conforme avança na configuração.

**Reference**: [Quickstart: Direct web traffic with Azure Application Gateway - Azure portal](https://learn.microsoft.com/azure/application-gateway/quick-create-portal)

**Criar o Azure Application Gateway**

1. Acesse o Azure portal.

1. Pesquise por e selecione **Azure Application Gateway**.

1. **Create** um novo gateway.

1. Na guia **Basics**, discuta **Tiers**, **Autoscaling** e **Instance counts**.

1. Na guia **Frontends**, discuta os tipos de IP address.

1. Na guia **Backends**, discuta quando usar um backend pool vazio.

1. Na guia **Configuration**, discuta as routing rules. Compare com as load balancer rules.

1. Explique que, após o gateway ser criado, você adicionaria backend targets e faria testes.
