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

**Configurar o VNet peering na primeira virtual network**

1. No **Azure portal**, selecione a primeira virtual network. Revise o valor do emparelhamento (peering).

1. Em **Configurações (Settings)**, selecione **Emparelhamentos (Peerings)** e **+ Adicionar (+ Add)** um novo emparelhamento (peering).

1. Configure o emparelhamento (peering) da segunda virtual network. Use os ícones de informação para revisar as diferentes configurações.

1. Quando o emparelhamento for concluído, verifique o status do emparelhamento (Peering status).

**Confirmar o VNet peering na segunda virtual network**

1. No **Azure portal**, selecione a segunda virtual network.

1. Em **Configurações (Settings)**, selecione **Emparelhamentos (Peerings)**.

1. Observe que um emparelhamento (peering) foi criado automaticamente. Observe que o **Status do emparelhamento (Peering Status)** está **Conectado (Connected)**.

1. No laboratório, os alunos irão criar o emparelhamento (peering) e testar a conexão entre máquinas virtuais (virtual machines).

## Configurar Roteamento de Rede e Endpoints

Nesta demonstração, aprenderemos como criar um route table, definir uma route personalizada e associar a route a uma subnet.

**Nota:** Esta demonstração requer uma virtual network com pelo menos uma subnet.

**Referência**: [Roteamento de tráfego de rede - tutorial - Azure portal](https://learn.microsoft.com/azure/virtual-network/tutorial-create-route-table-portal#create-a-route-table)

**Criar um route table**

1. Se houver tempo, revise o diagrama do tutorial. Explique por que é necessário criar uma user-defined route.

1. Acesse o **Azure portal**.

1. Pesquise e selecione **Tabelas de rotas (Route tables)**. Discuta quando a opção propagar rotas de gateway (propagate gateway routes) deve ser usada.

1. Crie um route table e explique quaisquer configurações incomuns.

1. Aguarde a implantação do novo route table.

**Adicionar uma route**

1. Selecione seu novo route table e, em seguida, selecione **Rotas (Routes)**.

1. Crie uma nova route (rota). Discuta os diferentes tipos de salto (hop types) que estão disponíveis.

1. Crie a nova route e aguarde a implantação do recurso.

**Associar um route table a uma subnet**

1. Navegue até a subnet que você deseja associar ao route table.

1. Selecione **Tabela de rotas (Route table)** e escolha seu novo route table.

1. Selecione **Salvar (Save)** para salvar suas alterações.
