---
lab:
  title: 'Lab 03: Gerenciar recursos do Azure usando Azure Resource Manager Templates'
  module: Administrar recursos do Azure
  description: Implantar recursos do Azure usando templates.
  duration: 50 minutes
  level: 400
  islab: true
  primarytopics:
    - Azure
    - Azure Resource Manager
    - Azure JSON templates
layout: default
---

# Laboratório 03 - Gerenciar recursos do Azure usando Azure Resource Manager Templates

## Introdução ao laboratório

Neste laboratório, você aprenderá como automatizar implantações de recursos. Você conhecerá templates do Azure Resource Manager e templates Bicep. Você aprenderá sobre as diferentes formas de implantar os templates.

Este laboratório requer uma assinatura do Azure. Seu tipo de assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua equipe quer analisar maneiras de automatizar e simplificar implantações de recursos. Sua organização está procurando formas de reduzir a sobrecarga administrativa, diminuir erros humanos e aumentar a consistência.

## Diagrama de arquitetura

![Diagrama das tarefas.](../media/az104-lab03-architecture.png)

## Habilidades da atividade

+ Tarefa 1: Criar um template do Azure Resource Manager.
+ Tarefa 2: Editar um template do Azure Resource Manager e reimplantar o template.
+ Tarefa 3: Configurar o Cloud Shell e implantar um template com Azure PowerShell.
+ Tarefa 4: Implantar um template com o Azure CLI.
+ Tarefa 5: Implantar um recurso usando Azure Bicep.

## Tarefa 1: Criar um template do Azure Resource Manager

Nesta tarefa, vamos criar um disco gerenciado (managed disk) no Azure portal. Managed disks são storages projetados para serem usados com máquinas virtuais (virtual machines). Depois que o disco for implantado, você exportará um modelo que pode ser usado em outras implantações.

1. Faça login no **Azure portal** - `https://portal.azure.com`.

1. Pesquise por e selecione `Disks`.

1. Na página **Centro de Armazenamento | Azure Disks (Storage Center | Azure Disks)**, selecione a aba **Recursos (Resources)** e então selecione **Criar (Create)**.

1. Na página **Criar um disco gerenciado (Create a managed disk)**, configure o disco e então selecione **Ok**.

    | Configuração | Valor |
    | --- | --- |
    | Assinatura | *sua assinatura* |
    | Grupo de recursos | `az104-rg3` (Se necessário, selecione **Criar novo (Create new)**.) |
    | Nome do disco | `az104-disk1` |
    | Região | **East US** |
    | Zona de disponibilidade | **Nenhuma redundância de infraestrutura necessária** |
    | Tipo de origem | **Nenhuma** |
    | Desempenho | **Standard HDD** (alterar tamanho) |
    | Tamanho | **32 Gib** |

    >**Observação:** Estamos criando um managed disk simples para que você possa praticar com modelos. Azure managed disks são volumes de armazenamento em nível de bloco que são gerenciados pelo Azure.

    >**Observação:** Se a implantação falhar devido a limites de capacidade ou cota, ajuste a configuração ou escolha uma região diferente.

1. Clique em **Revisar + Criar (Review + Create)** e depois selecione **Criar (Create)**.

1. Monitore as notificações (canto superior direito) e após a implantação selecione **Ir para o recurso (Go to resource)**.

1. No painel **Automação (Automation)**, selecione **Exportar modelo (Export template)**.

1. Reserve um minuto para revisar os arquivos **Modelo (Template)** e **Parâmetros (Parameters)**.

1. Na seção **Modelo (Template)**, clique em **Baixar (Download)** e salve o template no disco local. Em seguida, alterne para a seção **Parâmetros (Parameters)** e faça o mesmo.

1. Use o Explorador de Arquivos para abrir a pasta **Downloads** no seu computador. Observe que existem dois arquivos JSON (template e parameters).

   >**Você sabia?**  Você pode exportar um grupo de recursos (resource group) inteiro ou apenas recursos específicos dentro desse grupo.

## Tarefa 2: Editar um template do Azure Resource Manager e depois reimplantar o template

Nesta tarefa, você usará o template baixado para implantar um novo managed disk. Esta tarefa descreve como repetir implantações de forma rápida e fácil.

