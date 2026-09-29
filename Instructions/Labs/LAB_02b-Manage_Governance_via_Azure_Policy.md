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

# Lab 02b - Gerenciar Governança via Azure Policy

## Introdução ao laboratório

Neste laboratório, você aprenderá como implementar os planos de governança da sua organização. Você verá como as políticas do Azure podem garantir que decisões operacionais sejam aplicadas em toda a organização. Também aprenderá a usar tags de recurso para melhorar os relatórios.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Tempo estimado: 30 minutos

## Cenário do laboratório

A presença em nuvem da sua organização cresceu consideravelmente no último ano. Durante uma auditoria recente, você descobriu um número substancial de recursos que não têm um proprietário, projeto ou centro de custo definido. Para melhorar o gerenciamento dos recursos do Azure na sua organização, você decide implementar a seguinte funcionalidade:

- aplicar tags de recurso para anexar metadados importantes aos recursos do Azure
- impor o uso de tags de recurso para novos recursos usando Azure Policy
- atualizar recursos existentes com tags de recurso
- usar resource locks para proteger recursos configurados

## Diagrama de arquitetura

![Diagrama da arquitetura da tarefa.](../media/az104-lab02b-architecture.png)

## Habilidades do trabalho

+ Tarefa 1: Criar e atribuir tags via o Portal do Azure (Azure portal).
+ Tarefa 2: Impor o uso de tags com uma Azure Policy.
+ Tarefa 3: Aplicar tags via uma Azure Policy.
+ Tarefa 4: Configurar e testar resource locks.

## Tarefa 1: Atribuir tags via o Portal do Azure (Azure portal)

Nesta tarefa, você criará e atribuirá uma tag a um Grupo de recursos (Resource group) via o Portal do Azure (Azure portal). Tags são um componente crítico de uma estratégia de governança conforme descrito pelo Microsoft Well-Architected Framework e Cloud Adoption Framework. Tags permitem identificar rapidamente proprietários de recursos, datas de descomissionamento, contatos de grupo e outros pares nome/valor que sua organização considere importantes. Para esta tarefa, você atribuirá uma tag que identifica o Cost Center do recurso.

1. Faça login no **Portal do Azure (Azure portal)** - `https://portal.azure.com`.

1. Pesquise e selecione `Resource groups`.

1. Em Grupos de recursos (Resource groups), selecione **+ Criar (+ Create)**.

    | Configuração | Valor |
    | --- | --- |
    | Assinatura (Subscription) | sua assinatura |
    | Nome do grupo de recursos (Resource group name) | `az104-rg2` |
    | Região (Region) | **East US** |

    >**Nota:** Para cada laboratório deste curso, você criará um novo grupo de recursos. Isso permite que você localize e gerencie rapidamente seus recursos de laboratório.

1. Selecione **Avançar (Next)** e vá até a aba **Etiquetas (Tags)**. Forneça informações para uma nova tag.

    | Configuração | Valor |
    | --- | --- |
    | Nome (Name) | Cost Center |
    | Valor (Value) | 000 |

1. Selecione **Revisar + Criar (Review + Create)**, e então selecione **Criar (Create)**.

## Tarefa 2: Impor o uso de tags via uma Azure Policy

Nesta tarefa, você atribuirá a política integrada *Require a tag and its value on resources* ao grupo de recursos e avaliará o resultado. Azure Policy pode ser usado para impor configurações e, neste caso, governança, aos seus recursos do Azure.

1. No Portal do Azure (Azure portal), pesquise e selecione `Policy`.

