---
lab:
  title: 'Laboratório 01: Gerenciar identidades do Microsoft Entra ID'
  module: Administrar identidade
  description: Criar e configurar contas de usuário e de grupo.
  duration: 30 minutos
  level: 300
  islab: true
  primarytopics:
    - Azure
    - Microsoft Entra ID
    - Contas de usuário
layout: default
---

# Laboratório 01 - Gerenciar identidades do Microsoft Entra ID

## Introdução ao laboratório

Este é o primeiro de uma série de laboratórios para Administradores do Azure. Neste laboratório, você aprenderá sobre usuários e grupos. Usuários e grupos são os blocos de construção básicos para uma solução de identidade.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão descritos usando **East US**.

## Tempo estimado: 30 minutos

## Cenário do laboratório

Sua organização está construindo um novo ambiente de laboratório para testes pré-produção de aplicativos e serviços. Alguns engenheiros estão sendo contratados para gerenciar o ambiente do laboratório, incluindo as máquinas virtuais. Para permitir que os engenheiros se autentiquem usando Microsoft Entra ID, você recebeu a tarefa de provisionar usuários e grupos. Para minimizar a sobrecarga administrativa, a associação aos grupos deve ser atualizada automaticamente com base em títulos de trabalho.

## Diagrama de arquitetura

![Diagrama da arquitetura do laboratório 01.](../media/az104-lab01-architecture.png)

## Habilidades abordadas

+ Tarefa 1: Criar e configurar contas de usuário.
+ Tarefa 2: Criar grupos e adicionar membros.

## Tarefa 1: Criar e configurar contas de usuário

Nesta tarefa, você criará e configurará contas de usuário. As contas de usuário armazenam dados do usuário, como nome, departamento, localização e informações de contato.

1. Faça logon no **Azure portal** - `https://portal.azure.com`.

1. Para prosseguir ao portal, selecione **Cancelar (Cancel)** na tela de boas-vindas **Bem-vindo ao Azure (Welcome to Azure)**.

    >**Observação:** O Azure portal é usado em todos os laboratórios. Se você é novo no Azure, pesquise e selecione `Quickstart Center`. Reserve alguns minutos para assistir ao vídeo **Introdução ao portal do Azure (Getting started in the Azure portal)**. Mesmo que você já tenha usado o portal antes, encontrará algumas dicas sobre navegação e personalização da interface.

1. Pesquise e selecione `Microsoft Entra ID`. Microsoft Entra ID é a solução de gerenciamento de identidade e acesso baseada em nuvem da Azure. Reserve alguns minutos para se familiarizar com alguns dos recursos listados no painel esquerdo.

1. Selecione a página **Visão Geral (Overview)** e depois a guia **Gerenciar locatários (Manage tenants)**.

    >**Você sabia?** Um tenant é uma instância específica do Microsoft Entra ID contendo contas e grupos. Dependendo da sua situação, você pode criar mais tenants e usar a opção **Alternar (Switch)** para alternar entre eles.

1. Retorne à página **Entra ID** pressionando voltar no navegador ou selecionando a opção no caminho de navegação (breadcrumb).

1. Se tiver tempo, explore outras opções, como **Licenças (Licenses)** e **Redefinição de senha (Password reset)**.

### Criar um novo usuário

1. Na página **Gerenciar (Manage)**, selecione **Usuários (Users)**; em seguida, no menu suspenso **Novo usuário (New user)**, selecione **Criar novo usuário (Create new user)**.

1. Crie um novo usuário com as seguintes configurações (deixe as demais com os padrões). Na guia **Propriedades (Properties)**, observe todos os diferentes tipos de informações que podem ser incluídas na conta de usuário.

    | Configuração | Valor |
    | --- | --- |
    | User principal name | `az104-user1` |
    | Display name | `az104-user1` |
    | Gerar senha automaticamente (Auto-generate password) | **marcado** |
    | Conta habilitada (Account enabled) | **marcado** |
    | Cargo (Job title - guia Propriedades) | `IT Lab Administrator` |
    | Departamento (Department - guia Propriedades) | `IT` |
    | Local de uso (Usage location - guia Propriedades) | **Estados Unidos** |

1. Depois de revisar, selecione **Revisar e criar (Review + create)** e então **Criar (Create)**.

1. Atualize a página e confirme que o novo usuário foi criado.

### Convidar um usuário externo

1. No menu suspenso **Novo usuário (New user)**, selecione **Convidar um usuário externo (Invite an external user)**.

    | Configuração | Valor |
    | --- | --- |
    | Email | seu endereço de e-mail |
    | Display name | seu nome |
    | Enviar mensagem de convite (Send invite message) | **marcar a caixa** |
    | Mensagem (Message) | `Welcome to Azure and our group project` |

1. Vá para a guia **Propriedades (Properties)**. Complete as informações básicas, incluindo estes campos.

    | Configuração | Valor |
    | --- | --- |
    | Cargo (Job title)  | `IT Lab Administrator` |
    | Departamento (Department)  | `IT` |
    | Local de uso (Usage location - guia Propriedades) | **Estados Unidos** |

1. Selecione **Revisar e convidar (Review + invite)**, e então **Convidar (Invite)**.

