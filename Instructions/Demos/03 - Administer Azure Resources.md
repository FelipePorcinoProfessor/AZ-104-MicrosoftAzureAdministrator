---
demo:
    title: 'Demonstração 03: Administrar Recursos do Azure'
    module: 'Administrar Recursos do Azure'
layout: default
---
# 03 - Administrar Recursos do Azure

## Demonstração -- Microsoft Copilot for Azure

Nesta demonstração, exploramos o Copilot for Azure.

**Referência**: [Analisar, estimar e otimizar custos em nuvem usando Azure Copilot](https://learn.microsoft.com/azure/copilot/analyze-cost-management)

**Referência**: [Gerar scripts PowerShell usando Azure Copilot](https://learn.microsoft.com/azure/copilot/generate-powershell-scripts)

Ou, você pode usar qualquer outro cenário sugerido nesta página: [Executar tarefas](https://learn.microsoft.com/azure/copilot/capabilities#perform-tasks)

1. Acesse o portal e a janela do Copilot.

1. Use uma das instruções sugeridas ou escolha uma de sua autoria.

## Demonstração -- Azure Portal

Nesta demonstração, exploramos o Azure Portal.

**Referência**: [Gerenciar configurações e preferências do Azure portal](https://docs.microsoft.com/azure/azure-portal/set-preferences)

**Referência**: [Criar um painel no Azure portal](https://docs.microsoft.com/azure/azure-portal/azure-portal-dashboards)

**Referência**: [Como criar uma solicitação de suporte do Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)

1. Acesse o Azure Portal.

1. Selecione o ícone **Suporte e solução de problemas** (Support & Troubleshooting) na barra superior. Revise os links **Recursos de suporte** (Support resources).

1. Selecione o ícone **Configurações** (Settings) na barra superior. Revise as configurações **Aparência + visualizações de inicialização** (Appearance + startup views).

1. Use o menu lateral e selecione **Painel** (Dashboard). **Edite** (Edit) o painel usando a **Galeria de Blocos** (Tile Gallery). Discuta as opções de personalização.

1. Mostre como pesquisar e localizar recursos.

1. Use o menu superior esquerdo para localizar **Todos os serviços** (All services).

1. Se houver tempo, reveja outros recursos.

1. Pergunte se os alunos têm alguma pergunta.

## Demonstração -- Cloud Shell

Nesta demonstração, experimentamos o Cloud Shell.

**Referência**: [Introdução rápida ao Azure Cloud Shell](https://learn.microsoft.com/en-us/azure/cloud-shell/quickstart?tabs=azurecli)

**Configurar o Cloud Shell**

1.  Acesse o **Azure Portal**.

1.  Clique no ícone **Cloud Shell** na barra superior.

1.  Na página "Bem-vindo ao Shell" (Welcome to the Shell), observe suas seleções para Bash ou PowerShell. Selecione **PowerShell**.

1.  Explique como o Azure Cloud Shell requer um Azure file share para persistir arquivos. Se necessário, configure o compartilhamento de armazenamento.

**Experimente com o Azure PowerShell e o Bash**

1. Certifique-se de que o shell **PowerShell** esteja selecionado e execute alguns comandos. Por exemplo, **Get-AzSubscription** e **Get-AzResourceGroup**.

1. Mostre como o autocompletar funciona. Mostre como limpar a tela, **cls**.

1. Certifique-se de que o shell **Bash** esteja selecionado e execute alguns comandos. Por exemplo, **az account list** e **az resource list**.

1. Pergunte se os alunos têm alguma dúvida sobre o uso dos comandos do PowerShell ou do Bash.

**Experimente o Editor do Cloud Shell (Cloud Editor) (opcional)**

1. Para usar o Editor do Cloud Shell (Cloud Editor), selecione o ícone de **chaves**.

1. Selecione um arquivo no painel de navegação à esquerda. Por exemplo, **.profile**.

1. Observe na barra superior do editor as opções para **Configurações** (Tamanho do Texto e Fonte) (Settings (Text Size and Font)) e **Carregar/Baixar arquivos** (Upload/Download files).

1. Observe os três pontos (**\...**) no canto direito para **Salvar** (Save), **Fechar Editor** (Close Editor) e **Abrir Arquivo** (Open File).

1. Experimente conforme houver tempo, então **feche** o Editor do Cloud Shell (Cloud Editor).

1. Feche o Cloud Shell.

## Demonstração -- Modelos QuickStart

Nesta demonstração, exploramos os QuickStart Templates.

**Referência**: [Tutorial - Criar e implantar template - Azure Resource Manager](https://docs.microsoft.com/en-us/azure/azure-resource-manager/templates/template-tutorial-create-first-template?tabs=azure-powershell)

1. Comece navegando até a galeria [Azure Quickstart Templates](https://learn.microsoft.com/en-us/samples/browse/?expanded=azure&products=azure-resource-manager). Observe que há exemplos em JSON e Bicep.

1. Pergunte aos alunos se há templates específicos de interesse. Caso não, selecione um template. Por exemplo, o template [Implantar uma VM Windows simples com tags](https://learn.microsoft.com/en-us/samples/azure/azure-quickstart-templates/vm-simple-windows/).

1. Explique como o botão **Implantar no Azure** (Deploy to Azure) permite implantar o template diretamente pelo Azure Portal.

1. **Implante** (Deploy) o template JSON e discuta como você pode editar o template e o arquivo de parâmetros. Revise a finalidade dos arquivos. Se houver tempo, revise a sintaxe.

1. Retorne à galeria de exemplos de código e localize um template em Bicep. Por exemplo, [Criar uma Conta de Armazenamento Standard](https://learn.microsoft.com/en-us/samples/azure/azure-quickstart-templates/storage-account-create/).

1. **Implante** (Deploy) o template Bicep e discuta como você pode editar o template e o arquivo de parâmetros. Se houver tempo, revise a sintaxe.
