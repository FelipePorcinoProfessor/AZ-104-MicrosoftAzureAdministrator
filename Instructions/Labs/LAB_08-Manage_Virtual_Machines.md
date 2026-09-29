---
lab:
  title: 'Laboratório 08: Gerenciar Virtual Machines'
  module: Administrar Virtual Machines
  description: Criar e dimensionar Virtual Machines e Virtual Machine Scale Sets.
  duration: 50 minutes
  level: 400
  islab: true
  primarytopics:
  - Azure
  - Virtual machines
  - Virtual Machine Scale Sets
layout: default
---

# Laboratório 08 - Gerenciar Máquinas Virtuais

## Introdução do laboratório

Neste laboratório, você cria e compara máquinas virtuais (Virtual Machines) com conjuntos de dimensionamento de máquinas virtuais (Virtual Machine Scale Sets). Você aprende como criar, configurar e redimensionar uma única máquina virtual. Você aprende como criar um Virtual Machine Scale Set e configurar o autoscaling.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua organização quer explorar a implementação e configuração de Máquinas Virtuais do Azure. Primeiro, você implementa uma Máquina Virtual do Azure com escalonamento manual. Em seguida, você implementa um Virtual Machine Scale Set e explora o autoscaling.

## Competências do trabalho

+ Tarefa 1: Implantar máquinas virtuais do Azure resistentes a zonas usando o Azure portal.
+ Tarefa 2: Gerenciar o dimensionamento de computação e armazenamento para máquinas virtuais.
+ Tarefa 3: Criar e configurar Azure Virtual Machine Scale Sets.
+ Tarefa 4: Dimensionar Azure Virtual Machine Scale Sets.
+ Tarefa 5: Criar uma máquina virtual usando Azure PowerShell (opcional 1).
+ Tarefa 6: Criar uma máquina virtual usando o CLI (opcional 2).

## Diagrama de arquitetura de Azure Virtual Machines

![Diagrama das tarefas de arquitetura da VM.](../media/az104-lab08-vm-architecture.png)

## Tarefa 1: Implantar máquinas virtuais do Azure resistentes a zonas usando o Azure portal

Nesta tarefa, você implantará duas Máquinas Virtuais do Azure em diferentes zonas de disponibilidade (availability zones) usando o Azure portal. Zonas de disponibilidade oferecem o nível mais alto de SLA de tempo de atividade para máquinas virtuais em 99,99%. Para atingir esse SLA, você deve implantar pelo menos duas máquinas virtuais em diferentes zonas de disponibilidade.

1. Faça login no Azure portal - `https://portal.azure.com`.

1. Pesquise por e selecione `Virtual machines`; no painel **Máquinas virtuais (Virtual machines)**, clique em **Criar (+ Create)**, e então selecione no menu suspenso **máquina virtual (virtual machine)**. Observe suas outras escolhas.

1. Na aba **Básicos (Basics)**, no menu suspenso **Zona de disponibilidade (Availability zone)**, marque a caixa ao lado de **Zone 2**. Isso deve selecionar tanto **Zone 1** quanto **Zone 2**.

    >**Observação**: Isso irá implantar duas máquinas virtuais na região selecionada, uma em cada zone. Você atinge o SLA de 99,99% de uptime porque tem pelo menos duas VMs distribuídas por pelo menos duas zones. No cenário em que você possa precisar de apenas uma VM, é uma boa prática ainda implantar a VM em outra zone.

