---
lab:
   title: 'Laboratório 11: Implementar monitoramento'
   module: Administrar Monitoramento
   description: Configure alertas e consultas do Azure Monitor.
   duration: 45 minutes
   level: 300
   islab: true
   primarytopics:
   - Azure
   - Azure Monitor
layout: default
---

# Laboratório 11: Implementar monitoramento

## Introdução do laboratório

Neste laboratório, você implanta uma máquina virtual com monitoramento baseado em logs, verifica se os dados de monitoramento estão sendo coletados, cria um alerta do Activity Log e um action group, suprime notificações durante um período de manutenção e aciona o alerta excluindo a máquina virtual.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos. Os passos usam **East US**, mas você pode selecionar outra região, se necessário.

## Tempo estimado

45 minutos

## Cenário do laboratório

Sua organização migrou a infraestrutura para o Azure. Os administradores devem ser notificados sobre alterações significativas na infraestrutura. Você planeja usar Azure Monitor, Log Analytics, alertas, action groups e alert processing rules para monitorar uma máquina virtual.

## Tarefas do laboratório

- Tarefa 1: Implantar a infraestrutura do laboratório.
- Tarefa 2: Verificar os dados de monitoramento com Azure Monitor Logs.
- Tarefa 3: Criar um action group.
- Tarefa 4: Criar um alerta do Activity Log.
- Tarefa 5: Configurar uma alert processing rule.
- Tarefa 6: Acionar e verificar o alerta.

## Tarefa 1: Implantar a infraestrutura do laboratório

Nesta tarefa, você implanta uma máquina virtual e os recursos necessários para coletar dados de desempenho do guest em um Log Analytics workspace.

1. Baixe o arquivo do laboratório **\Allfiles\Labs\11\az104-11-vm-template.json** para o seu computador.

