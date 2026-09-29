---
lab:
  title: 'Lab 02b: Gerenciar Governança via Azure Policy'
  module: Administrar Governança e Conformidade
  description: Configurar Azure Policy para usar tags de recurso.
  duration: 30 minutes
  level: 300
  islab: true
  primarytopics:
    - Azure
    - Azure Policy
    - Resource locks
layout: default
---

# Lab 02b - Manage Governance via Azure Policy

## Lab introduction

Neste laboratório, você aprenderá como implementar os planos de governança da sua organização. Você verá como as políticas do Azure podem garantir que decisões operacionais sejam aplicadas em toda a organização. Também aprenderá a usar tags de recurso para melhorar os relatórios.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Estimated timing: 30 minutes

## Lab scenario

A presença em nuvem da sua organização cresceu consideravelmente no último ano. Durante uma auditoria recente, você descobriu um número substancial de recursos que não têm um proprietário, projeto ou centro de custo definido. Para melhorar o gerenciamento dos recursos do Azure na sua organização, você decide implementar a seguinte funcionalidade:

- aplicar tags de recurso para anexar metadados importantes aos recursos do Azure
- impor o uso de tags de recurso para novos recursos usando Azure Policy
- atualizar recursos existentes com tags de recurso
- usar resource locks para proteger recursos configurados

## Architecture diagram

![Diagram of the task architecture.](../media/az104-lab02b-architecture.png)

## Job skills

+ Task 1: Create and assign tags via the Azure portal.
+ Task 2: Enforce tagging via an Azure Policy.
+ Task 3: Apply tagging via an Azure Policy.
+ Task 4: Configure and test resource locks.

## Task 1: Assign tags via the Azure portal

Nesta tarefa, você criará e atribuirá uma tag a um Resource group via o Azure portal. Tags são um componente crítico de uma estratégia de governança conforme descrito pelo Microsoft Well-Architected Framework e Cloud Adoption Framework. Tags permitem identificar rapidamente proprietários de recursos, datas de descomissionamento, contatos de grupo e outros pares nome/valor que sua organização considere importantes. Para esta tarefa, você atribuirá uma tag que identifica o Cost Center do recurso.

1. Faça login no **Azure portal** - `https://portal.azure.com`.

1. Pesquise e selecione `Resource groups`.

1. Em Resource groups, selecione **+ Create**.

    | Setting | Value |
    | --- | --- |
    | Subscription name | your subscription |
    | Resource group name | `az104-rg2` |
    | Region | **East US** |

    >**Nota:** Para cada laboratório deste curso, você criará um novo resource group. Isso permite que você localize e gerencie rapidamente seus recursos de laboratório.

1. Selecione **Next** e vá até a aba **Tags**. Forneça informações para uma nova tag.

    | Setting | Value |
    | --- | --- |
    | Name | Cost Center |
    | Value | 000 |

1. Selecione **Review + Create**, e então selecione **Create**.

## Task 2: Enforce tagging via an Azure Policy

Nesta tarefa, você atribuirá a política integrada *Require a tag and its value on resources* ao resource group e avaliará o resultado. Azure Policy pode ser usado para impor configurações e, neste caso, governança, aos seus recursos do Azure.

1. No Azure portal, pesquise e selecione `Policy`.

