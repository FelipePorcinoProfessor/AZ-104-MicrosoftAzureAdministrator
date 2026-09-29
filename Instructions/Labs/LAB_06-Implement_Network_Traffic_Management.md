---
lab:
  title: 'Laboratório 06: Implementar Gerenciamento de Tráfego de Rede'
  module: Administrar Gerenciamento de Tráfego de Rede
  description: Criar e configurar Azure Load Balancer e Application Gateway.
  duration: 50 minutes
  level: 400
  islab: true
  primarytopics:
  - Azure
  - Azure Load Balancer
  - Azure Application Gateway
layout: default
---

# Lab 06 - Implementar Gerenciamento de Tráfego de Rede

## Introdução ao laboratório

Neste laboratório, você aprenderá como configurar e testar um Load Balancer público e um Application Gateway.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua organização possui um site público. Você precisa balancear as solicitações públicas de entrada entre diferentes máquinas virtuais. Você também precisa fornecer imagens e vídeos a partir de máquinas virtuais diferentes. Você pretende implementar um Azure Load Balancer e um Azure Application Gateway. Todos os recursos estão na mesma região.

## Habilidades do trabalho

+ Tarefa 1: Usar um template para provisionar uma infraestrutura.
+ Tarefa 2: Configurar um Azure Load Balancer.
+ Tarefa 3: Configurar um Azure Application Gateway.

## Tarefa 1: Usar um template para provisionar uma infraestrutura

Nesta tarefa, você usará um template para implantar uma rede virtual, um network security group e três máquinas virtuais.

1. Baixe os arquivos do laboratório **\\Allfiles\\Lab06** (template e parâmetros).

1. Entre no **Azure portal** - `https://portal.azure.com`.

1. Pesquise por e selecione `Deploy a custom template`.

1. Na página de implantação personalizada, selecione **Build your own template in the editor**.

1. Na página de editar template, selecione **Load file**.

1. Localize e selecione o arquivo **\\Allfiles\\Labs\\06\\az104-06-vms-template.json** e selecione **Open**.

1. Selecione **Save**.

1. Selecione **Edit parameters** e carregue o arquivo **\\Allfiles\\Labs\\06\\az104-06-vms-parameters.json**.

1. Selecione **Save**.

1. Use as seguintes informações para preencher os campos na página de implantação personalizada, deixando todos os outros campos com o valor padrão.

    | Setting       | Value         |
    | ---           | ---           |
    | Subscription  | sua assinatura do Azure |
    | Resource group | `az104-rg6` (Se necessário, selecione **Create new**) |
    | VM size | Selecione um tamanho disponível. Use **Standard_D2s_v5** se disponível. |
    | Password      | Forneça uma senha segura |

1. Selecione **Review + create** e então selecione **Create**.

    > [!NOTE]
    > O template fornece três tamanhos de VM atuais. Comece com **Standard_D2s_v5**. Se a implantação falhar porque o tamanho não está disponível ou o Azure não tem capacidade, selecione **Standard_D2s_v6** e reimplante no mesmo grupo de recursos. Se necessário, tente novamente com **Standard_D2s_v7**. Se uma nova tentativa falhar porque um recurso existente ou parcialmente implantado causa conflito, exclua **az104-rg6**. Reinicie a Tarefa 1 a partir de **Search for and select Deploy a custom template**, recarregue os arquivos de template e parâmetros, selecione **Create new** para recriar **az104-rg6**, e implante novamente com o tamanho de VM selecionado.

    >**Observação**: Aguarde a conclusão da implantação antes de passar para a próxima tarefa. A implantação deve levar aproximadamente 5 minutos.

    >**Observação**: Reveja os recursos que estão sendo implantados. Haverá uma rede virtual com três sub-redes. Cada sub-rede terá uma máquina virtual.

## Tarefa 2: Configurar um Azure Load Balancer

Nesta tarefa, você implementa um Azure Load Balancer na frente de duas máquinas virtuais do Azure na rede virtual. Load Balancers no Azure fornecem conectividade na camada 4 entre recursos, como máquinas virtuais. A configuração do Load Balancer inclui um endereço IP front-end para aceitar conexões, um backend pool e regras que definem como as conexões devem atravessar o load balancer.

## Diagrama de arquitetura - Load Balancer

>**Observação**: Observe que o Load Balancer está distribuindo entre duas máquinas virtuais na mesma rede virtual.

![Diagram of the lab tasks.](../media/az104-lab06-lb-architecture.png)

1. No Azure portal, pesquise e selecione `Load balancers`, então clique em + Create e selecione **Standard load balancer** no menu suspenso. no bloco **Load balancers**, clique em **+ Create**.

