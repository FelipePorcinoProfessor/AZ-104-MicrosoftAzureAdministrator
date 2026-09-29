---
lab:
  title: 'Laboratório 07: Gerenciar Azure storage'
  module: Administrar Azure Storage
  description: Configurar storage accounts do Azure, blob storage e file shares.
  duration: 50 minutes
  level: 400
  islab: true
  primarytopics:
    - Azure
    - Azure storage
    - Blob storage
    - File shares
layout: default
---

# Lab 07 - Gerenciar Azure Storage

## Introdução ao laboratório

Neste laboratório você aprenderá a criar storage accounts para Azure blobs e Azure files. Você aprenderá a configurar e proteger containers de blob. Também aprenderá a usar o Storage Browser para configurar e proteger file shares do Azure.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos foram escritos usando **East US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua organização atualmente armazena dados em repositórios on-premises. A maioria desses arquivos não é acessada com frequência. Você deseja minimizar o custo de armazenamento colocando arquivos acessados raramente em camadas de armazenamento com preço menor. Você também planeja explorar diferentes mecanismos de proteção que o Azure Storage oferece, incluindo acesso de rede, autenticação, autorização e replicação. Finalmente, você quer determinar em que medida o Azure Files é adequado para hospedar seus file shares on-premises.

## Diagrama de arquitetura

![Diagrama das tarefas.](../media/az104-lab07-architecture.png)

## Habilidades do trabalho

+ Tarefa 1: Criar e configurar uma storage account.
+ Tarefa 2: Criar e configurar blob storage seguro.
+ Tarefa 3: Criar e configurar o Azure File Storage.

## Tarefa 1: Criar e configurar uma storage account.

Nesta tarefa, você criará e configurará uma storage account. A storage account usará geo-redundant storage e não terá acesso público.

1. Faça logon no **Azure portal** - `https://portal.azure.com`.

1. Pesquise e selecione `Storage accounts`, selecione **Contas de armazenamento (Storage accounts)** nos resultados e então clique em **+ Criar (+ Create)**.

1. Na guia **Básicos (Basics)** da lâmina **Criar uma storage account (Create a storage account)**, especifique as seguintes configurações (deixe as demais com os valores padrão):

    | Configuração | Valor |
    | --- | --- |
    | Assinatura | o nome da sua assinatura do Azure |
    | Grupo de recursos | **az104-rg7** (criar novo) |
    | Nome da storage account | qualquer nome globalmente único entre 3 e 24 caracteres composto por letras e dígitos |
    | Região | **(US) East US** |
    | Desempenho | **Standard** (observe a opção Premium) |
    | Tipo de armazenamento preferido | **Azure Blob Storage or Azure Data Lake Storage** |
    | Redundância | **Geo-redundant storage** (observe as outras opções) |
    | Tornar os dados legíveis em caso de indisponibilidade regional. | Marque a caixa |

    >**Você sabia?** Você deve usar a camada de desempenho Standard para a maioria das aplicações. Use a camada Premium para aplicações corporativas ou de alto desempenho.

1. Nas guias **Avançado (Advanced)** e **Segurança (Security)**, use os ícones informativos para saber mais sobre as opções. Aceite os padrões.

1. Na guia **Rede (Networking)**, na seção **Acesso à rede pública (Public network access)**, selecione **Desabilitar (Disable)**. Isso restringirá o acesso de entrada enquanto permite o acesso de saída.

1. Revise a guia **Proteção de dados (Data protection)**. Observe que 7 dias é a política de retenção de soft delete padrão. Observe que você pode habilitar versioning para blobs. Aceite os padrões.

1. Revise a guia **Criptografia (Encryption)**. Observe as opções adicionais de segurança. Aceite os padrões.

1. Selecione **Revisar + criar (Review + create)**, aguarde o processo de validação terminar e então clique em **Criar (Create)**.

1. Depois que a storage account for implantada, selecione **Ir para o recurso (Go to resource)**.

1. Revise a lâmina **Visão geral (Overview)** e as configurações adicionais que podem ser alteradas. Essas são configurações globais para a storage account. Observe que a storage account pode ser usada para Blob containers, File shares, Queues e Tables.

1. Na lâmina **Segurança + rede (Security + networking)**, selecione **Rede (Networking)**. Observe que **Acesso à rede pública (Public network access)** está desabilitado.

    + Em Acesso à rede pública (Public network access), clique em **Gerenciar (Manage)** para abrir a lâmina de configuração Acesso à rede pública.

    + Defina **Acesso à rede pública (Public network access)** para **Habilitado (Enabled)**. Defina **Escopo de acesso à rede pública (Public network access scope)** para **Habilitar a partir de redes selecionadas (Enable from selected networks)**.

    + Na seção **Configurações de recurso: Redes virtuais, Endereços IP e exceções (Resource settings: Virtual networks, IP Addresses and exceptions)**, adicione o endereço IPv4 do seu cliente.

    + Salve suas alterações. Se um banner informativo aparecer sugerindo que você associe um perímetro de segurança de rede, você pode ignorá-lo e continuar.

