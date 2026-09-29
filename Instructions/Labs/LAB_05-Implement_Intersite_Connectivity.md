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

# Laboratório 05 - Implementar Conectividade entre Sites

## Introdução do laboratório

Neste laboratório, você explora a comunicação entre redes virtuais. Você implementará emparelhamento de redes virtuais (virtual network peering) e testará as conexões. Você também criará uma rota personalizada.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura poderá afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua organização segmenta aplicativos e serviços principais de TI (como DNS e serviços de segurança) de outras partes do negócio, incluindo seu departamento de manufatura. No entanto, em alguns cenários, aplicativos e serviços na área central precisam se comunicar com aplicativos e serviços na área de manufatura. Neste laboratório, você configura a conectividade entre as áreas segmentadas. Este é um cenário comum para separar produção de desenvolvimento ou para separar uma subsidiária de outra.

## Diagrama de arquitetura

![Diagrama de arquitetura do Lab 05](../media/az104-lab05-architecture.png)

## Habilidades

+ Tarefa 1: Criar uma máquina virtual em uma rede virtual.
+ Tarefa 2: Criar uma máquina virtual em uma rede virtual diferente.
+ Tarefa 3: Usar Network Watcher para testar a conexão entre máquinas virtuais.
+ Tarefa 4: Configurar emparelhamentos de redes virtuais (peerings) entre diferentes redes virtuais.
+ Tarefa 5: Usar Azure PowerShell para testar a conexão entre máquinas virtuais.
+ Tarefa 6: Criar uma rota personalizada.

## Tarefa 1: Criar uma máquina virtual e rede virtual de serviços centrais

Nesta tarefa, você cria uma rede virtual de serviços centrais com uma máquina virtual.

1. Faça logon no Azure portal - `https://portal.azure.com`.

1. Pesquise por e selecione `Virtual Machines`.

1. Na página de máquinas virtuais, selecione **Criar (Create)** e então selecione **Máquina virtual (Virtual machine)**.

1. Na aba Básicos (Basics), use as seguintes informações para completar o formulário e então selecione **Próximo : Discos (Next : Disks >)**. Para qualquer configuração não especificada, mantenha o valor padrão.

    | Configuração | Valor |
    | --- | --- |
    | Assinatura |  *sua assinatura* |
    | Grupo de recursos |  `az104-rg5` (Se necessário, **Criar novo (Create new)**.) |
    | Nome da máquina virtual |    `CoreServicesVM` |
    | Região | **(US) East US** |
    | Opções de disponibilidade | Sem redundância de infraestrutura necessária (No infrastructure redundancy required) |
    | Tipo de segurança | **Padrão (Standard)** |
    | Imagem (See all images) | **Windows Server 2025 Datacenter - x64 Gen2** (observe suas outras opções) |
    | Tamanho | **Standard_D2s_v5** |
    | Nome de usuário | `localadmin` |
    | Senha | **Forneça uma senha complexa (Provide a complex password)** |
    | Portas públicas de entrada | **Nenhuma (None)** |

    > [!NOTE]
    > Use **Standard_D2s_v5** primeiro. Se o tamanho estiver indisponível ou se o Azure não tiver capacidade, selecione **Standard_D2s_v6**. Se esse tamanho também estiver indisponível, selecione **Standard_D2s_v7**.

    ![Captura de tela da página Básica de criação da máquina virtual. ](../media/az104-lab05-createcorevm.png)

1. Na aba **Discos (Disks)** mantenha os padrões e então selecione **Próximo : Rede (Next : Networking >)**.

1. Na aba **Rede (Networking)**, para Virtual network, selecione **Criar novo (Create new)**.

1. Use as seguintes informações para configurar a rede virtual e então selecione **OK**. Se necessário, remova ou substitua as informações existentes.

    | Configuração | Valor |
    | --- | --- |
    | Nome | `CoreServicesVnet` (Criar ou editar (Create or edit)) |
    | Faixa de endereços | `10.0.0.0/16`  |
    | Nome da Subnet | `Core` |
    | Faixa de endereços da Subnet | `10.0.0.0/24` |

1. Selecione a aba **Monitoramento (Monitoring)**. Para Diagnósticos de inicialização, selecione **Desabilitar (Disable)**.

1. Selecione **Revisar + criar (Review + create)**, e então selecione **Criar (Create)**.

1. Você não precisa aguardar a criação dos recursos. Continue para a próxima tarefa.

    >**Nota:** Percebeu que nesta tarefa você criou a rede virtual enquanto criava a máquina virtual? Você também poderia criar a infraestrutura da rede virtual primeiro e então adicionar as máquinas virtuais.

## Tarefa 2: Criar uma máquina virtual em uma rede virtual diferente

Nesta tarefa, você cria uma rede virtual de serviços de manufatura com uma máquina virtual.

