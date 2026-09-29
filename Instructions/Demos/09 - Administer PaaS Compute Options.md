---
demo:
    title: 'Demonstração 09: Administrar opções de computação PaaS'
    module: 'Administrar opções de computação PaaS'
layout: default
---

# 09 - Administrar opções de computação PaaS

## Configurar Azure App Service Plans

Nesta demonstração, criaremos e trabalharemos com Azure App Service plans.

**Referência**: [Manage App Service plan - Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-plan-manage)

**Referência**: [Scale up an app in Azure App Service](https://learn.microsoft.com/azure/app-service/manage-scale-up)

**Referência**: [Automatic scaling in Azure App Service](https://learn.microsoft.com/azure/app-service/manage-automatic-scaling?tabs=azure-portal)

1. Use o Azure portal.

1. Pesquise e selecione **App Service plans**.

1. Crie um App Service plan simples. Discuta a necessidade de selecionar Windows ou Linux. Discuta os planos de preços agora ou nas próximas etapas.

1. Faça o deploy do seu novo app service plan.

1. Revise o painel **Scale up (App Service Plan)**. Discuta a diferença entre os planos **Dev/Test** e **Production**. Revise a lista de recursos.

1. Revise o painel **Scale out (App Service Plan)**. Analise a diferença entre **Manual** e **Rule-based**.

## Configurar Azure App Services

Nesta demonstração, criaremos um novo web app que executa um contêiner Docker. O contêiner exibe uma mensagem de boas-vindas.

**Referência**: [Create a Web App](https://learn.microsoft.com/training/modules/host-a-web-app-with-azure-app-service/3-exercise-create-a-web-app-in-the-azure-portal?pivots=csharp)

Nesta tarefa, criaremos um Azure App Service Web App.

1. Use o Azure portal.

1. Pesquise e selecione **App Services**.

1. **Create** um **Web App**.

    - Publish: **Code**. Revise outras escolhas.
    - Runtime stack: **.Net**. Revise outras escolhas.
    - Operating system: **Linux**

1. Selecione o plano de serviço **Free F1**.

1. **Review + create** o web app. Aguarde até o recurso ser implantado.

1. Na página **Overview**, verifique se o **Status** está **Running**.

1. Selecione a **URL** e verifique se a página placeholder padrão é carregada.

1. Se houver tempo, explore as opções de **Deployment slots**.

## Configurar Azure Container Instances

Nesta demonstração, criaremos, configuraremos e implantaremos um contêiner usando Azure Container Instances (ACI) a partir do Azure Portal. A aplicação ACI exibe uma página HTML estática com a imagem pública Microsoft Hello World.

**Referência**: [Quickstart - Deploy Docker container to container instance](https://learn.microsoft.com/en-us/azure/container-instances/container-instances-quickstart-portal)

1. Use o Azure portal.

1. Pesquise e selecione **Container instances**.

1. **Create** uma nova container instance.

1. Preencha o **Resource group** e o **Container name**.

1. Discuta as opções de **Image source**. Use **Quickstart images**.

1. Para **Container image** use **mcr.microsoft.com/azuredocs/aci-helloworld:latest (Linux)**. Esta imagem de exemplo para Linux empacota um pequeno web app escrito em Node.js que serve uma página HTML estática.

1. Na página **Networking**, especifique um **DNS name label** para o seu contêiner.

1. Deixe todas as outras configurações nos padrões e selecione **Review + create**.

1. Aguarde até o recurso ser implantado.

1. Na página **Overview** do recurso, verifique se o **Status** está **Running**.

1. Navegue até o **FQDN** da container instance e verifique se a página de boas-vindas é exibida.

**Observação**: Para evitar custos adicionais, exclua o recurso.

## Configurar Azure Container Apps

Nesta demonstração, criaremos e trabalharemos com Azure Container Apps.

**Referência**: [Quickstart: Deploy your first container app using the Azure portal](https://learn.microsoft.com/azure/container-apps/quickstart-portal)

1. Pesquise e selecione **Container Apps**.

1. Preencha os **Project details** e crie o **environment** para container apps.

1. **Review and create** o container app.

1. Use o link **Application URL** para visualizar sua aplicação.

1. Verifique se o navegador exibe a mensagem **Welcome to Azure Container Apps**.
