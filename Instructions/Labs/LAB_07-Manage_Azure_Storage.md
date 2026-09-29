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

+ Task 1: Create and configure a storage account.
+ Task 2: Create and configure secure blob storage.
+ Task 3: Create and configure secure Azure file storage.

## Task 1: Create and configure a storage account.

Nesta tarefa, você criará e configurará uma storage account. A storage account usará geo-redundant storage e não terá acesso público.

1. Faça logon no **Azure portal** - `https://portal.azure.com`.

1. Pesquise e selecione `Storage accounts`, selecione **Storage accounts** nos resultados e então clique em **+ Create**.

1. Na guia **Basics** da lâmina **Create a storage account**, especifique as seguintes configurações (deixe as demais com os valores padrão):

    | Setting | Value |
    | --- | --- |
    | Subscription          | the name of your Azure subscription  |
    | Resource group        | **az104-rg7** (create new) |
    | Storage account name  | any globally unique name between 3 and 24 in length consisting of letters and digits |
    | Region                | **(US) East US**  |
    | Performance           | **Standard** (notice the Premium option) |
    | Preferred storage type | **Azure Blob Storage or Azure Data Lake Storage** |
    | Redundancy            | **Geo-redundant storage** (notice the other options)|
    | Make read access to data available in the event of regional unavailability. | Check the box |

    >**Did you know?** Você deve usar a camada de desempenho Standard para a maioria das aplicações. Use a camada Premium para aplicações corporativas ou de alto desempenho.

1. Nas guias **Advanced** e **Security**, use os ícones informativos para saber mais sobre as opções. Aceite os padrões.

1. Na guia **Networking**, na seção **Public network access**, selecione **Disable**. Isso restringirá o acesso de entrada enquanto permite o acesso de saída.

1. Revise a guia **Data protection**. Observe que 7 dias é a política de retenção de soft delete padrão. Observe que você pode habilitar versioning para blobs. Aceite os padrões.

1. Revise a guia **Encryption**. Observe as opções adicionais de segurança. Aceite os padrões.

1. Selecione **Review + create**, aguarde o processo de validação terminar e então clique em **Create**.

1. Depois que a storage account for implantada, selecione **Go to resource**.

1. Revise a lâmina **Overview** e as configurações adicionais que podem ser alteradas. Essas são configurações globais para a storage account. Observe que a storage account pode ser usada para Blob containers, File shares, Queues e Tables.

1. Na lâmina **Security + networking**, selecione **Networking**. Observe que **Public network access** está disabled.

    + Em Public network access, clique **Manage** para abrir a lâmina de configuração Public network access.

    + Defina **Public network access** para **Enabled**. Defina **Public network access scope** para **Enable from selected networks**.

    + Na seção **Resource settings: Virtual networks, IP Addresses and exceptions**, Adicione o endereço IPv4 do seu cliente.

    + Salve suas alterações. Se um banner informativo aparecer sugerindo que você associe um perímetro de segurança de rede, você pode ignorá-lo e continuar.

1. Na lâmina **Data management**, selecione **Redundancy**. Observe as informações sobre as localizações do seu data center primário e secundário.

1. Na lâmina **Data management**, selecione **Lifecycle management**, e então selecione **Add a rule**.

    + Nomeie a regra `Movetocool`. Observe suas opções para limitar o escopo da regra. Clique **Next**.

    + Na página **Add rule**, *se* os base blobs foram modificados pela última vez há mais de `30` dias *então* **Move to cool storage**. Observe suas outras escolhas.

    + Observe que você pode configurar outras condições. Selecione **Add** quando terminar de explorar.

    ![Screenshot move to cool rule conditions.](../media/az104-lab07-movetocool.png)

## Task 2: Create and configure secure blob storage

Nesta tarefa, você criará um container de blob e fará upload de uma imagem. Blob containers são estruturas semelhantes a diretórios que armazenam dados não estruturados.

### Create a blob container and a time-based retention policy

1. Continue no Azure portal, trabalhando com sua storage account.

1. Na lâmina **Data storage**, selecione **Containers**.

1. Clique **+ Add container** e **Create** um container com as seguintes configurações:

    | Setting | Value |
    | --- | --- |
    | Name | `data`  |
    | Public access level | Notice the access level is set to private |

    ![Screenshot of create a container.](../media/az104-lab07-create-container.png)

1. No seu container, role até o espaçador de reticências (...) à extrema direita, selecione **Access policy**.

1. Se um aviso aparecer afirmando que a autorização com Shared Key está desabilitada para a conta, você pode ignorá-lo e continuar.

1. Na área **Immutable blob storage**, selecione **Add policy**, altere o tipo de **Legal hold** para **Time-based retention**.

    | Setting | Value |
    | --- | --- |
    | Policy type | **Time-based retention**  |
    | Set retention period for | `180` days |

