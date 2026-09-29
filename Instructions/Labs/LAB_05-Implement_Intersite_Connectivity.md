---
lab:
  title: 'Lab 05: Implementar Conectividade entre Sites'
  module: Administrar Conectividade entre Sites
  description: Criar e configurar virtual networks, rotas e peerings.
  duration: 50 minutes
  level: 400
  islab: true
  primarytopics:
  - Azure
  - Virtual networks
  - Routing
  - Peering
  - Network Watcher
layout: default
---

# Lab 05 - Implementar Conectividade entre Sites

## Introdução do laboratório

Neste laboratório, você explora a comunicação entre virtual networks. Você implementará virtual network peering e testará as conexões. Você também criará uma rota personalizada.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura poderá afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua organização segmenta aplicativos e serviços principais de TI (como DNS e serviços de segurança) de outras partes do negócio, incluindo seu departamento de manufatura. No entanto, em alguns cenários, aplicativos e serviços na área central precisam se comunicar com aplicativos e serviços na área de manufatura. Neste laboratório, você configura a conectividade entre as áreas segmentadas. Este é um cenário comum para separar produção de desenvolvimento ou para separar uma subsidiária de outra.

## Diagrama de arquitetura

![Diagrama de arquitetura do Lab 05](../media/az104-lab05-architecture.png)

## Habilidades

+ Tarefa 1: Criar uma máquina virtual em uma virtual network.
+ Tarefa 2: Criar uma máquina virtual em uma virtual network diferente.
+ Tarefa 3: Usar Network Watcher para testar a conexão entre máquinas virtuais.
+ Tarefa 4: Configurar peerings de virtual network entre diferentes virtual networks.
+ Tarefa 5: Usar Azure PowerShell para testar a conexão entre máquinas virtuais.
+ Tarefa 6: Criar uma rota personalizada.

## Tarefa 1: Criar uma máquina virtual e virtual network de serviços centrais

Nesta tarefa, você cria uma virtual network de serviços centrais com uma máquina virtual.

1. Faça logon no **Azure portal** - `https://portal.azure.com`.

1. Pesquise por e selecione `Virtual Machines`.

1. Na página de virtual machines, selecione **Create** e então selecione **Virtual machine**.

1. Na aba Basics, use as seguintes informações para completar o formulário e então selecione **Next : Disks >**. Para qualquer configuração não especificada, mantenha o valor padrão.

    | Configuração | Valor |
    | --- | --- |
    | Subscription |  *your subscription* |
    | Resource group |  `az104-rg5` (Se necessário, **Create new**. ) |
    | Virtual machine name |    `CoreServicesVM` |
    | Region | **(US) East US** |
    | Availability options | No infrastructure redundancy required |
    | Security type | **Standard** |
    | Image (See all images) | **Windows Server 2025 Datacenter - x64 Gen2** (observe suas outras opções) |
    | Size | **Standard_D2s_v5** |
    | Username | `localadmin` |
    | Password | **Provide a complex password** |
    | Public inbound ports | **None** |

    > [!NOTE]
    > Use **Standard_D2s_v5** first. If the size is unavailable or Azure lacks capacity, select **Standard_D2s_v6**. If that size is also unavailable, select **Standard_D2s_v7**.

    ![Captura de tela da página Básica de criação da máquina virtual. ](../media/az104-lab05-createcorevm.png)

1. Na aba **Disks** mantenha os padrões e então selecione **Next : Networking >**.

1. Na aba **Networking**, para Virtual network, selecione **Create new**.

1. Use as seguintes informações para configurar a virtual network e então selecione **OK**. Se necessário, remova ou substitua as informações existentes.

    | Configuração | Valor |
    | --- | --- |
    | Name | `CoreServicesVnet` (Create or edit) |
    | Address range | `10.0.0.0/16`  |
    | Subnet Name | `Core` |
    | Subnet address range | `10.0.0.0/24` |

1. Selecione a aba **Monitoring**. Para Boot diagnostics, selecione **Disable**.

1. Selecione **Review + create**, e então selecione **Create**.