1. Na lâmina **Gerenciamento de dados (Data management)**, selecione **Redundância (Redundancy)**. Observe as informações sobre as localizações do seu data center primário e secundário.

1. Na lâmina **Gerenciamento de dados (Data management)**, selecione **Gerenciamento de ciclo de vida (Lifecycle management)**, e então selecione **Adicionar uma regra (Add a rule)**.

    + Nomeie a regra `Movetocool`. Observe suas opções para limitar o escopo da regra. Clique em **Avançar (Next)**.

    + Na página **Adicionar regra (Add rule)**, *se* os base blobs foram modificados pela última vez há mais de `30` dias *então* **Mover para armazenamento frio (Move to cool storage)**. Observe suas outras escolhas.

    + Observe que você pode configurar outras condições. Selecione **Adicionar (Add)** quando terminar de explorar.

    ![Screenshot move to cool rule conditions.](../media/az104-lab07-movetocool.png)

## Tarefa 2: Criar e configurar blob storage seguro

Nesta tarefa, você criará um container de blob e fará upload de uma imagem. Blob containers são estruturas semelhantes a diretórios que armazenam dados não estruturados.

### Criar um container de blob e uma política de retenção baseada em tempo

1. Continue no Azure portal, trabalhando com sua storage account.

1. Na lâmina **Armazenamento de dados (Data storage)**, selecione **Contêineres (Containers)**.

1. Clique em **+ Adicionar contêiner (+ Add container)** e **Criar (Create)** um contêiner com as seguintes configurações:

    | Configuração | Valor |
    | --- | --- |
    | Nome | `data` |
    | Nível de acesso público | Observe que o nível de acesso está definido como privado |

    ![Screenshot of create a container.](../media/az104-lab07-create-container.png)

1. No seu contêiner, role até o espaçador de reticências (...) à extrema direita, selecione **Política de acesso (Access policy)**.

1. Se um aviso aparecer afirmando que a autorização com Shared Key está desabilitada para a conta, você pode ignorá-lo e continuar.

1. Na área **Armazenamento imutável de blobs (Immutable blob storage)**, selecione **Adicionar política (Add policy)**, altere o tipo de **Bloqueio legal (Legal hold)** para **Retenção por tempo (Time-based retention)**.

    | Configuração | Valor |
    | --- | --- |
    | Tipo de política | **Retenção por tempo (Time-based retention)** |
    | Definir período de retenção por | `180` dias |

1. Selecione **Salvar (Save)**.

### Gerenciar uploads de blob

1. No menu à esquerda da storage account, em **Configurações (Settings)**, selecione **Configuração (Configuration)** e configure **Permitir acesso por chave da conta de armazenamento (Allow storage account key access)** para **Habilitado (Enabled)**, em seguida clique **Salvar (Save)**.

1. Em seguida, navegue até **Controle de Acesso (IAM) (Access Control (IAM))**, clique **Adicionar atribuição de função (Add role assignment)**, selecione a função **Colaborador de Dados do Storage Blob**, e atribua-a à sua conta de usuário, então clique **Revisar + atribuir (Review + assign)**.

1. Em seguida, faça os mesmos passos do passo anterior para atribuir a função **Storage File Data Privileged Contributor**.

1. Depois que o acesso estiver configurado, selecione seu contêiner **data** e então clique em **Carregar (Upload)**.

1. Na lâmina **Carregar blob (Upload blob)**, expanda a seção **Avançado (Advanced)**.

    >**Observação**: Localize um arquivo para fazer upload. Pode ser qualquer tipo de arquivo, mas um arquivo pequeno é melhor. Um arquivo de exemplo pode ser baixado do diretório AllFiles.

    | Configuração | Valor |
    | --- | --- |
    | Procurar arquivos | adicione o arquivo que você selecionou para upload |
    | Selecione **Avançado (Advanced)** | |
    | Tipo de blob | **Block blob** |
    | Tamanho do bloco | **4 MiB** |
    | Camada de acesso | **Hot** (observe as outras opções) |
    | Enviar para pasta (Upload to folder) | `securitytest` |
    | Escopo de criptografia | Usar escopo de contêiner padrão existente |

1. Clique **Carregar (Upload)**.

1. Confirme que você tem uma nova pasta, e que seu arquivo foi enviado.

1. Selecione o arquivo enviado e revise as opções nas reticências (...) incluindo **Download**, **Excluir (Delete)**, **Alterar camada (Change tier)**, e **Adquirir lease (Acquire lease)**.

