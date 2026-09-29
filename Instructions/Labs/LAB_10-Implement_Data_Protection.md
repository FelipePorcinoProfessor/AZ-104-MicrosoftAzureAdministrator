---
lab:
  title: 'Laboratório 10: Implementar Proteção de Dados'
  module: Administrar Proteção de Dados
  description: Configure o Azure Backup para virtual machines.
  duration: 50 minutes
  level: 400
  islab: true
  primarytopics:
  - Azure
  - Virtual machines
  - Azure Backup
layout: default
---

# Laboratório 10 - Implementar Proteção de Dados

## Introdução ao laboratório

Neste laboratório, você aprende sobre backup e recuperação de Azure virtual machines. Você aprenderá a criar um Recovery Services vault e uma política de backup para Azure virtual machines. Você também aprenderá sobre recuperação de desastres com Azure Site Recovery.

Este laboratório requer uma assinatura do Azure. O tipo de assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar as regiões, mas os procedimentos foram escritos usando **East US** e **West US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua organização está avaliando como fazer backup e restaurar Azure virtual machines de perda de dados acidental ou maliciosa. Além disso, a organização quer explorar o uso do Azure Site Recovery para cenários de recuperação de desastres.

## Habilidades do trabalho

+ Tarefa 1: Use um template para provisionar uma infraestrutura.
+ Tarefa 2: Crie e configure um Recovery Services vault.
+ Tarefa 3: Configure backup a nível de Azure virtual machine.
+ Tarefa 4: Monitore o Azure Backup.
+ Tarefa 5: Habilite replicação de virtual machine.

## Diagrama de arquitetura

![Diagram of the architecture tasks.](../media/az104-lab10-architecture.png)

## Tarefa 1: Use um template para provisionar uma infraestrutura

Nesta tarefa, você usará um template para implantar uma virtual machine. A máquina virtual será usada para testar diferentes cenários de backup.

1. Faça o download dos arquivos do laboratório **\\Allfiles\\Lab10\\**.

1. Faça logon no **Azure portal** - `https://portal.azure.com`.

1. Pesquise por e selecione `Deploy a custom template`.

1. Na página de implantação personalizada, selecione **Build your own template in the editor**.

1. Na página de edição do template, selecione **Load file**.

1. Localize e selecione o arquivo **\\Allfiles\\Lab10\\az104-10-vms-edge-template.json** e selecione **Open**.

   > [!NOTE]
   > Reserve um momento para revisar o template. Estamos implantando uma virtual network e uma virtual machine para que possamos demonstrar backup e recuperação.

1. **Save** suas alterações.

1. Selecione **Edit parameters** e depois **Load file**.

1. Carregue e selecione o arquivo **\\Allfiles\\Lab10\\az104-10-vms-edge-parameters.json**.

1. **Save** suas alterações.

1. Use as seguintes informações para preencher os campos da implantação personalizada, deixando todos os outros campos com seus valores padrão:

    | Setting       | Value         |
    | ---           | ---           |
    | Subscription  | Your Azure subscription |
    | Resource group| `az104-rg-region1` (Se necessário, selecione **Create new**) |
    | Region        | **East US**   |
    | VM size       | Selecione um tamanho disponível. Use **Standard_D2s_v5** se disponível. |
    | Username      | **localadmin**   |
    | Password      | Forneça uma senha complexa |

1. Selecione **Review + Create**, então selecione **Create**.

    > [!NOTE]
    > O template fornece três tamanhos de VM atuais. Comece com **Standard_D2s_v5**. Se a implantação falhar porque o tamanho não está disponível ou o Azure não tem capacidade, selecione **Standard_D2s_v6** e reimplante no mesmo resource group. Se necessário, tente novamente com **Standard_D2s_v7**. Se uma nova tentativa falhar porque um recurso existente ou parcialmente implantado causa um conflito, exclua **az104-rg-region1**. Reinicie a Tarefa 1 a partir de **Search for and select Deploy a custom template**, recarregue os arquivos de template e de parâmetros, selecione **Create new** para recriar **az104-rg-region1**, e implante novamente com o tamanho de VM selecionado.

    > [!NOTE]
    > Aguarde a implantação do template e, em seguida, selecione **Go to resource**. Você deverá ter uma virtual machine em uma virtual network.