1. Na aba Básicos (Basics), continue completando a configuração:

    | Configuração | Valor |
    | --- | --- |
    | Assinatura | o nome da sua assinatura do Azure |
    | Grupo de recursos | **az104-rg8** (Se necessário, clique em **Criar novo (Create new)**) |
    | Nomes da(s) máquina(s) virtual(is) | `az104-vm1` e `az104-vm2` (Depois de selecionar ambas as availability zones, selecione **Editar nomes (Edit names)** embaixo do campo de nome da VM.) |
    | Região | **East US** |
    | Opções de disponibilidade | **Availability zone** |
    | Zona de disponibilidade | **Zone 1, 2** (leia a observação sobre usar virtual machine scale sets) |
    | Zona auto-selecionada | (mantenha o padrão) |
    | Zona selecionada pelo Azure (Preview) | desativado |
    | Tipo de segurança | **Standard** |
    | Imagem (See all images) | **Windows Server 2025 Datacenter - x64 Gen2** |
    | Instância Spot do Azure | **Desmarcado (Spot instance disabled)** |
    | Tamanho | **Standard_D2s_v5** |
    | Nome de usuário | `localadmin` |
    | Senha | **Forneça uma senha segura** |
    | Portas públicas de entrada | **Nenhuma (None)** |
    | Deseja usar uma licença existente do Windows Server? | **Desmarcado (No)** |

    > [!NOTE]
    > Use **Standard_D2s_v5** primeiro. Se o tamanho estiver indisponível ou o Azure não tiver capacidade, selecione **Standard_D2s_v6**. Se esse tamanho também estiver indisponível, selecione **Standard_D2s_v7**.

    ![Captura de tela da página de criação da VM.](../media/az104-lab08-create-vm.png)

1. Clique **Próximo : Discos > (Next : Disks >)**, especifique as seguintes configurações (deixe as outras com seus valores padrão):

    | Configuração | Valor |
    | --- | --- |
    | Tipo do disco do SO | **Premium SSD** |
    | Excluir com a VM | **Marcado (Checked)** |
    | Habilitar compatibilidade com Ultra Disk | **Não marcado (Unchecked)** |

1. Na seção **Criptografia de disco da VM (VM disk encryption)**, deixe **Encryption at host** em seu padrão (disabled).

1. Clique **Próximo : Rede > (Next : Networking >)**, aceite os padrões mas não forneça um load balancer, mude **Opções de balanceamento de carga (Load balancing options)** de **Azure Load Balancer** para **Nenhum (None)**.

    | Configuração | Valor |
    | --- | --- |
    | Excluir IP público e NIC quando a VM for excluída | **Marcado (Checked)** |
    | Opções de balanceamento de carga | **Nenhum (None)** |

1. Clique **Próximo : Gerenciamento > (Next : Management >)** e revise as configurações. Não faça alterações, deixe as caixas de seleção de **Metadata Security Protocol** e as configurações de **Microsoft Entra ID** em seus valores padrão.

1. Clique **Próximo : Monitoramento > (Next : Monitoring >)** e especifique as seguintes configurações (deixe as outras com seus valores padrão):

    | Configuração | Valor |
    | --- | --- |
    | Diagnósticos de inicialização (Boot diagnostics) | **Desativar (Disable)** |

1. Clique **Próximo : Avançado > (Next : Advanced >)**, mantenha os padrões, então clique **Revisar + Criar (Review + Create)**.

1. Após a validação, clique **Criar (Create)**.

1. Depois que a implantação terminar, dispense quaisquer coachmarks informativos ou sugestões que apareçam na página de implantação, como a dica de ferramenta **Dimensione sua VM (Scale out your VM)** ou alertas de **Gerenciamento de custos (Cost management)**. Continue para o próximo passo.

    >**Observação:** Note que à medida que a máquina virtual é implantada, a NIC, o disco e o endereço IP público (se configurado) são recursos criados e gerenciados de forma independente.

1. Aguarde a conclusão da implantação, então selecione **Ir para o recurso (Go to resource)**.

   >**Observação:** Monitore as mensagens de **Notificações (Notifications)**.

## Tarefa 2: Gerenciar o dimensionamento de computação e armazenamento para máquinas virtuais

Nesta tarefa, você irá dimensionar uma máquina virtual ajustando seu tamanho para um SKU diferente. O Azure fornece flexibilidade na seleção do tamanho da VM para que você possa ajustar uma VM por períodos de tempo se ela precisar de mais (ou menos) CPU e memória alocadas. Esse conceito se estende aos discos, onde você pode modificar o desempenho do disco ou aumentar a capacidade alocada.

1. Na máquina virtual **az104-vm1**, vá para **Visão geral (Overview)** e clique **Parar (Stop)** para desalocar a VM, então confirme.

