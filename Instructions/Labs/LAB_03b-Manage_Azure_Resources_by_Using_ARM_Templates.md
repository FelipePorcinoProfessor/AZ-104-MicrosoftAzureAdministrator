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

Neste laboratório, você aprenderá como automatizar implantações de recursos. Você conhecerá Azure Resource Manager templates e templates Bicep. Você aprenderá sobre as diferentes formas de implantar os templates.

Este laboratório requer uma assinatura do Azure. Seu tipo de assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua equipe quer analisar maneiras de automatizar e simplificar implantações de recursos. Sua organização está procurando formas de reduzir a sobrecarga administrativa, diminuir erros humanos e aumentar a consistência.

## Diagrama de arquitetura

![Diagrama das tarefas.](../media/az104-lab03-architecture.png)

## Habilidades da atividade

+ Task 1: Create an Azure Resource Manager template.
+ Task 2: Edit an Azure Resource Manager template and redeploy the template.
+ Task 3: Configure the Cloud Shell and deploy a template with Azure PowerShell.
+ Task 4: Deploy a template with the CLI.
+ Task 5: Deploy a resource by using Azure Bicep.

## Task 1: Create an Azure Resource Manager template

Nesta tarefa, vamos criar um managed disk no Azure portal. Managed disks são storages projetados para serem usados com virtual machines. Depois que o disco for implantado, você exportará um template que pode ser usado em outras implantações.

1. Faça login no **Azure portal** - `https://portal.azure.com`.

1. Pesquise por e selecione `Disks`.

1. Na página **Storage Center | Azure Disks**, selecione a aba **Resources**, e então selecione **Create**.

1. Na página **Create a managed disk**, configure o disco e então selecione **Ok**.

    | Configuração | Valor |
    | --- | --- |
    | Subscription | *your subscription* |
    | Resource Group | `az104-rg3` (Se necessário, selecione **Create new**.)
    | Disk name | `az104-disk1` |
    | Region | **East US** |
    | Availability zone | **No infrastructure redundancy required** |
    | Source type | **None** |
    | Performance | **Standard HDD** (change size) |
    | Size | **32 Gib** |

    >**Observação:** Estamos criando um managed disk simples para que você possa praticar com templates. Azure managed disks são volumes de armazenamento em nível de bloco que são gerenciados pelo Azure.

    >**Observação:** Se a implantação falhar devido a limites de capacidade ou cota, ajuste a configuração ou escolha uma região diferente.

1. Clique em **Review + Create** e depois selecione **Create**.

1. Monitore as notificações (canto superior direito) e após a implantação selecione **Go to resource**.

1. No painel **Automation**, selecione **Export template**.

1. Reserve um minuto para revisar os arquivos **Template** e **Parameters**.

1. Na seção **Template**, clique em **Download** e salve o template no disco local. Em seguida, alterne para a seção **Parameters** e faça o mesmo.

1. Use o File Explorer para abrir a pasta **Downloads** no seu computador. Observe que existem dois arquivos JSON (template e parameters).

   >**Você sabia?**  Você pode exportar um resource group inteiro ou apenas recursos específicos dentro desse resource group.

## Task 2: Edit an Azure Resource Manager template and then redeploy the template

Nesta tarefa, você usará o template baixado para implantar um novo managed disk. Esta tarefa descreve como repetir implantações de forma rápida e fácil.

1. No Azure portal, pesquise por e selecione `Deploy a custom template`.

1. No painel **Custom deployment**, observe que há a opção de usar um **Quickstart template**. Existem muitos templates integrados conforme mostrado no menu suspenso.

1. Em vez de usar um Quickstart, selecione **Build your own template in the editor**.

1. No painel **Edit template**, clique em **Load file** e faça upload do arquivo **template.json** que você baixou para o disco local.

1. No painel do editor, faça as seguintes alterações.

    + Altere **disks_az104_disk1_name** para `disk_name` (duas ocorrências para alterar)
    + Altere **az104-disk1** para `az104-disk2` (uma ocorrência para alterar)

1. Observe que este é um disco **Standard**. A localização é **eastus**. O tamanho do disco é **32GB**.

1. **Save** suas alterações.

1. Não esqueça do arquivo de parameters. Selecione **Edit parameters**, clique em **Load file** e faça upload do **parameters.json**.

1. Faça esta alteração para que corresponda ao arquivo template.

    Altere **disks_az104_disk1_name** para **disk_name** (uma ocorrência para alterar)

1. **Save** suas alterações.

1. Complete as configurações de implantação personalizada:

    | Configuração | Valor |
    | --- |--- |
    | Subscription | *your subscription* |
    | Resource Group | `az104-rg3` |
    | Region | **(US) East US** |
    | Disk_name | `az104-disk2` |

1. Selecione **Review + Create** e depois selecione **Create**.

1. Selecione **Go to resource**. Verifique que **az104-disk2** foi criado.

