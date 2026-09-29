---
lab:
  title: 'Laboratório 04: Implementar Redes Virtuais'
  module: Implement Virtual Networking
  description: Configure virtual networks, network security groups, and DNS zones.
  duration: 50 minutes
  level: 400
  islab: true
  primarytopics:
  - Azure
  - Virtual networks
  - Network security groups
  - Azure DNS
layout: default
---

# Lab 04 - Implement Virtual Networking

## Introdução do laboratório

Este laboratório é o primeiro de três laboratórios que foca em virtual networking. Neste laboratório, você aprenderá o básico de virtual networking e subnetting. Você aprenderá como proteger sua rede com network security groups e application security groups. Você também aprenderá sobre DNS zones e records.

Este laboratório requer uma assinatura do Azure. Seu tipo de assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos foram escritos usando **East US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua organização global planeja implementar virtual networks. O objetivo imediato é acomodar todos os recursos existentes. No entanto, a organização está em fase de crescimento e quer garantir capacidade adicional para esse crescimento.

A virtual network **CoreServicesVnet** tem o maior número de recursos. Uma grande quantidade de crescimento é prevista, então um espaço de endereço grande é necessário para esta virtual network.

A virtual network **ManufacturingVnet** contém sistemas para as operações das instalações de fabricação. A organização prevê um grande número de dispositivos internos conectados para que seus sistemas obtenham dados.

## Diagrama de arquitetura

![Network layout](../media/az104-lab04-architecture.png)

Essas virtual networks e subnets são estruturadas de forma a acomodar os recursos existentes e permitir o crescimento projetado. Vamos criar essas virtual networks e subnets para estabelecer a base da nossa infraestrutura de rede.

>**Você sabia?**: É uma boa prática evitar sobreposição de intervalos de endereços IP para reduzir problemas e simplificar a solução de problemas. A sobreposição é uma preocupação em toda a rede, seja na cloud ou on-premises. Muitas organizações projetam um esquema de endereçamento IP em nível empresarial para evitar sobreposição e planejar crescimento futuro.

## Competências desenvolvidas

+ Tarefa 1: Criar uma virtual network com subnets usando o portal.
+ Tarefa 2: Criar uma virtual network e subnets usando um template.
+ Tarefa 3: Criar e configurar comunicação entre um Application Security Group e um Network Security Group.
+ Tarefa 4: Configurar public e private Azure DNS zones.

## Tarefa 1: Criar uma virtual network com subnets usando o portal

A organização planeja um grande crescimento para os serviços centrais. Nesta tarefa, você criará a virtual network e as subnets associadas para acomodar os recursos existentes e o crescimento planejado. Nesta tarefa, você usará o Azure portal.

1. Faça login no **Azure portal** - `https://portal.azure.com`.

1. Procure por e selecione `Virtual Networks`.

1. Selecione **Create** na página Virtual networks.

1. Complete a guia **Basics** para o CoreServicesVnet.

	|  **Option**         | **Value**            |
	| ------------------ | -------------------- |
	| Resource Group     | `az104-rg4` (se necessário, criar novo) |
	| Name               | `CoreServicesVnet`     |
	| Region             | (US) **East US**         |

    >**Observação:** Se a implantação falhar devido a limites de capacidade ou cota, ajuste a configuração ou escolha uma região diferente.

1. Vá para a guia **Address space**.

	|  **Option**         | **Value**            |
	| ------------------ | -------------------- |
	| IPv4 address space | Substitua o IPv4 address space pré-populado por `10.20.0.0/16` (separe as entradas)  |

1. Selecione **+ Add a subnet**. Complete o nome e as informações de endereço para cada subnet. Certifique-se de selecionar **Add** para cada nova subnet.

	>**Observação:** Certifique-se de excluir o subnet padrão - seja antes ou depois de criar os outros subnets.

	| **Subnet**             | **Option**           | **Value**              |
	| ---------------------- | -------------------- | ---------------------- |
	| SharedServicesSubnet   | Name          | `SharedServicesSubnet`   |
	|                        | Starting address		| `10.20.10.0`          |
	|						 | Size					| `/24`	|
	| DatabaseSubnet         | Name          | `DatabaseSubnet`         |
	|                        | Starting address		| `10.20.20.0`        |
	|						 | Size					| `/24`	|

	>**Observação:** Toda virtual network deve ter pelo menos um subnet. Lembre-se que cinco endereços IP serão sempre reservados, então considere isso no seu planejamento.

1. Para finalizar a criação do CoreServicesVnet e seus subnets associados, selecione **Review + create**.

1. Verifique se sua configuração passou na validação e, em seguida, selecione **Create**.