1. Selecione **Save**.

### Gerenciar uploads de blob

1. No menu à esquerda da storage account, em **Settings**, selecione **Configuration** e configure **Allow storage account key access** para **Enabled**, em seguida clique **Save**.

1. Em seguida, navegue até **Access Control (IAM)**, clique **Add role assignment**, selecione a role **Storage Blob Data Contributor**, e atribua-a à sua conta de usuário, então clique **Review + assign**.

1. Em seguida, faça os mesmos passos do passo anterior para atribuir a role **Storage File Data Privileged Contributor**.

1. Depois que o acesso estiver configurado, selecione seu container **data** e então clique **Upload**.

1. Na lâmina **Upload blob**, expanda a seção **Advanced**.

    >**Note**: Localize um arquivo para fazer upload. Pode ser qualquer tipo de arquivo, mas um arquivo pequeno é melhor. Um arquivo de exemplo pode ser baixado do diretório AllFiles.

    | Setting | Value |
    | --- | --- |
    | Browse for files | add the file you have selected to upload |
    | Select **Advanced** | |
    | Blob type | **Block blob** |
    | Block size | **4 MiB** |
    | Access tier | **Hot**  (notice the other options) |
    | Upload to folder | `securitytest` |
    | Encryption scope | Use existing default container scope |

1. Clique **Upload**.

1. Confirme que você tem uma nova pasta, e que seu arquivo foi enviado.

1. Selecione o arquivo enviado e revise as opções nas reticências (...) incluindo **Download**, **Delete**, **Change tier**, e **Acquire lease**.

1. Selecione o arquivo enviado para abrir seu painel Overview, então copie a URL usando o botão **Copy to clipboard** na tabela Properties. Cole a URL em uma nova janela do navegador **InPrivate**.

1. Você deverá ver uma mensagem em formato XML informando **ResourceNotFound** ou **PublicAccessNotPermitted**.

    > **Note**: Isso é esperado, já que o container que você criou tem o nível de acesso público definido como **Private (no anonymous access)**.

### Configurar acesso limitado ao blob storage

1. Navegue de volta ao arquivo que você enviou e selecione as reticências (…) à extrema direita, então selecione **Generate SAS**.

1. Observe o banner de aviso indicando que a autorização com Shared Key está desabilitada para esta conta — isso significa que a opção Account key signing estará indisponível.

1. Especifique as seguintes configurações (deixe as demais com seus valores padrão): Navegue de volta ao arquivo que você enviou e selecione as reticências (…) à extrema direita, então selecione **Generate SAS** e especifique as seguintes configurações (deixe as demais com seus valores padrão):

    | Setting | Value |
    | --- | --- |
    | Signing method | **User delegation key** |
    | Permissions | **Read** (notice your other choices) |
    | Start date | yesterday's date |
    | Start time | current time |
    | Expiry date | tomorrow's date |
    | Expiry time | current time |
    | Allowed IP addresses | leave blank |

1. Clique **Generate SAS token and URL**.

1. Copie a entrada **Blob SAS URL** para a área de transferência.

1. Abra outra janela do navegador InPrivate e navegue até a Blob SAS URL que você copiou no passo anterior.

    >**Note**: Você deverá conseguir visualizar o conteúdo do arquivo.

## Task 3: Create and configure an Azure File storage

Nesta tarefa, você criará e configurará Azure File shares. Você usará o Storage Browser para gerenciar o file share.

### Criar o file share e enviar um arquivo

1. No Azure portal, navegue de volta para sua storage account, na lâmina **Data storage**, clique **Classic file shares**.

1. Clique **+ Classic file share** e na guia **Basics** dê ao file share um nome, `share1`.

1. Observe as opções de **Access tier**. Mantenha o padrão **Transaction optimized**.

1. Vá para a guia **Backup** e assegure-se de que **Enable backup** **não** esteja marcado. Estamos desabilitando o backup para simplificar a configuração do laboratório.

1. Clique **Review + create**, e então **Create**. Aguarde o file share ser implantado.

1. Depois que o file share for criado, um banner informativo aparecerá solicitando que você habilite o backup — você pode ignorar esta mensagem e continuar.

    ![Screenshot of the create file share page.](../media/az104-lab07-create-share.png)

### Explore o Storage Browser e envie um arquivo

1. Retorne à sua storage account e selecione **Storage browser**. O Azure Storage Browser é uma ferramenta do portal que permite visualizar rapidamente todos os serviços de armazenamento sob sua conta.

1. Selecione **Clasic file shares** e verifique se seu diretório **share1** está presente.

1. Selecione seu diretório **share1** e observe que você pode **+ Add directory**. Isso permite criar uma estrutura de pastas.