1. Crie um load balancer com as seguintes configurações (deixe as demais com os valores padrão) então clique em **Next : Frontend IP configuration**:

    | Setting | Value |
    | --- | --- |
    | Subscription | sua assinatura do Azure |
    | Resource group | **az104-rg6** |
    | Name | `az104-lb` |
    | Region | A **mesma** região onde você implantou as VMs |
    | SKU  | **Standard** |
    | Type | **Public** |
    | Tier | **Regional** |

     ![Screenshot of the create load balancer page.](../media/az104-lab06-create-lb1.png)

1. Na aba **Frontend IP configuration**, clique em **Add a frontend IP configuration** e use as seguintes configurações:

    | Setting | Value |
    | --- | --- |
    | Name | `az104-fe` |
    | IP type | IP address |
    | Gateway Load balancer | None |
    | Public IP address | Selecione **Create new** (use as instruções no próximo passo) |

1. No popup **Add a public IP address**, use as seguintes configurações antes de clicar em **Save** duas vezes. Quando concluído clique em **Next : Backend pools >**.

    | Setting | Value |
    | --- | --- |
    | Name | `az104-lbpip` |
    | SKU | Standard |
    | Tier | Regional |
    | Assignment | Static |
    | Routing Preference | **Microsoft network** |

    >**Observação:** A SKU Standard fornece um endereço IP estático. Endereços IP estáticos são atribuídos quando o recurso é criado e liberados quando o recurso é excluído.

1. Na aba **Backend pools**, clique em **Add a backend pool** com as seguintes configurações (deixe as demais com os valores padrão). Clique em **Add** e então **Save**. Clique em **Next : Inbound rules >**.

    | Setting | Value |
    | --- | --- |
    | Name | `az104-be` |
    | Virtual network | **az104-06-vnet1 (az104-rg6)** |
    | Backend Pool Configuration | **NIC** |
    | Click **Add** to add a virtual machine |  |
    | az104-06-vm0 | **marque a caixa** |
    | az104-06-vm1 | **marque a caixa** |

   > **Nota:** Ao criar o endereço IP público, verifique se a Região corresponde à região onde suas VMs estão implantadas (North Europe). O portal pode pré-selecionar uma região diferente como East US 2 — altere se necessário antes de prosseguir.

1. Se tiver tempo, revise as outras abas, então clique em **Review + create**. Garanta que não haja erros de validação, então clique em **Create**.

1. Aguarde a implantação do load balancer e então clique em **Go to resource**.

**Adicione uma regra para determinar como o tráfego de entrada é distribuído**

1. No bloco **Settings**, selecione **Load balancing rules**.

1. Selecione **+ Add**. Adicione uma regra de balanceamento com as seguintes configurações (deixe as demais com os valores padrão). Ao configurar a regra use os ícones informativos para aprender sobre cada configuração. Quando terminar clique em **Save**.

    | Setting | Value |
    | --- | --- |
    | Name | `az104-lbrule` |
    | IP Version | **IPv4** |
    | Frontend IP Address | **az104-fe** |
    | Backend pool | **az104-be** |
    | Protocol | **TCP** |
    | Port | `80` |
    | Backend port | `80` |
    | Health probe | **Create new** |
    | Name | `az104-hp` |
    | Protocol | **TCP** |
    | Port | `80` |
    | Interval | `5` |
    | Close the create health probe window | **Save** |
    | Session persistence | **None** |
    | Idle timeout (minutes) | `4` |
    | Enable TCP reset | **Disabled** |
    | Enable Floating IP | **Disabled** |
    | Outbound source network address translation (SNAT) | **Recommended** |

1. Selecione **Frontend IP configuration** na página do Load Balancer. Copie o endereço IP público.

1. Abra outra aba do navegador e navegue até o endereço IP. Verifique que a janela do navegador exibe a mensagem **Hello World from az104-06-vm0** ou **Hello World from az104-06-vm1**.

1. Atualize a janela para verificar se a mensagem alterna para a outra máquina virtual. Isso demonstra o load balancer rotacionando entre as máquinas virtuais.

    > **Observação**: Pode ser necessário atualizar mais de uma vez ou abrir uma nova janela do navegador em modo InPrivate.

## Tarefa 3: Configurar um Azure Application Gateway

Nesta tarefa, você implementa um Azure Application Gateway na frente de duas máquinas virtuais do Azure. Um Application Gateway fornece balanceamento de carga na camada 7, Web Application Firewall (WAF), terminação SSL e criptografia de ponta a ponta para os recursos definidos no backend pool. O Application Gateway roteia imagens para uma máquina virtual e vídeos para a outra máquina virtual.