1. No Azure portal, pesquise por e navegue até **Máquinas Virtuais (Virtual Machines)**.

1. Na página de máquinas virtuais, selecione **Criar (Create)** e então selecione **Máquina virtual (Virtual machine)**.

1. Na aba Básicos (Basics), use as seguintes informações para completar o formulário e então selecione **Próximo : Discos (Next : Disks >)**. Para qualquer configuração não especificada, mantenha o valor padrão.

    | Configuração | Valor |
    | --- | --- |
    | Assinatura |  *sua assinatura* |
    | Grupo de recursos |  `az104-rg5` |
    | Nome da máquina virtual |    `ManufacturingVM` |
    | Região | **(US) East US** |
    | Tipo de segurança | **Padrão (Standard)** |
    | Opções de disponibilidade | Sem redundância de infraestrutura necessária (No infrastructure redundancy required) |
    | Imagem (See all images) | **Windows Server 2025 Datacenter - x64 Gen2** |
    | Tamanho | **Standard_D2s_v5** |
    | Nome de usuário | `localadmin` |
    | Senha | **Forneça uma senha complexa (Provide a complex password)** |
    | Portas públicas de entrada | **Nenhuma (None)** |

    > [!NOTE]
    > Use **Standard_D2s_v5** primeiro. Se o tamanho estiver indisponível ou se o Azure não tiver capacidade, selecione **Standard_D2s_v6**. Se esse tamanho também estiver indisponível, selecione **Standard_D2s_v7**.

1. Na aba **Discos (Disks)** mantenha os padrões e então selecione **Próximo : Rede (Next : Networking >)**.

1. Na aba **Rede (Networking)**, para Virtual network, selecione **Criar novo (Create new)**.

1. Use as seguintes informações para configurar a rede virtual e então selecione **OK**.  Se necessário, remova ou substitua o intervalo de endereços existente.

    | Configuração | Valor |
    | --- | --- |
    | Nome | `ManufacturingVnet` |
    | Faixa de endereços | `172.16.0.0/16`  |
    | Nome da Subnet | `Manufacturing` |
    | Faixa de endereços da Subnet | `172.16.0.0/24` |

1. Selecione a aba **Monitoramento (Monitoring)**. Para Diagnósticos de inicialização, selecione **Desabilitar (Disable)**.

1. Selecione **Revisar + criar (Review + create)**, e então selecione **Criar (Create)**.

## Tarefa 3: Usar Network Watcher para testar a conexão entre máquinas virtuais


Nesta tarefa, você verificará se recursos em redes virtuais emparelhadas podem se comunicar entre si. Network Watcher será usado para testar a conexão. Antes de continuar, certifique-se de que ambas as máquinas virtuais foram implantadas e estão em execução.

1. No Azure portal, pesquise por e selecione `Network Watcher`.

1. No Network Watcher, no menu Ferramentas de diagnóstico de rede (Network diagnostic tools), selecione **Solução de problemas de conexão (Connection troubleshoot)**.

1. Use as seguintes informações para preencher os campos na página **Solução de problemas de conexão (Connection troubleshoot)**.

    | Campo | Valor |
    | --- | --- |
    | Tipo de origem           | **Máquina virtual (Virtual machine)**   |
    | Máquina virtual de origem       | **CoreServicesVM**    |
    | Tipo de destino      | **Selecionar uma máquina virtual (Select a virtual machine)**   |
    | Máquina virtual de destino       | **ManufacturingVM**   |
    | Versão de IP preferida  | **Ambas (Both)**              |
    | Protocolo              | **TCP**               |
    | Porta de destino      | `3389`                |
    | Porta de origem           | *Em branco*         |
    | Testes de diagnóstico      | *Padrões*      |

    ![Azure Portal mostrando as configurações do Connection Troubleshoot.](../media/az104-lab05-connection-troubleshoot.png)

1. Selecione **Executar testes de diagnóstico (Run diagnostic tests)**.

    >**Nota**: Pode levar alguns minutos para que os resultados sejam retornados. As seleções na tela ficarão acinzentadas enquanto os resultados estão sendo coletados. Observe que o teste de conectividade (Connectivity test) mostra "Unreachable". Isso faz sentido porque as máquinas virtuais estão em redes virtuais diferentes.

## Tarefa 4: Configurar emparelhamentos de redes virtuais entre redes virtuais

Nesta tarefa, você cria um emparelhamento de rede virtual para habilitar comunicações entre recursos nas redes virtuais.

1. No Azure portal, selecione a rede virtual `CoreServicesVnet`.

1. Em CoreServicesVnet, em **Configurações (Settings)**, selecione **Emparelhamentos (Peerings)**.

