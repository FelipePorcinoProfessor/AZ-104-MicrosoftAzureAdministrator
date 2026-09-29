---
lab:
  title: 'Laboratório 09b: Implement Azure Container Instances'
  module: Administrar PaaS Compute Options
  description: Implemente e implante Azure Container Instances.
  duration: 15 minutes
  level: 300
  islab: true
  primarytopics:
    - Azure
    - Azure Container Instances
layout: default
---

# Laboratório 09b - Implement Azure Container Instances

## Introdução do laboratório

Neste laboratório, você aprenderá como implementar e implantar Azure Container Instances.

Este laboratório requer uma assinatura do Azure. O tipo de assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Tempo estimado: 15 minutos

## Cenário do laboratório

Sua organização possui um aplicativo web que é executado em uma máquina virtual no seu data center local. A organização quer migrar todos os aplicativos para a nuvem, mas não deseja gerenciar um grande número de servidores. Você decide avaliar Azure Container Instances e Docker.

## Diagrama de arquitetura

![Diagrama das tarefas.](../media/az104-lab09b-aci-architecture.png)

## Habilidades do trabalho

- Tarefa 1: Deploy de uma Azure Container Instance usando uma imagem Docker.
- Tarefa 2: Testar e verificar a implantação de uma Azure Container Instance.

## Tarefa 1: Deploy de uma Azure Container Instance usando uma imagem Docker

Nesta tarefa, você criará um aplicativo web simples usando uma imagem Docker. Docker é uma plataforma que fornece a capacidade de empacotar e executar aplicativos em ambientes isolados chamados contêineres. Azure Container Instances fornece o ambiente de computação para a imagem do contêiner.

1. Faça logon no **Azure portal** - `https://portal.azure.com`.

1. No Azure portal, pesquise e selecione `Container instances` e então, na lâmina **Container instances**, clique em **+ Create**.

1. Na guia **Basics** da lâmina **Create container instance**, especifique as seguintes configurações (deixe as demais com os valores padrão):

    | Setting | Value |
    | ---- | ---- |
    | Subscription | Select your Azure subscription |
    | Resource group | `az104-rg9` (Se necessário, selecione **Create new**) |
    | Container name | `az104-c1` |
    | Region | **East US** (ou uma região disponível próxima a você)|
    | Image Source | **Quickstart images** |
    | Image | **mcr.microsoft.com/azuredocs/aci-helloworld:latest (Linux)** |

1. Clique em **Next: Networking >** e especifique as seguintes configurações (deixe as demais com os valores padrão):

    | Setting | Value |
    | --- | --- |
    | DNS name label | qualquer rótulo de host DNS válido e globalmente único |

    >**Observação**: Seu contêiner estará publicamente acessível em dns-name-label.region.azurecontainer.io. Se você receber a mensagem de erro **DNS name label not available**, especifique um valor diferente.

1. Clique em **Next: Monitoring >** e desmarque **Enable container instance logs**.

1. Clique em **Next: Advanced >**, revise as configurações sem fazer alterações.

1. Clique em **Review + Create**, verifique se a validação foi aprovada e então selecione **Create**.

    >**Observação**: Aguarde a conclusão da implantação. Isso deve levar 2–3 minutos.

    >**Observação**: Enquanto aguarda, você pode se interessar em visualizar o [código por trás do aplicativo de exemplo](https://github.com/Azure-Samples/aci-helloworld). Para ver o código, navegue pela pasta \\app.

## Tarefa 2: Testar e verificar a implantação de uma Azure Container Instance

Nesta tarefa, você revisará a implantação da instância do contêiner. Por padrão, a Azure Container Instance é acessível pela porta 80. Depois que a instância for implantada, você pode navegar até o contêiner usando o nome DNS que forneceu na tarefa anterior.

1. Quando a implantação for concluída, selecione o link **Go to resource**.

1. Na lâmina **Overview** da instância do contêiner, verifique se **Status** está relatado como **Running**.

1. Copie o valor do **FQDN** da instância do contêiner, abra uma nova aba do navegador e navegue até a URL correspondente.

     ![Screenshot da página de overview do ACI no portal.](../media/az104-lab09b-aci-overview.png)

1. Verifique se a página **Welcome to Azure Container Instance** é exibida. Atualize a página várias vezes para criar algumas entradas de log e depois feche a aba do navegador.

1. Na seção **Settings** da lâmina da instância do contêiner, clique em **Containers**, e então clique em **Logs**.

1. Verifique se você vê as entradas de log que representam as solicitações HTTP GET geradas ao exibir o aplicativo no navegador.

## Limpeza dos seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que os custos sejam minimizados. A maneira mais fácil de excluir os recursos do laboratório é excluir o grupo de recursos do laboratório.

+ No Azure portal, selecione o grupo de recursos, selecione **Delete the resource group**, **Enter resource group name**, e então clique em **Delete**. Em seguida, clique em **Delete** novamente na caixa de diálogo de confirmação que aparece.
+ Usando o Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Amplie seu aprendizado com o Copilot
Copilot pode auxiliá-lo a aprender como usar as ferramentas de script do Azure. Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precise de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para testar estes prompts.

+ Resuma os passos para criar e configurar uma Azure Container Instance.
+ Quais são as maneiras de executar um contêiner serverless no Azure?

## Aprenda mais com treinamento autônomo

+ [Run container images in Azure Container Instances](https://learn.microsoft.com/training/modules/create-run-container-images-azure-container-instances/). Aprenda como Azure Container Instances pode ajudá-lo a implantar contêineres rapidamente, como definir variáveis de ambiente e especificar políticas de reinício do contêiner.

## Principais conclusões

Parabéns por completar o laboratório. Aqui estão os principais pontos deste laboratório.

+ Azure Container Instances (ACI) é um serviço que permite implantar contêineres na nuvem pública Microsoft Azure.
+ ACI não exige que você provisionie ou gerencie qualquer infraestrutura subjacente.
+ ACI oferece suporte tanto a contêineres Linux quanto a contêineres Windows.
+ As cargas de trabalho no ACI geralmente são iniciadas e interrompidas por algum tipo de processo ou gatilho e normalmente são de curta duração.
