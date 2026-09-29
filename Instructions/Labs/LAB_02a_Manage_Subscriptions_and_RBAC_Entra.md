---
lab:
  title: 'Laboratório 02a: Gerenciar Assinaturas e RBAC'
  module: Administrar Governança e Conformidade
  description: Revisar e atribuir funções do Azure.
  duration: 20 minutes
  level: 300
  islab: true
  primarytopics:
    - Azure
    - Azure Roles
layout: default
---

# Lab 02a - Manage Subscriptions and RBAC

## Lab introduction

Neste laboratório, você aprenderá sobre role-based access control. Você aprende como usar permissões e escopos para controlar quais ações identidades podem e não podem executar. Você também aprenderá como facilitar o gerenciamento de assinaturas usando management groups.

Este laboratório requer uma assinatura do Azure. O tipo de assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas as etapas estão escritas usando **East US**.

## Estimated timing: 20 minutes

## Lab scenario

Para simplificar o gerenciamento de recursos do Azure em sua organização, você foi encarregado de implementar a seguinte funcionalidade:

- Criar um management group que inclua todas as suas assinaturas do Azure.

- Conceder permissões para enviar solicitações de suporte para todas as assinaturas no management group. As permissões devem ser limitadas apenas a:

    - Criar e gerenciar máquinas virtuais
    - Criar tickets de solicitação de suporte (não incluir registro de providers do Azure)

## Architecture diagram

![Diagrama das tarefas do laboratório.](../media/az104-lab02a-architecture.png)

## Job skills

+ Task 1: Implement management groups.
+ Task 2: Review and assign a built-in Azure role.
+ Task 3: Create a custom RBAC role.
+ Task 4: Monitor role assignments with the Activity Log.

## Task 1: Implement Management Groups

Nesta tarefa, você criará e configurará management groups. Management groups são usados para organizar e segmentar assinaturas logicamente. Eles permitem que RBAC e Azure Policy sejam atribuídos e herdados por outros management groups e assinaturas. Por exemplo, se sua organização tiver uma equipe de suporte dedicada para a Europa, você pode organizar as assinaturas europeias em um management group para fornecer à equipe de suporte acesso a essas assinaturas (sem fornecer acesso individual a todas as assinaturas). No nosso cenário, todos do Help Desk precisarão criar uma solicitação de suporte em todas as assinaturas.

1. Sign in to the **Azure portal** - `https://portal.azure.com`.

1. Search for and select `Microsoft Entra ID`.

1. In the **Manage** blade, select **Properties**.

1. Review the **Access management for Azure resources** area. Notice/read that you can manage access to all Azure subscriptions and management groups in the tenant.

1. Search for and select **Management groups**.

1. On the **Management groups** blade, click **+ Create**.

1. Create a management group with the following settings. Select **Submit** when you are done.

    | Setting | Value |
    | --- | --- |
    | Management group ID | `az104-mg1` (must be unique in the directory) |
    | Management group display name | `az104-mg1` |

1. **Refresh** the management group page to ensure your new management group displays. This may take a minute.

   >**Nota:** Você percebeu o management group root? O root management group é integrado à hierarquia para agrupar todos os management groups e assinaturas. Esse management group root permite que políticas globais e atribuições de funções do Azure sejam aplicadas no nível do diretório. Após criar um management group, você adicionaria quaisquer assinaturas que devam ser incluídas no grupo.

1. If you receive permission errors in Task 2 after creating the management group, sign out of the Azure portal and sign back in to the **Azure portal** - `https://portal.azure.com`.


    >**Nota:** Isso atualiza suas credenciais e pode resolver erros "AuthorizationFailed" nas lâminas de IAM do management group.


## Task 2: Review and assign a built-in Azure role