1. No painel **Overview**, selecione o resource group, **az104-rg3**. Você deve ter agora dois discos.

1. Na seção **Settings**, clique em **Deployments**.

    >**Observação:** Todos os detalhes das implantações são documentados no resource group. É uma boa prática revisar as primeiras implantações baseadas em template para garantir sucesso antes de usar os templates em operações de grande escala.

1. Selecione uma implantação e revise o conteúdo dos painéis **Input** e **Template**.

## Task 3: Configure the Cloud Shell and deploy a template with PowerShell

Nesta tarefa, você trabalhará com o Azure Cloud Shell e Azure PowerShell. Azure Cloud Shell é um terminal interativo, autenticado e acessível pelo navegador para gerenciar recursos do Azure. Ele fornece a flexibilidade de escolher a experiência de shell que melhor se adapta à forma como você trabalha, seja Bash ou PowerShell. Nesta tarefa, você usará PowerShell para implantar um template.

1. Selecione o ícone **Cloud Shell** no canto superior direito do Azure Portal. Alternativamente, você pode navegar diretamente para `https://shell.azure.com`.

   ![Screenshot of cloud shell icon.](../media/az104-lab03-cloudshell-icon.png)

1. Quando for solicitado a selecionar **Bash** ou **PowerShell**, selecione **PowerShell**. Se o Cloud Shell carregar totalmente no modo Bash, selecione **Switch to PowerShell**.

    >**Você sabia?**  Se você trabalha majoritariamente com sistemas Linux, o Bash (CLI) tende a ser mais familiar. Se você trabalha majoritariamente com sistemas Windows, Azure PowerShell tende a ser mais familiar.

1. O Cloud Shell pode iniciar diretamente em uma sessão PowerShell efêmera. Nenhuma configuração de storage account é necessária.

    >**Observação:** Aguarde o prompt do PowerShell aparecer antes de prosseguir.

1. Selecione o ícone **Manage files** (barra superior) e então selecione **Upload**.

1. Faça upload dos arquivos de template e parameters da pasta **Downloads**.

1. Selecione o ícone **Editor (pencil)** e navegue até o arquivo JSON do template à esquerda no painel de navegação.

1. Faça uma alteração. Por exemplo, altere o nome do disco para **az104-disk3**. Use **Ctrl+S** para salvar suas alterações, depois **Ctrl+Q** para fechar o editor.

    >**Observação**: Você pode direcionar a implantação do template para um resource group, subscription, management group ou tenant. Dependendo do escopo da implantação, você usa comandos diferentes.

1. Se você tiver múltiplas subscriptions, primeiro garanta que o PowerShell esteja usando o contexto de subscription correto. Execute os seguintes comandos e, se necessário, substitua `<your-subscription-id>` pela subscription que contém **az104-rg3**.

    ```powershell
    Get-AzContext
    Set-AzContext -Subscription <your-subscription-id>
    ```

    >**Observação:** Isso é especialmente importante quando o Cloud Shell está em modo efêmero, porque a subscription ativa pode diferir do contexto do Azure portal.

1. Para implantar em um resource group, use **New-AzResourceGroupDeployment**.

    ```powershell
    New-AzResourceGroupDeployment -ResourceGroupName az104-rg3 -TemplateFile template.json -TemplateParameterFile parameters.json
    ```
1. Garanta que o comando seja concluído e que o ProvisioningState esteja **Succeeded**.

1. Confirme que o disco foi criado.

   ```powershell
   Get-AzDisk | ft Name,ResourceGroupName,Location,DiskSizeGb,ProvisioningState
   ```

## Task 4: Deploy a template with the CLI

1. Continue no **Cloud Shell** e selecione **Switch to Bash**. **Confirme** sua escolha.

1. Se você tiver múltiplas subscriptions, primeiro garanta que o contexto de subscription correto esteja definido executando `az account show` para confirmar a subscription ativa. Se não corresponder à subscription que contém **az104-rg3**, execute `az account set --subscription <your-subscription-id>` antes de prosseguir. Isso é especialmente importante quando o Cloud Shell está em modo efêmero, pois alternar de PowerShell para Bash pode redefinir o contexto da subscription.

    ```sh
    az account show
    az account set --subscription <your-subscription-id>
    ```

1. Verifique se seus arquivos estão disponíveis no armazenamento do Cloud Shell. Se você concluiu a tarefa anterior, seus arquivos de template devem estar disponíveis.

    ```sh
    ls
    ```

1. Selecione o **Editor** (pencil) e navegue até o arquivo JSON do template.

1. Faça uma alteração. Por exemplo, altere o nome do disco para **az104-disk4**. Use **Ctrl+S** para salvar suas alterações, depois **Ctrl+Q** para fechar o editor.

    >**Observação**: Você pode direcionar a implantação do template para um resource group, subscription, management group ou tenant. Dependendo do escopo da implantação, você usa comandos diferentes.

1. Para implantar em um resource group, use **az deployment group create**.

    ```sh
    az deployment group create --resource-group az104-rg3 --template-file template.json --parameters parameters.json
    ```

