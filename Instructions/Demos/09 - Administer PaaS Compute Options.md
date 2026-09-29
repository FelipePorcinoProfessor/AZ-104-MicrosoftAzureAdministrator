---
demo:
    title: 'Demonstração 09: Administrar opções de computação PaaS'
    module: 'Administrar opções de computação PaaS'
layout: default
---

# 09 - Administrar opções de computação PaaS

## Configurar Planos do Azure App Service (App Service Plans)

Nesta demonstração, criaremos e trabalharemos com planos do Azure App Service (App Service plans).

**Referência**: [Manage App Service plan - Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-plan-manage)

**Referência**: [Scale up an app in Azure App Service](https://learn.microsoft.com/azure/app-service/manage-scale-up)

**Referência**: [Automatic scaling in Azure App Service](https://learn.microsoft.com/azure/app-service/manage-automatic-scaling?tabs=azure-portal)

1. Use o Azure portal.

1. Pesquise e selecione **Planos do App Service (App Service plans)**.

1. Crie um plano do App Service simples. Discuta a necessidade de selecionar Windows ou Linux. Discuta os planos de preços agora ou nas próximas etapas.

1. Implante o seu novo plano do App Service.

1. Revise o painel **Escalonamento vertical (Scale up) (App Service Plan)**. Discuta a diferença entre os planos **Dev/Test** e **Production**. Revise a lista de recursos.

1. Revise o painel **Escalonamento horizontal (Scale out) (App Service Plan)**. Analise a diferença entre **Manual** e **Baseado em regras (Rule-based)**.

## Configurar Azure App Services

Nesta demonstração, criaremos um novo web app que executa um contêiner Docker. O contêiner exibe uma mensagem de boas-vindas.

**Referência**: [Create a Web App](https://learn.microsoft.com/training/modules/host-a-web-app-with-azure-app-service/3-exercise-create-a-web-app-in-the-azure-portal?pivots=csharp)

Nesta tarefa, criaremos um Azure App Service Web App.

1. Use o Azure portal.

1. Pesquise e selecione **App Services**.

1. **Criar (Create)** um **Web App**.

    - Publicar: **Código (Code)**. Revise outras opções.
    - Pilha de runtime (Runtime stack): **.NET**. Revise outras escolhas.
    - Sistema operacional: **Linux**

1. Selecione o plano de serviço **Free F1**.

1. **Revisar e criar (Review + create)** o web app. Aguarde até o recurso ser implantado.

1. Na página **Visão geral (Overview)**, verifique se o **Status** está **Em execução (Running)**.

1. Selecione a **URL** e verifique se a página de espaço reservado (placeholder) padrão é carregada.

1. Se houver tempo, explore as opções de **Slots de implantação (Deployment slots)**.

## Configurar Azure Container Instances

Nesta demonstração, criaremos, configuraremos e implantaremos um contêiner usando Azure Container Instances (ACI) a partir do Azure Portal. A aplicação ACI exibe uma página HTML estática com a imagem pública Microsoft Hello World.

**Referência**: [Quickstart - Deploy Docker container to container instance](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-quickstart-portal)

1. Use o Azure portal.

1. Pesquise e selecione **Instâncias de contêiner (Container instances)**.

1. **Criar (Create)** uma nova instância de contêiner.

1. Preencha o **Grupo de recursos (Resource group)** e o **Nome do contêiner (Container name)**.

1. Discuta as opções de **Fonte da imagem (Image source)**. Use **Imagens Quickstart (Quickstart images)**.

1. Para **Imagem do contêiner (Container image)** use **mcr.microsoft.com/azuredocs/aci-helloworld:latest (Linux)**. Esta imagem de exemplo para Linux empacota um pequeno web app escrito em Node.js que serve uma página HTML estática.

1. Na página **Rede (Networking)**, especifique um **Rótulo de nome DNS (DNS name label)** para o seu contêiner.

1. Deixe todas as outras configurações nos padrões e selecione **Revisar e criar (Review + create)**.

1. Aguarde até o recurso ser implantado.

1. Na página **Visão geral (Overview)** do recurso, verifique se o **Status** está **Em execução (Running)**.

1. Navegue até o **FQDN** da instância de contêiner e verifique se a página de boas-vindas é exibida.

**Observação**: Para evitar custos adicionais, exclua o recurso.

## Configurar Azure Container Apps

Nesta demonstração, criaremos e trabalharemos com Azure Container Apps.

**Referência**: [Quickstart: Deploy your first container app using the Azure portal](https://learn.microsoft.com/azure/container-apps/quickstart-portal)

1. Pesquise e selecione **Aplicativos de contêiner (Container Apps)**.

1. Preencha os **Detalhes do projeto (Project details)** e crie o **ambiente (environment)** para os aplicativos de contêiner.

1. **Revisar e criar (Review + create)** o aplicativo de contêiner (container app).

1. Use o link **URL do aplicativo (Application URL)** para visualizar sua aplicação.

1. Verifique se o navegador exibe a mensagem **Bem-vindo ao Azure Container Apps (Welcome to Azure Container Apps)**.
