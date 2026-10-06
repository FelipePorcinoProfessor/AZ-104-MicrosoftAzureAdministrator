---
lab:
  title: 'Laboratório 06: Implementar Gerenciamento de Tráfego de Rede'
  module: Administrar Gerenciamento de Tráfego de Rede
  description: Criar e configurar Azure Balanceador de carga e Application Gateway.
  duration: 50 minutes
  level: 400
  islab: true
  primarytopics:
  - Azure
  - Azure Balanceador de carga
  - Azure Application Gateway
layout: default
---

# Lab 06 - Implementar Gerenciamento de Tráfego de Rede

## Introdução ao laboratório

Neste laboratório, você aprenderá como configurar e testar um Balanceador de carga público e um Application Gateway.

Este laboratório requer uma assinatura do Azure. O tipo da sua assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos estão escritos usando **East US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua organização possui um site público. Você precisa balancear as solicitações públicas de entrada entre diferentes máquinas virtuais. Você também precisa fornecer imagens e vídeos a partir de máquinas virtuais diferentes. Você pretende implementar um Azure Balanceador de carga e um Azure Application Gateway. Todos os recursos estão na mesma região.

## Habilidades do trabalho

+ Tarefa 1: Usar um template para provisionar uma infraestrutura.
+ Tarefa 2: Configurar um Azure Balanceador de carga.
+ Tarefa 3: Configurar um Azure Application Gateway.

## Tarefa 1: Usar um template para provisionar uma infraestrutura

Nesta tarefa, você usará um template para implantar uma rede virtual, um network security group e três máquinas virtuais.

