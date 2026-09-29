---
lab:
  title: 'Lab 09a: Implementar Web Apps'
  module: Administrar opções de computação PaaS
  description: Configure e dimensione Web Apps do Azure.
  duration: 20 minutes
  level: 300
  islab: true
  primarytopics:
    - Azure
    - Web Apps
    - Deployment slots
layout: default
---

# Lab 09a - Implementar Web Apps


## Introdução ao laboratório

Neste laboratório, você aprenderá sobre Web Apps do Azure. Você aprenderá a configurar um web app para exibir uma aplicação Hello World em um repositório externo do GitHub. Você aprenderá a criar um slot de staging e a trocar (swap) com o slot de produção. Você também aprenderá sobre autoscaling para acomodar mudanças na demanda.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando East US.

## Tempo estimado: 20 minutos

## Cenário do laboratório

Sua organização está interessada em Web Apps do Azure para hospedar os sites da empresa. Os sites atualmente estão hospedados em um datacenter local. Os sites estão executando em servidores Windows usando o runtime PHP. O hardware está perto do fim da vida útil e em breve precisará ser substituído. Sua organização quer evitar custos com hardware novo usando o Azure para hospedar os sites.

## Diagrama de arquitetura

![Diagram of the tasks.](../media/az104-lab09a-architecture.png)

## Habilidades práticas

+ Tarefa 1: Criar e configurar um Web App do Azure.
+ Tarefa 2: Criar e configurar um slot de implantação.
+ Tarefa 3: Configurar as definições de implantação do Web App.
+ Tarefa 4: Trocar (swap) slots de implantação.
+ Tarefa 5: Configurar e testar o autoscaling do Web App do Azure.

## Tarefa 1: Criar e configurar um Web App do Azure

Nesta tarefa, você criará um Web App do Azure. Azure App Services é uma solução Platform As a Service (PAAS) para web, mobile e outras aplicações baseadas na web. Web Apps faz parte do Azure App Services hospedando a maioria dos ambientes de runtime, como PHP, Java e .NET. O App Service plan que você selecionar determina o compute, armazenamento e recursos do web app.

1. Faça login no **Azure portal** - `https://portal.azure.com`.

1. Pesquise por e selecione `App Services`.

1. Selecione **+ Create**, no menu suspenso, **Web App**. Observe as outras opções.

1. Na guia **Basics** do blade **Create Web App**, especifique as seguintes configurações (deixe as demais com os valores padrão):

    | Setting | Value |
    | --- | ---|
    | Subscription | sua assinatura do Azure |
    | Resource group | `az104-rg9` (se necessário, selecione **Create new**) |
    | Web app name | qualquer nome globalmente único |
    | Publish | **Code** |
    | Runtime stack | **PHP 8.2** |
    | Operating system | **Linux** |
    | Region | **East US** |
    | Pricing plans | **Premium V3 P1V3** |
    | Zone redundancy | aceite os padrões |

 1. Clique em **Review + create**, e então em **Create**.

    >**Observação**: Aguarde até que o Web App seja criado antes de prosseguir para a próxima tarefa. Isso deve levar cerca de um minuto.

    >**Observação**: Se a implantação falhar, altere para outra região e tente novamente. Isso pode ocorrer devido a cotas em diferentes regiões.

1. Após a implantação, selecione **Go to resource**.

## Tarefa 2: Criar e configurar um slot de implantação

Nesta tarefa, você criará um slot de implantação de staging. Os deployment slots permitem que você realize testes antes de disponibilizar sua aplicação ao público (ou aos seus usuários finais). Depois de realizar os testes, você pode trocar (swap) o slot de development ou staging para production. Muitas organizações usam slots para testes pré-produção. Além disso, muitas organizações executam múltiplos slots para cada aplicação (por exemplo, development, QA, test e production).

1. No blade do Web App recém-implantado, clique no link **Default domain** para exibir a página padrão do site em uma nova aba do navegador.

