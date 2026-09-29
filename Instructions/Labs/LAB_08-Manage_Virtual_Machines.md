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

# Lab 08 - Manage Virtual Machines

## Lab introduction

Neste laboratório, você cria e compara virtual machines com virtual machine scale sets. Você aprende como criar, configurar e redimensionar uma única virtual machine. Você aprende como criar um Virtual Machine Scale Set e configurar o autoscaling.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Estimated timing: 50 minutes

## Lab scenario

Sua organização quer explorar a implementação e configuração de Virtual Machines do Azure. Primeiro, você implementa uma Virtual Machine do Azure com escalonamento manual. Em seguida, você implementa um Virtual Machine Scale Set e explora o autoscaling.

## Job skills

+ Task 1: Deploy zone-resilient Azure virtual machines by using the Azure portal.
+ Task 2: Manage compute and storage scaling for virtual machines.
+ Task 3: Create and configure Azure Virtual Machine Scale Sets.
+ Task 4: Scale Azure Virtual Machine Scale Sets.
+ Task 5: Create a virtual machine using Azure PowerShell (optional 1).
+ Task 6: Create a virtual machine using the CLI (optional 2).

## Azure Virtual Machines Architecture Diagram

![Diagram of the vm architecture tasks.](../media/az104-lab08-vm-architecture.png)

## Task 1: Deploy zone-resilient Azure virtual machines by using the Azure portal

Nesta tarefa, você implantará duas Virtual Machines do Azure em diferentes availability zones usando o Azure portal. Availability zones oferecem o nível mais alto de SLA de tempo de atividade para virtual machines em 99,99%. Para atingir esse SLA, você deve implantar pelo menos duas virtual machines em diferentes availability zones.

1. Faça login no Azure portal - `https://portal.azure.com`.

1. Pesquise por e selecione `Virtual machines`, no painel **Virtual machines**, clique em **+ Create**, e então selecione no menu suspenso **virtual machine**. Observe suas outras escolhas.

1. Na aba **Basics**, no menu suspenso **Availability zone**, marque a caixa ao lado de **Zone 2**. Isso deve selecionar tanto **Zone 1** quanto **Zone 2**.

    >**Observação**: Isso irá implantar duas virtual machines na região selecionada, uma em cada zone. Você atinge o SLA de 99,99% de uptime porque tem pelo menos duas VMs distribuídas por pelo menos duas zones. No cenário em que você possa precisar de apenas uma VM, é uma boa prática ainda implantar a VM em outra zone.

1. Na aba Basics, continue completando a configuração:

    | Setting | Value |
    | --- | --- |
    | Subscription | o nome da sua assinatura do Azure |
    | Resource group |  **az104-rg8** (Se necessário, clique em **Create new**) |
    | Virtual machine names | `az104-vm1` and `az104-vm2` (After selecting both availability zones, select **Edit names** under the VM name field.) |
    | Region | **East US** |
    | Availability options | **Availability zone** |
    | Availability zone | **Zone 1, 2** (read the note about using virtual machine scale sets) |
    | Self-selected zone | (take the default) |
    | Azure-selected zone (Preview) | disabled |
    | Security type | **Standard** |
    | Image (See all images) | **Windows Server 2025 Datacenter - x64 Gen2** |
    | Azure Spot instance | **unchecked** |
    | Size | **Standard_D2s_v5** |
    | Username | `localadmin` |
    | Password | **Provide a secure password** |
    | Public inbound ports | **None** |
    | Would you like to use an existing Windows Server license? | **Unchecked** |

    > [!NOTE]
    > Use **Standard_D2s_v5** first. If the size is unavailable or Azure lacks capacity, select **Standard_D2s_v6**. If that size is also unavailable, select **Standard_D2s_v7**.

    ![Screenshot of the create vm page.](../media/az104-lab08-create-vm.png)

1. Clique **Next : Disks >** , especifique as seguintes configurações (deixe as outras com seus valores padrão):

    | Setting | Value |
    | --- | --- |
    | OS disk type | **Premium SSD** |
    | Delete with VM | **checked** (default) |
    | Enable Ultra Disk compatibility | **Unchecked** |

1. Na seção **VM disk encryption**, deixe **Encryption at host** em seu padrão (disabled).

1. Clique **Next : Networking >** aceite os padrões mas não forneça um load balancer，mude **Load balancing options** de **Azure load balancer** para **None**.

    | Setting | Value |
    | --- | --- |
    | Delete public IP and NIC when VM is deleted | **Checked** |
    | Load balancing options | **None** |

