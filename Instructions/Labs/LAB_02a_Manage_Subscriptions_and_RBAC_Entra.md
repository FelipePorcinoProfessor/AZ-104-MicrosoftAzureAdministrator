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

# Laboratório 02a - Gerenciar Assinaturas e RBAC

## Introdução do laboratório

Neste laboratório, você aprenderá sobre controle de acesso baseado em função (Role-Based Access Control - RBAC). Você aprenderá como usar permissões e escopos para controlar quais ações identidades podem e não podem executar. Você também aprenderá como facilitar o gerenciamento de assinaturas usando grupos de gerenciamento (management groups).

Este laboratório requer uma assinatura do Azure. O tipo de assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas as etapas estão escritas usando **East US**.

## Tempo estimado: 20 minutos

## Cenário do laboratório

Para simplificar o gerenciamento de recursos do Azure em sua organização, você foi encarregado de implementar a seguinte funcionalidade:

- Criar um grupo de gerenciamento (management group) que inclua todas as suas assinaturas do Azure.

- Conceder permissões para enviar solicitações de suporte para todas as assinaturas no grupo de gerenciamento. As permissões devem ser limitadas apenas a:

    - Criar e gerenciar máquinas virtuais
    - Criar tickets de solicitação de suporte (não incluir registro de providers do Azure)

## Diagrama de arquitetura

![Diagrama das tarefas do laboratório.](../media/az104-lab02a-architecture.png)

## Habilidades praticadas

+ Tarefa 1: Implementar grupos de gerenciamento (management groups).
+ Tarefa 2: Revisar e atribuir uma função integrada do Azure.
+ Tarefa 3: Criar uma função RBAC personalizada.
+ Tarefa 4: Monitorar atribuições de função com o Log de atividade (Activity log).

## Tarefa 1: Implementar Management Groups

Nesta tarefa, você criará e configurará grupos de gerenciamento (management groups). Grupos de gerenciamento são usados para organizar e segmentar assinaturas logicamente. Eles permitem que RBAC e Azure Policy sejam atribuídos e herdados por outros grupos de gerenciamento e assinaturas. Por exemplo, se sua organização tiver uma equipe de suporte dedicada para a Europa, você pode organizar as assinaturas europeias em um grupo de gerenciamento para fornecer à equipe de suporte acesso a essas assinaturas (sem fornecer acesso individual a todas as assinaturas). No nosso cenário, todos do Help Desk precisarão criar uma solicitação de suporte em todas as assinaturas.

1. Faça logon no portal do Azure - `https://portal.azure.com`.

1. Pesquise e selecione `Microsoft Entra ID`.

1. No painel **Gerenciar (Manage)**, selecione **Propriedades (Properties)**.

1. Revise a área **Gerenciamento de acesso para recursos do Azure (Access management for Azure resources)**. Observe que você pode gerenciar o acesso a todas as assinaturas do Azure e grupos de gerenciamento no locatário.

1. Pesquise e selecione **Grupos de gerenciamento (Management groups)**.

1. No painel **Grupos de gerenciamento (Management groups)**, clique em **+ Criar (+ Create)**.

1. Crie um grupo de gerenciamento com as seguintes configurações. Selecione **Enviar (Submit)** quando terminar.

    | Configuração | Valor |
    | --- | --- |
    | Management group ID | `az104-mg1` (deve ser único no diretório) |
    | Management group display name | `az104-mg1` |

1. Atualize a página do grupo de gerenciamento para garantir que seu novo grupo de gerenciamento seja exibido. Isso pode levar um minuto. (Use o botão **Atualizar (Refresh)** do navegador/portal, se necessário.)

   >**Nota:** Você percebeu o root do grupo de gerenciamento (management group root)? O root management group é integrado à hierarquia para agrupar todos os grupos de gerenciamento e assinaturas. Esse grupo root permite que políticas globais e atribuições de funções do Azure sejam aplicadas no nível do diretório. Após criar um grupo de gerenciamento, você adicionaria quaisquer assinaturas que devam ser incluídas no grupo.