1. Em CoreServicesVnet, em Emparelhamentos, selecione **+ Adicionar (+ Add)**. Se não especificado, mantenha o padrão.

    | **Parâmetro**                                    | **Valor**                             |
    | --------------------------------------------- | ------------------------------------- |
    | Nome do link de peering (Peering link name)                             | `ManufacturingVnet-to-CoreServicesVnet` |
    | Virtual network    | **ManufacturingVnet (az104-rg5)**  |
    | Permitir que 'ManufacturingVnet' acesse 'CoreServicesVnet' (Allow 'ManufacturingVnet' to access 'CoreServicesVnet')  | selecionado (padrão) |
    | Permitir que 'ManufacturingVnet' receba tráfego encaminhado de 'CoreServicesVnet' (Allow 'ManufacturingVnet' to receive forwarded traffic from 'CoreServicesVnet') | selecionado  |
    | Nome do link de peering (Peering link name)                             | `CoreServicesVnet-to-ManufacturingVnet` |
    | Permitir que 'CoreServicesVnet' acesse 'ManufacturingVnet' (Allow 'CoreServicesVnet' to access 'ManufacturingVnet')            | selecionado (padrão) |
    | Permitir que 'CoreServicesVnet' receba tráfego encaminhado de 'ManufacturingVnet' (Allow 'CoreServicesVnet' to receive forwarded traffic from 'ManufacturingVnet') | selecionado |

4. Clique em **Adicionar (Add)**.

5. Em CoreServicesVnet, sob Emparelhamentos, verifique se o emparelhamento **CoreServicesVnet-to-ManufacturingVnet** está listado. Atualize a página para garantir que o status do emparelhamento (Peering status) esteja **Conectado (Connected)**.

6. Mude para a **ManufacturingVnet** e verifique se o emparelhamento **ManufacturingVnet-to-CoreServicesVnet** está listado. Certifique-se de que o status do emparelhamento (Peering status) esteja **Conectado (Connected)**. Pode ser necessário **Atualizar (Refresh)** a página.

## Tarefa 5: Usar Azure PowerShell para testar a conexão entre máquinas virtuais

Nesta tarefa, você retestará a conexão entre as máquinas virtuais em redes virtuais diferentes.

### Verificar o endereço IP privado do CoreServicesVM

1. No Azure portal, pesquise por e selecione a máquina virtual `CoreServicesVM`.

1. No painel **Visão geral (Overview)**, na seção **Rede (Networking)**, registre o **Endereço IP privado (Private IP address)** da máquina. Você precisará dessa informação para testar a conexão.

### Testar a conexão para o CoreServicesVM a partir do **ManufacturingVM**.