1. Aguarde a implantação da virtual network e então selecione **Go to resource**.

1. Reserve um minuto para verificar o **Address space** e os **Subnets**. Observe suas outras escolhas na lâmina **Settings**.

1. Na seção **Automation**, selecione **Export template**, e então aguarde o template ser gerado.

1. Selecione a guia **Template** e **Download** o template. Em seguida, alterne para a guia **Parameters** e repita a operação **Download**.

1. Navegue na máquina local até a pasta **Downloads**.

1. Antes de prosseguir, certifique-se de que você tem o arquivo **template.json**. Você usará este template para criar o ManufacturingVnet na próxima tarefa.

## Tarefa 2: Criar uma virtual network e subnets usando um template

Nesta tarefa, você criará a virtual network ManufacturingVnet e os subnets associados. A organização prevê crescimento para os escritórios de manufatura, portanto os subnets são dimensionados para o crescimento esperado. Para esta tarefa, você usará um template para criar os recursos.

1. Localize o arquivo **template.json** exportado na tarefa anterior. Deve estar na sua pasta **Downloads**.

1. Edite o arquivo usando o editor de sua escolha. Muitos editores têm um recurso de *change all occurrences*. Se você estiver usando Visual Studio Code, certifique-se de que está trabalhando em uma **trusted window** e não em **restricted mode**. Consulte o diagrama de arquitetura para verificar os detalhes.

### Faça alterações para a virtual network ManufacturingVnet

1. Substitua todas as ocorrências de **CoreServicesVnet** por `ManufacturingVnet`.

1. Substitua todas as ocorrências de **10.20.0.0** por `10.30.0.0`.

### Faça alterações para os subnets do ManufacturingVnet

1. Altere todas as ocorrências de **SharedServicesSubnet** para `SensorSubnet1`.

1. Altere todas as ocorrências de **10.20.10.0/24** para `10.30.20.0/24`.

1. Altere todas as ocorrências de **DatabaseSubnet** para `SensorSubnet2`.

1. Altere todas as ocorrências de **10.20.20.0/24** para `10.30.21.0/24`.

1. Leia o arquivo novamente e certifique-se de que tudo esteja correto. Use o diagrama de arquitetura para nomes de recursos e endereços IP.

1. Certifique-se de **Salvar** suas alterações.

>**Observação:** Existem arquivos de template prontos no diretório de arquivos do laboratório.

### Faça alterações no arquivo de parâmetros

1. Localize o arquivo **parameters.json** exportado na tarefa anterior. Deve estar na sua pasta **Downloads**.

1. Edite o arquivo usando o editor de sua escolha.

1. Substitua a única ocorrência de **CoreServicesVnet** por `ManufacturingVnet`.

1. **Salve** suas alterações.

### Implantar o template personalizado

1. No portal, pesquise por e selecione `Deploy a custom template`.

1. Selecione **Build your own template in the editor** e então **Load file**.

1. Selecione o arquivo **template.json** com suas alterações para Manufacturing e, em seguida, selecione **Save**.

1. Selecione **Edit parameters**, e então **Load file**.

1. Selecione o arquivo **parameters.json** com suas alterações para Manufacturing e, em seguida, selecione **Save**.

1. Certifique-se de que seu resource group, **az104-rg4**, esteja selecionado.

1. Selecione **Review + create** e então **Create**.

1. Aguarde o template ser implantado, então confirme (no portal) que a virtual network e os subnets Manufacturing foram criados.

>**Observação:** Se for necessário implantar mais de uma vez, você poderá descobrir que alguns recursos foram concluídos com sucesso e a implantação está falhando. Você pode remover manualmente esses recursos e tentar novamente.

## Tarefa 3: Criar e configurar comunicação entre um Application Security Group e um Network Security Group

Nesta tarefa, criaremos um Application Security Group e um Network Security Group. O NSG terá uma regra de segurança de entrada que permite tráfego do ASG. O NSG também terá uma regra de saída que nega acesso à Internet.

### Criar o Application Security Group (ASG)

1. No Azure portal, pesquise por e selecione `Application security groups`.

1. Clique em **Create** e forneça as informações básicas.

    | Setting | Value |
    | -- | -- |
    | Subscription | *sua assinatura* |
    | Resource group | **az104-rg4** |
    | Name | `asg-web` |
    | Region | **East US**  |

1. Clique em **Review + create** e então, após a validação, clique em **Create**.

>**Observação:** Neste ponto, você associaria o ASG com máquina(s) virtual(is). Essas máquinas serão afetadas pela regra de entrada do NSG que você criará na próxima tarefa.

### Criar o Network Security Group e associá-lo ao CoreServicesVnet

1. No Azure portal, pesquise por e selecione `Network security groups`.