1. No painel **Criação (Authoring)**, selecione **Definições (Definitions)**. Reserve um momento para navegar pela lista de [definições de policy integradas (built-in policy definitions)](https://learn.microsoft.com/azure/governance/policy/samples/built-in-policies) disponíveis para uso. Observe que você também pode pesquisar uma definição.

    ![Captura de tela da definição de política.](../media/az104-lab02b-policytags.png)

1. Pesquise pela política integrada `Require a tag and its value on resources`. Selecione a policy e reserve um minuto para revisar a definição.

1. Selecione **Atribuir política (Assign policy)**.

1. Especifique o **Escopo (Scope)** clicando no botão de reticências e selecionando os seguintes valores. Clique em **Selecionar (Select)** quando terminar.

    | Configuração | Valor |
    | --- | --- |
    | Assinatura (Subscription) | *sua assinatura* |
    | Grupo de recursos (Resource Group) | **az104-rg2** |

    >**Nota**: Você pode atribuir políticas no nível de management group, assinatura (subscription) ou grupo de recursos (resource group). Você também tem a opção de especificar exclusões, como assinaturas, grupos de recursos ou recursos individuais. Neste cenário, queremos a tag em todos os recursos do grupo de recursos.

1. Configure as propriedades **Básico (Basics)** da atribuição especificando as seguintes configurações (deixe as demais nos padrões):

    | Configuração | Valor |
    | --- | --- |
    | Nome da atribuição (Assignment name) | `Require Cost Center tag and its value on resources` |
    | Descrição (Description) | `Require Cost Center tag and its value on all resources in the resource group`|
    | Aplicação da policy (Policy enforcement) | Habilitado (Enabled) |

    >**Nota**: O **Nome da atribuição (Assignment name)** é preenchido automaticamente com o nome da policy que você selecionou, mas você pode alterá-lo. A **Descrição (Description)** é opcional. Observe que você pode desabilitar a policy a qualquer momento.

1. Clique em **Avançar (Next)** e defina **Parâmetros (Parameters)** com os seguintes valores:

    | Configuração | Valor |
    | --- | --- |
    | Nome da tag (Tag Name) | `Cost Center` |
    | Valor da tag (Tag Value) | `000` |

1. Clique em **Avançar (Next)** e revise as abas **Correção (Remediation)** e **Identidade gerenciada (Managed Identity)**. Deixe a caixa **Create a Managed Identity** desmarcada na aba **Identidade gerenciada (Managed Identity)**.

1. Clique em **Revisar + Criar (Review + Create)** e então clique em **Criar (Create)**.

    >**Nota**: Agora você verificará se a nova atribuição de policy está em vigor tentando criar uma Storage account no grupo de recursos. Você criará a storage account sem adicionar a tag exigida.

    >**Nota**: Pode levar entre 5 e 10 minutos para a policy entrar em vigor.

1. No portal, pesquise e selecione `Storage Accounts`, e selecione **+ Criar (+ Create)**.

1. Na aba **Básico (Basics)** do painel **Criar conta de armazenamento (Create storage account)**, complete a configuração.

    | Configuração | Valor |
    | --- | --- |
    | Grupo de recursos (Resource group) | **az104-rg2** |
    | Nome da conta de armazenamento (Storage account name) | *any globally unique combination of between 3 and 24 lower case letters and digits, starting with a letter* |

1. Selecione **Revisar (Review)** e então clique em **Criar (Create)**.

1. Você deverá receber uma mensagem **Validação falhou (Validation failed)**. Visualize a mensagem para identificar a razão da falha. Verifique se a mensagem de erro indica que a implantação do recurso foi impedida pela policy.

    ![Captura de tela do erro de policy proibida.](../media/az104-lab02b-policyerror.png)

>**Nota**: Ao clicar na aba **Erro bruto (Raw Error)**, você pode encontrar mais detalhes sobre o erro, incluindo o nome da role definition **Require a tag and its value on resources**. A implantação falhou porque a storage account que você tentou criar não possuía uma tag chamada **Cost Center** com seu valor configurado como **Default**.

## Tarefa 3: Aplicar tags via uma Azure Policy

Nesta tarefa, usaremos uma nova definição de policy para remediar quaisquer recursos não conformes. Neste cenário, faremos com que quaisquer recursos filhos de um grupo de recursos herdem a tag **Cost Center** que foi definida no grupo de recursos.

1. No Portal do Azure (Azure portal), pesquise e selecione `Policy`.

1. Na seção **Criação (Authoring)**, clique em **Atribuições (Assignments)**.

1. Na lista de atribuições, clique no ícone de reticências na linha que representa a atribuição da policy **Require a tag and its value on resources** e use o item de menu **Excluir atribuição (Delete assignment)** para excluir a atribuição.

1. Quando for solicitado **Deseja excluir a atribuição da política? (Do you want to delete the policy assignment?)**, selecione **Sim (Yes)**.

1. Clique em **Atribuir política (Assign policy)** e especifique o **Escopo (Scope)** clicando no botão de reticências e selecionando os seguintes valores:

    | Configuração | Valor |
    | --- | --- |
    | Assinatura (Subscription) | sua Azure subscription |
    | Grupo de recursos (Resource Group) | `az104-rg2` |

1. Para especificar a **Definição de policy (Policy definition)**, clique no botão de reticências e então pesquise por e selecione `Inherit a tag from the resource group if missing`.

1. Selecione **Adicionar (Add)** e então configure as demais propriedades **Básico (Basics)** da atribuição.

    | Configuração | Valor |
    | --- | --- |
    | Nome da atribuição (Assignment name) | `Inherit the Cost Center tag and its value 000 from the resource group if missing` |
    | Descrição (Description) | `Inherit the Cost Center tag and its value 000 from the resource group if missing` |
    | Aplicação da policy (Policy enforcement) | Habilitado (Enabled) |

1. Clique em **Avançar (Next)** e defina **Parâmetros (Parameters)** com os seguintes valores:

    | Configuração | Valor |
    | --- | --- |
    | Nome da tag (Tag Name) | `Cost Center` |

1. Clique em **Avançar (Next)** e, na aba **Correção (Remediation)**, configure as seguintes opções (deixe as demais com seus padrões):

    | Configuração | Valor |
    | --- | --- |
    | Criar uma tarefa de correção (Create a remediation task) | habilitado |
    | Policy para remediar (Policy to remediate) | **Inherit a tag from the resource group if missing** |

    >**Nota**: Esta definição de policy inclui o efeito **Modify**. Portanto, é necessária uma identidade gerenciada (managed identity).

    ![Captura de tela da página de correção da policy. ](../media/az104-lab02b-policyremediation.png)

1. Clique em **Revisar + Criar (Review + Create)** e então clique em **Criar (Create)**.

    >**Nota**: Para verificar se a nova atribuição de policy está em vigor, você criará outra storage account no mesmo grupo de recursos sem adicionar explicitamente a tag exigida.

    >**Nota**: Pode levar entre 5 e 10 minutos para a policy entrar em vigor.

1. Pesquise e selecione `Storage Account` e clique em **+ Criar (+ Create)**.

1. Na aba **Básico (Basics)** do painel **Criar conta de armazenamento (Create storage account)**, verifique que você está usando o Grupo de recursos (Resource Group) ao qual a Policy foi aplicada e especifique as seguintes configurações (deixe as demais com seus padrões) e clique em **Revisar (Review)**:

    | Configuração | Valor |
    | --- | --- |
    | Nome da conta de armazenamento (Storage account name) | *any globally unique combination of between 3 and 24 lower case letters and digits, starting with a letter* |
    | Serviço principal (Primary service) | Azure Blob Storage or Azure Data Lake Storage |

    >**Nota**: Serviço principal pode ser listado como Tipo de armazenamento preferido (Preferred Storage Type)

1. Verifique que desta vez a validação passou e clique em **Criar (Create)**.

1. Uma vez que a nova storage account for provisionada, clique em **Ir para o recurso (Go to resource)**.

1. No painel **Etiquetas (Tags)**, observe que a tag **Cost Center** com o valor **000** foi automaticamente atribuída ao recurso.

    >**Você sabia?** Se você pesquisar e selecionar **Etiquetas (Tags)** no portal, você pode visualizar os recursos com uma tag específica.

## Tarefa 4: Configurar e testar resource locks

Nesta tarefa, você configurará e testará um resource lock. Locks impedem exclusões ou modificações de um recurso.

1. Pesquise e selecione seu grupo de recursos.

1. No painel **Configurações (Settings)**, selecione **Bloqueios (Locks)**.

1. Selecione **Adicionar (Add)** e preencha as informações do bloqueio de recurso. Quando terminar, selecione **Ok**.

    | Configuração | Valor |
    | --- | --- |
    | Nome do bloqueio (Lock name) | `rg-lock` |
    | Tipo de bloqueio (Lock type) | **exclusão (delete)** (observe a seleção para **somente leitura (read-only)**) |

1. Navegue até o painel **Visão geral (Overview)** do grupo de recursos e selecione **Excluir o grupo de recursos (Delete resource group)**.

1. No campo de texto **Digite o nome do grupo de recursos para confirmar a exclusão (Enter resource group name to confirm deletion)** forneça o nome do grupo de recursos, `az104-rg2`. Observe que você pode copiar e colar o nome do grupo de recursos.

1. Observe o aviso: Excluir este grupo de recursos e seus recursos dependentes é uma ação permanente e não pode ser desfeita. (Deleting this resource group and its dependent resources is a permanent action and cannot be undone.) Selecione **Excluir (Delete)**. Quando uma segunda caixa de confirmação **Excluir (Delete)** aparecer, clique em **Excluir (Delete)** novamente para confirmar.

1. Você deverá receber uma notificação negando a exclusão.

    ![Captura de tela da falha ao excluir.](../media/az104-lab02b-failuretodelete.png)

    >**Nota:** Você precisará remover o bloqueio se pretende excluir o grupo de recursos.

## Limpeza dos seus recursos

Se você estiver trabalhando com **sua própria subscription**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e os custos sejam minimizados. A maneira mais fácil de excluir os recursos do laboratório é excluir o grupo de recursos do laboratório.

+ No Portal do Azure (Azure portal), selecione o grupo de recursos, selecione **Excluir o grupo de recursos (Delete the resource group)**, **Digite o nome do grupo de recursos (Enter resource group name)**, e então clique em **Excluir (Delete)**.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Aprenda mais com o Copilot
Copilot pode ajudá-lo a aprender a usar as ferramentas de script do Azure. Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (no canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para testar estes prompts.
+ Quais são os comandos do Azure PowerShell e do CLI para adicionar e remover resource locks em um grupo de recursos?
+ Tabule as diferenças entre Azure Policy e Azure RBAC, incluindo exemplos.
+ Quais são os passos para impor uma Azure Policy e remediar recursos que não estão em conformidade?
+ Como posso obter um relatório de recursos do Azure com tags específicas?

## Saiba mais com treinamento auto-guiado
+ [Azure Policy Initiatives](https://learn.microsoft.com/training/modules/sovereignty-policy-initiatives/). Neste módulo, você aprende como Azure Policy initiatives podem ser usadas para impor padrões organizacionais, avaliar conformidade em escala e gerenciar recursos do Azure de forma eficaz.

## Principais aprendizados

Parabéns por completar o laboratório. Aqui estão os principais pontos deste lab.

+ Azure tags são metadados que consistem em um par chave-valor. Tags descrevem um recurso particular no seu ambiente. Em particular, o uso de tags no Azure permite que você rotule seus recursos de maneira lógica.
+ Azure Policy estabelece convenções para recursos. Definições de policy (policy definitions) descrevem condições de conformidade dos recursos e o efeito a ser aplicado se uma condição for atendida. Uma condição compara um campo de propriedade do recurso ou um valor a um valor requerido. Existem muitas definições de policy integradas (built-in policy definitions) e você pode customizar as policies.
+ O recurso de tarefa de correção (remediation task) do Azure Policy é usado para trazer recursos para conformidade com base em uma definição e atribuição. Recursos que não estão em conformidade com uma atribuição de definição com efeito modify ou deployIfNotExist podem ser trazidos para conformidade usando uma tarefa de correção (remediation task).
+ Você pode configurar um resource lock em uma subscription, grupo de recursos (resource group) ou recurso (resource). O bloqueio pode proteger um recurso de exclusões e modificações acidentais por usuários. O bloqueio substitui quaisquer permissões de usuário.
+ Azure Policy é uma prática de segurança pré-implantação. RBAC e resource locks são práticas de segurança pós-implantação.