## Diagrama de arquitetura - Application Gateway

>**Observação**: Este Application Gateway está funcionando na mesma rede virtual que o Load Balancer. Isso pode não ser típico em um ambiente de produção.

![Diagram of the lab tasks.](../media/az104-lab06-gw-architecture.png)

1. No Azure portal, pesquise e selecione `Virtual networks`.

1. No bloco **Virtual networks**, na lista de redes virtuais, clique em **az104-06-vnet1**.

1. No bloco da rede virtual **az104-06-vnet1**, na seção **Settings**, clique em **Subnets**, e então clique em **+ Subnet**.

1. Adicione uma sub-rede com as seguintes configurações (deixe as demais com os valores padrão).

    | Setting | Value |
    | --- | --- |
    | Name | `subnet-appgw` |
    | Starting address| `10.60.3.224` |
    | Size | `/27` - Certifique-se de que o **starting address** ainda é **10.60.3.224**|

1. Na seção **Private subnet**, deixe **Enable private subnet (no default outbound access)** marcada.

1. Clique em **Add**.

    > **Observação**: Esta sub-rede será usada pelo Azure Application Gateway. O Application Gateway requer uma sub-rede dedicada de tamanho /27 ou maior.

1. No Azure portal, pesquise e selecione `Application gateways` e, no bloco **Application gateways**, clique em **+ Create**.

1. Na aba **Basics**, especifique as seguintes configurações (deixe as demais com os valores padrão):

    | Setting | Value |
    | --- | --- |
    | Subscription | sua assinatura do Azure |
    | Resource group | `az104-rg6` |
    | Application gateway name | `az104-appgw` |
    | Region | A **mesma** região do Azure que você usou na Tarefa 1 |
    | Tier | **Standard V2** |
    | Enable autoscaling | **No** |
    | Instance count | `2` |
    | IP address type | **IPv4 only**|
    | HTTP2 | **Disabled**
    | FIPS mode 140-2 | deixe padrão|
    | Virtual network | **az104-06-vnet1** |
    | Subnet | **subnet-appgw (10.60.3.224/27)** |

1. Clique em **Next : Frontends >** e especifique as seguintes configurações (deixe as demais com os valores padrão). Quando concluído, clique **OK**.

    | Setting | Value |
    | --- | --- |
    | Frontend IP address type | **Public** |
    | Public IP address| **Add new** |
    | Name | `az104-gwpip` |
    | Availability zone | **ZoneRedundant** |

    >**Observação:** O Application Gateway pode ter tanto um endereço IP público quanto privado.

1. Clique em **Next : Backends >** e então **Add a backend pool**. Especifique as seguintes configurações (deixe as demais com os valores padrão). Quando concluído clique **Add**.

    | Setting | Value |
    | --- | --- |
    | Name | `az104-appgwbe` |
    | Add backend pool without targets | **No** |
    | Virtual machine | **az104-06-nic1 (10.60.1.4)** |
    | Virtual machine | **az104-06-nic2 (10.60.2.4)** |

1. Clique em **Add a backend pool**. Este é o backend pool para **images**. Especifique as seguintes configurações (deixe as demais com os valores padrão). Quando concluído clique **Add**.

    | Setting | Value |
    | --- | --- |
    | Name | `az104-imagebe` |
    | Add backend pool without targets | **No** |
    | Virtual machine | **az104-06-nic1 (10.60.1.4)** |

1. Clique em **Add a backend pool**. Este é o backend pool para **video**. Especifique as seguintes configurações (deixe as demais com os valores padrão). Quando concluído clique **Add**.

    | Setting | Value |
    | --- | --- |
    | Name | `az104-videobe` |
    | Add backend pool without targets | **No** |
    | Virtual machine | **az104-06-nic2 (10.60.2.4)** |

1. Selecione **Next : Configuration >** e então **Add a routing rule**. Complete as informações.

    | Setting | Value |
    | --- | --- |
    | Rule name | `az104-gwrule` |
    | Priority | `10` |
    | Listener name | `az104-listener` |
    | Frontend IP | **Public IPv4** |
    | Protocol | **HTTP** |
    | Port | `80` |
    | Listener type | **Basic** |

1. Vá para a aba **Backend targets**. Selecione **Add** após completar as informações básicas.

   | Setting | Value |
    | --- | --- |
    | Backend target | `az104-appgwbe` |
    | Backend settings | `az104-http` (create new) |

   >**Observação:** Tire um minuto para ler as informações sobre **Cookie-based affinity** e **Connection draining**.