1. No blade **Authoring**, selecione **Definitions**. Reserve um momento para navegar pela lista de [built-in policy definitions](https://learn.microsoft.com/azure/governance/policy/samples/built-in-policies) disponíveis para uso. Observe que você também pode pesquisar uma definição.

    ![Screenshot of the policy definition.](../media/az104-lab02b-policytags.png)

1. Pesquise pela política integrada `Require a tag and its value on resources`. Selecione a policy e reserve um minuto para revisar a definição.

1. Selecione **Assign policy**.

1. Especifique o **Scope** clicando no botão de reticências e selecionando os seguintes valores. Clique em **Select** quando terminar.

    | Setting | Value |
    | --- | --- |
    | Subscription | *your subscription* |
    | Resource Group | **az104-rg2** |

    >**Nota**: Você pode atribuir políticas no nível de management group, subscription ou resource group. Você também tem a opção de especificar exclusões, como assinaturas, resource groups ou recursos individuais. Neste cenário, queremos a tag em todos os recursos do resource group.

1. Configure as propriedades **Basics** da atribuição especificando as seguintes configurações (deixe as demais nos padrões):

    | Setting | Value |
    | --- | --- |
    | Assignment name | `Require Cost Center tag and its value on resources` |
    | Description | `Require Cost Center tag and its value on all resources in the resource group`|
    | Policy enforcement | Enabled |

    >**Nota**: O **Assignment name** é preenchido automaticamente com o nome da policy que você selecionou, mas você pode alterá-lo. A **Description** é opcional. Observe que você pode desabilitar a policy a qualquer momento.

1. Clique em **Next** e defina **Parameters** com os seguintes valores:

    | Setting | Value |
    | --- | --- |
    | Tag Name | `Cost Center` |
    | Tag Value | `000` |

1. Clique em **Next** e revise as abas **Remediation** e **Managed Identity**. Deixe a caixa **Create a Managed Identity** desmarcada na aba **Managed Identity**.

1. Clique em **Review + Create** e então clique em **Create**.

    >**Nota**: Agora você verificará se a nova policy assignment está em vigor tentando criar uma Storage account no resource group. Você criará a storage account sem adicionar a tag exigida.

    >**Nota**: Pode levar entre 5 e 10 minutos para a policy entrar em vigor.

1. No portal, pesquise e selecione `Storage Accounts`, e selecione **+ Create**.

1. Na aba **Basics** do blade **Create storage account**, complete a configuração.

    | Setting | Value |
    | --- | --- |
    | Resource group | **az104-rg2** |
    | Storage account name | *any globally unique combination of between 3 and 24 lower case letters and digits, starting with a letter* |

1. Selecione **Review** e então clique em **Create**.

1. Você deverá receber uma mensagem **Validation failed**. Visualize a mensagem para identificar a razão da falha. Verifique se a mensagem de erro indica que a implantação do recurso foi impedida pela policy.

    ![Screenshot of the disallowed policy error.](../media/az104-lab02b-policyerror.png)

>**Nota**: Ao clicar na aba **Raw Error**, você pode encontrar mais detalhes sobre o erro, incluindo o nome da role definition **Require a tag and its value on resources**. A implantação falhou porque a storage account que você tentou criar não possuía uma tag chamada **Cost Center** com seu valor configurado como **Default**.

## Task 3: Apply tagging via an Azure policy

Nesta tarefa, usaremos uma nova definição de policy para remediar quaisquer recursos não conformes. Neste cenário, faremos com que quaisquer recursos filhos de um resource group herdem a tag **Cost Center** que foi definida no resource group.

1. No Azure portal, pesquise e selecione `Policy`.

1. Na seção **Authoring**, clique em **Assignments**.

1. Na lista de assignments, clique no ícone de reticências na linha que representa a atribuição da policy **Require a tag and its value on resources** e use o item de menu **Delete assignment** para excluir a atribuição.

1. Quando for solicitado **Do you want to delete the policy assignment?**, selecione **Yes**.

1. Clique em **Assign policy** e especifique o **Scope** clicando no botão de reticências e selecionando os seguintes valores:

    | Setting | Value |
    | --- | --- |
    | Subscription | your Azure subscription |
    | Resource Group | `az104-rg2` |

1. Para especificar a **Policy definition**, clique no botão de reticências e então pesquise por e selecione `Inherit a tag from the resource group if missing`.

1. Selecione **Add** e então configure as demais propriedades **Basics** da atribuição.

    | Setting | Value |
    | --- | --- |
    | Assignment name | `Inherit the Cost Center tag and its value 000 from the resource group if missing` |
    | Description | `Inherit the Cost Center tag and its value 000 from the resource group if missing` |
    | Policy enforcement | Enabled |

1. Clique em **Next** e defina **Parameters** com os seguintes valores:

    | Setting | Value |
    | --- | --- |
    | Tag Name | `Cost Center` |

1. Clique em **Next** e, na aba **Remediation**, configure as seguintes opções (deixe as demais com seus padrões):

    | Setting | Value |
    | --- | --- |
    | Create a remediation task | enabled |
    | Policy to remediate | **Inherit a tag from the resource group if missing** |

    >**Nota**: Esta policy definition inclui o efeito **Modify**. Portanto, é necessária uma managed identity.

    ![Screenshot of the policy remediation page. ](../media/az104-lab02b-policyremediation.png)

1. Clique em **Review + Create** e então clique em **Create**.

    >**Nota**: Para verificar se a nova policy assignment está em vigor, você criará outra storage account no mesmo resource group sem adicionar explicitamente a tag exigida.

    >**Nota**: Pode levar entre 5 e 10 minutos para a policy entrar em vigor.

1. Pesquise e selecione `Storage Account` e clique em **+ Create**.

1. Na aba **Basics** do blade **Create storage account**, verifique que você está usando o Resource Group ao qual a Policy foi aplicada e especifique as seguintes configurações (deixe as demais com seus padrões) e clique em **Review**:

    | Setting | Value |
    | --- | --- |
    | Storage account name | *any globally unique combination of between 3 and 24 lower case letters and digits, starting with a letter* |
    | Primary service | Azure Blob Storage or Azure Data Lake Storage |

    >**Nota**: Primary service pode ser listado como Preferred Storage Type

1. Verifique que desta vez a validação passou e clique em **Create**.

1. Uma vez que a nova storage account for provisionada, clique em **Go to resource**.

1. No blade **Tags**, observe que a tag **Cost Center** com o valor **000** foi automaticamente atribuída ao recurso.

    >**Você sabia?** Se você pesquisar e selecionar **Tags** no portal, você pode visualizar os recursos com uma tag específica.

## Task 4: Configure and test resource locks

Nesta tarefa, você configurará e testará um resource lock. Locks impedem exclusões ou modificações de um recurso.

1. Pesquise e selecione seu resource group.

1. No blade **Settings**, selecione **Locks**.

1. Selecione **Add** e preencha as informações do resource lock. Quando terminar, selecione **Ok**.

    | Setting | Value |
    | --- | --- |
    | Lock name | `rg-lock` |
    | Lock type | **delete** (observe a seleção para read-only) |

1. Navegue até o blade **Overview** do resource group e selecione **Delete resource group**.

1. No textbox **Enter resource group name to confirm deletion** forneça o nome do resource group, `az104-rg2`. Observe que você pode copiar e colar o nome do resource group.

1. Observe o aviso: Deleting this resource group and its dependent resources is a permanent action and cannot be undone. Selecione **Delete**. Quando uma segunda caixa de confirmação Delete aparecer, clique em **Delete** novamente para confirmar.

1. Você deverá receber uma notificação negando a exclusão.

    ![Screenshot of the failure to delete message.](../media/az104-lab02b-failuretodelete.png)

    >**Nota:** Você precisará remover o lock se pretende deletar o resource group.

## Cleanup your resources

Se você estiver trabalhando com **sua própria subscription**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e os custos sejam minimizados. A maneira mais fácil de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ No Azure portal, selecione o resource group, selecione **Delete the resource group**, **Enter resource group name**, e então clique em **Delete**.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Extend your learning with Copilot
Copilot pode ajudá-lo a aprender a usar as ferramentas de script do Azure. Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (no canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para testar estes prompts.
+ What are the Azure PowerShell and CLI commands for adding and deleting resource locks on a resource group?
+ Tabulate the differences between Azure policy and Azure RBAC, include examples.
+ What are the steps to enforce Azure policy and remediate resources which are not compliant?
+ How can I get a report of Azure resources with specific tags?

## Learn more with self-paced training
+ [Azure Policy Initiatives](https://learn.microsoft.com/training/modules/sovereignty-policy-initiatives/). Neste módulo, você aprende como Azure Policy initiatives podem ser usadas para impor padrões organizacionais, avaliar conformidade em escala e gerenciar recursos do Azure de forma eficaz.

## Key takeaways

Parabéns por completar o laboratório. Aqui estão os principais pontos deste lab.

+ Azure tags são metadados que consistem em um par chave-valor. Tags descrevem um recurso particular no seu ambiente. Em particular, o uso de tags no Azure permite que você rotule seus recursos de maneira lógica.
+ Azure Policy estabelece convenções para recursos. Policy definitions descrevem condições de conformidade dos recursos e o efeito a ser aplicado se uma condição for atendida. Uma condição compara um campo de propriedade do recurso ou um valor a um valor requerido. Existem muitas built-in policy definitions e você pode customizar as policies.
+ O recurso de remediation task do Azure Policy é usado para trazer recursos para conformidade com base em uma definição e atribuição. Recursos que não estão em conformidade com uma atribuição de definição com efeito modify ou deployIfNotExist podem ser trazidos para conformidade usando uma remediation task.
+ Você pode configurar um resource lock em uma subscription, resource group ou resource. O lock pode proteger um recurso de exclusões e modificações acidentais por usuários. O lock substitui quaisquer permissões de usuário.
+ Azure Policy é uma prática de segurança pré-implantação. RBAC e resource locks são práticas de segurança pós-implantação.
