---

demo:
    title: 'Demonstração 02: Administrar Governança e Conformidade'
    module: 'Administrar Governança e Conformidade'
layout: default
---

# 02 - Administrar Governança e Conformidade

## Configurar Assinaturas

Esta área não possui uma demonstração formal.

**Referência**: [Create an additional Azure subscription](https://docs.microsoft.com/azure/cost-management-billing/manage/create-subscription)

## Configurar Azure Policy

Nesta demonstração, trabalharemos com políticas do Azure.

**Referência**: [Tutorial: Build policies to enforce compliance - Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Atribuir uma política**

1.  Acesse o Microsoft Azure portal.

2.  Pesquise por e selecione **Política (Policy)**.

3.  Selecione **Atribuições (Assignments)** e então **Atribuir política (Assign Policy)**.

5.  Explique o **Escopo (Scope)**, que determina em quais recursos ou agrupamentos de recursos a atribuição de política (policy assignment) é aplicada.

6.  Selecione o elipse de **Definição de política (Policy definition)** para abrir a lista de definições disponíveis. Dedique um tempo para revisar as definições de política integradas.

7.  Pesquise por e selecione a política **Allowed locations**. Esta política permite restringir as localizações que sua organização pode especificar ao implantar recursos.

8.  Vá para a guia **Parâmetros (Parameters)** e, usando o menu suspenso, selecione um ou mais locais permitidos (Allowed locations).

9.  Clique em **Revisar + criar (Review + create)** e então em **Criar (Create)** para criar a política.

**Criar e atribuir uma Definição de iniciativa (Initiative Definition)**

1.  Retorne à página do serviço **Política (Policy)** e selecione **Definições (Definitions)** em Autoria (Authoring).

2.  Selecione **Definição de iniciativa (Initiative Definition)** no topo da página.

3.  Forneça um **Nome (Name)** e uma **Descrição (Description)**.

4.  Crie uma nova categoria usando **Criar novo (Create new)**.

5.  No painel à direita, **Adicione (Add)** a política **Allowed locations**.

6.  Adicione mais uma política de sua escolha.

7.  Clique em **Salvar (Save)** para salvar suas alterações e então em **Atribuir (Assign)** para atribuir sua Definição de iniciativa (Initiative Definition) à sua assinatura.

**Verificar conformidade**

1.  Retorne à página do serviço **Política (Policy)**.

2.  Selecione **Conformidade (Compliance)**.

3.  Revise o status da sua política e da sua definição.

**Verificar tarefas de remediação**

1.  Retorne à página do serviço **Política (Policy)**.

2.  Selecione **Remediação (Remediation)**.

3.  Revise quaisquer tarefas de remediação listadas.

4.  Quando tiver tempo, remova a política e a iniciativa.

## Configurar Controle de Acesso Baseado em Função (Role-Based Access Control)

Nesta demonstração, aprenderemos sobre atribuições de função (role assignments).

**Referência**: [Tutorial: Grant a user access to Azure resources using the Azure portal - Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

**Referência**: [Quickstart - Check access for a user to Azure resources - Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/check-access)

**Localizar a lâmina Controle de Acesso (IAM) (Access Control (IAM))**

1.  Acesse o Microsoft Azure portal e selecione um grupo de recursos (resource group). Anote qual grupo de recursos você utiliza.

2.  Selecione a lâmina **Controle de Acesso (IAM) (Access Control (IAM))**.

3.  Essa lâmina estará disponível para muitos recursos diferentes, permitindo que você controle permissões.

**Revisar permissões da função**

1.  Selecione a aba **Funções (Roles)** (no topo).

1.  Revise o grande número de funções integradas (built-in roles) disponíveis.

1.  Dê duplo clique em uma função e então selecione **Permissões (Permissions)** (no topo).

1.  Continue explorando a função até conseguir visualizar as ações **Ler (Read), Gravar (Write) e Excluir (Delete)** para essa função.

1.  Retorne à lâmina **Controle de Acesso (IAM) (Access Control (IAM))**.

**Adicionar uma atribuição de função (role assignment)**

1.  Crie um usuário ou selecione um usuário existente.

1.  Selecione **Adicionar atribuição de função (Add role assignment)** e escolha uma função. Por exemplo, *owner*.

1.  Selecione **Verificar acesso (Check access)**.

1.  Revise as permissões do usuário.

1.  Observe que você pode utilizar **Atribuições de negação (Deny assignments)**.