1. Você não precisa aguardar a criação dos recursos. Continue para a próxima tarefa.

    >**Nota:** Percebeu que nesta tarefa você criou a virtual network enquanto criava a máquina virtual? Você também poderia criar a infraestrutura da virtual network primeiro e então adicionar as máquinas virtuais.

## Tarefa 2: Criar uma máquina virtual em uma virtual network diferente

Nesta tarefa, você cria uma virtual network de serviços de manufatura com uma máquina virtual.

1. No Azure portal, pesquise por e navegue até **Virtual Machines**.

1. Na página de virtual machines, selecione **Create** e então selecione **Virtual machine**.

1. Na aba Basics, use as seguintes informações para completar o formulário e então selecione **Next : Disks >**. Para qualquer configuração não especificada, mantenha o valor padrão.

    | Configuração | Valor |
    | --- | --- |
    | Subscription |  *your subscription* |
    | Resource group |  `az104-rg5` |
    | Virtual machine name |    `ManufacturingVM` |
    | Region | **(US) East US** |
    | Security type | **Standard** |
    | Availability options | No infrastructure redundancy required |
    | Image (See all images) | **Windows Server 2025 Datacenter - x64 Gen2** |
    | Size | **Standard_D2s_v5** |
    | Username | `localadmin` |
    | Password | **Provide a complex password** |
    | Public inbound ports | **None** |

    > [!NOTE]
    > Use **Standard_D2s_v5** first. If the size is unavailable or Azure lacks capacity, select **Standard_D2s_v6**. If that size is also unavailable, select **Standard_D2s_v7**.

1. Na aba **Disks** mantenha os padrões e então selecione **Next : Networking >**.

1. Na aba **Networking**, para Virtual network, selecione **Create new**.

1. Use as seguintes informações para configurar a virtual network e então selecione **OK**.  Se necessário, remova ou substitua o intervalo de endereços existente.

    | Configuração | Valor |
    | --- | --- |
    | Name | `ManufacturingVnet` |
    | Address range | `172.16.0.0/16`  |
    | Subnet Name | `Manufacturing` |
    | Subnet address range | `172.16.0.0/24` |

1. Selecione a aba **Monitoring**. Para Boot Diagnostics, selecione **Disable**.

1. Selecione **Review + create**, e então selecione **Create**.

## Tarefa 3: Usar Network Watcher para testar a conexão entre máquinas virtuais


Nesta tarefa, você verificará se recursos em virtual networks peered podem se comunicar entre si. Network Watcher será usado para testar a conexão. Antes de continuar, certifique-se de que ambas as máquinas virtuais foram implantadas e estão em execução.

1. No Azure portal, pesquise por e selecione `Network Watcher`.

1. No Network Watcher, no menu Network diagnostic tools, selecione **Connection troubleshoot**.

1. Use as seguintes informações para preencher os campos na página **Connection troubleshoot**.

    | Campo | Valor |
    | --- | --- |
    | Source type           | **Virtual machine**   |
    | Virtual machine       | **CoreServicesVM**    |
    | Destination type      | **Select a virtual machine**   |
    | Virtual machine       | **ManufacturingVM**   |
    | Preferred IP Version  | **Both**              |
    | Protocol              | **TCP**               |
    | Destination port      | `3389`                |
    | Source port           | *Blank*         |
    | Diagnostic tests      | *Defaults*      |

    ![Azure Portal mostrando as configurações do Connection Troubleshoot.](../media/az104-lab05-connection-troubleshoot.png)

1. Selecione **Run diagnostic tests**.

    >**Nota**: Pode levar alguns minutos para que os resultados sejam retornados. As seleções na tela ficarão acinzentadas enquanto os resultados estão sendo coletados. Observe que o **Connectivity test** mostra **Unreachable**. Isso faz sentido porque as máquinas virtuais estão em virtual networks diferentes.


## Tarefa 4: Configurar peerings de virtual network entre virtual networks

Nesta tarefa, você cria um virtual network peering para habilitar comunicações entre recursos nas virtual networks.