1. Garanta que o comando seja concluído e que o ProvisioningState esteja **Succeeded**.

1. Confirme que o disco foi criado.

     ```sh
     az disk list --resource-group az104-rg3 --output table
     ```

## Task 5: Deploy a resource by using Azure Bicep

Nesta tarefa, você usará um arquivo Bicep para implantar um managed disk. Bicep é uma ferramenta declarativa de automação construída sobre ARM templates.

1. Localize o arquivo **\Allfiles\Lab03\azuredeploydisk.bicep**.

1. Continue trabalhando no **Cloud Shell** em uma sessão **Bash**.

1. Selecione **Manage files** e então **Upload** o arquivo Bicep para o Cloud Shell.

1. Selecione **Open editor**.

1. Selecione o arquivo **azuredeploydisk.bicep**.

1. Reserve um minuto para ler o arquivo de template Bicep. Observe como o recurso disk é definido.

1. Faça as seguintes alterações:

    + Altere o valor de **managedDiskName**, linha 2, para **az104-disk5**.
    + Altere o valor de **diskSizeinGiB**; linha 7, para **32**.
    + Altere o valor de **sku name**, linha 26, para **StandardSSD_LRS**.

1. Use **Ctrl+S** para salvar suas alterações, depois **Ctrl+Q** para fechar o editor.

1. Agora, implante o template.

    ```
    az deployment group create --resource-group az104-rg3 --template-file azuredeploydisk.bicep
    ```

1. Confirme que o disco foi criado.

    ```sh
    az disk list --resource-group az104-rg3 --output table
    ```

    >**Observação:** Você implantou com sucesso cinco managed disks, cada um de uma forma diferente. Bom trabalho!

## Limpeza dos seus recursos

Se você estiver trabalhando com **sua própria subscription**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que os custos sejam minimizados. A maneira mais fácil de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ No Azure portal, selecione o resource group, selecione **Delete the resource group**, **Enter resource group name**, e então clique em **Delete**. Quando a caixa de diálogo de confirmação aparecer informando que excluir o resource group é permanente e não pode ser desfeito, clique em **Delete** novamente para completar a exclusão.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Expanda seu aprendizado com o Copilot

Copilot pode ajudá-lo a aprender como usar as ferramentas de script do Azure. Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precisar de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue para *copilot.microsoft.com*. Reserve alguns minutos para testar estes prompts.

+ What is the format of the Azure Resource Manager template file? Explain each component with examples.
+ How do I use an existing Azure Resource Manager template?
+ Compare and contrast Azure Resource Manager templates and Azure Bicep templates.


## Aprenda mais com treinamento autodirigido

+ [Deploy Azure infrastructure by using JSON ARM templates](https://learn.microsoft.com/training/modules/create-azure-resource-manager-template-vs-code/). Escreva JSON Azure Resource Manager templates (ARM templates) usando Visual Studio Code para implantar sua infraestrutura no Azure de forma consistente e confiável.
+ [Review the features and tools for Azure Cloud Shell](https://learn.microsoft.com/training/modules/review-features-tools-for-azure-cloud-shell/). Recursos e ferramentas do Cloud Shell.
+ [Create Azure Resources Using Azure CLI](https://learn.microsoft.com/training/modules/create-azure-resources-by-using-azure-cli/). Aprenda a instalar o Azure CLI no Windows, Linux e macOS, executar comandos interativamente, criar scripts de automação Bash e solucionar problemas comuns.
+ [Build your first Bicep template](https://learn.microsoft.com/training/modules/build-first-bicep-template/). Defina recursos do Azure dentro de um template Bicep. Melhore a consistência e confiabilidade de suas implantações, reduza o esforço manual necessário e escale suas implantações entre ambientes. Seu template será flexível e reutilizável usando parâmetros, variáveis, expressões e módulos.

## Principais conclusões

Parabéns por completar o laboratório. Aqui estão os principais pontos deste laboratório.

+ Azure Resource Manager templates permitem implantar, gerenciar e monitorar todos os recursos da sua solução como um grupo, em vez de tratar esses recursos individualmente.
+ Um Azure Resource Manager template é um arquivo JavaScript Object Notation (JSON) que permite gerenciar sua infraestrutura de forma declarativa em vez de scripts.
+ Em vez de passar parâmetros como valores inline em seu template, você pode usar um arquivo JSON separado que contenha os valores dos parâmetros.
+ Azure Resource Manager templates podem ser implantados de várias maneiras, incluindo o Azure portal, Azure PowerShell e CLI.
+ Bicep é uma alternativa aos Azure Resource Manager templates. Bicep usa uma sintaxe declarativa para implantar recursos do Azure.
+ Bicep fornece sintaxe concisa, segurança de tipo confiável e suporte para reutilização de código. Bicep oferece uma experiência de autoria de primeira classe para suas soluções de infrastructure-as-code no Azure.