1. No Azure portal, pesquise por e selecione `Deploy a custom template`.

1. No painel **Implantação personalizada (Custom deployment)**, observe que há a opção de usar um **Template de início rápido (Quickstart template)**. Existem muitos templates integrados conforme mostrado no menu suspenso.

1. Em vez de usar um Quickstart, selecione **Criar seu próprio template no editor (Build your own template in the editor)**.

1. No painel **Editar template (Edit template)**, clique em **Carregar arquivo (Load file)** e faça upload do arquivo **template.json** que você baixou para o disco local.

1. No painel do editor, faça as seguintes alterações.

    + Altere **disks_az104_disk1_name** para `disk_name` (duas ocorrências para alterar)
    + Altere **az104-disk1** para `az104-disk2` (uma ocorrência para alterar)

1. Observe que este é um disco **Standard**. A localização é **eastus**. O tamanho do disco é **32GB**.

1. **Salvar (Save)** suas alterações.

1. Não esqueça do arquivo de parâmetros. Selecione **Editar parâmetros (Edit parameters)**, clique em **Carregar arquivo (Load file)** e faça upload do **parameters.json**.

1. Faça esta alteração para que corresponda ao arquivo modelo.

    Altere **disks_az104_disk1_name** para **disk_name** (uma ocorrência para alterar)

1. **Salvar (Save)** suas alterações.

1. Complete as configurações de implantação personalizada:

    | Configuração | Valor |
    | --- |--- |
    | Assinatura | *sua assinatura* |
    | Grupo de recursos | `az104-rg3` |
    | Região | **East US** |
    | Nome_do_disco | `az104-disk2` |

1. Selecione **Revisar + Criar (Review + Create)** e depois selecione **Criar (Create)**.

1. Selecione **Ir para o recurso (Go to resource)**. Verifique que **az104-disk2** foi criado.

1. No painel **Visão Geral (Overview)**, selecione o grupo de recursos, **az104-rg3**. Você deve ter agora dois discos.

1. Na seção **Configurações (Settings)**, clique em **Implantações (Deployments)**.

    >**Observação:** Todos os detalhes das implantações são documentados no grupo de recursos (resource group). É uma boa prática revisar as primeiras implantações baseadas em modelo para garantir sucesso antes de usar os modelos em operações de grande escala.

1. Selecione uma implantação e revise o conteúdo dos painéis **Entrada (Input)** e **Modelo (Template)**.

## Tarefa 3: Configurar o Cloud Shell e implantar um template com PowerShell

Nesta tarefa, você trabalhará com o Cloud Shell e Azure PowerShell. Cloud Shell é um terminal interativo, autenticado e acessível pelo navegador para gerenciar recursos do Azure. Ele fornece a flexibilidade de escolher a experiência de shell que melhor se adapta à forma como você trabalha, seja Bash ou PowerShell. Nesta tarefa, você usará PowerShell para implantar um modelo.

1. Selecione o ícone **Cloud Shell** no canto superior direito do Azure Portal. Alternativamente, você pode navegar diretamente para `https://shell.azure.com`.

   ![Captura de tela do ícone do Cloud Shell.](../media/az104-lab03-cloudshell-icon.png)

1. Quando for solicitado a selecionar **Bash** ou **PowerShell**, selecione **PowerShell**. Se o Cloud Shell carregar totalmente no modo Bash, selecione **Alternar para PowerShell (Switch to PowerShell)**.

    >**Você sabia?**  Se você trabalha majoritariamente com sistemas Linux, o Bash (CLI) tende a ser mais familiar. Se você trabalha majoritariamente com sistemas Windows, Azure PowerShell tende a ser mais familiar.

1. O Cloud Shell pode iniciar diretamente em uma sessão PowerShell efêmera. Nenhuma configuração de storage account é necessária.

    >**Observação:** Aguarde o prompt do PowerShell aparecer antes de prosseguir.

1. Selecione o ícone **Gerenciar arquivos** (barra superior) e então selecione **Carregar (Upload)**.

1. Faça upload dos arquivos de modelo e parâmetros da pasta **Downloads**.