1. Feche a nova aba do navegador e, de volta ao Azure portal, na seção **Deployment** do blade do Web App, clique em **Deployment slots**.

1. Clique em **Add slot**, e adicione um novo slot com as seguintes configurações:

    | Setting | Value |
    | --- | ---|
    | Name | `staging` |
    | Clone settings from | **Do not clone settings**|

1. Selecione **Add** para criar o slot.

1. Atualize a página para visualizar os slots Production e Staging.

1. Selecione a entrada que representa o slot de staging recém-criado.

    >**Observação**: Isso abrirá o blade exibindo as propriedades do slot de staging.

1. Revise o blade do slot de staging e observe que sua URL difere daquela atribuída ao slot de produção.

## Tarefa 3: Configurar as definições de implantação do Web App

Nesta tarefa, você configurará as definições de implantação do Web App. As definições de implantação permitem implantação contínua. Isso garante que o App Service tenha a versão mais recente da aplicação.

1. No slot **staging**, selecione **Configuration** e então **General settings**.

    >**Observação:** Certifique-se de que você está no blade do slot staging (e não no slot de production).

1. Em **SCM Basic Auth Publishing Credentials**, habilite a caixa de seleção e selecione **Apply**.

1. Assegure que a seção **Deployment** do menu do serviço esteja expandida, e selecione **Deployment Center** e então **Settings**.

1. Se surgir um banner de alerta afirmando "SCM basic authentication is disabled for your app", selecione **Enable here** e complete as etapas para habilitá-la.

1. Na lista suspensa **Source**, selecione **External Git**. Observe as outras opções.

1. No campo repository, insira `https://github.com/Azure-Samples/php-docs-hello-world`

1. No campo branch, insira `master`.

1. Selecione **Save**.

1. No slot de staging, selecione **Overview**.

1. Selecione o link **Default domain**, e abra a URL em uma nova aba.

1. Verifique que o slot de staging exibe **Hello World**.

>**Observação:** A implantação pode levar um minuto. Certifique-se de **Refresh** a página da aplicação.

## Tarefa 4: Trocar (swap) slots de implantação

Nesta tarefa, você trocará o slot de staging com o slot de production. Trocar um slot permite que você utilize o código que testou no slot de staging e o mova para produção. O Azure portal também solicitará confirmação se você precisar mover outras configurações de aplicação que tenham sido customizadas para o slot. Trocar slots é uma tarefa comum para equipes de aplicação e equipes de suporte, especialmente ao implantar atualizações rotineiras e correções de bugs.

1. Navegue de volta para o blade **Deployment slots**, e então selecione **Swap**.

1. Revise as configurações padrão e clique em **Start Swap**. Aguarde a notificação de que a troca foi concluída.

1. Retorne à página inicial do portal. Você deverá ver tanto o web app de production quanto o slot de staging.

1. Pesquise por `App Services` e selecione seu App Service web app. Isso o retornará ao slot de Deployment de Production.

1. Selecione o App Service web app e, no blade **Overview** do Web App, selecione o link **Default domain** para exibir a página inicial do site.

1. Verifique que a página de produção agora exibe a página **Hello World!**.

    >**Observação:** Copie a **URL** do Domínio padrão (Default domain); você precisará dela para o teste de carga na próxima tarefa.

## Tarefa 5: Configurar e testar o autoscaling do Web App do Azure

Nesta tarefa, você configurará o autoscaling do Web App do Azure. O autoscaling permite manter desempenho ideal para seu web app quando o tráfego aumenta. Para determinar quando o app deve escalar, você pode monitorar métricas como uso de CPU, memória ou largura de banda.

1. No painel esquerdo, na seção **App Service plan**, selecione **Scale out**.

    >**Observação:** Assegure-se de que você está trabalhando no slot de production, e não no slot de staging.