1. No Azure portal, selecione a virtual network `CoreServicesVnet`.

1. Em CoreServicesVnet, em **Settings**, selecione **Peerings**.

1. Em CoreServicesVnet, em Peerings, selecione **+ Add**. Se não especificado, mantenha o padrão.

    | **Parâmetro**                                    | **Valor**                             |
    | --------------------------------------------- | ------------------------------------- |
    | Peering link name                             | `ManufacturingVnet-to-CoreServicesVnet` |
    | Virtual network    | **ManufacturingVnet (az104-rg5)**  |
    | Allow 'ManufacturingVnet' to access 'CoreServicesVnet'  | selecionado (padrão) |
    | Allow 'ManufacturingVnet' to receive forwarded traffic from 'CoreServicesVnet' | selecionado  |
    | Peering link name                             | `CoreServicesVnet-to-ManufacturingVnet` |
    | Allow 'CoreServicesVnet' to access 'ManufacturingVnet'            | selecionado (padrão) |
    | Allow 'CoreServicesVnet' to receive forwarded traffic from 'ManufacturingVnet' | selecionado |


4. Clique em **Add**.

5. Em CoreServicesVnet, sob Peerings, verifique se o peering **CoreServicesVnet-to-ManufacturingVnet** está listado. Atualize a página para garantir que o **Peering status** esteja **Connected**.

6. Mude para a **ManufacturingVnet** e verifique se o peering **ManufacturingVnet-to-CoreServicesVnet** está listado. Certifique-se de que o **Peering status** esteja **Connected**. Pode ser necessário **Refresh** a página.

## Tarefa 5: Usar Azure PowerShell para testar a conexão entre máquinas virtuais

Nesta tarefa, você retestará a conexão entre as máquinas virtuais em virtual networks diferentes.

### Verificar o endereço IP privado do CoreServicesVM

1. No Azure portal, pesquise por e selecione a `CoreServicesVM` virtual machine.

1. No painel **Overview**, na seção **Networking**, registre o **Private IP address** da máquina. Você precisará dessa informação para testar a conexão.

### Testar a conexão para o CoreServicesVM a partir do **ManufacturingVM**.