Nesta tarefa, você revisará as funções integradas e atribuirá a função VM Contributor a um membro do Help Desk. Azure fornece um grande número de [built-in roles](https://learn.microsoft.com/azure/role-based-access-control/built-in-roles).

>**Nota:** Nas etapas a seguir, você atribuirá a função ao grupo **helpdesk**. Se você não tiver um grupo Help Desk, leve um minuto para criá-lo.

1. Select the **az104-mg1** management group.

1. Select the **Access control (IAM)** blade, and then the **Roles** tab.

1. Scroll through the built-in role definitions that are available. **View** a role to get detailed information about the **Permissions**, **JSON**, and **Assignments**. You will often use *owner*, *contributor*, and *reader*.

1. Select **+ Add**, from the drop-down menu, select **Add role assignment**.

1. On the **Add role assignment** blade, search for and select the **Virtual Machine Contributor**. The Virtual machine contributor role lets you manage virtual machines, but not access their operating system or manage the virtual network and storage account they are connected to. This is a good role for the Help Desk. Select **Next**.

    >**Você sabia?** Azure originally provided only the **Classic** deployment model. This has been replaced by the **Azure Resource Manager** deployment model. As a best practice, do not use classic resources.

1. On the **Members** tab, **Select Members**.

1. Search for and select the `helpdesk` group. Click **Select**, then click **Next**.

1. On the **Conditions** tab, click **Next**.

1. On the **Assignment type** tab, leave the assignment type as **Eligible** and the time-bound settings at their defaults, then click **Next**.

1. Click **Review + assign** to create the role assignment.

1. Continue on the **Access control (IAM)** blade. On the **Role assignments** tab, confirm the **helpdesk** group has the **Virtual Machine Contributor** role.

    >**Nota:** Como boa prática, sempre atribua funções a grupos e não a indivíduos.

    >**Você sabia?** Esta atribuição pode não realmente conceder a você privilégios adicionais. Se você já tem a função Owner, essa função inclui todas as permissões associadas à função VM Contributor.

## Task 3: Create a custom RBAC role

Nesta tarefa, você criará uma função RBAC personalizada. Funções personalizadas são uma parte central da implementação do princípio de menor privilégio para um ambiente. Built-in roles podem ter permissões demais para seu cenário. Também criaremos uma nova função e removeremos permissões que não são necessárias. Você tem um plano para gerenciar permissões sobrepostas?

1. Continue working on your management group. Navigate to the **Access control (IAM)** blade.

1. Select **+ Add**, from the drop-down menu, select **Add custom role**.

1. On the Basics tab complete the configuration.

    | Setting | Value |
    | --- | --- |
    | Custom role name | `Custom Support Request` |
    | Description | `A custom contributor role for support requests.` |

1. For **Baseline permissions**, select **Clone a role**. In the **Role to clone** drop-down menu, select **Support Request Contributor**.

    ![Captura de tela clonando uma função.](../media/az104-lab02a-clone-role.png)

1. Select **Next** to move to the **Permissions** tab, and then select **+ Exclude permissions**.

1. In the resource provider search field, enter `.Support` and select **Microsoft.Support**.

1. In the list of permissions, place a checkbox next to **Other: Registers Support Resource Provider** and then select **Add**. The role should be updated to include this permission as a *NotAction*.

    >**Nota:** Um resource provider do Azure é um conjunto de operações REST que habilitam funcionalidade para um serviço específico do Azure. Não queremos que o Help Desk tenha essa capacidade, então ela está sendo removida da função clonada.

1. On the **Assignable scopes** tab, ensure your management group is listed, then click **Next**.

1. Review the JSON for the *Actions*, *NotActions*, and *AssignableScopes* that are customized in the role.

1. Select **Review + Create**, and then select **Create**.

    >**Nota:** Neste ponto, você criou uma função personalizada e a atribuiu ao management group.

## Task 4: Monitor role assignments with the Activity Log

Nesta tarefa, você visualizará o activity log para determinar se alguém criou uma nova função.

1. In the portal locate the **az104-mg1** resource and select **Activity log**. The activity log provides insight into subscription-level events.

1. Review the activities for role assignments. The activity log can be filtered for specific operations.

    ![Captura de tela da página Activity log com filtro configurado.](../media/az104-lab02a-searchactivitylog.png)

## Cleanup your resources

Se você estiver trabalhando com **sua própria assinatura**, leve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que os custos sejam minimizados. A maneira mais fácil de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ In the Azure portal, select the management group, select **Delete** and click on **Yes** to confirm the deletion.
+ Using Azure PowerShell, `Remove-AzManagementGroup -GroupName az104-mg1`.
+ Using the CLI, `az account management-group delete --name az104-mg1`.

## Extend your learning with Copilot

Copilot pode ajudá-lo a aprender como usar as ferramentas de script do Azure. Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precise de mais informações. Abra um navegador Edge e escolha Copilot (no canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.
+ Create two tables highlighting important PowerShell and CLI commands to get information about organization subscriptions on Azure and explain each command in the column “Explanation”.
+ What is the format of the Azure RBAC JSON file?
+ What are the basic steps for creating a custom Azure RBAC role?
+ What is the difference between Azure RBAC roles and Microsoft Entra ID roles?

## Learn more with self-paced training

+ [Secure your Azure resources with Azure role-based access control (Azure RBAC)](https://learn.microsoft.com/training/modules/secure-azure-resources-with-rbac/). Use Azure RBAC to manage access to resources in Azure.

## Key takeaways

Parabéns por concluir o laboratório. Aqui estão os principais pontos deste laboratório.

+ Management groups são usados para organizar assinaturas.
+ O management group root integrado inclui todos os management groups e assinaturas.
+ Azure possui muitas funções integradas. Você pode atribuir essas funções para controlar o acesso aos recursos.
+ Você pode criar novas funções ou customizar funções existentes.
+ Funções são definidas em um arquivo formatado em JSON e incluem *Actions*, *NotActions*, e *AssignableScopes*.
+ Você pode usar o Activity Log para monitorar atribuições de funções.