1. Faça login no [Azure portal](https://portal.azure.com).

1. Pesquise por e selecione **Deploy a custom template**.

1. Na página de implantação personalizada, selecione **Build your own template in the editor**.

1. Selecione **Load file**.

1. Localize e selecione **az104-11-vm-template.json**, e então selecione **Open**.

1. Selecione **Save**.

1. Insira os valores a seguir, deixando todas as outras configurações nos valores padrão.

   | Configuração | Valor |
   | --- | --- |
   | Subscription | Sua assinatura do Azure |
   | Resource group | **az104-rg11**; crie-o se necessário |
   | Region | **East US** |
   | VM size | Selecione um tamanho disponível. Use **Standard_D2s_v5** se disponível. |
   | Username | `localadmin` |
   | Password | Uma senha complexa |

1. Selecione **Review + create**, e então selecione **Create**.

   > [!NOTE]
   > O template fornece três tamanhos atuais de VM. Comece com **Standard_D2s_v5**. Se a implantação falhar porque o tamanho não está disponível ou o Azure não tem capacidade, selecione **Standard_D2s_v6** e reimplante no mesmo grupo de recursos. Se necessário, tente novamente com **Standard_D2s_v7**. Se uma nova tentativa falhar porque um recurso existente ou parcialmente implantado causa um conflito, exclua **az104-rg11**. Reinicie a Tarefa 1 a partir de **Search for and select Deploy a custom template**, recarregue o template, selecione **Create new** para recriar **az104-rg11**, e implante novamente com o tamanho de VM selecionado.

1. Aguarde a conclusão da implantação e então selecione **Go to resource group**.

### Verificar a implantação

1. Na página do resource group **az104-rg11**, confirme que os seguintes recursos existem:

   - Virtual machine **az104-vm0**
   - Log Analytics workspace com um nome que comece com **az104-law11-**
   - Data collection rule **az104-dcr11**
   - A virtual network, network interface, public IP address, network security group e storage account

1. Abra **az104-vm0**.

1. Se o status da máquina virtual estiver **Stopped**, selecione **Start** e aguarde até que o status mude para **Running**.

1. Em **Settings**, selecione **Extensions + applications**.

1. Confirme que **AzureMonitorWindowsAgent** tem status **Provisioning succeeded**.

1. Retorne para **az104-rg11**, e então abra **az104-dcr11**.

1. Em **Configuration**, selecione **Resources**, e confirme que **az104-vm0** está associada à data collection rule.

> [!NOTE]
> Os dados podem demorar vários minutos para aparecer depois que o Azure Monitor Agent e a data collection rule são implantados. Não exclua a máquina virtual até concluir a Tarefa 2.

## Tarefa 2: Verificar dados de monitoramento com Azure Monitor Logs

Nesta tarefa, você verifica se o Azure Monitor Agent está enviando heartbeat e dados de desempenho da VM para o Log Analytics workspace. Execute esta tarefa antes de excluir a máquina virtual.

1. Em **az104-rg11**, abra o Log Analytics workspace cujo nome começa com **az104-law11-**.

1. Em **General**, selecione **Logs**.

1. Feche a janela de boas-vindas ou o **Queries hub**, se aparecerem.

1. Se necessário, selecione **KQL mode** no menu de modo do editor de consultas.

   ![Screenshot of the queries tab.](../media/az104-lab11-queries.png)

1. Substitua qualquer texto no editor de consultas pela seguinte consulta, e então selecione **Run**.

   ```kusto
   Heartbeat
   | where TimeGenerated > ago(30m)
   | where Computer =~ "az104-vm0"
   | summarize HeartbeatCount = count(), LastHeartbeat = max(TimeGenerated)
       by Computer, Category
   ```

1. Confirme que os resultados contêm **az104-vm0**.

   > [!NOTE]
   > Se a consulta não retornar registros, espere cinco minutos e execute-a novamente. Se ainda não retornar registros, verifique se a extensão Azure Monitor Agent foi provisionada com sucesso e se a data collection rule está associada à máquina virtual.

1. Substitua a consulta pela seguinte consulta, e então selecione **Run**.

   ```kusto
   InsightsMetrics
   | where TimeGenerated > ago(30m)
   | where Computer =~ "az104-vm0"
   | where Name == "UtilizationPercentage"
   | summarize AverageUtilization = avg(Val)
       by bin(TimeGenerated, 5m), Computer
   | render timechart
   ```

1. Confirme que a consulta retorna dados de desempenho da VM.

> [!IMPORTANT]
> Continue somente após ambas as consultas retornarem dados. Registros previamente ingeridos permanecem no workspace depois que a máquina virtual é excluída, mas a máquina virtual não pode enviar dados que não foram coletados antes da exclusão.

## Tarefa 3: Criar um action group

Nesta tarefa, você cria um action group que envia uma notificação por e-mail quando o alerta é acionado.

1. No portal do Azure, pesquise por e selecione **Monitor**.

1. Selecione **Alerts**, e então selecione **Action groups**.

1. Selecione **Create**.

1. Na guia **Basics**, insira os seguintes valores.

   | Configuração | Valor |
   | --- | --- |
   | Subscription | Sua assinatura do Azure |
   | Resource group | **az104-rg11** |
   | Region | **Global** |
   | Action group name | `Alert the operations team` |
   | Display name | `AlertOpsTeam` |

1. Selecione **Next: Notifications**.

1. Insira as seguintes configurações de notificação.

   | Configuração | Valor |
   | --- | --- |
   | Notification type | **Email/SMS message/Push/Voice** |
   | Name | `VM was deleted` |

1. Selecione **Email**, insira seu endereço de e-mail e então selecione **OK**.

1. Selecione **Review + create**, e então selecione **Create**.

1. Confirme que você recebeu um e-mail informando que foi adicionado ao action group. A entrega pode levar vários minutos.

## Tarefa 4: Criar um alerta do Activity Log

Nesta tarefa, você cria um alerta para a operação do Activity Log que exclui uma máquina virtual.

> [!NOTE]
> A exclusão de máquina virtual é uma operação administrativa do Activity Log. Não é uma métrica de VM. O nome da operação é `Microsoft.Compute/virtualMachines/delete`.

1. Em **Azure Monitor**, selecione **Alerts**.

1. Selecione **Create**, e então selecione **Alert rule**.

1. Na guia **Scope**, selecione sua subscription, e então selecione **Apply**.

1. Selecione a guia **Condition**.

1. Em **Select a signal**, selecione **Activity log**.

1. Selecione **Delete Virtual Machine (Virtual Machines)**, e então selecione **Apply**.

1. Em **Alert logic**, deixe **Event level** e **Status** definidos como **All selected**.

   > [!TIP]
   > Se **See all signals** relatar **Couldn't load metric query signals**, tente selecionar **Delete Virtual Machine (Virtual Machines)**, e então selecione **Apply**. Se a condição for aplicada, continue com os passos do portal. A mensagem afeta sinais de consulta de métrica e não impede o sinal do Activity Log de funcionar. Se você não conseguir selecionar ou aplicar **Delete Virtual Machine (Virtual Machines)**, use o **Cloud Shell fallback for a signal-loading error** abaixo.

1. Selecione a guia **Actions**.

1. Em **Select actions**, selecione **Use action groups**.

1. Selecione **Alert the operations team**, e então selecione **Select**.

1. Selecione a guia **Details**, e então insira os seguintes valores.

   | Configuração | Valor |
   | --- | --- |
   | Subscription | Sua assinatura do Azure |
   | Resource group | **az104-rg11** |
   | Alert rule name | `VM was deleted` |
   | Alert rule description | `A VM in the subscription was deleted` |
   | Region | **Global** |
   | Enable alert rule upon creation | Selecionado |

1. Selecione **Review + create**, e então selecione **Create**.

1. Em **Azure Monitor**, selecione **Alerts** > **Alert rules**.

1. Confirme que **VM was deleted** está habilitado antes de continuar.

### Cloud Shell fallback for a signal-loading error

Se o portal não conseguir exibir o sinal **Delete Virtual Machine**, use o Azure Cloud Shell para criar a mesma alert rule sem o seletor de sinais.

1. Abra o **Cloud Shell** e selecione **Bash**.

1. Execute os seguintes comandos.

   ```azurecli
   subscriptionId=$(az account show --query id --output tsv)
   actionGroupId=$(az monitor action-group show \
     --resource-group az104-rg11 \
     --name "Alert the operations team" \
     --query id \
     --output tsv)

   az monitor activity-log alert create \
     --name "VM was deleted" \
     --resource-group az104-rg11 \
     --scope "/subscriptions/$subscriptionId" \
     --condition "category=Administrative and operationName=Microsoft.Compute/virtualMachines/delete" \
     --action-group "$actionGroupId" \
     --description "A VM in the subscription was deleted"
   ```

1. Quando o comando for bem-sucedido, retorne para **Azure Monitor** > **Alerts** > **Alert rules**.

1. Confirme que **VM was deleted** está habilitado, e então continue para a Tarefa 5.

## Tarefa 5: Configurar uma alert processing rule

Nesta tarefa, você configura uma regra que suprime notificações durante um período de manutenção planejada.

1. Em **Azure Monitor**, selecione **Alerts** > **Alert processing rules**.

1. Selecione **Create**.

1. Na guia **Scope**, selecione sua subscription, e então selecione **Apply**.

1. Selecione **Next: Rule settings**.

1. Selecione **Suppress notifications**.

1. Selecione **Next: Scheduling**.

1. Configure a seguinte programação.

   | Configuração | Valor |
   | --- | --- |
   | Apply the rule | **At a specific time** |
   | Start | Data de hoje às 22:00 |
   | End | Data de amanhã às 07:00 |
   | Time zone | Seu fuso horário local |

   ![Screenshot of the scheduling section of an alert processing rule.](../media/az104-lab11-alert-processing-rule-schedule.png)

1. Selecione **Next: Details**.

1. Insira os seguintes valores.

   | Configuração | Valor |
   | --- | --- |
   | Subscription | Sua assinatura do Azure |
   | Resource group | **az104-rg11** |
   | Rule name | `Planned Maintenance` |
   | Description | `Suppress notifications during planned maintenance.` |

1. Selecione **Review + create**, e então selecione **Create**.

> [!NOTE]
> A programação está fora do horário normal usado para concluir este laboratório, portanto não deverá suprimir a notificação de exclusão. Se seu horário atual cair dentro da janela configurada, ajuste a programação antes de acionar o alerta.

## Tarefa 6: Acionar e verificar o alerta

Nesta tarefa, você exclui a máquina virtual e confirma que o alerta do Activity Log foi acionado.

> [!IMPORTANT]
> Confirme que a regra de alerta **VM was deleted** está habilitada antes de excluir a máquina virtual.

1. No portal do Azure, pesquise por e selecione **Virtual machines**.

1. Selecione a caixa de seleção para **az104-vm0**.

1. Selecione **Delete**.

1. No painel **Delete resources**, reveja os recursos selecionados.

1. Insira `delete` no campo de confirmação, e então selecione **Delete**.

1. Se um segundo diálogo de confirmação aparecer, selecione **Delete** novamente.

1. Selecione o ícone **Notifications** e aguarde até que a máquina virtual seja excluída com sucesso.

1. Aguarde um e-mail com um assunto indicando que o alerta do Azure Monitor **VM was deleted** foi ativado.

   ![Screenshot of alert email.](../media/az104-lab11-alert-email.png)

   > [!NOTE]
   > Entradas do Activity Log e notificações de alerta podem levar vários minutos para aparecer.

1. Em **Azure Monitor**, selecione **Alerts**.

1. Confirme que um alerta chamado **VM was deleted** aparece.

1. Abra o alerta e revise seu escopo, condição, operation name, status e histórico.

1. Opcionalmente, retorne ao Log Analytics workspace e execute novamente as consultas da Tarefa 2. Os registros coletados antes da exclusão permanecem disponíveis conforme o período de retenção do workspace.

## Limpar recursos

Se você estiver usando sua própria assinatura, exclua o grupo de recursos do laboratório para evitar cobranças desnecessárias.

1. No portal do Azure, abra **az104-rg11**.

1. Selecione **Delete resource group**.

1. Insira `az104-rg11` para confirmar a exclusão.

1. Selecione **Delete**, e então confirme a exclusão se solicitado.

Você também pode usar o Azure PowerShell:

```azurepowershell
Remove-AzResourceGroup -Name az104-rg11
```

Ou Azure CLI:

```azurecli
az group delete --name az104-rg11
```

## Principais conclusões

- Host and recommended VM metrics não provam que o monitoramento de VM baseado em logs esteja configurado.
- Azure Monitor Logs requer um Log Analytics workspace e um caminho de coleta de dados apropriado.
- Azure Monitor Agent usa uma data collection rule e uma associação para enviar dados de monitoramento do guest para um workspace.
- A ingestão de monitoramento deve ser verificada antes de excluir o recurso que gera os dados.
- A exclusão de máquina virtual é uma operação administrativa do Activity Log, e não uma métrica de VM.
- Action groups definem os destinatários de notificação, enquanto alert processing rules controlam quando as notificações são entregues.