1. Clique **Next : Management >** e revise as configurações. Não faça alterações, deixe as checkboxes de **Metadata Security Protocol** e as configurações de **Microsoft Entra ID** em seus valores padrão.

1. Clique **Next : Monitoring >** e especifique as seguintes configurações (deixe as outras com seus valores padrão):

    | Setting | Value |
    | --- | --- |
    | Boot diagnostics | **Disable** |

1. Clique **Next : Advanced >**, mantenha os padrões, então clique **Review + Create**.

1. Após a validação, clique **Create**.

1. Depois que a implantação terminar, dispense quaisquer coachmarks informativos ou sugestões que apareçam na página de implantação, como a dica de ferramenta **Scale out your VM** ou alertas de **Cost management**. Continue para o próximo passo.

    >**Observação:** Note que à medida que a virtual machine é implantada, a NIC, o disco e o endereço IP público (se configurado) são recursos criados e gerenciados de forma independente.

1. Aguarde a conclusão da implantação, então selecione **Go to resource**.

   >**Observação:** Monitore as mensagens de **Notification**.

## Task 2: Manage compute and storage scaling for virtual machines

Nesta tarefa, você irá dimensionar uma virtual machine ajustando seu tamanho para um SKU diferente. O Azure fornece flexibilidade na seleção do tamanho da VM para que você possa ajustar uma VM por períodos de tempo se ela precisar de mais (ou menos) CPU e memória alocadas. Esse conceito se estende aos discos, onde você pode modificar o desempenho do disco ou aumentar a capacidade alocada.

1. Na virtual machine **az104-vm1**, vá para **Overview** e clique **Stop** para desalocar a VM, então confirme.

1. Depois que a VM mostrar **Stopped (deallocated)**, no painel **Availability + scale**, selecione **Size**.

1. Defina o tamanho da virtual machine para **Standard_D4s_v5** e clique **Resize**. Quando solicitado, confirme a alteração.

    > [!NOTE]
    > A virtual machine foi criada com **Standard_D2s_v5**. Redimensionar para **Standard_D4s_v5** aumenta de 2 vCPUs e 8 GiB de memória para 4 vCPUs e 16 GiB de memória. Se esse tamanho estiver indisponível, selecione **Standard_D4s_v6**. Se esse tamanho também estiver indisponível, selecione **Standard_D4s_v7**.

    ![Screenshot of the resize the virtual machine.](../media/az104-lab08-resize-vm.png)

1. Na área **Settings**, selecione **Disks**.

1. Em **Data disks** selecione **+ Create and attach a new disk**. Configure as definições (deixe as demais com seus valores padrão).

    | Setting | Value |
    | --- | --- |
    | Disk name | `vm1-disk1` |
    | Storage type | **Standard HDD** |
    | Size (GiB) | `32` |

1. Clique **Apply**.

1. Depois que o disco for criado, clique **Detach** (se necessário, role para a direita para ver o ícone de detach), e então clique **Apply**.

    >**Observação**: Desanexar remove o disco da VM mas o mantém em storage para uso posterior.

1. Usando o **Global Search**, pesquise por e selecione `Disks`.

1. No painel **Storage Center | Azure Disks**, selecione a aba **Resources**, e então selecione o objeto **vm1-disk1**.

    >**Observação:** O painel **Overview** também fornece informações de desempenho e uso para o disco.

1. No painel **Settings**, selecione **Size + performance**.

1. Defina o tipo de armazenamento para **Standard SSD**, e então clique **Save**.

1. Navegue de volta para a virtual machine **az104-vm1** e selecione **Disks**.

1. Na seção **Data disk**, selecione **Attach existing disks**.

1. No menu suspenso **Disk name**, selecione **VM1-DISK1**.

1. Verifique se o disco agora é **Standard SSD**.

1. Selecione **Apply** para salvar suas alterações.

    >**Observação:** Você agora criou uma virtual machine, redimensionou o SKU e o tamanho do data disk. Na próxima tarefa, usamos Virtual Machine Scale Sets para automatizar o processo de escalonamento.

## Azure Virtual Machine Scale Sets Architecture Diagram

![Diagram of the vmss architecture tasks.](../media/az104-lab08-vmss-architecture.png)

## Task 3: Create and configure Azure Virtual Machine Scale Sets

Nesta tarefa, você implantará um Virtual Machine Scale Set do Azure em availability zones. VM Scale Sets reduzem a sobrecarga administrativa de automação ao permitir que você configure métricas ou condições que permitam que o scale set seja escalado horizontalmente, scale in ou scale out.

1. No Azure portal, pesquise por e selecione `Virtual machine scale sets` e, no painel **Virtual machine scale sets**, clique **+ Create**.

