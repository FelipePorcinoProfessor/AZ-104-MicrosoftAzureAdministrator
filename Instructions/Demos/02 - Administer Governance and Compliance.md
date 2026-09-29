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

Nesta demonstração, trabalharemos com Azure policies.

**Referência**: [Tutorial: Build policies to enforce compliance - Azure Policy](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

**Atribuir uma policy**

1.  Acesse o Azure portal.

2.  Pesquise por e selecione **Policy**.

3.  Selecione **Assignments** e então **Assign Policy**.

5.  Explique o **Scope**, que determina em quais recursos ou agrupamentos de recursos a policy assignment é aplicada.

6.  Selecione o elipse de **Policy definition** para abrir a lista de definições disponíveis. Reserve um tempo para revisar as definições de policy integradas.

7.  Pesquise por e selecione a policy **Allowed locations**. Esta policy permite restringir as localizações que sua organização pode especificar ao implantar recursos.

8.  Vá para a guia **Parameters** e, usando o menu suspenso, selecione uma ou mais allowed locations.

9.  Clique em **Review + create** e então em **Create** para criar a policy.

**Criar e atribuir uma initiative definition**

1.  Retorne à página do Azure Policy e selecione **Definitions** em Authoring.

2.  Selecione **Initiative Definition** no topo da página.

3.  Forneça um **Name** e uma **Description**.

4.  Crie uma nova Category usando **Create new**.

5.  No painel à direita, **Add** a policy **Allowed locations**.

6.  Adicione mais uma policy de sua escolha.

7.  Clique em **Save** para salvar suas alterações e então em **Assign** para atribuir sua initiative definition à sua subscription.

**Verificar conformidade**

1.  Retorne à página do serviço Azure Policy.

2.  Selecione **Compliance**.

3.  Revise o status de sua policy e de sua definition.

**Verificar tarefas de remediação**

1.  Retorne à página do serviço Azure Policy.

2.  Selecione **Remediation**.

3.  Revise quaisquer tarefas de remediação listadas.

4.  Quando tiver tempo, remova a policy e a initiative.

## Configurar Role-Based Access Control

Nesta demonstração, aprenderemos sobre role assignments.

**Referência**: [Tutorial: Grant a user access to Azure resources using the Azure portal - Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

**Referência**: [Quickstart - Check access for a user to Azure resources - Azure RBAC](https://docs.microsoft.com/azure/role-based-access-control/check-access)

**Localizar o blade Access Control**

1.  Acesse o Azure portal e selecione um resource group. Anote qual resource group você utiliza.

2.  Selecione a lâmina **Access Control (IAM)**.

3.  Essa blade estará disponível para muitos recursos diferentes, permitindo que você controle permissões.

**Revisar permissões de role**

1.  Selecione a aba **Roles** (no topo).

1.  Revise o grande número de built-in roles disponíveis.

1.  Dê duplo clique em um role e então selecione **Permissions** (no topo).

1.  Continue explorando o role até conseguir visualizar as ações **Read, Write, and Delete** para esse role.

1.  Retorne à lâmina **Access Control (IAM)**.

**Adicionar um role assignment**

1.  Crie um usuário ou selecione um usuário existente.

1.  Selecione **Add role assignment** e escolha um role. Por exemplo, *owner*.

1.  Selecione **Check access**.

1.  Revise as permissões do usuário.

1.  Observe que você pode utilizar **Deny assignments**.