>**Você sabia?** Existem várias maneiras de verificar conexões. Nesta tarefa, você usa **Run command**. Você também poderia continuar a usar o Network Watcher. Ou poderia usar uma [Remote Desktop Connection](https://learn.microsoft.com/azure/virtual-machines/windows/connect-rdp#connect-to-the-virtual-machine) para acessar a máquina virtual. Uma vez conectado, use **test-connection**. Se tiver tempo, experimente RDP.

1. Mude para a máquina virtual `CoreServicesVM`. No painel **Operations**, selecione **Run command**, então selecione **RunPowerShellScript**. Execute o seguinte comando para habilitar a regra do Windows Firewall que permite tráfego RDP de entrada:

    ```Powershell
    Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
    ```

1. Aguarde a conclusão do script, então mude para a máquina virtual `ManufacturingVM`.

1. No painel **Operations**, selecione o painel **Run command**.

1. Selecione **RunPowerShellScript** e execute o comando **Test-NetConnection**. Certifique-se de usar o endereço IP privado do **CoreServicesVM**.

    ```Powershell
    Test-NetConnection <CoreServicesVM private IP address> -port 3389
    ```
1. Pode levar alguns minutos para que o script exceda o tempo limite. O topo da página exibirá a mensagem informativa *Script execution in progress...*


1. O teste de conexão deverá ter sucesso porque o peering foi configurado. O nome do computador e o endereço remoto nesta imagem podem ser diferentes.

   ![Janela do PowerShell com Test-NetConnection bem-sucedido.](../media/az104-lab05-success.png)

## Tarefa 6: Criar uma rota personalizada

Nesta tarefa, você quer controlar o tráfego de rede entre a subnet de perímetro e a subnet interna de serviços centrais. Um virtual network appliance será instalado na subnet de perímetro e todo o tráfego deve ser roteado para lá.

1. Pesquise por e selecione `CoreServicesVnet`.

1. Selecione **Subnets** e então **+ Subnet**. Certifique-se de selecionar **Add** para salvar suas alterações.

    | Configuração | Valor |
    | --- | --- |
    | Name | `perimeter` |
    | Starting address | `10.0.1.0/24`  |


1. No Azure portal, pesquise por e selecione `Route tables`, selecione **+ Create**.

1. Insira os seguintes detalhes, selecione **Review + create**, e então selecione **Create**.

    | Configuração | Valor |
    | --- | --- |
    | Subscription | your subscription |
    | Resource group | `az104-rg5`  |
    | Region | **East US** |
    | Name | `rt-CoreServices` |
    | Enable peering routes | **Yes** |

1. Após a implantação da route table, pesquise por e selecione **Route Tables**.

1. Selecione o recurso (não a caixa de seleção) **rt-CoreServices**

1. Expanda **Settings** então selecione **Routes** e então **+ Add**. Crie uma rota de um futuro Network Virtual Appliance (NVA) para a virtual network CoreServices.

    | Configuração | Valor |
    | --- | --- |
    | Route name | `PerimetertoCore` |
    | Destination type | **IP Addresses** |
    | Destination IP addresses | `10.0.0.0/16` (core services virtual network) |
    | Next hop type | **Virtual appliance** (observe suas outras escolhas) |
    | Next hop address | `10.0.1.7` (future NVA) |

1. Selecione **Add**. A última coisa a fazer é associar a rota à subnet.

1. Selecione **Subnets** e então **+ Associate**. Complete a configuração.

    | Configuração | Valor |
    | --- | --- |
    | Virtual network | **CoreServicesVnet (az104-rg5)** |
    | Subnet | **Perimeter** |

>**Nota**: Você criou uma user defined route para direcionar o tráfego do DMZ para o novo NVA.

## Limpeza dos seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que os custos sejam minimizados. A maneira mais fácil de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ No Azure portal, selecione o resource group, selecione **Delete the resource group**, **Enter resource group name**, e então clique em **Delete**. Quando o diálogo **Delete confirmation** aparecer informando que excluir o resource group é permanente e não pode ser desfeito, clique em **Delete** novamente para concluir a exclusão.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Expanda seu aprendizado com o Copilot
Copilot pode ajudá-lo a aprender como usar as ferramentas de scripting do Azure. Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue para *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.

+ How can I use Azure PowerShell or Azure CLI commands to add a virtual network peering between vnet1 and vnet2?
+ Create a table highlighting various Azure and 3rd party monitoring tools supported on Azure. Highlight when to use each tool.
+ When would I create a custom network route in Azure?

## Aprenda mais com treinamentos em ritmo próprio

+ [Introduction to Virtual Networks](https://learn.microsoft.com/training/modules/introduction-to-azure-virtual-networks/). Neste módulo, você aprende a projetar e implementar serviços de rede do Azure. Você aprende sobre virtual networks, public and private IPs, DNS, virtual network peering, routing e Azure Virtual NAT.
+ [Manage and control traffic flow in your Azure deployment with routes](https://learn.microsoft.com/training/modules/control-network-traffic-flow-with-routes/). Aprenda a controlar o tráfego de rede de virtual networks do Azure implementando rotas personalizadas.


## Principais pontos

Parabéns por completar o laboratório. Aqui estão os principais pontos deste laboratório.

+ Por padrão, recursos em virtual networks diferentes não podem se comunicar.
+ Virtual network peering permite conectar de forma transparente duas ou mais virtual networks no Azure.
+ Virtual networks pareadas aparecem como uma única para fins de conectividade.
+ O tráfego entre máquinas virtuais em virtual networks pareadas usa a infraestrutura backbone da Microsoft.
+ Rotas definidas pelo sistema são criadas automaticamente para cada subnet em uma virtual network. Rotas definidas pelo usuário substituem ou adicionam às rotas padrão do sistema.
+ Azure Network Watcher fornece um conjunto de ferramentas para monitorar, diagnosticar e visualizar métricas e logs para recursos IaaS do Azure.