1. Se você receber erros de permissão na Tarefa 2 após criar o grupo de gerenciamento, faça logout do portal do Azure e entre novamente no portal do Azure - `https://portal.azure.com`.

    >**Nota:** Isso atualiza suas credenciais e pode resolver erros "AuthorizationFailed" nas lâminas de IAM do grupo de gerenciamento.

## Tarefa 2: Revisar e atribuir uma função integrada do Azure

Nesta tarefa, você revisará as funções integradas e atribuirá a função Virtual Machine Contributor a um membro do Help Desk. O Azure fornece um grande número de [funções integradas](https://learn.microsoft.com/azure/role-based-access-control/built-in-roles).

>**Nota:** Nas etapas a seguir, você atribuirá a função ao grupo **helpdesk**. Se você não tiver um grupo Help Desk, leve um minuto para criá-lo.

1. Selecione o grupo de gerenciamento **az104-mg1**.

1. Selecione o painel **Controle de acesso (IAM) (Access control (IAM))** e, em seguida, a aba **Funções (Roles)**.

1. Percorra as definições de funções integradas que estão disponíveis. Clique em **Visualizar (View)** em uma função para obter informações detalhadas sobre as seções **Permissões (Permissions)**, **JSON** e **Atribuições (Assignments)**. Você frequentemente usará *owner*, *contributor* e *reader*.

1. Selecione **+ Adicionar (+ Add)** e, no menu suspenso, selecione **Adicionar atribuição de função (Add role assignment)**.

1. No painel **Adicionar atribuição de função (Add role assignment)**, pesquise e selecione **Virtual Machine Contributor**. A função Virtual Machine Contributor permite gerenciar máquinas virtuais, mas não acessar o sistema operacional delas nem gerenciar a rede virtual e a conta de armazenamento às quais estão conectadas. Esta é uma boa função para o Help Desk. Selecione **Avançar (Next)**.

    >**Você sabia?** O Azure originalmente fornecia apenas o modelo de implantação **Classic**. Isso foi substituído pelo modelo de implantação **Azure Resource Manager**. Como boa prática, não use recursos clássicos.

1. Na aba **Membros (Members)**, clique em **Selecionar membros (Select Members)**.

1. Pesquise e selecione o grupo `helpdesk`. Clique em **Selecionar (Select)** e, em seguida, clique em **Avançar (Next)**.

1. Na aba **Condições (Conditions)**, clique em **Avançar (Next)**.

1. Na aba **Tipo de atribuição (Assignment type)**, deixe o tipo de atribuição como **Elegível (Eligible)** e as configurações de tempo como padrão, então clique em **Avançar (Next)**.

1. Clique em **Revisar e atribuir (Review + assign)** para criar a atribuição da função.

1. Continue no painel **Controle de acesso (IAM)**. Na aba **Atribuições de função (Role assignments)**, confirme que o grupo **helpdesk** possui a função **Virtual Machine Contributor**.

    >**Nota:** Como boa prática, sempre atribua funções a grupos e não a indivíduos.

    >**Você sabia?** Esta atribuição pode não realmente conceder a você privilégios adicionais. Se você já tem a função Owner, essa função inclui todas as permissões associadas à função VM Contributor.

## Tarefa 3: Criar uma função RBAC personalizada

Nesta tarefa, você criará uma função RBAC personalizada. Funções personalizadas são uma parte central da implementação do princípio de menor privilégio para um ambiente. Funções integradas podem ter permissões demais para seu cenário. Também criaremos uma nova função e removeremos permissões que não são necessárias. Você tem um plano para gerenciar permissões sobrepostas?

1. Continue trabalhando no seu grupo de gerenciamento. Navegue até o painel **Controle de acesso (IAM) (Access control (IAM))**.

1. Selecione **+ Adicionar (+ Add)** e, no menu suspenso, selecione **Adicionar função personalizada (Add custom role)**.

1. Na aba **Básicos (Basics)**, complete a configuração.

    | Configuração | Valor |
    | --- | --- |
    | Nome da função personalizada | `Custom Support Request` |
    | Descrição | `A custom contributor role for support requests.` |

1. Para **Permissões de base (Baseline permissions)**, selecione **Clonar uma função (Clone a role)**. No menu suspenso **Função para clonar (Role to clone)**, selecione **Support Request Contributor**.

    ![Captura de tela clonando uma função.](../media/az104-lab02a-clone-role.png)

1. Selecione **Avançar (Next)** para ir para a aba **Permissões (Permissions)**, e então selecione **+ Excluir permissões (+ Exclude permissions)**.

1. No campo de pesquisa de resource providers, insira `.Support` e selecione **Microsoft.Support**.

1. Na lista de permissões, marque a caixa ao lado de **Other: Registers Support Resource Provider** e então selecione **Adicionar (Add)**. A função deverá ser atualizada para incluir essa permissão como um *NotAction*.

    >**Nota:** Um resource provider do Azure é um conjunto de operações REST que habilitam funcionalidade para um serviço específico do Azure. Não queremos que o Help Desk tenha essa capacidade, então ela está sendo removida da função clonada.

1. Na aba **Escopos atribuíveis (Assignable scopes)**, assegure-se de que seu grupo de gerenciamento esteja listado, então clique em **Avançar (Next)**.

1. Revise o JSON para as propriedades *Actions*, *NotActions* e *AssignableScopes* que foram personalizadas na função.

1. Selecione **Revisar e criar (Review + Create)** e então selecione **Criar (Create)**.

    >**Nota:** Neste ponto, você criou uma função personalizada e a atribuiu ao grupo de gerenciamento.

## Tarefa 4: Monitorar atribuições de função com o Activity Log

Nesta tarefa, você visualizará o log de atividade para determinar se alguém criou uma nova função.

1. No portal localize o recurso **az104-mg1** e selecione **Log de atividade (Activity log)**. O log de atividade fornece visão sobre eventos a nível de assinatura.

1. Revise as atividades para atribuições de função. O log de atividade pode ser filtrado por operações específicas.

    ![Captura de tela da página Activity log com filtro configurado.](../media/az104-lab02a-searchactivitylog.png)

## Limpar seus recursos

Se você estiver trabalhando com **sua própria assinatura**, leve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que os custos sejam minimizados. A maneira mais fácil de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ No portal do Azure, selecione o grupo de gerenciamento, selecione **Excluir (Delete)** e clique em **Sim (Yes)** para confirmar a exclusão.
+ Usando o Azure PowerShell, `Remove-AzManagementGroup -GroupName az104-mg1`.
+ Usando o CLI, `az account management-group delete --name az104-mg1`.

## Estenda seu aprendizado com o Copilot

Copilot pode ajudá-lo a aprender como usar as ferramentas de script do Azure. Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precise de mais informações. Abra um navegador Edge e escolha Copilot (no canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.
+ Crie duas tabelas destacando comandos importantes do PowerShell e do CLI para obter informações sobre as assinaturas da organização no Azure e explique cada comando na coluna “Explicação”.
+ Qual é o formato do arquivo JSON do Azure RBAC?
+ Quais são os passos básicos para criar uma função RBAC personalizada no Azure?
+ Qual é a diferença entre funções Azure RBAC e funções do Microsoft Entra ID?

## Aprenda mais com treinamento autodirigido

+ [Proteja seus recursos do Azure com controle de acesso baseado em função do Azure (Azure RBAC)](https://learn.microsoft.com/training/modules/secure-azure-resources-with-rbac/). Use o Azure RBAC para gerenciar o acesso a recursos no Azure.

## Principais conclusões

Parabéns por concluir o laboratório. Aqui estão os principais pontos deste laboratório.

+ Grupos de gerenciamento (management groups) são usados para organizar assinaturas.
+ O grupo de gerenciamento root integrado inclui todos os grupos de gerenciamento e assinaturas.
+ O Azure possui muitas funções integradas. Você pode atribuir essas funções para controlar o acesso aos recursos.
+ Você pode criar novas funções ou customizar funções existentes.
+ Funções são definidas em um arquivo formatado em JSON e incluem *Actions*, *NotActions*, e *AssignableScopes*.
+ Você pode usar o Log de atividade (Activity Log) para monitorar atribuições de funções.