1. Se você vir um erro de autorização, selecione **Switch Azure AD Account** (ou altere o método de autenticação para **Microsoft Entra user account**) na barra de ferramentas do Storage browser.

1. Selecione **Upload**. Navegue até um arquivo de sua escolha e então clique **Upload**.

    >**Note**: Você pode visualizar file shares e gerenciá-los no Storage Browser. Atualmente não há restrições.

### Restringir o acesso de rede à storage account

1. No portal, pesquise e selecione **Network foundation**.

1. Em `Virtual networks` clique **Create**. Na guia Basics, defina **Resource group** como `az104-rg7` e dê à virtual network um **name**, `vnet1`.

1. Aceite os padrões para os demais parâmetros, selecione **Review + create**, e então **Create**.

1. Aguarde a virtual network ser implantada, e então selecione **Go to resource**.

1. Na seção **Settings**, selecione a lâmina **Service endpoints**.
    + Selecione **Add**.
    + No drop-down **Service** selecione **Microsoft.Storage**.
    + Deixe o drop-down **Service endpoint policies** em seu padrão de **0 selected**.
    + No drop-down **Subnets** marque a subnet **Default**.
    + Clique **Add** para salvar suas alterações.

1. Retorne para sua storage account.

1. Na lâmina **Security + networking**, selecione **Networking**.

1. Em **Public network access** selecione **Manage**.

1. Selecione **Add a virtual network** e então **Add existing network**.

1. Selecione **vnet1** e a subnet **default**, selecione **Add**.

1. Na seção **IPv4 Addresses**, **Delete** o endereço IP da sua máquina. O tráfego permitido deve vir apenas da virtual network.

1. Certifique-se de **Save** suas alterações.

    >**Note:** A storage account agora deve ser acessada apenas a partir da virtual network que você acabou de criar.

1. Selecione o **Storage browser** e **Refresh** a página. Navegue até seu file share ou conteúdo de blob.

    >**Note:** Você deverá receber uma mensagem *not authorized to perform this operation*. Você não está se conectando a partir da virtual network. Pode levar alguns minutos para que isso entre em vigor. Você ainda pode conseguir visualizar o file share, mas não os arquivos ou blobs na storage account.


![Screenshot unauthorized access.](../media/az104-lab07-notauthorized.png)

## Limpeza dos recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que o custo seja minimizado. A maneira mais fácil de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ No Azure portal, selecione o resource group, selecione **Delete the resource group**, **Enter resource group name**, e então clique **Delete**. Quando o segundo diálogo de confirmação aparecer afirmando que excluir o resource group e seus recursos dependentes é uma ação permanente, clique **Delete** novamente para completar a exclusão.
+ Using Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Using the CLI, `az group delete --name resourceGroupName`.

## Amplie seu aprendizado com o Copilot
Copilot pode ajudá-lo a aprender como usar as ferramentas de script do Azure. Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precisar de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.

+ Provide an Azure PowerShell script to create a storage account with a blob container.
+ Provide a checklist I can use to ensure my Azure storage account is secure.
+ Create a table to compare Azure storage redundancy models.

## Aprenda mais com treinamento auto-guiado

+ [Guided Project - Azure Files and Azure Blobs](https://learn.microsoft.com/training/modules/guided-project-azure-files-azure-blobs/). Pratique armazenar dados de negócios com segurança usando Azure Blob Storage e Azure Files.
+ [Create an Azure Storage account](https://learn.microsoft.com/training/modules/create-azure-storage-account/). Crie uma Azure Storage account com as opções corretas para as necessidades do seu negócio.
+ [Manage the Azure Blob storage lifecycle](https://learn.microsoft.com/training/modules/manage-azure-blob-storage-lifecycle). Aprenda a gerenciar a disponibilidade de dados ao longo do ciclo de vida do Azure Blob storage.

## Principais aprendizados

Parabéns por completar o laboratório. Aqui estão os principais aprendizados deste laboratório.

+ Uma Azure storage account contém todos os seus objetos de Azure Storage: blobs, files, queues e tables. A storage account fornece um namespace único para seus dados do Azure Storage que é acessível de qualquer lugar do mundo via HTTP ou HTTPS.
+ Azure storage fornece vários modelos de redundância incluindo Locally redundant storage (LRS), Zone-redundant storage (ZRS), e Geo-redundant storage (GRS).
+ Azure blob storage permite que você armazene grandes quantidades de dados não estruturados na plataforma de armazenamento da Microsoft. Blob significa Binary Large Object, o que inclui objetos como imagens e arquivos multimídia.
+ Azure file Storage fornece armazenamento compartilhado para dados estruturados. Os dados podem ser organizados em pastas.
+ Immutable storage fornece a capacidade de armazenar dados em um estado write once, read many (WORM). As políticas de immutable storage podem ser baseadas em tempo ou legal-hold.
