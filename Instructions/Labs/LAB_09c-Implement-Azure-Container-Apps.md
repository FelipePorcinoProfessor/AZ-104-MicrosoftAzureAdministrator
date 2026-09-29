---
lab:
  title: 'Lab 09c: Implementar Azure Container Apps'
  module: Administrar opções de computação PaaS
  description: Implementar e implantar Azure Container Apps.
  duration: 15 minutes
  level: 300
  islab: true
  primarytopics:
    - Azure
    - Azure Container Apps
layout: default
---

# Lab 09c - Implementar Azure Container Apps

## Introdução do laboratório

Neste laboratório, você aprenderá como implementar e implantar Azure Container Apps.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Tempo estimado: 15 minutos

## Cenário do laboratório

Sua organização tem um aplicativo web que é executado em uma máquina virtual no seu data center on-premises. A organização deseja mover todos os aplicativos para a nuvem, mas não quer ter um grande número de servidores para gerenciar. Você decide avaliar Azure Container Apps.

## Diagrama de arquitetura

![Diagram of the tasks.](../media/az104-lab09b-aca-architecture.png)

## Habilidades do trabalho

- Tarefa 1: Criar e configurar um Azure Container App e ambiente.
- Tarefa 2: Testar e verificar a implantação do Azure Container App.

## Tarefa 1: Criar e configurar um Azure Container App e ambiente

Azure Container Apps leva o conceito de um cluster Kubernetes gerenciado um passo adiante e gerencia o ambiente do cluster, além de fornecer outros serviços gerenciados sobre o cluster. Ao contrário de um cluster Azure Kubernetes, onde você ainda precisa gerenciar o cluster, uma instância de Azure Container Apps remove parte da complexidade de configurar um cluster Kubernetes.

1. No portal do Azure, pesquise por e selecione `Container Apps`.

1. Selecione **+ Create**, no menu suspenso, **Container App**. Observe as outras opções.

1. Use as seguintes informações para preencher os detalhes na guia **Basics**.

    | Setting | Action |
    |---|---|
    | Subscription | Selecione sua assinatura do Azure |
    | Resource group | `az104-rg9` |
    | Container app name |  `my-app` |
    | Region    | **East US** |
    | Container Apps Environment | Selecione **Create new environment** > Defina o nome do ambiente como `my-environment` > **Create** |

1. Clique na guia **Next: Container** e certifique-se de que **Use quickstart image** esteja marcado. Pode ser necessário rolar para cima para visualizar essa configuração.

1. Certifique-se de que **Quickstart image** esteja definida como **Simple hello world container**.

1. Em **Application ingress settings**, configure as opções de ingress conforme necessário e, em seguida, clique em **Next: Tags**.

1. Selecione **Review and create** e depois **Create**.

    >**Observação:** Aguarde a implantação do container app. Isso levará alguns minutos.

## Tarefa 2: Testar e verificar a implantação do Azure Container App

Por padrão, o container app do Azure que você criar aceitará tráfego na porta 80 usando o aplicativo de exemplo Hello World. Azure Container Apps fornecerá um nome DNS para o aplicativo. Copie e navegue até essa URL para garantir que o aplicativo esteja em execução.

1. Selecione **Go to resource** para visualizar seu novo container app.

1. Selecione o link ao lado de *Application URL* para visualizar seu aplicativo.

    ![Screenshot of the ACA overview page in the portal.](../media/az104-lab09b-aca-overview.png)

1. Verifique se você recebe a mensagem **Your container app is running with a Hello World image**.

## Limpeza dos seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que o custo seja minimizado. A maneira mais fácil de excluir os recursos do laboratório é excluir o grupo de recursos do laboratório.

+ No portal do Azure, selecione o grupo de recursos, selecione **Delete the resource group**, **Enter resource group name**, e então clique em **Delete**.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Amplie seu aprendizado com o Copilot
O Copilot pode ajudá-lo a aprender como usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.

+ Resuma os passos para criar e configurar um Azure Container App.
+ Compare e contraste Azure Container Apps com Azure Kubernetes Service.

## Aprenda mais com treinamentos autoguiados


+ [Configurar um container app no Azure Container Apps](https://learn.microsoft.com/training/modules/configure-container-app-azure-container-apps/). Examina os recursos e capacidades do Azure Container Apps e, em seguida, foca em como criar, configurar, escalar e gerenciar container apps usando Azure Container Apps.
+ [Implementar Azure Container Apps](https://learn.microsoft.com/training/modules/implement-azure-container-apps/). Aprenda como Azure Container Apps pode ajudá-lo a implantar e gerenciar microsserviços e aplicativos conteinerizados em uma plataforma serverless.


## Principais conclusões

Parabéns por completar o laboratório. Aqui estão os principais pontos deste laboratório.

+ Azure Container Apps (ACA) é uma plataforma serverless que permite manter menos infraestrutura e reduzir custos enquanto executa aplicativos conteinerizados.
+ Container Apps fornece configuração de servidor, orquestração de containers e detalhes de implantação.
+ Workloads no ACA geralmente são processos de longa execução, como um Web App.