1. Na seção **Path-based routing**, selecione **Add multiple targets to create a path-based rule**. Você criará duas regras. Clique em **Add** após a primeira regra e então **Add** após a segunda regra.

    **Regra - roteando para o backend de images**

    | Setting | Value |
    | --- | --- |
    | Path | `/image/*` |
    | Target name | `images` |
    | Backend settings | **az104-http** |
    | Backend target | `az104-imagebe` |

    **Regra - roteando para o backend de videos**

    | Setting | Value |
    | --- | --- |
    | Path | `/video/*` |
    | Target name | `videos` |
    | Backend settings | **az104-http** |
    | Backend target | `az104-videobe` |

1. Certifique-se de verificar suas alterações, então selecione **Next : Tags >**. Nenhuma alteração é necessária.

1. Selecione **Next : Review + create >** e então clique **Create**.

    > **Observação**: Aguarde a criação da instância do Application Gateway. Isso levará aproximadamente 5-10 minutos. Enquanto aguarda, considere revisar alguns dos links de treinamento autodidata no final desta página.

1. Após o deployment do application gateway, pesquise e selecione **az104-appgw**.

1. No recurso **Application gateway**, na seção **Monitoring**, selecione **Backend health**.

1. Garanta que ambos os servidores no backend pool exibam **Healthy**.

1. No bloco **Overview**, copie o valor do **Frontend public IP address**.

1. Abra outra janela do navegador e teste esta URL - `http://<frontend ip address>/image/`.

1. Verifique que você é direcionado para o servidor de imagens (vm1).

1. Abra outra janela do navegador e teste esta URL - `http://<frontend ip address>/video/`.

1. Verifique que você é direcionado para o servidor de vídeo (vm2).

> **Observação**: Pode ser necessário atualizar mais de uma vez ou abrir uma nova janela do navegador em modo InPrivate.

## Remova seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que o custo seja minimizado. A maneira mais simples de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ No Azure portal, selecione o resource group, selecione **Delete the resource group**, **Enter resource group name**, e então clique em **Delete**. Quando o segundo diálogo de confirmação aparecer, clique em Delete novamente.
+ Usando o Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Amplie seu aprendizado com o Copilot

O Copilot pode ajudar você a aprender como usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.

+ Compare e contraste o Azure Load Balancer com o Azure Application Gateway. Ajude-me a decidir em quais cenários devo usar cada produto.
+ Quais ferramentas estão disponíveis para solucionar problemas de conexões a um Azure Load Balancer?
+ Quais são os passos básicos para configurar o Azure Application Gateway? Forneça um checklist em alto nível.
+ Crie uma tabela destacando três soluções de balanceamento de carga do Azure. Para cada solução mostre protocolos suportados, políticas de roteamento, afinidade de sessão e offloading de TLS.

## Saiba mais com treinamento autodidata

+ [Introduction to Azure Load Balancer](https://learn.microsoft.com/training/modules/intro-to-azure-load-balancer/). Este módulo explica o que o Azure Load Balancer faz, como funciona e quando você deve escolher usar o Load Balancer como solução para atender às necessidades da sua organização.
+ [Introduction to Azure Application Gateway](https://learn.microsoft.com/training/modules/intro-to-azure-application-gateway/). Este módulo explica o que o Azure Application Gateway faz, como funciona e quando você deve escolher usar o Application Gateway como solução para atender às necessidades da sua organização.

## Principais conclusões

Parabéns por concluir o laboratório. Aqui estão os pontos principais deste laboratório.

+ Azure Load Balancer é uma excelente opção para distribuir tráfego de rede entre múltiplas máquinas virtuais na camada de transporte (camada 4 do OSI - TCP e UDP).
+ Load Balancers públicos são usados para balancear o tráfego da internet para suas VMs. Um load balancer interno (ou privado) é usado quando são necessários IPs privados no frontend apenas.
+ O Basic load balancer é para aplicações de pequena escala que não precisam de alta disponibilidade ou redundância. O Standard load balancer é para alto desempenho e latência ultrabaixa.
+ Azure Application Gateway é um balanceador de carga para tráfego web (camada 7 do OSI) que permite gerenciar o tráfego para suas aplicações web.
+ A camada Standard do Application Gateway oferece toda a funcionalidade L7, incluindo balanceamento de carga. A camada WAF adiciona um firewall para verificar tráfego malicioso.
+ Um Application Gateway pode tomar decisões de roteamento com base em atributos adicionais de uma requisição HTTP, por exemplo URI path ou host headers.