1. Selecione o arquivo enviado para abrir seu painel Visão geral (Overview), então copie a URL usando o botão **Copiar para a área de transferência (Copy to clipboard)** na tabela Propriedades (Properties). Cole a URL em uma nova janela do navegador InPrivate.

1. Você deverá ver uma mensagem em formato XML informando **ResourceNotFound** ou **PublicAccessNotPermitted**.

    > **Observação**: Isso é esperado, já que o contêiner que você criou tem o nível de acesso público definido como **Private (no anonymous access)**.

### Configurar acesso limitado ao blob storage

1. Navegue de volta ao arquivo que você enviou e selecione as reticências (…) à extrema direita, então selecione **Gerar SAS (Generate SAS)**.

1. Observe o banner de aviso indicando que a autorização com Shared Key está desabilitada para esta conta — isso significa que a opção Account key signing estará indisponível.

1. Especifique as seguintes configurações (deixe as demais com seus valores padrão): Navegue de volta ao arquivo que você enviou e selecione as reticências (…) à extrema direita, então selecione **Gerar SAS (Generate SAS)** e especifique as seguintes configurações (deixe as demais com seus valores padrão):

    | Configuração | Valor |
    | --- | --- |
    | Método de assinatura | **User delegation key** |
    | Permissões | **Read** (observe suas outras escolhas) |
    | Data de início | data de ontem |
    | Hora de início | hora atual |
    | Data de expiração | data de amanhã |
    | Hora de expiração | hora atual |
    | Endereços IP permitidos | deixar em branco |

1. Clique **Gerar token SAS e URL (Generate SAS token and URL)**.

1. Copie a entrada **Blob SAS URL** para a área de transferência.

1. Abra outra janela do navegador InPrivate e navegue até a Blob SAS URL que você copiou no passo anterior.

    >**Observação**: Você deverá conseguir visualizar o conteúdo do arquivo.

## Tarefa 3: Criar e configurar o Azure File Storage

Nesta tarefa, você criará e configurará Azure File shares. Você usará o Storage Browser para gerenciar o file share.

### Criar o file share e enviar um arquivo

1. No Azure portal, navegue de volta para sua storage account, na lâmina **Armazenamento de dados (Data storage)**, clique **Compartilhamentos de arquivos clássicos (Classic file shares)**.

1. Clique **+ Compartilhamento de arquivo clássico (+ Classic file share)** e na guia **Básico (Basics)** dê ao file share um nome, `share1`.

1. Observe as opções de **Camada de acesso (Access tier)**. Mantenha o padrão **Transaction optimized**.

1. Vá para a guia **Backup (Backup)** e assegure-se de que **Habilitar backup (Enable backup)** **não** esteja marcado. Estamos desabilitando o backup para simplificar a configuração do laboratório.

1. Clique **Revisar + criar (Review + create)**, e então **Criar (Create)**. Aguarde o file share ser implantado.

1. Depois que o file share for criado, um banner informativo aparecerá solicitando que você habilite o backup — você pode ignorar esta mensagem e continuar.

    ![Screenshot of the create file share page.](../media/az104-lab07-create-share.png)

### Explore o Storage Browser e envie um arquivo

1. Retorne à sua storage account e selecione **Explorador de Armazenamento (Storage browser)**. O Azure Storage Browser é uma ferramenta do portal que permite visualizar rapidamente todos os serviços de armazenamento sob sua conta.

1. Selecione **Compartilhamentos de arquivos clássicos (Classic file shares)** e verifique se seu diretório **share1** está presente.

1. Selecione seu diretório **share1** e observe que você pode **+ Adicionar diretório (+ Add directory)**. Isso permite criar uma estrutura de pastas.

1. Se você vir um erro de autorização, selecione **Alternar conta do Azure AD (Switch Azure AD Account)** (ou altere o método de autenticação para **conta de usuário do Microsoft Entra (Microsoft Entra user account)**) na barra de ferramentas do Explorador de Armazenamento.

1. Selecione **Carregar (Upload)**. Navegue até um arquivo de sua escolha e então clique **Carregar (Upload)**.

    >**Observação**: Você pode visualizar file shares e gerenciá-los no Explorador de Armazenamento. Atualmente não há restrições.

### Restringir o acesso de rede à storage account

1. No portal, pesquise e selecione **Fundação de rede (Network foundation)**.

1. Em `Virtual networks` clique **Criar (Create)**. Na guia **Básicos (Basics)**, defina **Grupo de recursos (Resource group)** como `az104-rg7` e dê à virtual network um **nome (name)**, `vnet1`.

1. Aceite os padrões para os demais parâmetros, selecione **Revisar + criar (Review + create)**, e então **Criar (Create)**.

1. Aguarde a virtual network ser implantada, e então selecione **Ir para o recurso (Go to resource)**.