1. **Atualize** a página e confirme que o usuário convidado foi criado. Você deverá receber o e-mail de convite em breve.

    >**Observação:** É improvável que você esteja criando contas de usuário individualmente. Você sabe como sua organização planeja criar e gerenciar contas de usuário?

## Tarefa 2: Criar grupos e adicionar membros

Nesta tarefa, você criará uma conta de grupo. Contas de grupo podem incluir contas de usuário ou dispositivos. Existem duas maneiras básicas de atribuir membros a grupos: Estática e Dinâmica. Grupos estáticos exigem que administradores adicionem e removam membros manualmente. Grupos dinâmicos são atualizados automaticamente com base nas propriedades de uma conta de usuário ou dispositivo. Por exemplo, job title.

1. No **Azure portal**, pesquise e selecione `Microsoft Entra ID`. Na página **Gerenciar (Manage)**, selecione **Grupos (Groups)**.

1. Reserve um minuto para se familiarizar com as configurações de grupo no painel esquerdo.

   + **Expiração (Expiration)** permite que você configure uma duração de vida do grupo em dias. Após esse período, o grupo deve ser renovado pelo proprietário.
   + **Política de nomenclatura (Naming policy)** permite que você configure palavras bloqueadas e adicione um prefixo ou sufixo aos nomes de grupo.

1. Na página **Todos os grupos (All groups)**, selecione **+ Novo grupo (+ New group)** e crie um novo grupo.

    | Configuração | Valor |
    | --- | --- |
    | Group type | **Security** |
    | Group name | `IT Lab Administrators` |
    | Group description | `Administrators that manage the IT lab` |
    | Membership type | **Assigned** |

    >**Observação**: Uma licença Entra ID Premium P1 ou P2 é necessária para associação dinâmica. Se outros **Tipos de associação (Membership types)** estiverem disponíveis, as opções aparecerão no menu suspenso.

    ![Captura de tela de criação de grupo atribuído.](../media/az104-lab01-create-assigned-group.png)

1. Selecione **Nenhum proprietário selecionado (No owners selected)**.

1. Na página **Adicionar proprietários (Add owners)**, pesquise e **selecione** a si mesmo (mostrado no canto superior direito) como proprietário (owner). Observe que você pode ter mais de um proprietário.

1. Selecione **Nenhum membro selecionado (No members selected)**.

1. No painel **Adicionar membros (Add members)**, pesquise e **selecione** o **az104-user1** e o **usuário convidado (guest user)** que você convidou. Adicione ambos os usuários ao grupo.

1. Selecione **Criar (Create)** para implantar o grupo.

1. **Atualize** a página e verifique se seu grupo foi criado.

1. Selecione o novo grupo e revise as informações de **Membros (Members)** e **Proprietários (Owners)**.

>**Observação:** Você pode estar gerenciando um grande número de grupos. Sua organização tem um plano para criar grupos e adicionar membros?

## Amplie seu aprendizado com o Copilot

Copilot pode ajudá-lo a aprender como usar as ferramentas de script do Azure. Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precise de mais informações. Abra um navegador Edge e escolha Copilot (no canto superior direito) ou acesse copilot.microsoft.com. Reserve alguns minutos para experimentar estes prompts.
+ Quais são os comandos do Azure PowerShell e do Azure CLI para criar um grupo de segurança chamado IT Admins? Forneça a página de referência oficial do comando.
+ Forneça uma estratégia passo a passo para gerenciar usuários e grupos no Microsoft Entra ID.
+ Quais são os passos no Azure portal para criar usuários e grupos em massa?
+ Forneça uma tabela comparativa de contas de usuário internas e externas (guest) do Microsoft Entra ID.

## Aprenda mais com treinamentos self-paced

+ [Entenda o Microsoft Entra ID](https://learn.microsoft.com/training/modules/understand-azure-active-directory/). Compare Microsoft Entra ID com Active Directory DS, aprenda sobre Microsoft Entra ID P1 e P2 e explore Microsoft Entra Domain Services para gerenciar dispositivos e aplicativos com associação a domínio na nuvem.
+ [Criar usuários e grupos do Azure no Microsoft Entra ID](https://learn.microsoft.com//training/modules/create-users-and-groups-in-azure-active-directory/). Crie usuários no Microsoft Entra ID. Entenda os diferentes tipos de grupos. Crie um grupo e adicione membros. Gerencie contas business-to-business de guest.
+ [Permitir que usuários redefinam suas senhas com self-service password reset do Microsoft Entra](https://learn.microsoft.com/training/modules/allow-users-reset-their-password/). Avalie o self-service password reset para permitir que usuários na sua organização redefinam suas senhas ou desbloqueiem suas contas. Configure, teste e valide o self-service password reset.

## Principais conclusões

Parabéns por concluir o laboratório. Aqui estão os principais pontos deste laboratório:

+ Um tenant representa sua organização e ajuda a gerenciar uma instância específica dos serviços Microsoft na nuvem para seus usuários internos e externos.
+ Microsoft Entra ID possui contas de usuário e guest. Cada conta tem um nível de acesso específico ao escopo do trabalho esperado.
+ Grupos reúnem usuários ou dispositivos relacionados. Existem dois tipos de grupos, incluindo Security e Microsoft 365.
+ A associação a grupos pode ser atribuída de forma estática ou dinâmica.