1. Depois que a VM mostrar **Stopped (deallocated)**, no painel **Disponibilidade + dimensionamento (Availability + scale)**, selecione **Tamanho (Size)**.

1. Defina o tamanho da máquina virtual para **Standard_D4s_v5** e clique **Redimensionar (Resize)**. Quando solicitado, confirme a alteração.

    > [!NOTE]
    > A máquina virtual foi criada com **Standard_D2s_v5**. Redimensionar para **Standard_D4s_v5** aumenta de 2 vCPUs e 8 GiB de memória para 4 vCPUs e 16 GiB de memória. Se esse tamanho estiver indisponível, selecione **Standard_D4s_v6**. Se esse tamanho também estiver indisponível, selecione **Standard_D4s_v7**.

    ![Captura de tela do redimensionamento da máquina virtual.](../media/az104-lab08-resize-vm.png)

1. Na área **Configurações (Settings)**, selecione **Discos (Disks)**.

1. Em **Discos de dados (Data disks)** selecione **+ Criar e anexar um novo disco (Create and attach a new disk)**. Configure as definições (deixe as demais com seus valores padrão).

    | Configuração | Valor |
    | --- | --- |
    | Nome do disco | `vm1-disk1` |
    | Tipo de armazenamento | **Standard HDD** |
    | Tamanho (GiB) | `32` |

1. Clique **Aplicar (Apply)**.

1. Depois que o disco for criado, clique **Desanexar (Detach)** (se necessário, role para a direita para ver o ícone de desanexar), e então clique **Aplicar (Apply)**.

    >**Observação**: Desanexar remove o disco da VM mas o mantém em storage para uso posterior.

1. Usando a **Pesquisa Global (Global Search)**, pesquise por e selecione `Disks`.

1. No painel **Centro de Armazenamento | Discos do Azure (Storage Center | Azure Disks)**, selecione a aba **Recursos (Resources)**, e então selecione o objeto **vm1-disk1**.

    >**Observação:** O painel **Visão geral (Overview)** também fornece informações de desempenho e uso para o disco.

1. No painel **Configurações (Settings)**, selecione **Tamanho + desempenho (Size + performance)**.

1. Defina o tipo de armazenamento para **Standard SSD**, e então clique **Salvar (Save)**.

1. Navegue de volta para a máquina virtual **az104-vm1** e selecione **Discos (Disks)**.

1. Na seção **Data disk**, selecione **Anexar discos existentes (Attach existing disks)**.

1. No menu suspenso **Nome do disco (Disk name)**, selecione **VM1-DISK1**.

1. Verifique se o disco agora é **Standard SSD**.

1. Selecione **Aplicar (Apply)** para salvar suas alterações.

    >**Observação:** Você agora criou uma máquina virtual, redimensionou o SKU e o tamanho do data disk. Na próxima tarefa, usamos Virtual Machine Scale Sets para automatizar o processo de escalonamento.

## Diagrama de arquitetura de Azure Virtual Machine Scale Sets

![Diagrama das tarefas de arquitetura do VMSS.](../media/az104-lab08-vmss-architecture.png)

## Tarefa 3: Criar e configurar Azure Virtual Machine Scale Sets

Nesta tarefa, você implantará um Virtual Machine Scale Set do Azure em availability zones. VM Scale Sets reduzem a sobrecarga administrativa de automação ao permitir que você configure métricas ou condições que permitam que o scale set seja escalado horizontalmente (scale out/in).

1. No Azure portal, pesquise por e selecione `Virtual machine scale sets` e, no painel **Virtual machine scale sets**, clique **Criar (+ Create)**.