1. Na seção **Scaling**, selecione **Automatic**. Observe a opção **Rules Based**. O dimensionamento baseado em regras pode ser configurado para diferentes métricas do app.

1. No campo **Maximum burst**, selecione **2**. Defina **Minimum instances** para 1.

1. Se você tiver um slot de staging, abra o **Azure Cloud Shell** e execute o seguinte comando para definir o contador mínimo de instâncias elásticas do slot de staging para 1 antes de selecionar **Save**:

   ```bash
   az webapp update --resource-group az104-rg9 --name <your-web-app-name> --slot staging --minimum-elastic-instance-count 1
   ```

    >**Observação:** Substitua `<your-web-app-name>` pelo nome do seu app. Depois que o comando for bem-sucedido, retorne para **Scale out** no web app de production e selecione **Save**.

    ![Screenshot of the autoscale page.](../media/az104-lab09a-autoscale.png)

1. Selecione **Save**.

1. Selecione **Diagnose and solve problems** (painel esquerdo da página principal do web app).

1. No quadro **Load Test your App**, selecione **Create Load Test**.

    + Selecione **+ Create** e dê um **nome** ao seu teste de carga.  O nome deve ser único.
    + Selecione **Review + create** e então **Create**.

1. Aguarde a criação do teste de carga e, em seguida, selecione **Go to resource**.

1. Em **Overview** | **Create by adding HTTP requests**, selecione **Create**.

1. Na guia **Test plan**, clique em **Add request**. No campo **URL**, cole a URL do seu **Default domain**. Certifique-se de que esteja formatada corretamente e comece com **https://**. Selecione **Add** para salvar suas alterações.

1. Selecione **Review + create** e **Create**.

    >**Observação:** A criação do teste pode levar alguns minutos. Acompanhe as notificações.

1. Navegue até o teste (ele está listado na página inicial).

1. Atualize e revise as métricas ao vivo, incluindo **Virtual users**, **Response time** e **Requests/sec**.

1. Selecione **Stop** para iniciar o pedido de parada, então selecione **Stop** novamente na caixa de confirmação para concluir a execução do teste. Você não precisa aguardar o término natural do teste.

## Limpeza dos seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que o custo seja minimizado. A maneira mais fácil de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ No Azure portal, selecione o resource group, selecione **Delete the resource group**, **Enter resource group name**, e então clique em **Delete**. Quando uma segunda caixa de confirmação Delete aparecer, clique em **Delete** novamente para completar a exclusão.
+ Usando o Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando a CLI, `az group delete --name resourceGroupName`.

## Amplie seu aprendizado com o Copilot
O Copilot pode ajudar você a aprender como usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue para *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.

+ Resuma os passos para criar e configurar um Web App do Azure.
+ Quais são as maneiras de escalar um Web App do Azure?

## Aprenda mais com treinamentos autoguiados

+ [Host a web application with Azure App Service](https://learn.microsoft.com/en-us/training/modules/host-a-web-app-with-azure-app-service/). Aprenda como criar um website através da plataforma hospedada Web App no Azure App Service.
+ [Configure web app settings](https://learn.microsoft.com/en-us/training/modules/configure-web-app-settings/). Aprenda como criar e gerenciar application settings, instalar certificados SSL/TLS para proteger o tráfego web, habilitar logging de diagnóstico, criar mapeamentos de aplicativo virtual para diretórios, e gerenciar recursos do app.

## Principais conclusões

Parabéns por concluir o laboratório. Aqui estão os principais pontos deste laboratório.

+ Azure App Services permite que você construa, implante e dimensione rapidamente web apps.
+ App Service inclui suporte para muitos ambientes de desenvolvimento, incluindo ASP.NET, Java, PHP e Python.
+ Deployment slots permitem criar ambientes separados para implantar e testar seu web app.
+ Você pode escalar manualmente ou automaticamente um web app para lidar com demanda adicional.
+ Há uma grande variedade de ferramentas de diagnóstico e teste disponíveis.