1. Selecione o ícone **Editor (lápis)** e navegue até o arquivo JSON do modelo à esquerda no painel de navegação.

1. Faça uma alteração. Por exemplo, altere o nome do disco para **az104-disk3**. Use **Ctrl+S** para salvar suas alterações, depois **Ctrl+Q** para fechar o editor.

    >**Observação**: Você pode direcionar a implantação do modelo para um grupo de recursos (resource group), assinatura, management group ou tenant. Dependendo do escopo da implantação, você usa comandos diferentes.

1. Se você tiver múltiplas assinaturas, primeiro garanta que o PowerShell esteja usando o contexto da assinatura correto. Execute os seguintes comandos e, se necessário, substitua `<your-subscription-id>` pela assinatura que contém **az104-rg3**.

    ```powershell
    Get-AzContext
    Set-AzContext -Subscription <your-subscription-id>
    ```

    >**Observação:** Isso é especialmente importante quando o Cloud Shell está em modo efêmero, porque a assinatura ativa pode diferir do contexto do Azure portal.

1. Para implantar em um grupo de recursos, use **New-AzResourceGroupDeployment**.

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName az104-rg3 -TemplateFile template.json -TemplateParameterFile parameters.json
    ```
1. Garanta que o comando seja concluído e que o ProvisioningState esteja **Succeeded**.

1. Confirme que o disco foi criado.

   ```powershell
   Get-AzDisk | ft Name,ResourceGroupName,Location,DiskSizeGb,ProvisioningState
   ```

## Tarefa 4: Implantar um template com o CLI

1. Continue no **Cloud Shell** e selecione **Alternar para Bash (Switch to Bash)**. **Confirme** sua escolha.

1. Se você tiver múltiplas assinaturas, primeiro garanta que o contexto da assinatura correto esteja definido executando `az account show` para confirmar a assinatura ativa. Se não corresponder à assinatura que contém **az104-rg3**, execute `az account set --subscription <your-subscription-id>` antes de prosseguir. Isso é especialmente importante quando o Cloud Shell está em modo efêmero, pois alternar de PowerShell para Bash pode redefinir o contexto da assinatura.

    ```sh
    az account show
    az account set --subscription <your-subscription-id>
    ```

1. Verifique se seus arquivos estão disponíveis no armazenamento do Cloud Shell. Se você concluiu a tarefa anterior, seus arquivos de modelo devem estar disponíveis.

    ```sh
    ls
    ```

1. Selecione o **Editor (lápis)** e navegue até o arquivo JSON do modelo.

1. Faça uma alteração. Por exemplo, altere o nome do disco para **az104-disk4**. Use **Ctrl+S** para salvar suas alterações, depois **Ctrl+Q** para fechar o editor.

    >**Observação**: Você pode direcionar a implantação do modelo para um grupo de recursos (resource group), assinatura, management group ou tenant. Dependendo do escopo da implantação, você usa comandos diferentes.

1. Para implantar em um grupo de recursos, use **az deployment group create**.

    ```sh
    az deployment group create --resource-group az104-rg3 --template-file template.json --parameters parameters.json
    ```

1. Garanta que o comando seja concluído e que o ProvisioningState esteja **Succeeded**.

1. Confirme que o disco foi criado.

     ```sh
     az disk list --resource-group az104-rg3 --output table
     ```

## Tarefa 5: Implantar um recurso usando Azure Bicep

Nesta tarefa, você usará um arquivo Bicep para implantar um managed disk. Bicep é uma ferramenta declarativa de automação construída sobre ARM templates.

1. Localize o arquivo **\Allfiles\Lab03\azuredeploydisk.bicep**.

1. Continue trabalhando no **Cloud Shell** em uma sessão **Bash**.

1. Selecione **Gerenciar arquivos** e então **Carregar (Upload)** o arquivo Bicep para o Cloud Shell.

1. Selecione **Abrir editor (Open editor)**.

1. Selecione o arquivo **azuredeploydisk.bicep**.

1. Reserve um minuto para ler o arquivo de modelo Bicep. Observe como o recurso disk é definido.

1. Faça as seguintes alterações:

    + Altere o valor de **managedDiskName**, linha 2, para **az104-disk5**.
    + Altere o valor de **diskSizeinGiB**, linha 7, para **32**.
    + Altere o valor de **sku name**, linha 26, para **StandardSSD_LRS**.

1. Use **Ctrl+S** para salvar suas alterações, depois **Ctrl+Q** para fechar o editor.

1. Agora, implante o modelo.

    ```
    az deployment group create --resource-group az104-rg3 --template-file azuredeploydisk.bicep
    ```

1. Confirme que o disco foi criado.

    ```sh
    az disk list --resource-group az104-rg3 --output table
    ```

    >**Observação:** Você implantou com sucesso cinco managed disks, cada um de uma forma diferente. Bom trabalho!

## Limpeza dos seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que os custos sejam minimizados. A maneira mais fácil de excluir os recursos do laboratório é excluir o grupo de recursos do laboratório.

+ No Azure portal, selecione o grupo de recursos, selecione **Excluir o grupo de recursos (Delete the resource group)**, **Digite o nome do grupo de recursos (Enter the resource group name)**, e então clique em **Excluir (Delete)**. Quando a caixa de diálogo de confirmação aparecer informando que excluir o grupo de recursos é permanente e não pode ser desfeito, clique em **Excluir (Delete)** novamente para completar a exclusão.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Expanda seu aprendizado com o Copilot

Copilot pode ajudá-lo a aprender como usar as ferramentas de script do Azure. Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precisar de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue para *copilot.microsoft.com*. Reserve alguns minutos para testar estes prompts.

+ Qual é o formato do arquivo de modelo do Azure Resource Manager? Explique cada componente com exemplos.
+ Como eu uso um modelo existente do Azure Resource Manager?
+ Compare e contraste modelos do Azure Resource Manager e templates do Azure Bicep.

## Aprenda mais com treinamento autodirigido

+ [Implantar infraestrutura do Azure usando templates JSON](https://learn.microsoft.com/training/modules/create-azure-resource-manager-template-vs-code/). Escreva templates JSON do Azure Resource Manager (ARM templates) usando Visual Studio Code para implantar sua infraestrutura no Azure de forma consistente e confiável.
+ [Revise os recursos e ferramentas do Cloud Shell](https://learn.microsoft.com/training/modules/review-features-tools-for-azure-cloud-shell/). Recursos e ferramentas do Cloud Shell.
+ [Criar recursos do Azure usando Azure CLI](https://learn.microsoft.com/training/modules/create-azure-resources-by-using-azure-cli/). Aprenda a instalar o Azure CLI no Windows, Linux e macOS, executar comandos interativamente, criar scripts de automação Bash e solucionar problemas comuns.
+ [Construa seu primeiro template Bicep](https://learn.microsoft.com/training/modules/build-first-bicep-template/). Defina recursos do Azure dentro de um template Bicep. Melhore a consistência e confiabilidade de suas implantações, reduza o esforço manual necessário e escale suas implantações entre ambientes. Seu template será flexível e reutilizável usando parâmetros, variáveis, expressões e módulos.

## Principais conclusões

Parabéns por completar o laboratório. Aqui estão os principais pontos deste laboratório.

+ Templates do Azure Resource Manager permitem implantar, gerenciar e monitorar todos os recursos da sua solução como um grupo, em vez de tratar esses recursos individualmente.
+ Um template do Azure Resource Manager é um arquivo JavaScript Object Notation (JSON) que permite gerenciar sua infraestrutura de forma declarativa em vez de scripts.
+ Em vez de passar parâmetros como valores inline em seu template, você pode usar um arquivo JSON separado que contenha os valores dos parâmetros.
+ Templates do Azure Resource Manager podem ser implantados de várias maneiras, incluindo o Azure portal, Azure PowerShell e Azure CLI.
+ Bicep é uma alternativa aos templates do Azure Resource Manager. Bicep usa uma sintaxe declarativa para implantar recursos do Azure.
+ Bicep fornece sintaxe concisa, segurança de tipo confiável e suporte para reutilização de código. Bicep oferece uma experiência de autoria de primeira classe para suas soluções de infrastructure as code (infraestrutura como código) no Azure.