1. Na aba **Básicos (Basics)** do painel **Criar um virtual machine scale set (Create a virtual machine scale set)**, especifique as seguintes configurações (deixe as outras com seus valores padrão) e clique **Próximo : Spot > (Next : Spot >)**:

    | Configuração | Valor |
    | --- | --- |
    | Assinatura | o nome da sua assinatura do Azure  |
    | Grupo de recursos | **az104-rg8**  |
    | Nome do virtual machine scale set | `vmss1` |
    | Região | **(US)East US** |
    | Zona de disponibilidade | **Zones 1, 2, 3** |
    | Modo de orquestração | **Uniform** |
    | Tipo de segurança | **Standard** |
    | Opções de dimensionamento | **Revise e aceite os padrões**. Nós alteraremos isso na próxima tarefa. |
    | Imagem (See all images) | **Windows Server 2025 Datacenter - x64 Gen2** |
    | Executar com desconto Azure Spot | **Desmarcado (Spot instance disabled)** |
    | Tamanho | **Standard_D2s_v5** |
    | Nome de usuário | `localadmin` |
    | Senha | **Forneça uma senha segura**  |
    | Já possui uma licença do Windows Server? | **Desmarcado (No)** |

    > [!NOTE]
    > Use **Standard_D2s_v5** primeiro. Se o tamanho estiver indisponível ou o Azure não tiver capacidade, selecione **Standard_D2s_v6**. Se esse tamanho também estiver indisponível, selecione **Standard_D2s_v7**.

    >**Observação**: Para a lista de regiões do Azure que suportam a implantação de máquinas virtuais Windows em availability zones, consulte [Quais são as Zonas de Disponibilidade no Azure?](https://docs.microsoft.com/en-us/azure/availability-zones/az-overview)

    ![Captura de tela da página de criação do VMSS.](../media/az104-lab08-create-vmss.png)

1. Na aba **Spot**, aceite os padrões e selecione **Próximo : Discos > (Next : Disks >)**.

1. Na aba **Discos (Disks)**, aceite os valores padrão e clique **Próximo : Rede > (Next : Networking >)**.

1. Na página **Rede (Networking)**, selecione o link **Editar rede virtual (Edit virtual network)**. Faça algumas alterações. Quando terminar, selecione **OK**.

    | Configuração | Valor |
    | --- | --- |
    | Nome | `vmss-vnet` |
    | Intervalo de endereços | `10.82.0.0/20` (exclua o intervalo de endereços existente) |
    | Nome da sub-rede | `subnet0` |
    | Intervalo da sub-rede | `10.82.0.0/24` |

1. Na aba **Rede (Networking)**, clique no ícone **Editar interface de rede (Edit network interface)** à direita da entrada da interface de rede.

1. Para a seção **NIC network security group**, selecione **Avançado (Advanced)** e então clique em **Criar novo (Create new)** embaixo do menu suspenso **Configurar grupo de segurança de rede (Configure network security group)**.

1. No painel **Criar grupo de segurança de rede (Create network security group)**, especifique as seguintes configurações (deixe as outras com seus valores padrão):

    | Configuração | Valor |
    | --- | --- |
    | Nome | **vmss1-nsg** |

1. Clique **Adicionar uma regra de entrada (Add an inbound rule)** e adicione uma regra de segurança de entrada com as seguintes configurações (deixe as outras com seus valores padrão):

    | Configuração | Valor |
    | --- | --- |
    | Origem | **Any** |
    | Intervalos de portas de origem | * |
    | Destino | **Any** |
    | Serviço | **HTTP** |
    | Ação | **Permitir (Allow)** |
    | Prioridade | **1010** |
    | Nome | `allow-http` |

1. Clique **Adicionar (Add)** e, de volta no painel **Criar grupo de segurança de rede (Create network security group)**, clique **OK**.

1. No painel **Editar interface de rede (Edit network interface)**, na seção **Endereço IP público (Public IP address)**, clique **Habilitado (Enabled)** e clique **OK**.

1. Na aba **Rede (Networking)**, embaixo da seção **Balanceamento de carga (Load balancing)**, confirme que Opções de balanceamento de carga está definido como **Azure Load Balancer** (selecionado por padrão). Especifique o seguinte (deixe as outras com seus valores padrão).

    | Configuração | Valor |
    | --- | --- |
    | Opções de balanceamento de carga | **Azure Load Balancer** |
    | Selecionar um load balancer | **Criar um load balancer (Create a load balancer)** |

1. Na página **Criar um load balancer (Create a load balancer)**, no painel lateral que se abre, defina o nome do Load Balancer para `vmss-lb`. Deixe Type, Protocol, e todas as configurações de Rules (incluindo Load balancer rule e Inbound NAT rule) em seus valores padrão. Clique **Criar (Create)** quando terminar e então **Próximo : Gerenciamento > (Next : Management >)**.

    | Configuração | Valor |
    | --- | --- |
    | Nome do load balancer | `vmss-lb` |

    >**Observação:** Pausa por um minuto e revise o que você fez. Neste ponto, você configurou o virtual machine scale set com discos e rede. Na configuração de rede você criou um network security group e permitiu HTTP. Você também criou um load balancer com um endereço IP público.

1. Na aba **Gerenciamento (Management)**, especifique as seguintes configurações (deixe as outras com seus valores padrão):

    | Configuração | Valor |
    | --- | --- |
    | Diagnósticos de inicialização (Boot diagnostics) | **Desativar (Disable)** |

1. Clique **Próximo : Integridade > (Next : Health >)**.

1. Na aba **Integridade (Health)**, revise as configurações padrão sem fazer alterações e clique **Próximo : Avançado > (Next : Advanced >)**.

1. Na aba **Avançado (Advanced)**, clique **Revisar + criar (Review + create)**.

1. Na aba **Revisar + criar (Review + create)**, garanta que a validação passou e clique **Criar (Create)**.

    >**Observação**: Aguarde a conclusão da implantação do virtual machine scale set. Isso deve levar aproximadamente 5 minutos. Enquanto espera, revise a [documentação](https://learn.microsoft.com/azure/virtual-machine-scale-sets/overview).

## Tarefa 4: Dimensionar Azure Virtual Machine Scale Sets

Nesta tarefa, você dimensiona o virtual machine scale set usando uma regra de escala personalizada.

1. Selecione **Ir para o recurso (Go to resource)** ou pesquise por e selecione o scale set **vmss1**.

1. Expanda a seção **Disponibilidade + dimensionamento (Availability + scale)** e então selecione **Dimensionamento (Scaling)**.

1. Selecione **Autoscale personalizado (Custom autoscale)**. Então altere o **Modo de escala (Scale mode)** para **Escalar com base em métrica (Scale based on metric)**. Uma mensagem de aviso aparecerá indicando que nenhuma regra de escala está definida — clique no link **Adicionar uma regra (Add a rule)** dentro dessa mensagem.

    >**Você sabia?** Você pode usar **Escala manual (Manual scale)** ou **Autoscale personalizado (Custom autoscale)**. Em scale sets com um pequeno número de instâncias de VM, aumentar ou diminuir a contagem de instâncias (Escala manual) pode ser o melhor. Em scale sets com um grande número de instâncias de VM, escalar com base em métricas (Autoscale personalizado) pode ser mais apropriado.

Regra de scale out (aumentar)

1. Vamos criar uma regra que aumenta automaticamente o número de instâncias de VM. Essa regra faz scale out quando a carga média da CPU é maior que 70% durante um período de 10 minutos. Quando a regra dispara, o número de instâncias de VM é aumentado em 50%.

    | Configuração | Valor |
    | --- | --- |
    | Fonte da métrica | **Recurso atual (Current resource - vmss1)** |
    | Namespace da métrica | **Virtual Machine Host** |
    | Nome da métrica | **Percentage CPU** (revise suas outras escolhas) |
    | Operador | **Maior que (Greater than)** |
    | Limite da métrica para acionar a ação de escala | **70** |
    | Duração (minutos) | **10** |
    | Estatística do intervalo de tempo | **Média (Average)** |
    | Operação | **Aumentar porcentagem por (Increase percent by)** (mude o padrão) |
    | Período de cooldown (minutos) | **5** |
    | Percentual | **50** |

    >**Observação**: A operação padrão é "Increase count by" (Aumentar contagem por), você precisa alterá-la para "Increase percent by" (Aumentar porcentagem por)

    ![Captura de tela da página de adicionar regra de dimensionamento.](../media/az104-lab08-scale-rule.png)

1. Certifique-se de **Salvar (Save)** suas alterações.

Regra de scale in (reduzir)

1. Durante noites ou finais de semana, a demanda pode diminuir, então é importante criar uma regra de scale in.

1. Vamos criar uma regra que diminui o número de instâncias de VM em um scale set. O número de instâncias deve diminuir quando a carga média de CPU cair abaixo de 30% durante um período de 10 minutos. Quando a regra dispara, o número de instâncias de VM é diminuído em 20%.

1. Selecione **Adicionar uma regra (Add a rule)**, ajuste as configurações, então selecione **Adicionar (Add)**.

    | Configuração | Valor |
    | --- | --- |
    | Operador | **Menor que (Less than)** |
    | Limiar | **30** |
    | Operação | **Diminuir porcentagem por (decrease percentage by)** |
    | Percentual | **50** |

1. Certifique-se de **Salvar (Save)** suas alterações.

Definir os limites de instância

1. Quando suas regras de autoscale são aplicadas, os limites de instâncias asseguram que você não faça scale out além do número máximo de instâncias ou scale in além do número mínimo de instâncias.

1. **Limites de instância (Instance limits)** são mostrados na página **Dimensionamento (Scaling)** após as regras.

    | Configuração | Valor |
    | --- | --- |
    | Mínimo | **2** |
    | Máximo | **10** |
    | Padrão | **2** |

1. Certifique-se de **Salvar (Save)** suas alterações.

1. Na página **vmss1**, selecione **Instâncias (Instances)**. Aqui é onde você monitoraria o número de instâncias de máquina virtual.

    >**Observação:** Se você estiver interessado em usar Azure PowerShell para a criação de máquinas virtuais, tente a Tarefa 5. Se estiver interessado em usar o CLI para criar máquinas virtuais, tente a Tarefa 6.

## Tarefa 5: Criar uma máquina virtual usando Azure PowerShell (opção 1)

1. Use o ícone (canto superior direito) para iniciar uma sessão do **Cloud Shell**. Alternativamente, navegue diretamente para `https://shell.azure.com`.

1. Certifique-se de selecionar **PowerShell**. Se necessário, configure o armazenamento do shell.

1. Execute o comando a seguir para criar uma máquina virtual. Quando solicitado, forneça um nome de usuário e senha para criar a conta de administrador local na VM. Enquanto aguarda, confira o comando de referência [New-AzVM](https://learn.microsoft.com/powershell/module/az.compute/new-azvm?view=azps-11.1.0) para todos os parâmetros associados à criação de uma máquina virtual.

    ```powershell
    New-AzVm `
    -ResourceGroupName 'az104-rg8' `
    -Name 'myPSVM' `
    -Location 'East US' `
    -Image 'Win2019Datacenter' `
    -Zone '1' `
    -Size 'Standard_D2s_v5' `
    -Credential (Get-Credential)
    ```

    > [!NOTE]
    > Use **Standard_D2s_v5** primeiro. Se o comando falhar porque o tamanho está indisponível ou o Azure não tiver capacidade, execute-o novamente com **Standard_D2s_v6**. Se esse comando falhar pelo mesmo motivo, execute-o novamente com **Standard_D2s_v7**.

1. Depois que o comando completar, use **Get-AzVM** para listar as máquinas virtuais no seu grupo de recursos.

    ```powershell
    Get-AzVM `
    -ResourceGroupName 'az104-rg8' `
    -Status
    ```

1. Verifique se sua nova máquina virtual está listada e o **Status** é **Running (Em execução)**.

1. Use **Stop-AzVM** para desalocar sua máquina virtual. Digite **Yes** para confirmar.

    ```powershell
    Stop-AzVM `
    -ResourceGroupName 'az104-rg8' `
    -Name 'myPSVM'
    ```

1. Use **Get-AzVM** com o parâmetro **-Status** para verificar se a máquina está **deallocated**.

    >**Você sabia?** Quando você usa o Azure para parar sua máquina virtual, o status fica *deallocated*. Isso significa que quaisquer IPs públicos não estáticos são liberados, e você para de pagar pelos custos de compute da VM.

## Tarefa 6: Criar uma máquina virtual usando o CLI (opção 2)

1. Use o ícone (canto superior direito) para iniciar uma sessão do **Cloud Shell**. Alternativamente, navegue diretamente para `https://shell.azure.com`.

1. Certifique-se de selecionar **Bash**. Se necessário, configure o armazenamento do shell.

1. Execute o comando a seguir para criar uma máquina virtual. Enquanto espera, confira o comando de referência [az vm create](https://learn.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) para todos os parâmetros associados à criação de uma máquina virtual.

    ```sh
    az vm create --name myCLIVM --resource-group az104-rg8 --image Canonical:0001-com-ubuntu-server-jammy:22_04-lts:latest --admin-username localadmin --generate-ssh-keys
    ```

1. Depois que o comando completar, use **az vm show** para verificar se sua máquina foi criada.

    ```sh
    az vm show --name  myCLIVM --resource-group az104-rg8 --show-details --output table
    ```

1. Verifique se o **powerState** é **VM Running**.

1. Use **az vm deallocate** para desalocar sua máquina virtual. Digite **Yes** para confirmar.

    ```sh
    az vm deallocate --resource-group az104-rg8 --name myCLIVM
    ```

1. Use **az vm show** para garantir que o **powerState** é **VM deallocated**.

    >**Você sabia?** Quando você usa o Azure para parar sua máquina virtual, o status fica *deallocated*. Isso significa que quaisquer IPs públicos não estáticos são liberados, e você para de pagar pelos custos de compute da VM.

## Limpeza dos seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que o custo seja minimizado. A maneira mais fácil de excluir os recursos do laboratório é excluir o grupo de recursos (resource group) do laboratório.

+ No Azure portal, selecione o grupo de recursos (resource group), selecione **Excluir o grupo de recursos (Delete the resource group)**, digite o **Nome do grupo de recursos (Enter resource group name)**, e então clique **Excluir (Delete)**.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Expanda seu aprendizado com o Copilot
O Copilot pode ajudar você a aprender como usar as ferramentas de script do Azure. O Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue para *copilot.microsoft.com*. Reserve alguns minutos para testar estes prompts.

+ Forneça os passos e os comandos do Azure CLI para criar uma máquina virtual Linux.
+ Revise as maneiras de escalar máquinas virtuais e melhorar o desempenho.
+ Descreva as políticas de gerenciamento do ciclo de vida de armazenamento do Azure e como elas podem otimizar custos.

(Observação: os prompts acima são exemplos; personalize conforme necessário.)

## Aprenda mais com treinamentos autodidáticos

+ [Introdução às máquinas virtuais do Azure](https://learn.microsoft.com/training/modules/intro-to-azure-virtual-machines/). Aprenda sobre as decisões que você toma antes de criar uma máquina virtual, as opções para criar e gerenciar a VM, e as extensões e serviços que você usa para gerenciar sua VM.
+ [Criar uma máquina virtual Windows no Azure](https://learn.microsoft.com/training/modules/create-windows-virtual-machine-in-azure/). Crie uma máquina virtual Windows usando o Azure portal. Conecte-se a uma máquina virtual Windows em execução usando Área de Trabalho Remota (Remote Desktop).
+ [Projeto guiado: Implantar e administrar máquinas virtuais Linux no Azure](https://learn.microsoft.com/training/modules/guided-project-deploy-administer-linux-virtual-machines-azure/). Aprenda tarefas de administrador de máquinas virtuais Linux.

## Principais aprendizados

Parabéns por completar o laboratório. Aqui estão os principais aprendizados deste laboratório.

+ Azure virtual machines são recursos de computação sob demanda e escaláveis.
+ Azure virtual machines fornecem opções de escalonamento tanto vertical quanto horizontal.
+ Configurar Azure virtual machines inclui escolher um sistema operacional, tamanho, armazenamento e configurações de rede.
+ Azure Virtual Machine Scale Sets permitem criar e gerenciar um grupo de VMs balanceadas por carga.
+ As máquinas virtuais em um Virtual Machine Scale Set são criadas a partir da mesma imagem e configuração.
+ Em um Virtual Machine Scale Set, o número de instâncias de VM pode aumentar ou diminuir automaticamente em resposta à demanda ou a uma programação definida.