## Tarefa 2: Crie e configure um Recovery Services vault

Nesta tarefa, você criará um Recovery Services vault. Um Recovery Services vault fornece armazenamento para os dados das virtual machines.

1. No Azure portal, pesquise por e selecione `Recovery Services vaults` e, na lâmina **Recovery Services vaults**, clique em **+ Create**.

1. Na lâmina **Create Recovery Services vault**, especifique as seguintes configurações:

    | Settings | Value |
    | --- | --- |
    | Subscription | o nome da sua assinatura do Azure |
    | Resource group | `az104-rg-region1`  |
    | Vault Name | `az104-rsv-region1` |
    | Region | **East US** |

    >**Nota**: Certifique-se de que você especificou a mesma região para a qual implantou as virtual machines na tarefa anterior.

    ![Screenshot of the recovery services vault.](../media/az104-lab10-create-rsv.png)

1. Clique em **Review + Create**, verifique se a validação foi bem-sucedida e então clique em **Create**.

    >**Nota**: Aguarde a conclusão da implantação. A implantação deve levar alguns minutos.

1. Quando a implantação for concluída, clique em **Go to Resource**.

1. Na seção **Settings**, clique em **Properties**.

1. Selecione o link **Update** sob o rótulo **Backup Configuration**.

1. Na lâmina **Backup Configuration**, revise as opções para **Storage replication type**. Mantenha a configuração padrão **Geo-redundant** e feche a lâmina.

    >**Nota**: Esta configuração só pode ser alterada se não houver itens de backup existentes.

    >**Você sabia?** A opção [Cross Region Restore](https://learn.microsoft.com/azure/backup/backup-create-recovery-services-vault#set-cross-region-restore) permite restaurar dados em uma região secundária pareada do Azure.

1. Selecione o link **Update** sob o rótulo **Security Settings > Soft Delete Settings**.

1. Na lâmina **Soft delete Settings**, verifique que o **Soft delete retention period** é **14** dias e feche a lâmina.

>**Você sabia?** O Azure possui dois tipos de vaults: Recovery Services vaults e Backup vaults. A principal diferença está nas fontes de dados que podem ser protegidas. Aprenda mais sobre [as diferenças](https://learn.microsoft.com/answers/questions/405915/what-is-difference-between-recovery-services-vault).

## Tarefa 3: Configure backup a nível de Azure virtual machine

Nesta tarefa, você implementará backup a nível de virtual machine do Azure. Como parte do backup da VM, você precisará definir a política de backup e de retenção que será aplicada ao backup. Diferentes VMs podem ter políticas de backup e retenção diferentes atribuídas a elas.

   >**Nota**: Antes de iniciar esta tarefa, certifique-se de que a implantação iniciada na primeira tarefa deste laboratório foi concluída com sucesso.

1. Na lâmina do Recovery Services vault, clique em **Overview**, em seguida clique em **+ Backup**.

1. Na lâmina **Backup Goal**, especifique as seguintes configurações:

    | Settings | Value |
    | --- | --- |
    | Where is your workload running? | **Azure** (observe suas outras opções) |
    | What do you want to backup? | **Virtual machine** (observe suas outras opções)|

1. Selecione **Backup**.

1. Observe que existem dois **Policy sub types**: **Enhanced** e **Standard**. Revise as opções e selecione **Standard**.

1. Em **Backup policy**, selecione **Create a new policy**.

1. Defina uma nova política de backup com as seguintes configurações (deixe as demais com os valores padrão):

    | Setting | Value |
    | ---- | ---- |
    | Policy name | `az104-backup` |
    | Frequency | **Daily** |
    | Time | **12:00 AM** |
    | Timezone | o nome do seu fuso horário local |
    | Retain instant recovery snapshot(s) for | **2** Days(s) |

    ![Screenshot of the backup policy page.](../media/az104-lab10-backup-policy.png)

1. Clique **OK** para criar a política e então, na seção **Virtual Machines**, selecione **Add** (role a página para baixo).

1. Na lâmina **Select virtual machines**, selecione **az-104-10-vm0**, clique em **OK**, e então, de volta na lâmina **Backup**, clique em **Enable backup**.

    >**Nota**: Aguarde enquanto o backup é habilitado. Isso deve levar aproximadamente 2 minutos.

1. Após a implantação, selecione **Go to resource**.

1. Na seção **Protected items**, clique em **Backup items**, e então clique na entrada **Azure virtual machine**.

1. Selecione o link **View details** para **az104-10-vm0**, e revise os valores das entradas **Backup Pre-Check** e **Last Backup Status**.

    >**Nota:** Observe que o backup está pendente.

1. Selecione **Backup now**, aceite o valor padrão na lista suspensa **Retain Backup Till**, e clique em **OK**.

    >**Nota**: Não espere o backup ser concluído; prossiga para a próxima tarefa.

## Tarefa 4: Monitore o Azure Backup

Nesta tarefa, você implantará uma storage account do Azure. Em seguida, você configurará o vault para enviar os logs e métricas para a storage account. Esse repositório pode então ser usado com Log Analytics ou outras soluções de monitoramento de terceiros.

1. No Azure portal, pesquise por e selecione `Storage accounts`.

1. Na página Storage accounts, selecione **Create**.

1. Use as seguintes informações para definir a storage account, então selecione **Review + create**.

    | Settings | Value |
    | --- | --- |
    | Subscription          | *Your subscription*    |
    | Resource group        | **az104-rg-region1**        |
    | Storage account name  | Forneça um nome globalmente único   |
    | Region                | **East US**   |

1. Selecione **Create**.

    >**Nota**: Aguarde a conclusão da implantação. Deve levar cerca de um minuto.

1. Pesquise e selecione seu Recovery Services vault.

1. Na lâmina **Monitoring**, selecione **Diagnostic Settings** e então selecione **Add diagnostic setting**.

1. Nomeie a configuração `Logs and Metrics to storage`.

1. Marque as seguintes categorias de logs e métricas:

    - **Azure Backup Reporting Data**
    - **Addon Azure Backup Job Data**
    - **Addon Azure Backup Alert Data**
    - **Azure Site Recovery Jobs**
    - **Azure Site Recovery Events**

1. Nos detalhes de Destino, marque a opção **Archive to a storage account**.

1. No campo de lista suspensa Storage account, selecione a storage account que você implantou nesta tarefa.

1. Selecione **Save**.

1. Retorne ao seu Recovery Services vault, na lâmina **Monitoring** selecione **Backup jobs**.

1. Localize a operação de backup para a virtual machine **az104-10-vm0**.

1. **View details** (role para a direita para encontrar o link) do job de backup.

## Tarefa 5: Habilite replicação de virtual machine

1. No Azure portal, pesquise por e selecione `Recovery Services vaults` e, na lâmina **Recovery Services vaults**, clique em **+ Create**.

1. Na lâmina **Create Recovery Services vault**, especifique as seguintes configurações:

    | Settings | Value |
    | --- | --- |
    | Subscription | o nome da sua assinatura do Azure |
    | Resource group | `az104-rg-region2` (Se necessário, selecione **Create new**) |
    | Vault Name | `az104-rsv-region2` |
    | Region | **West US** |

    >**Nota**: Certifique-se de especificar uma região **diferente** da virtual machine.

1. Clique em **Review + Create**, verifique se a validação passou e então clique em **Create**.

    >**Nota**: Aguarde a conclusão da implantação. A implantação deve levar alguns minutos.

1. Pesquise por e selecione a virtual machine `az104-10-vm0`.

1. Na lâmina **Backup + Disaster recovery**, selecione **Disaster recovery**.

1. Na aba **Basics**, observe o **Target region**.

1. Selecione **Next: Advanced settings**. As seleções de recursos foram feitas para você.

1. Role para baixo e **Create** a automation account.

   >**Nota:** É importante que as configurações estejam populadas; caso contrário, a validação falhará.

1. Selecione **Review + Start replication** e então **Start replication**.

    >**Nota**: Habilitar a replicação levará entre 10 e 15 minutos. Observe as mensagens de notificação no canto superior direito do portal. Enquanto aguarda, considere revisar os links de treinamento em ritmo próprio no final desta página.

1. Uma vez que a replicação esteja completa, pesquise e localize seu Recovery Services Vault, **az104-rsv-region2**. Pode ser necessário **Refresh** da página.

1. Na seção **Protected items**, selecione **Replicated items**.

1. Verifique se a virtual machine está aparecendo como saudável em **replication health**. Observe que o status mostrará a sincronização (iniciando em 0%) e, finalmente, mostrará **Protected** após a sincronização inicial ser concluída.

   ![Screenshot of the replicated items page.](../media/az104-lab10-replicated-items.png)

1. Selecione a virtual machine para ver mais detalhes.

>**Você sabia?** É uma boa prática [testar o failover de uma VM protegida](https://learn.microsoft.com/azure/site-recovery/tutorial-dr-drill-azure#run-a-test-failover-for-a-single-vm).

## Limpe seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e os custos sejam minimizados. A maneira mais fácil de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ No Azure portal, selecione o resource group, selecione **Delete the resource group**, **Enter resource group name**, e então clique em **Delete**. Quando o diálogo **Delete confirmation** aparecer informando que a ação é permanente e não pode ser desfeita, clique em **Delete** novamente.
+ Usando o Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

   >**Nota:** Para excluir um Recovery Services vault do Azure, você primeiro deve remover todas as dependências, como itens protegidos, backup servers e storage accounts, desabilitar recursos de segurança como soft delete, e então excluir o vault. Um exemplo de [script PowerShell](https://learn.microsoft.com/azure/backup/scripts/delete-recovery-services-vault) está disponível.

## Amplie seu aprendizado com o Copilot
O Copilot pode ajudar você a aprender como usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas pelo laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.

+ What products does Azure Backup support?
+ Summarize the steps for backing up and restoring an Azure virtual machine with Azure Backup.
+ How can I use Azure PowerShell or the CLI to check the status of an Azure Backup job.
+ Provide at least five best practices for configuring Azure virtual machine backups.

## Aprenda mais com treinamentos em ritmo próprio

+ [Introduction to Azure Backup](https://learn.microsoft.com/training/modules/intro-to-azure-backup/). Descreva como os recursos do Azure Backup funcionam para fornecer soluções de backup para suas necessidades.
+ [Protect your virtual machines by using Azure Backup](https://learn.microsoft.com/training/modules/protect-virtual-machines-with-azure-backup/). Use o Azure Backup para ajudar a proteger servidores on-premises, virtual machines, SQL Server, Azure file shares e outras cargas de trabalho.


## Principais conclusões

Parabéns por concluir o laboratório. Aqui estão as principais conclusões deste laboratório.

+ O serviço Azure Backup fornece soluções simples, seguras e econômicas para fazer backup e recuperar seus dados.
+ O Azure Backup pode proteger recursos on-premises e na nuvem, incluindo virtual machines e file shares.
+ Políticas do Azure Backup configuram a frequência dos backups e o período de retenção para os pontos de recuperação.
+ O Azure Site Recovery é uma solução de recuperação de desastres que fornece proteção para suas virtual machines e aplicações.
+ O Azure Site Recovery replica suas cargas de trabalho para um site secundário e, no caso de uma interrupção ou desastre, você pode fazer failover para o site secundário e retomar as operações com tempo de inatividade mínimo.
+ Um Recovery Services vault armazena seus dados de backup e minimiza a sobrecarga de gerenciamento.
