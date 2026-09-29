---
demo:
    title: 'Demonstração 05: Administrar Conectividade entre Sites'
    module: 'Administrar Conectividade entre Sites'
layout: default
---

# 05 - Administrar Conectividade entre Sites

## Configurar VNet Peering

**Nota:** Para esta demonstração você precisará de duas virtual networks.

**Referência**: [Conectar virtual networks com VNet peering - tutorial](https://docs.microsoft.com/azure/virtual-network/tutorial-connect-virtual-networks-portal)

**Configure VNet peering on the first virtual network**

1. No **Azure portal**, selecione a primeira virtual network. Revise o valor do peering.

1. Em **Settings**, selecione **Peerings** e **+ Add** um novo peering.

1. Configure o peering da segunda virtual network. Use os ícones de informação para revisar as diferentes configurações.

1. Quando o peering for concluído, verifique o **Peering status**.

**Confirm VNet peering on the second virtual network**

1. No **Azure portal**, selecione a segunda virtual network.

1. Em **Settings**, selecione **Peerings**.

1. Observe que um peering foi criado automaticamente. Observe que o **Peering Status** está **Connected**.

1. No laboratório, os alunos irão criar o peering e testar a conexão entre virtual machines.

## Configurar Roteamento de Rede e Endpoints

Nesta demonstração, aprenderemos como criar um route table, definir
uma route personalizada e associar a route a uma subnet.

**Nota:** Esta demonstração requer uma virtual network com pelo menos uma subnet.

**Referência**: [Roteamento de tráfego de rede - tutorial - Azure portal](https://learn.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal#create-a-route-table)

**Create a routing table**

1. Se houver tempo, revise o diagrama do tutorial. Explique por que é necessário criar uma user-defined route.

1. Acesse o Azure portal.

1. Pesquise e selecione **Route tables**. Discuta quando **propagate gateway routes** deve ser usado.

1. Crie um routing table, explique quaisquer configurações incomuns.

1. Aguarde a implantação do novo routing table.

**Add a route**

1.  Selecione seu novo routing table, e então selecione **Routes**.

1.  Crie uma nova **route**. Discuta os diferentes **hop types** que estão disponíveis.

1.  Crie a nova route e aguarde a implantação do recurso.

**Associate a route table to a subnet**

1.  Navegue até a subnet que você deseja associar ao routing table.

1.  Selecione **Route table** e escolha seu novo routing table.

1.  Selecione **Save** para salvar suas alterações.