>**Você sabia?** Existem várias maneiras de verificar conexões. Nesta tarefa, você usa **Executar comando (Run command)**. Você também poderia continuar a usar o Network Watcher. Ou poderia usar uma [Remote Desktop Connection](https://learn.microsoft.com/azure/virtual-machines/windows/connect-rdp#connect-to-the-virtual-machine) para acessar a máquina virtual. Uma vez conectado, use **test-connection**. Se tiver tempo, experimente RDP.

1. Mude para a máquina virtual `CoreServicesVM`. No painel **Operações (Operations)**, selecione **Executar comando (Run command)**, então selecione **Executar script do PowerShell (RunPowerShellScript)**. Execute o seguinte comando para habilitar a regra do Windows Firewall que permite tráfego RDP de entrada:

    ```Powershell
    Enable-NetFirewallRule -DisplayGroup "Remote Desktop"
    ```

1. Aguarde a conclusão do script, então mude para a máquina virtual `ManufacturingVM`.

1. No painel **Operações (Operations)**, selecione o painel **Executar comando (Run command)**.

1. Selecione **Executar script do PowerShell (RunPowerShellScript)** e execute o comando **Test-NetConnection**. Certifique-se de usar o endereço IP privado do **CoreServicesVM**.

    ```Powershell
    Test-NetConnection <CoreServicesVM private IP address> -port 3389
    ```

1. Pode levar alguns minutos para que o script exceda o tempo limite. O topo da página exibirá a mensagem informativa *Execução do script em andamento...* (*Script execution in progress...*)


1. O teste de conexão deverá ter sucesso porque o emparelhamento foi configurado. O nome do computador e o endereço remoto nesta imagem podem ser diferentes.

   ![Janela do PowerShell com Test-NetConnection bem-sucedido.](../media/az104-lab05-success.png)

## Tarefa 6: Criar uma rota personalizada

Nesta tarefa, você quer controlar o tráfego de rede entre a subnet de perímetro e a subnet interna de serviços centrais. Um Network Virtual Appliance (NVA) será instalado na subnet de perímetro e todo o tráfego deve ser roteado para lá.

1. Pesquise por e selecione `CoreServicesVnet`.

1. Selecione **Sub-redes (Subnets)** e então **+ Sub-rede (+ Subnet)**. Certifique-se de selecionar **Adicionar (Add)** para salvar suas alterações.

    | Configuração | Valor |
    | --- | --- |
    | Nome | `perimeter` |
    | Endereço inicial | `10.0.1.0/24`  |


1. No Azure portal, pesquise por e selecione `Route tables`, selecione **+ Criar (+ Create)**.

1. Insira os seguintes detalhes, selecione **Revisar + criar (Review + create)**, e então selecione **Criar (Create)**.

    | Configuração | Valor |
    | --- | --- |
    | Assinatura | sua assinatura |
    | Grupo de recursos | `az104-rg5`  |
    | Região | **East US** |
    | Nome | `rt-CoreServices` |
    | Habilitar rotas de peering | **Sim (Yes)** |

1. Após a implantação da tabela de rotas (route table), pesquise por e selecione **Tabelas de rotas (Route Tables)**.

1. Selecione o recurso (não a caixa de seleção) **rt-CoreServices**

1. Expanda **Configurações (Settings)** então selecione **Rotas (Routes)** e então **+ Adicionar (+ Add)**. Crie uma rota para um futuro Network Virtual Appliance (NVA) para a rede virtual CoreServices.

    | Configuração | Valor |
    | --- | --- |
    | Nome da rota | `PerimetertoCore` |
    | Tipo de destino | **Endereços IP (IP Addresses)** |
    | Endereços IP de destino | `10.0.0.0/16` (rede virtual core services) |
    | Tipo de próximo salto | **Appliance virtual (Virtual appliance)** (observe suas outras escolhas) |
    | Endereço do próximo salto | `10.0.1.7` (futuro NVA; future NVA) |

1. Selecione **Adicionar (Add)**. A última coisa a fazer é associar a rota à subnet.

1. Selecione **Sub-redes (Subnets)** e então **+ Associar (+ Associate)**. Complete a configuração.

    | Configuração | Valor |
    | --- | --- |
    | Virtual network | **CoreServicesVnet (az104-rg5)** |
    | Subnet | **Perimeter** |

>**Nota**: Você criou uma rota definida pelo usuário (user defined route) para direcionar o tráfego do DMZ para o novo NVA.

## Limpeza dos seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que os custos sejam minimizados. A maneira mais fácil de excluir os recursos do laboratório é excluir o grupo de recursos (resource group) do laboratório.

+ No Azure portal, selecione o grupo de recursos, selecione **Excluir o grupo de recursos (Delete the resource group)**, **Digite o nome do grupo de recursos (Enter resource group name)**, e então clique em **Excluir (Delete)**. Quando o diálogo **Confirmação de exclusão (Delete confirmation)** aparecer informando que excluir o grupo de recursos é permanente e não pode ser desfeito, clique em **Excluir (Delete)** novamente para concluir a exclusão.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Expanda seu aprendizado com o Copilot
Copilot pode ajudá-lo a aprender como usar as ferramentas de scripting do Azure. Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue para *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.

+ Como posso usar comandos do Azure PowerShell ou Azure CLI para adicionar um emparelhamento de redes virtuais (virtual network peering) entre vnet1 e vnet2?
+ Crie uma tabela destacando várias ferramentas de monitoramento do Azure e de terceiros suportadas no Azure. Indique quando usar cada ferramenta.
+ Quando eu deveria criar uma rota de rede personalizada no Azure?

## Aprenda mais com treinamentos em ritmo próprio

+ [Introdução às Virtual Networks](https://learn.microsoft.com/training/modules/introduction-to-azure-virtual-networks/). Neste módulo, você aprende a projetar e implementar serviços de rede do Azure. Você aprende sobre redes virtuais, endereços IP públicos e privados, DNS, emparelhamento de redes virtuais (virtual network peering), roteamento e Azure Virtual NAT.
+ [Gerencie e controle o fluxo de tráfego na sua implantação do Azure com rotas](https://learn.microsoft.com/training/modules/control-network-traffic-flow-with-routes/). Aprenda a controlar o tráfego de rede de redes virtuais do Azure implementando rotas personalizadas.


## Principais pontos

Parabéns por completar o laboratório. Aqui estão os principais pontos deste laboratório.

+ Por padrão, recursos em redes virtuais diferentes não podem se comunicar.
+ O emparelhamento de redes virtuais (virtual network peering) permite conectar de forma transparente duas ou mais redes virtuais no Azure.
+ Redes virtuais pareadas aparecem como uma única para fins de conectividade.
+ O tráfego entre máquinas virtuais em redes virtuais pareadas usa a infraestrutura backbone da Microsoft.
+ Rotas definidas pelo sistema são criadas automaticamente para cada subnet em uma rede virtual. Rotas definidas pelo usuário substituem ou adicionam às rotas padrão do sistema.
+ Azure Network Watcher fornece um conjunto de ferramentas para monitorar, diagnosticar e visualizar métricas e logs para recursos IaaS do Azure.