1. Baixe os arquivos do laboratório + [\\Allfiles\\Lab06](https://github.com/FelipePorcinoProfessor/AZ-104-MicrosoftAzureAdministrator/tree/master/Allfiles/Labs/06).  (template e parâmetros).

1. Entre no **Azure portal** - `https://portal.azure.com`.

1. Pesquise por e selecione `Deploy a custom template`.

1. Na página de implantação personalizada, selecione **Crie seu próprio template no editor (Build your own template in the editor)**.

1. Na página de editar template, selecione **Carregar arquivo (Load file)**.

1. Localize e selecione o arquivo **\\Allfiles\\Labs\\06\\az104-06-vms-template.json** e selecione **Abrir (Open)**.

1. Selecione **Salvar (Save)**.

1. Selecione **Editar parâmetros (Edit parameters)** e carregue o arquivo **\\Allfiles\\Labs\\06\\az104-06-vms-parameters.json**.

1. Selecione **Salvar (Save)**.

1. Use as seguintes informações para preencher os campos na página de implantação personalizada, deixando todos os outros campos com o valor padrão.

    | Configuração       | Valor         |
    | ---                | ---           |
    | Assinatura (Subscription)  | sua assinatura do Azure |
    | Grupo de recursos (Resource group) | `az104-rg6` (Se necessário, selecione **Criar novo (Create new)**) |
    | Tamanho da VM (VM size) | Selecione um tamanho disponível. Use **Standard_D2s_v5** se disponível. |
    | Senha (Password)      | Forneça uma senha segura |

1. Selecione **Revisar + criar (Review + create)** e então selecione **Criar (Create)**.

    > [!NOTE]
    > O template fornece três tamanhos de VM atuais. Comece com **Standard_D2s_v5**. Se a implantação falhar porque o tamanho não está disponível ou o Azure não tem capacidade, selecione **Standard_D2s_v6** e reimplante no mesmo grupo de recursos. Se necessário, tente novamente com **Standard_D2s_v7**. Se uma nova tentativa falhar porque um recurso existente ou parcialmente implantado causa conflito, exclua **az104-rg6**. Reinicie a Tarefa 1 a partir de **Pesquise e selecione Deploy a custom template**, recarregue os arquivos de template e parâmetros, selecione **Criar novo (Create new)** para recriar **az104-rg6**, e implante novamente com o tamanho de VM selecionado.

    >**Observação**: Aguarde a conclusão da implantação antes de passar para a próxima tarefa. A implantação deve levar aproximadamente 5 minutos.

    >**Observação**: Reveja os recursos que estão sendo implantados. Haverá uma rede virtual com três sub-redes. Cada sub-rede terá uma máquina virtual.

## Tarefa 2: Configurar um Azure Balanceador de carga

Nesta tarefa, você implementa um Azure Balanceador de carga na frente de duas máquinas virtuais do Azure na rede virtual. Balanceador de cargas no Azure fornecem conectividade na camada 4 entre recursos, como máquinas virtuais. A configuração do Balanceador de carga inclui um endereço IP front-end para aceitar conexões, um backend pool e regras que definem como as conexões devem atravessar o Balanceador de carga.

## Diagrama de arquitetura - Balanceador de carga

>**Observação**: Observe que o Balanceador de carga está distribuindo entre duas máquinas virtuais na mesma rede virtual.

![Diagrama das tarefas do laboratório.](../media/az104-lab06-lb-architecture.png)

1. No Azure portal, pesquise e selecione `Balanceador de cargas`, então clique em **+ Criar (+ Create)** e selecione **Standard Balanceador de carga** no menu suspenso. No bloco **Load balancers (Balanceadores de carga)**, clique em **+ Criar (+ Create)**.

1. Crie um Balanceador de carga com as seguintes configurações (deixe as demais com os valores padrão) então clique em **Próximo : Configuração de IP de front-end (Next : Frontend IP configuration)**:

    | Configuração | Valor |
    | --- | --- |
    | Assinatura (Subscription) | sua assinatura do Azure |
    | Grupo de recursos (Resource group) | **az104-rg6** |
    | Nome (Name) | `az104-lb` |
    | Região (Region) | A **mesma** região onde você implantou as VMs |
    | SKU  | **Standard** |
    | Tipo (Type) | **Public** |
    | Camada (Tier) | **Regional** |

     ![Captura de tela da página de criação do Balanceador de carga.](../media/az104-lab06-create-lb1.png)

1. Na aba **Configuração de IP de front-end (Frontend IP configuration)**, clique em **Adicionar uma configuração de IP de front-end (Add a frontend IP configuration)** e use as seguintes configurações:

    | Configuração | Valor |
    | --- | --- |
    | Nome (Name) | `az104-fe` |
    | Tipo de IP (IP type) | IP address |
    | Gateway Balanceador de carga | None |
    | Endereço IP público (Public IP address) | Selecione **Criar novo (Create new)** (use as instruções no próximo passo) |

1. No popup **Adicionar um endereço IP público (Add a public IP address)**, use as seguintes configurações antes de clicar em **Salvar (Save)** duas vezes. Quando concluído clique em **Próximo : Pools de backend (Next : Backend pools >)**.

    | Configuração | Valor |
    | --- | --- |
    | Nome (Name) | `az104-lbpip` |
    | SKU | Standard |
    | Camada (Tier) | Regional |
    | Atribuição (Assignment) | Static |
    | Preferência de roteamento (Routing Preference) | **Microsoft network** |

    >**Observação:** A SKU Standard fornece um endereço IP estático. Endereços IP estáticos são atribuídos quando o recurso é criado e liberados quando o recurso é excluído.

1. Na aba **Pools de backend (Backend pools)**, clique em **Adicionar um pool de backend (Add a backend pool)** com as seguintes configurações (deixe as demais com os valores padrão). Clique em **Adicionar (Add)** e então **Salvar (Save)**. Clique em **Próximo : Regras de entrada (Next : Inbound rules >)**.

    | Configuração | Valor |
    | --- | --- |
    | Nome (Name) | `az104-be` |
    | Rede virtual (Virtual network) | **az104-06-vnet1 (az104-rg6)** |
    | Configuração do pool de backend (Backend Pool Configuration) | **NIC** |
    | Clique em **Adicionar (Add)** para adicionar uma máquina virtual |  |
    | az104-06-vm0 | **marque a caixa (check the box)** |
    | az104-06-vm1 | **marque a caixa (check the box)** |

   > **Nota:** Ao criar o endereço IP público, verifique se a Região corresponde à região onde suas VMs estão implantadas (North Europe). O portal pode pré-selecionar uma região diferente como East US 2 — altere se necessário antes de prosseguir.

1. Se tiver tempo, revise as outras abas, então clique em **Revisar + criar (Review + create)**. Garanta que não haja erros de validação, então clique em **Criar (Create)**.

1. Aguarde a implantação do Balanceador de carga e então clique em **Ir para o recurso (Go to resource)**.

**Adicione uma regra para determinar como o tráfego de entrada é distribuído**

1. No bloco **Configurações (Settings)**, selecione **Regras de balanceamento (Load balancing rules)**.

1. Selecione **+ Adicionar (+ Add)**. Adicione uma regra de balanceamento com as seguintes configurações (deixe as demais com os valores padrão). Ao configurar a regra use os ícones informativos para aprender sobre cada configuração. Quando terminar clique em **Salvar (Save)**.

    | Configuração | Valor |
    | --- | --- |
    | Nome (Name) | `az104-lbrule` |
    | Versão de IP (IP Version) | **IPv4** |
    | Endereço IP de front-end (Frontend IP Address) | **az104-fe** |
    | Pool de backend (Backend pool) | **az104-be** |
    | Protocolo (Protocol) | **TCP** |
    | Porta (Port) | `80` |
    | Porta do backend (Backend port) | `80` |
    | Verificação de integridade (Health probe) | **Criar novo (Create new)** |
    | Nome (Name) | `az104-hp` |
    | Protocolo (Protocol) | **TCP** |
    | Porta (Port) | `80` |
    | Intervalo (Interval) | `5` |
    | Feche a janela de criar verificação de integridade | **Salvar (Save)** |
    | Persistência de sessão (Session persistence) | **Nenhuma (None)** |
    | Tempo limite ocioso (minutos) (Idle timeout (minutes)) | `4` |
    | Ativar reset TCP (Enable TCP reset) | **Desabilitado (Disabled)** |
    | Habilitar IP flutuante (Enable Floating IP) | **Desabilitado (Disabled)** |
    | Tradução de endereço de rede de origem de saída (SNAT) | **Recomendado (Recommended)** |

1. Selecione **Configuração de IP de front-end (Frontend IP configuration)** na página do Balanceador de carga. Copie o endereço IP público.

1. Abra outra aba do navegador e navegue até o endereço IP. Verifique que a janela do navegador exibe a mensagem **Hello World from az104-06-vm0** ou **Hello World from az104-06-vm1**.

1. Atualize a janela para verificar se a mensagem alterna para a outra máquina virtual. Isso demonstra o Balanceador de carga rotacionando entre as máquinas virtuais.

    > **Observação**: Pode ser necessário atualizar mais de uma vez ou abrir uma nova janela do navegador em modo InPrivate.

## Tarefa 3: Configurar um Azure Application Gateway

Nesta tarefa, você implementa um Azure Application Gateway na frente de duas máquinas virtuais do Azure. Um Application Gateway fornece balanceamento de carga na camada 7, Web Application Firewall (WAF), terminação SSL e criptografia de ponta a ponta para os recursos definidos no backend pool. O Application Gateway roteia imagens para uma máquina virtual e vídeos para a outra máquina virtual.

## Diagrama de arquitetura - Application Gateway

>**Observação**: Este Application Gateway está funcionando na mesma rede virtual que o Balanceador de carga. Isso pode não ser típico em um ambiente de produção.

![Diagrama das tarefas do laboratório.](../media/az104-lab06-gw-architecture.png)

1. No Azure portal, pesquise e selecione `Virtual networks`.

1. No bloco **Redes virtuais (Virtual networks)**, na lista de redes virtuais, clique em **az104-06-vnet1**.

1. No bloco da rede virtual **az104-06-vnet1**, na seção **Configurações (Settings)**, clique em **Sub-redes (Subnets)**, e então clique em **+ Sub-rede (+ Subnet)**.

1. Adicione uma sub-rede com as seguintes configurações (deixe as demais com os valores padrão).

    | Configuração | Valor |
    | --- | --- |
    | Nome (Name) | `subnet-appgw` |
    | Endereço inicial (Starting address) | `10.60.3.224` |
    | Tamanho (Size) | `/27` - Certifique-se de que o **endereço inicial (starting address)** ainda é **10.60.3.224**|

1. Na seção **Sub-rede privada (Private subnet)**, deixe **Habilitar sub-rede privada (sem acesso de saída padrão) (Enable private subnet (no default outbound access))** marcada.

1. Clique em **Adicionar (Add)**.

    > **Observação**: Esta sub-rede será usada pelo Azure Application Gateway. O Application Gateway requer uma sub-rede dedicada de tamanho /27 ou maior.

1. No Azure portal, pesquise e selecione `Application gateways` e, no bloco **Application gateways (Gateways de aplicação)**, clique em **+ Criar (+ Create)**.

1. Na aba **Básico (Basics)**, especifique as seguintes configurações (deixe as demais com os valores padrão):

    | Configuração | Valor |
    | --- | --- |
    | Assinatura (Subscription) | sua assinatura do Azure |
    | Grupo de recursos (Resource group) | `az104-rg6` |
    | Nome do Application Gateway (Application gateway name) | `az104-appgw` |
    | Região (Region) | A **mesma** região do Azure que você usou na Tarefa 1 |
    | Camada (Tier) | **Standard V2** |
    | Habilitar escalonamento automático (Enable autoscaling) | **Não (No)** |
    | Contagem de instâncias (Instance count) | `2` |
    | Tipo de endereço IP (IP address type) | **IPv4 only**|
    | HTTP2 | **Desabilitado (Disabled)**
    | Modo FIPS 140-2 (FIPS mode 140-2) | deixe padrão |
    | Rede virtual (Virtual network) | **az104-06-vnet1** |
    | Sub-rede (Subnet) | **subnet-appgw (10.60.3.224/27)** |

1. Clique em **Próximo : Frontends (Next : Frontends >)** e especifique as seguintes configurações (deixe as demais com os valores padrão). Quando concluído, clique **OK**.

    | Configuração | Valor |
    | --- | --- |
    | Tipo de endereço IP de front-end (Frontend IP address type) | **Public** |
    | Endereço IP público (Public IP address) | **Adicionar novo (Add new)** |
    | Nome (Name) | `az104-gwpip` |
    | Zona de disponibilidade (Availability zone) | **ZoneRedundant** |

    >**Observação:** O Application Gateway pode ter tanto um endereço IP público quanto privado.

1. Clique em **Próximo : Backends (Next : Backends >)** e então **Adicionar um pool de backend (Add a backend pool)**. Especifique as seguintes configurações (deixe as demais com os valores padrão). Quando concluído clique **Adicionar (Add)**.

    | Configuração | Valor |
    | --- | --- |
    | Nome (Name) | `az104-appgwbe` |
    | Adicionar pool de backend sem alvos (Add backend pool without targets) | **Não (No)** |
    | Máquina virtual (Virtual machine) | **az104-06-nic1 (10.60.1.4)** |
    | Máquina virtual (Virtual machine) | **az104-06-nic2 (10.60.2.4)** |

1. Clique em **Adicionar um pool de backend (Add a backend pool)**. Este é o pool de backend para **images**. Especifique as seguintes configurações (deixe as demais com os valores padrão). Quando concluído clique **Adicionar (Add)**.

    | Configuração | Valor |
    | --- | --- |
    | Nome (Name) | `az104-imagebe` |
    | Adicionar pool de backend sem alvos (Add backend pool without targets) | **Não (No)** |
    | Máquina virtual (Virtual machine) | **az104-06-nic1 (10.60.1.4)** |

1. Clique em **Adicionar um pool de backend (Add a backend pool)**. Este é o pool de backend para **video**. Especifique as seguintes configurações (deixe as demais com os valores padrão). Quando concluído clique **Adicionar (Add)**.

    | Configuração | Valor |
    | --- | --- |
    | Nome (Name) | `az104-videobe` |
    | Adicionar pool de backend sem alvos (Add backend pool without targets) | **Não (No)** |
    | Máquina virtual (Virtual machine) | **az104-06-nic2 (10.60.2.4)** |

1. Selecione **Próximo : Configuração (Next : Configuration >)** e então **Adicionar uma regra de roteamento (Add a routing rule)**. Complete as informações.

    | Configuração | Valor |
    | --- | --- |
    | Nome da regra (Rule name) | `az104-gwrule` |
    | Prioridade (Priority) | `10` |
    | Nome do listener (Listener name) | `az104-listener` |
    | IP de front-end (Frontend IP) | **Public IPv4** |
    | Protocolo (Protocol) | **HTTP** |
    | Porta (Port) | `80` |
    | Tipo de listener (Listener type) | **Básico (Basic)** |

1. Vá para a aba **Alvos de backend (Backend targets)**. Selecione **Adicionar (Add)** após completar as informações básicas.

   | Configuração | Valor |
    | --- | --- |
    | Alvo de backend (Backend target) | `az104-appgwbe` |
    | Configurações de backend (Backend settings) | `az104-http` (criar novo) |

   >**Observação:** Tire um minuto para ler as informações sobre **Afinidade baseada em cookie (Cookie-based affinity)** e **Drenagem de conexão (Connection draining)**.

1. Na seção **Roteamento baseado em caminho (Path-based routing)**, selecione **Adicionar múltiplos alvos para criar uma regra baseada em caminho (Add multiple targets to create a path-based rule)**. Você criará duas regras. Clique em **Adicionar (Add)** após a primeira regra e então **Adicionar (Add)** após a segunda regra.

    **Regra - roteando para o backend de images**

    | Configuração | Valor |
    | --- | --- |
    | Caminho (Path) | `/image/*` |
    | Nome do alvo (Target name) | `images` |
    | Configurações de backend (Backend settings) | **az104-http** |
    | Alvo de backend (Backend target) | `az104-imagebe` |

    **Regra - roteando para o backend de videos**

    | Configuração | Valor |
    | --- | --- |
    | Caminho (Path) | `/video/*` |
    | Nome do alvo (Target name) | `videos` |
    | Configurações de backend (Backend settings) | **az104-http** |
    | Alvo de backend (Backend target) | `az104-videobe` |

1. Certifique-se de verificar suas alterações, então selecione **Próximo : Tags (Next : Tags >)**. Nenhuma alteração é necessária.

1. Selecione **Próximo : Revisar + criar (Next : Review + create >)** e então clique **Criar (Create)**.

    > **Observação**: Aguarde a criação da instância do Application Gateway. Isso levará aproximadamente 5-10 minutos. Enquanto aguarda, considere revisar alguns dos links de treinamento autodidata no final desta página.

1. Após o deployment do application gateway, pesquise e selecione **az104-appgw**.

1. No recurso **Application gateway**, na seção **Monitoramento (Monitoring)**, selecione **Integridade do backend (Backend health)**.

1. Garanta que ambos os servidores no backend pool exibam **Healthy**.

1. No bloco **Visão geral (Overview)**, copie o valor do **Endereço IP público de front-end (Frontend public IP address)**.

1. Abra outra janela do navegador e teste esta URL - `http://<frontend ip address>/image/`.

1. Verifique que você é direcionado para o servidor de imagens (vm1).

1. Abra outra janela do navegador e teste esta URL - `http://<frontend ip address>/video/`.

1. Verifique que você é direcionado para o servidor de vídeo (vm2).

> **Observação**: Pode ser necessário atualizar mais de uma vez ou abrir uma nova janela do navegador em modo InPrivate.

## Remova seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que o custo seja minimizado. A maneira mais simples de excluir os recursos do laboratório é excluir o grupo de recursos do laboratório.

+ No Azure portal, selecione o grupo de recursos (resource group), selecione **Excluir o grupo de recursos (Delete the resource group)**, **Digite o nome do grupo de recursos (Enter resource group name)**, e então clique em **Excluir (Delete)**. Quando o segundo diálogo de confirmação aparecer, clique em **Excluir (Delete)** novamente.
+ Usando o Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Amplie seu aprendizado com o Copilot

O Copilot pode ajudar você a aprender como usar as ferramentas de script do Azure. O Copilot também pode ajudar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para experimentar estes prompts.

+ Compare e contraste o Azure Balanceador de carga com o Azure Application Gateway. Ajude-me a decidir em quais cenários devo usar cada produto.
+ Quais ferramentas estão disponíveis para solucionar problemas de conexões a um Azure Balanceador de carga?
+ Quais são os passos básicos para configurar o Azure Application Gateway? Forneça uma lista de verificação em alto nível.
+ Crie uma tabela destacando três soluções de balanceamento de carga do Azure. Para cada solução mostre protocolos suportados, políticas de roteamento, afinidade de sessão e offloading de TLS.

## Saiba mais com treinamento autodidata

+ [Introdução ao Azure Balanceador de carga](https://learn.microsoft.com/training/modules/intro-to-azure-load-balancer/). Este módulo explica o que o Azure Balanceador de carga faz, como funciona e quando você deve escolher usar o Balanceador de carga como solução para atender às necessidades da sua organização.
+ [Introdução ao Azure Application Gateway](https://learn.microsoft.com/training/modules/intro-to-azure-application-gateway/). Este módulo explica o que o Azure Application Gateway faz, como funciona e quando você deve escolher usar o Application Gateway como solução para atender às necessidades da sua organização.

## Principais conclusões

Parabéns por concluir o laboratório. Aqui estão os pontos principais deste laboratório.

+ Azure Balanceador de carga é uma excelente opção para distribuir tráfego de rede entre múltiplas máquinas virtuais na camada de transporte (camada 4 do OSI - TCP e UDP).
+ Balanceador de cargas públicos são usados para balancear o tráfego da internet para suas VMs. Um Balanceador de carga interno (ou privado) é usado quando são necessários IPs privados no frontend apenas.
+ O Basic Balanceador de carga é para aplicações de pequena escala que não precisam de alta disponibilidade ou redundância. O Standard Balanceador de carga é para alto desempenho e latência ultrabaixa.
+ Azure Application Gateway é um balanceador de carga para tráfego web (camada 7 do OSI) que permite gerenciar o tráfego para suas aplicações web.
+ A camada Standard do Application Gateway oferece toda a funcionalidade L7, incluindo balanceamento de carga. A camada WAF adiciona um firewall para verificar tráfego malicioso.
+ Um Application Gateway pode tomar decisões de roteamento com base em atributos adicionais de uma requisição HTTP, por exemplo URI path ou host headers.