>**Observação:** Você também pode localizar este recurso usando o menu do Azure portal (ícone no canto superior esquerdo). Selecione **Create a resource** e então no blade **Networking**, selecione **Network security group**.

1. Selecione **+ Create** e forneça informações na guia **Basics**.

    | Setting | Value |
    | -- | -- |
    | Subscription | *sua assinatura* |
    | Resource group | **az104-rg4** |
    | Name | `myNSGSecure` |
    | Region | **East US**  |

1. Clique em **Review + create** e então, após a validação, clique em **Create**.

1. Após a implantação do NSG, clique em **Go to resource**.

1. Em **Settings** clique em **Subnets** e então **Associate**.

    | Setting | Value |
    | -- | -- |
    | Virtual network | **CoreServicesVnet (az104-rg4)** |
    | Subnet | **SharedServicesSubnet** |

1. Clique em **OK** para salvar a associação.

### Configurar uma regra de segurança de entrada para permitir tráfego do ASG

1. Continue trabalhando com seu NSG. Na área **Settings**, selecione **Inbound security rules**.

1. Revise as regras de entrada padrão. Observe que apenas outras virtual networks e load balancers têm acesso permitido.

1. Selecione **+ Add**.

1. No blade **Add inbound security rule**, use as seguintes informações para adicionar uma regra de porta de entrada. Esta regra permite tráfego do ASG. Quando terminar, selecione **Add**.

    | Setting | Value |
    | -- | -- |
    | Source | **Application security group** |
    | Source application security groups | **asg-web** |
    | Source port ranges |  * |
    | Destination | **Any** |
    | Service | **Custom** (observe suas outras escolhas) |
    | Destination port ranges | **80,443** |
    | Protocol | **TCP** |
    | Action | **Allow** |
    | Priority | **100** |
    | Name | `AllowASG` |

### Configurar uma regra de saída do NSG que nega acesso à Internet

1. Depois de criar sua regra de entrada do NSG, selecione **Outbound security rules**.

1. Observe a regra **AllowInternetOutBound**. Também observe que a regra não pode ser excluída e a prioridade é 65001.

1. Selecione **+ Add** e então configure uma regra de saída que negue acesso à Internet. Quando terminar, selecione **Add**.

    | Setting | Value |
    | -- | -- |
    | Source | **Any** |
    | Source port ranges |  * |
    | Destination | **Service tag** |
    | Destination service tag | **Internet** |
    | Service | **Custom** |
    | Destination port ranges | `*` |
    | Protocol | **Any** |
    | Action | **Deny** |
    | Priority | **4096** |
    | Name | `DenyInternetOutbound` |


## Tarefa 4: Configurar public e private Azure DNS zones

Nesta tarefa, você criará e configurará public e private DNS zones.

### Configurar uma DNS zone pública

Você pode configurar Azure DNS para resolver nomes de host no seu domínio público. Por exemplo, se você comprou o domínio contoso.xyz de um registrador de domínios, você pode configurar Azure DNS para hospedar o domínio `contoso.com` e resolver www.contoso.xyz para o endereço IP do seu web server ou web app.

1. No portal, pesquise por e selecione `DNS zones`.

1. Selecione **+ Create**.

1. Configure a guia **Basics**.

    | Property | Value    |
    |:---------|:---------|
    | Subscription | **Selecione sua assinatura** |
    | Resource group | **az104-rg4** |
    | Name | `contoso.com` (este nome deve ser único, contoso.com está reservado então altere para outro.) |
    | Region |**East US** (revise o ícone informativo) |

1. Selecione **Review + create** e então **Create**.

1. Aguarde a DNS zone ser implantada e então selecione **Go to resource**.

1. Na lâmina **Overview** observe os nomes dos quatro name servers do Azure DNS atribuídos à zone. **Copie** um dos endereços do name server. Você precisará dele em um passo futuro para o comando nslookup abaixo..

1. Expanda o blade **DNS Management** e selecione **Recordsets**. Clique em **+Add**.

    | Property | Value    |
    |:---------|:---------|
    | Name | **www** |
    | Type | **A** |
    | Alias record set | **No** |
    | TTL | **1** |
    | IP address | **10.1.1.4** |

>**Observação:** Em um cenário real, você inseriria o endereço IP público do seu web server.

1. Selecione **Add** e verifique se seu domínio tem um A record chamado **www**.

1. Abra um prompt de comando e execute o seguinte comando. Se você tiver alterado o nome de domínio, faça o ajuste.

   ```sh
   nslookup www.contosoxyz104.com <name server name you copied in step 6 above>
   ```