1. Na aba **Basics** do painel **Create a virtual machine scale set**, especifique as seguintes configurações (deixe as outras com seus valores padrão) e clique **Next : Spot >**:

    | Setting | Value |
    | --- | --- |
    | Subscription | o nome da sua assinatura do Azure  |
    | Resource group | **az104-rg8**  |
    | Virtual machine scale set name | `vmss1` |
    | Region | **(US)East US** |
    | Availability zone | **Zones 1, 2, 3** |
    | Orchestration mode | **Uniform** |
    | Security type | **Standard** |
    | Scaling options | **Review and take the defaults**. We will change this in the next task. |
    | Image (See all images) | **Windows Server 2025 Datacenter - x64 Gen2** |
    | Run with Azure Spot discount | **Unchecked** |
    | Size | **Standard_D2s_v5** |
    | Username | `localadmin` |
    | Password | **Provide a secure password**  |
    | Already have a Windows Server license? | **Unchecked** |

    > [!NOTE]
    > Use **Standard_D2s_v5** first. If the size is unavailable or Azure lacks capacity, select **Standard_D2s_v6**. If that size is also unavailable, select **Standard_D2s_v7**.

    >**Observação**: Para a lista de regiões do Azure que suportam a implantação de máquinas virtuais Windows em availability zones, consulte [What are Availability Zones in Azure?](https://docs.microsoft.com/en-us/azure/availability-zones/az-overview)

    ![Screenshot of the create vmss page. ](../media/az104-lab08-create-vmss.png)

1. Na aba **Spot**, aceite os padrões e selecione **Next : Disks >**.

1. Na aba **Disks**, aceite os valores padrão e clique **Next : Networking >**.

1. Na página **Networking**, selecione o link **Edit virtual network**. Faça algumas alterações. Quando terminar, selecione **OK**.

    | Setting | Value |
    | --- | --- |
    | Name | `vmss-vnet` |
    | Address range | `10.82.0.0/20` (delete the existing address range) |
    | Subnet name | `subnet0` |
    | Subnet range | `10.82.0.0/24` |

1. Na aba **Networking**, clique no ícone **Edit network interface** à direita da entrada da interface de rede.

1. Para a seção **NIC network security group**, selecione **Advanced** e então clique em **Create new** embaixo do menu suspenso **Configure network security group**.

1. No painel **Create network security group**, especifique as seguintes configurações (deixe as outras com seus valores padrão):

    | Setting | Value |
    | --- | --- |
    | Name | **vmss1-nsg** |

1. Clique **Add an inbound rule** e adicione uma regra de segurança de entrada com as seguintes configurações (deixe as outras com seus valores padrão):

    | Setting | Value |
    | --- | --- |
    | Source | **Any** |
    | Source port ranges | * |
    | Destination | **Any** |
    | Service | **HTTP** |
    | Action | **Allow** |
    | Priority | **1010** |
    | Name | `allow-http` |

1. Clique **Add** e, de volta no painel **Create network security group**, clique **OK**.

1. No painel **Edit network interface**, na seção **Public IP address**, clique **Enabled** e clique **OK**.

1. Na aba **Networking**, embaixo da seção **Load balancing**, confirme que Load balancing options está definido como Azure load balancer (selecionado por padrão). Especifique o seguinte (deixe as outras com seus valores padrão).

    | Setting | Value |
    | --- | --- |
    | Load balancing options | **Azure load balancer** |
    | Select a load balancer | **Create a load balancer** |

1. Na página **Create a load balancer**, no painel lateral que se abre, defina o Load balancer name para `vmss-lb`. Deixe Type, Protocol, e todas as configurações de Rules (incluindo Load balancer rule e Inbound NAT rule) em seus valores padrão. Clique **Create** quando terminar e então **Next : Management >**.

    | Setting | Value |
    | --- | --- |
    | Load balancer name | `vmss-lb` |

    >**Observação:** Pause por um minuto e revise o que você fez. Neste ponto, você configurou o virtual machine scale set com discos e networking. Na configuração de rede você criou um network security group e permitiu HTTP. Você também criou um load balancer com um endereço IP público.

1. Na aba **Management**, especifique as seguintes configurações (deixe as outras com seus valores padrão):

    | Setting | Value |
    | --- | --- |
    | Boot diagnostics | **Disable** |

1. Clique **Next : Health >**.

1. Na aba **Health**, revise as configurações padrão sem fazer alterações e clique **Next : Advanced >**.

1. Na aba **Advanced**, clique **Review + create**.

1. Na aba **Review + create**, garanta que a validação passou e clique **Create**.

    >**Observação**: Aguarde a conclusão da implantação do virtual machine scale set. Isso deve levar aproximadamente 5 minutos. Enquanto espera, revise a [documentation](https://learn.microsoft.com/azure/virtual-machine-scale-sets/overview).

## Task 4: Scale Azure Virtual Machine Scale Sets

Nesta tarefa, você dimensiona o virtual machine scale set usando uma regra de escala customizada.

1. Selecione **Go to resource** ou pesquise por e selecione o scale set **vmss1**.

1. Expanda a seção **Availability + scale** e então selecione **Scaling**.

1. Selecione **Custom autoscale**. Então altere o **Scale mode** para **Scale based on metric**. Uma mensagem de aviso aparecerá indicando que nenhuma regra de escala está definida — clique no link **Add a rule** dentro dessa mensagem.

    >**Você sabia?** Você pode usar **Manual scale** ou **Custom autoscale**. Em scale sets com um pequeno número de instâncias de VM, aumentar ou diminuir a contagem de instâncias (Manual scale) pode ser o melhor. Em scale sets com um grande número de instâncias de VM, escalar com base em métricas (Custom autoscale) pode ser mais apropriado.

**Scale out rule**

1. Vamos criar uma regra que aumenta automaticamente o número de instâncias de VM. Essa regra faz scale out quando a carga média da CPU é maior que 70% durante um período de 10 minutos. Quando a regra dispara, o número de instâncias de VM é aumentado em 50%.

    | Setting | Value |
    | --- | --- |
    | Metric source | **Current resource (vmss1)** |
    | Metric namespace | **Virtual Machine Host** |
    | Metric name | **Percentage CPU** (review your other choices) |
    | Operator | **Greater than** |
    | Metric threshold to trigger scale action | **70** |
    | Duration (minutes) | **10** |
    | Time grain statistic | **Average** |
    | Operation | **Increase percent by** (change the default) |
    | Cool down (minutes) | **5** |
    | Percentage | **50** |

    >**Observação**: O padrão de Operation é "Increase count by", você precisa alterá-lo para "Increase percent by"

    ![Screenshot of the scaling add rule page.](../media/az104-lab08-scale-rule.png)

1. Certifique-se de **Save** suas alterações.

**Scale in rule**

1. Durante noites ou finais de semana, a demanda pode diminuir, então é importante criar uma regra de scale in.

1. Vamos criar uma regra que diminui o número de instâncias de VM em um scale set. O número de instâncias deve diminuir quando a carga média de CPU cair abaixo de 30% durante um período de 10 minutos. Quando a regra dispara, o número de instâncias de VM é diminuído em 20%.

1. Selecione **Add a rule**, ajuste as configurações, então selecione **Add**.

    | Setting | Value |
    | --- | --- |
    | Operator | **Less than** |
    | Threshold | **30** |
    | Operation | **decrease percentage by** (review your other choices) |
    | Percentage | **50** |

1. Certifique-se de **Save** suas alterações.

**Set the instance limits**

1. Quando suas regras de autoscale são aplicadas, os limites de instâncias asseguram que você não faça scale out além do número máximo de instâncias ou scale in além do número mínimo de instâncias.

1. **Instance limits** são mostrados na página **Scaling** após as regras.

    | Setting | Value |
    | --- | --- |
    | Minimum | **2** |
    | Maximum | **10** |
    | Default | **2** |

1. Certifique-se de **Save** suas alterações

1. Na página **vmss1**, selecione **Instances**. Aqui é onde você monitoraria o número de instâncias de virtual machine.

    >**Observação:** Se você estiver interessado em usar Azure PowerShell para a criação de virtual machines, tente a Task 5. Se estiver interessado em usar o CLI para criar virtual machines, tente a Task 6.

## Task 5: Create a virtual machine using Azure PowerShell (option 1)

1. Use o ícone (canto superior direito) para iniciar uma sessão do **Cloud Shell**. Alternativamente, navegue diretamente para `https://shell.azure.com`.

1. Certifique-se de selecionar **PowerShell**. Se necessário, configure o armazenamento do shell.

1. Execute o comando a seguir para criar uma virtual machine. Quando solicitado, forneça um nome de usuário e senha para criar a conta de administrador local na VM. Enquanto aguarda, confira o comando de referência [New-AzVM](https://learn.microsoft.com/powershell/module/az.compute/new-azvm?view=azps-11.1.0) para todos os parâmetros associados à criação de uma virtual machine.

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
    > Use **Standard_D2s_v5** first. If the command fails because the size is unavailable or Azure lacks capacity, rerun it with **Standard_D2s_v6**. If that command fails for the same reason, rerun it with **Standard_D2s_v7**.

1. Depois que o comando completar, use **Get-AzVM** para listar as virtual machines no seu resource group.

    ```powershell
    Get-AzVM `
    -ResourceGroupName 'az104-rg8' `
    -Status
    ```

1. Verifique se sua nova virtual machine está listada e o **Status** é **Running**.

1. Use **Stop-AzVM** para desalocar sua virtual machine. Digite **Yes** para confirmar.

    ```powershell
    Stop-AzVM `
    -ResourceGroupName 'az104-rg8' `
    -Name 'myPSVM' 
    ```

1. Use **Get-AzVM** com o parâmetro **-Status** para verificar se a máquina está **deallocated**.

    >**Você sabia?** Quando você usa o Azure para parar sua virtual machine, o status fica *deallocated*. Isso significa que quaisquer IPs públicos não estáticos são liberados, e você para de pagar pelos custos de compute da VM.

## Task 6: Create a virtual machine using the CLI (option 2)

1. Use o ícone (canto superior direito) para iniciar uma sessão do **Cloud Shell**. Alternativamente, navegue diretamente para `https://shell.azure.com`.

1. Certifique-se de selecionar **Bash**. Se necessário, configure o armazenamento do shell.

1. Execute o comando a seguir para criar uma virtual machine. Enquanto espera, confira o comando de referência [az vm create](https://learn.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create) para todos os parâmetros associados à criação de uma virtual machine.

    ```sh
    az vm create --name myCLIVM --resource-group az104-rg8 --image Canonical:0001-com-ubuntu-server-jammy:22_04-lts:latest --admin-username localadmin --generate-ssh-keys
    ```

1. Depois que o comando completar, use **az vm show** para verificar se sua máquina foi criada.

    ```sh
    az vm show --name  myCLIVM --resource-group az104-rg8 --show-details --output table
    ```

1. Verifique se o **powerState** é **VM Running**.

1. Use **az vm deallocate** para desalocar sua virtual machine. Digite **Yes** para confirmar.

    ```sh
    az vm deallocate --resource-group az104-rg8 --name myCLIVM
    ```

1. Use **az vm show** para garantir que o **powerState** é **VM deallocated**.

    >**Você sabia?** Quando você usa o Azure para parar sua virtual machine, o status fica *deallocated*. Isso significa que quaisquer IPs públicos não estáticos são liberados, e você para de pagar pelos custos de compute da VM.

## Cleanup your resources

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que o custo seja minimizado. A maneira mais fácil de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ No Azure portal, selecione o resource group, selecione **Delete the resource group**, **Enter resource group name**, e então clique **Delete**.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Extend your learning with Copilot
O Copilot pode ajudar você a aprender como usar as ferramentas de script do Azure. O Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue para *copilot.microsoft.com*. Reserve alguns minutos para testar estes prompts.

+ Provide the steps and the Azure CLI commands to create a Linux virtual machine.
+ Review the ways you can scale virtual machines and improve performance.
+ Describe Azure storage lifecycle management policies and how they can optimize costs.

## Learn more with self-paced training

+ [Introduction to Azure virtual machines](https://learn.microsoft.com/training/modules/intro-to-azure-virtual-machines/). Learn about the decisions you make before creating a virtual machine, the options to create and manage the VM, and the extensions and services you use to manage your VM.
+ [Create a Windows virtual machine in Azure](https://learn.microsoft.com/training/modules/create-windows-virtual-machine-in-azure/). Create a Windows virtual machine using the Azure portal. Connect to a running Windows virtual machine using Remote Desktop
+ [Guided Project: Deploy and administer Linux virtual machines on Azure](https://learn.microsoft.com/training/modules/guided-project-deploy-administer-linux-virtual-machines-azure/). Learn Linux virtual machine administrator tasks.

## Key takeaways

Parabéns por completar o laboratório. Aqui estão os principais aprendizados deste laboratório.

+ Azure virtual machines são recursos de computação sob demanda e escaláveis.
+ Azure virtual machines fornecem opções de escalonamento tanto vertical quanto horizontal.
+ Configurar Azure virtual machines inclui escolher um sistema operacional, tamanho, armazenamento e configurações de rede.
+ Azure Virtual Machine Scale Sets permitem criar e gerenciar um grupo de VMs balanceadas por carga.
+ As virtual machines em um Virtual Machine Scale Set são criadas a partir da mesma imagem e configuração.
+ Em um Virtual Machine Scale Set, o número de instâncias de VM pode aumentar ou diminuir automaticamente em resposta à demanda ou a uma programação definida.