1. Na seção **Configurações (Settings)**, selecione a lâmina **Endpoints de serviço (Service endpoints)**.
    + Selecione **Adicionar (Add)**.
    + No drop-down **Serviço (Service)** selecione **Microsoft.Storage**.
    + Deixe o drop-down **Políticas de endpoint de serviço (Service endpoint policies)** em seu padrão de **0 selecionados (0 selected)**.
    + No drop-down **Sub-redes (Subnets)** marque a sub-rede **Default**.
    + Clique **Adicionar (Add)** para salvar suas alterações.

1. Retorne para sua storage account.

1. Na lâmina **Segurança + rede (Security + networking)**, selecione **Rede (Networking)**.

1. Em **Acesso à rede pública (Public network access)** selecione **Gerenciar (Manage)**.

1. Selecione **Adicionar uma rede virtual (Add a virtual network)** e então **Adicionar rede existente (Add existing network)**.

1. Selecione **vnet1** e a sub-rede **default**, selecione **Adicionar (Add)**.

1. Na seção **Endereços IPv4 (IPv4 Addresses)**, **Excluir (Delete)** o endereço IP da sua máquina. O tráfego permitido deve vir apenas da virtual network.

1. Certifique-se de **Salvar (Save)** suas alterações.

    >**Observação:** A storage account agora deve ser acessada apenas a partir da virtual network que você acabou de criar.

1. Selecione o **Explorador de Armazenamento (Storage browser)** e **Atualizar (Refresh)** a página. Navegue até seu file share ou conteúdo de blob.

    >**Observação:** Você deverá receber uma mensagem *não autorizado a executar esta operação (not authorized to perform this operation)*. Você não está se conectando a partir da virtual network. Pode levar alguns minutos para que isso entre em vigor. Você ainda pode conseguir visualizar o file share, mas não os arquivos ou blobs na storage account.

![Captura de tela de acesso não autorizado.](../media/az104-lab07-notauthorized.png)

## Limpeza dos recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que o custo seja minimizado. A maneira mais fácil de excluir os recursos do laboratório é excluir o grupo de recursos do laboratório.

+ No Azure portal, selecione o grupo de recursos, selecione **Excluir o grupo de recursos (Delete the resource group)**, **Digite o nome do grupo de recursos (Enter resource group name)**, e então clique em **Excluir (Delete)**. Quando o segundo diálogo de confirmação aparecer afirmando que excluir o grupo de recursos e seus recursos dependentes é uma ação permanente, clique **Excluir (Delete)** novamente para completar a exclusão.
+ Usando o Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Amplie seu aprendizado com o Copilot
Copilot pode ajudá-lo a aprender como usar as ferramentas de script do Azure. Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precisar de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.

+ Forneça um script Azure PowerShell para criar uma storage account com um container de blob.
+ Forneça uma checklist que eu possa usar para garantir que minha conta de armazenamento do Azure esteja segura.
+ Crie uma tabela para comparar os modelos de redundância do Azure Storage.

## Aprenda mais com treinamento auto-guiado

+ [Guided Project - Azure Files and Azure Blobs](https://learn.microsoft.com/training/modules/guided-project-azure-files-azure-blobs/). Pratique armazenar dados de negócios com segurança usando Azure Blob Storage e Azure Files.
+ [Create an Azure Storage account](https://learn.microsoft.com/training/modules/create-azure-storage-account/). Crie uma Azure Storage account com as opções corretas para as necessidades do seu negócio.
+ [Manage the Azure Blob storage lifecycle](https://learn.microsoft.com/training/modules/manage-azure-blob-storage-lifecycle). Aprenda a gerenciar a disponibilidade de dados ao longo do ciclo de vida do Azure Blob storage.

## Principais aprendizados

Parabéns por completar o laboratório. Aqui estão os principais aprendizados deste laboratório.

+ Uma Azure storage account contém todos os seus objetos de Azure Storage: blobs, files, queues e tables. A storage account fornece um namespace único para seus dados do Azure Storage que é acessível de qualquer lugar do mundo via HTTP ou HTTPS.
+ O Azure Storage fornece vários modelos de redundância incluindo Locally redundant storage (LRS), Zone-redundant storage (ZRS) e Geo-redundant storage (GRS).
+ O Azure Blob Storage permite que você armazene grandes quantidades de dados não estruturados na plataforma de armazenamento da Microsoft. Blob significa Binary Large Object, o que inclui objetos como imagens e arquivos multimídia.
+ O Azure File Storage fornece armazenamento compartilhado para dados estruturados. Os dados podem ser organizados em pastas.
+ Immutable storage fornece a capacidade de armazenar dados em um estado write once, read many (WORM). As políticas de immutable storage podem ser baseadas em tempo ou legal-hold.