1. Verifique se o host www.contosoxyz104.com resolve para o endereço IP que você forneceu. Isso confirma que a resolução de nomes está funcionando corretamente.

### Configurar uma DNS zone privada

Uma private DNS zone fornece serviços de resolução de nomes dentro de virtual networks. Uma private DNS zone só é acessível a partir das virtual networks às quais ela está vinculada e não pode ser acessada pela internet.

1. No portal, pesquise por e selecione `Private dns zones`.

1. Selecione **+ Create**.

1. Na guia **Basics** de Create private DNS zone, insira as informações conforme listado na tabela abaixo:

    | Property | Value    |
    |:---------|:---------|
    | Subscription | **Selecione sua assinatura** |
    | Resource group | **az104-rg4** |
    | Name | `private.contoso.com` (ajuste se você teve que renomear) |
    | Region |**East US** |

1. Selecione **Review + create** e então **Create**.

1. Aguarde a DNS zone ser implantada e então selecione **Go to resource**.

1. Observe na lâmina **Overview** que não há name server records.

1. Expanda o blade **DNS Management** e então selecione **Virtual network links**. Configure o link.

    | Property | Value    |
    |:---------|:---------|
    | Link name | `manufacturing-link` |
    | Virtual network | `ManufacturingVnet` |

1. Selecione **Create** e aguarde a criação do link.

1. No blade **DNS Management** selecione **+ Recordsets**. Agora você adicionaria um record para cada virtual machine que precisa de suporte a resolução de nome privada.

    | Property | Value    |
    |:---------|:---------|
    | Name | **sensorvm** |
    | Type | **A** |
    | TTL | **1** |
    | IP address | **10.1.1.4** |

 >**Observação:** Em um cenário real, você inseriria o endereço IP para uma VM específica de manufatura.

## Limpar seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que o custo seja minimizado. A forma mais fácil de excluir os recursos do laboratório é excluir o resource group do laboratório.

+ No Azure portal, selecione o resource group, selecione **Delete the resource group**, **Enter resource group name**, e então clique **Delete**.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Expanda seu aprendizado com o Copilot

Copilot pode ajudá-lo a aprender como usar as ferramentas de script do Azure. O Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (no canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para testar estes prompts.
+ Compartilhe as 10 melhores práticas ao implantar e configurar uma virtual network no Azure.
+ Como usar Azure PowerShell e Azure CLI para criar uma virtual network com um public IP address e um subnet.
+ Explique as regras inbound e outbound do Azure Network Security Group e como elas são usadas.
+ Qual é a diferença entre Azure Network Security Groups e Azure Application Security Groups? Compartilhe exemplos de quando usar cada um desses grupos.
+ Forneça um guia passo a passo sobre como diagnosticar quaisquer problemas de rede enfrentados ao implantar uma rede no Azure. Também compartilhe o raciocínio usado em cada etapa do troubleshooting.

## Aprenda mais com treinamento autodirigido

+ [Introduction to Azure Virtual Networks](https://learn.microsoft.com/training/modules/introduction-to-azure-virtual-networks/). Design and implement core Azure Networking infrastructure such as virtual networks, public and private IPs, DNS, virtual network peering, routing, and Azure Virtual NAT.
+ [Secure and isolate access to Azure resources by using network security groups and service endpoints](https://learn.microsoft.com/training/modules/secure-and-isolate-with-nsg-and-service-endpoints/). Network security groups and service endpoints help you secure your virtual machines and Azure services from unauthorized network access.
+ [Host your domain on Azure DNS](https://learn.microsoft.com/training/modules/host-domain-azure-dns/). Create a DNS zone for your domain name. Create DNS records to map the domain to an IP address. Test that the domain name resolves to your web server.

## Principais conclusões

Parabéns por concluir o laboratório. Aqui estão os principais aprendizados deste laboratório.

+ Uma virtual network é uma representação da sua própria rede na cloud.
+ Ao projetar virtual networks é uma boa prática evitar sobreposição de intervalos de endereços IP. Isso reduzirá problemas e simplificará a solução de problemas.
+ Um subnet é um intervalo de endereços IP na virtual network. Você pode dividir uma virtual network em vários subnets para organização e segurança.
+ Um network security group contém regras de segurança que permitem ou negam o tráfego de rede. Existem regras de entrada e saída padrão que você pode personalizar conforme suas necessidades.
+ Application security groups são usados para proteger grupos de servidores com uma função comum, como web servers ou database servers.
+ Azure DNS é um serviço de hospedagem para domínios DNS que fornece resolução de nomes. Você pode configurar Azure DNS para resolver nomes de host no seu domínio público. Você também pode usar private DNS zones para atribuir nomes DNS às virtual machines (VMs) em suas virtual networks do Azure.
