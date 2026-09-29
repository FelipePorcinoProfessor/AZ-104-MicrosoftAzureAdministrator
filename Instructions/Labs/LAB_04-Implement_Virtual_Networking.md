---
lab:
  title: 'Laboratório 04: Implementar Redes Virtuais'
  module: Implementar Redes Virtuais
  description: Configure redes virtuais, grupos de segurança de rede e zonas DNS.
  duration: 50 minutes
  level: 400
  islab: true
  primarytopics:
  - Azure
  - Redes virtuais
  - Grupos de segurança de rede
  - Azure DNS
layout: default
---

# Laboratório 04 - Implementar Redes Virtuais

## Introdução do laboratório

Este laboratório é o primeiro de três laboratórios que foca em redes virtuais. Neste laboratório, você aprenderá o básico de redes virtuais e subdivisão de sub-redes (subnetting). Você aprenderá como proteger sua rede com Network Security Groups (grupos de segurança de rede) e Application Security Groups (grupos de segurança de aplicação). Você também aprenderá sobre zonas DNS e registros.

Este laboratório requer uma assinatura do Azure. Seu tipo de assinatura pode afetar a disponibilidade de recursos neste laboratório. Você pode alterar a região, mas os passos foram escritos usando **East US**.

## Tempo estimado: 50 minutos

## Cenário do laboratório

Sua organização global planeja implementar redes virtuais. O objetivo imediato é acomodar todos os recursos existentes. No entanto, a organização está em fase de crescimento e quer garantir capacidade adicional para esse crescimento.

A rede virtual **CoreServicesVnet** tem o maior número de recursos. Uma grande quantidade de crescimento é prevista, então um espaço de endereço grande é necessário para esta rede virtual.

A rede virtual **ManufacturingVnet** contém sistemas para as operações das instalações de fabricação. A organização prevê um grande número de dispositivos internos conectados para que seus sistemas obtenham dados.

## Diagrama de arquitetura

![Network layout](../media/az104-lab04-architecture.png)

Essas redes virtuais e sub-redes são estruturadas de forma a acomodar os recursos existentes e permitir o crescimento projetado. Vamos criar essas redes virtuais e sub-redes para estabelecer a base da nossa infraestrutura de rede.

>**Você sabia?**: É uma boa prática evitar sobreposição de intervalos de endereços IP para reduzir problemas e simplificar a solução de problemas. A sobreposição é uma preocupação em toda a rede, seja na nuvem ou on-premises. Muitas organizações projetam um esquema de endereçamento IP em nível empresarial para evitar sobreposição e planejar crescimento futuro.

## Competências desenvolvidas

+ Tarefa 1: Criar uma rede virtual com sub-redes usando o portal.
+ Tarefa 2: Criar uma rede virtual e sub-redes usando um template.
+ Tarefa 3: Criar e configurar comunicação entre um Application Security Group (ASG) e um Network Security Group (NSG).
+ Tarefa 4: Configurar zonas DNS públicas e privadas no Azure.

## Tarefa 1: Criar uma rede virtual com sub-redes usando o portal

A organização planeja um grande crescimento para os serviços centrais. Nesta tarefa, você criará a rede virtual e as sub-redes associadas para acomodar os recursos existentes e o crescimento planejado. Nesta tarefa, você usará o Azure portal.

1. Faça login no **Azure portal** - `https://portal.azure.com`.

1. Procure por e selecione `Virtual Networks`.

1. Selecione **Criar** na página Virtual networks.

1. Complete a guia **Básicos** para o CoreServicesVnet.

	|  **Opção**         | **Valor**            |
	| ------------------ | -------------------- |
	| Grupo de recursos  | `az104-rg4` (se necessário, criar novo) |
	| Nome               | `CoreServicesVnet`     |
	| Região             | (US) **East US**         |

    >**Observação:** Se a implantação falhar devido a limites de capacidade ou cota, ajuste a configuração ou escolha uma região diferente.

1. Vá para a guia **Espaço de endereço**.

	|  **Opção**         | **Valor**            |
	| ------------------ | -------------------- |
	| Espaço de endereços IPv4 | Substitua o espaço de endereços IPv4 pré-populado por `10.20.0.0/16` (separe as entradas)  |

1. Selecione **+ Adicionar uma sub-rede**. Complete o nome e as informações de endereço para cada sub-rede. Certifique-se de selecionar **Adicionar** para cada nova sub-rede.

	>**Observação:** Certifique-se de excluir a sub-rede padrão - seja antes ou depois de criar as outras sub-redes.

	| **Sub-rede**             | **Opção**           | **Valor**              |
	| ---------------------- | -------------------- | ---------------------- |
	| SharedServicesSubnet   | Nome                 | `SharedServicesSubnet`   |
	|                        | Endereço inicial     | `10.20.10.0`          |
	|                        | Tamanho              | `/24`	|
	| DatabaseSubnet         | Nome                 | `DatabaseSubnet`         |
	|                        | Endereço inicial     | `10.20.20.0`        |
	|                        | Tamanho              | `/24`	|

	>**Observação:** Toda rede virtual deve ter pelo menos uma sub-rede. Lembre-se que cinco endereços IP serão sempre reservados, então considere isso no seu planejamento.

1. Para finalizar a criação do CoreServicesVnet e suas sub-redes associadas, selecione **Revisar + criar**.

1. Verifique se sua configuração passou na validação e, em seguida, selecione **Criar**.

1. Aguarde a implantação da rede virtual e então selecione **Ir para o recurso**.

1. Reserve um minuto para verificar o **Espaço de endereços** e as **Sub-redes**. Observe suas outras escolhas na lâmina **Configurações**.

1. Na seção **Automação**, selecione **Exportar modelo**, e então aguarde o template ser gerado.

1. Selecione a guia **Modelo** e **Download** o template. Em seguida, alterne para a guia **Parâmetros** e repita a operação **Download**.

1. Navegue na máquina local até a pasta **Downloads**.

1. Antes de prosseguir, certifique-se de que você tem o arquivo **template.json**. Você usará este template para criar a ManufacturingVnet na próxima tarefa.

## Tarefa 2: Criar uma rede virtual e sub-redes usando um template

Nesta tarefa, você criará a rede virtual ManufacturingVnet e as sub-redes associadas. A organização prevê crescimento para os escritórios de manufatura, portanto as sub-redes são dimensionadas para o crescimento esperado. Para esta tarefa, você usará um template para criar os recursos.

1. Localize o arquivo **template.json** exportado na tarefa anterior. Deve estar na sua pasta **Downloads**.

1. Edite o arquivo usando o editor de sua escolha. Muitos editores têm um recurso para alterar todas as ocorrências (*alterar todas as ocorrências*). Se você estiver usando Visual Studio Code, certifique-se de que está trabalhando em uma **janela confiável (trusted window)** e não em **modo restrito (restricted mode)**. Consulte o diagrama de arquitetura para verificar os detalhes.

### Faça alterações para a rede virtual ManufacturingVnet

1. Substitua todas as ocorrências de **CoreServicesVnet** por `ManufacturingVnet`.

1. Substitua todas as ocorrências de **10.20.0.0** por `10.30.0.0`.

### Faça alterações para as sub-redes do ManufacturingVnet

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

1. Selecione **Criar seu próprio modelo no editor** e então **Carregar arquivo**.

1. Selecione o arquivo **template.json** com suas alterações para Manufacturing e, em seguida, selecione **Salvar**.

1. Selecione **Editar parâmetros**, e então **Carregar arquivo**.

1. Selecione o arquivo **parameters.json** com suas alterações para Manufacturing e, em seguida, selecione **Salvar**.

1. Certifique-se de que seu grupo de recursos, **az104-rg4**, esteja selecionado.

1. Selecione **Revisar + criar** e então **Criar**.

1. Aguarde o template ser implantado, então confirme (no portal) que a rede virtual e as sub-redes Manufacturing foram criadas.

>**Observação:** Se for necessário implantar mais de uma vez, você poderá descobrir que alguns recursos foram concluídos com sucesso e a implantação está falhando. Você pode remover manualmente esses recursos e tentar novamente.

## Tarefa 3: Criar e configurar comunicação entre um Application Security Group e um Network Security Group

Nesta tarefa, criaremos um Application Security Group (ASG) e um Network Security Group (NSG). O NSG terá uma regra de segurança de entrada que permite tráfego do ASG. O NSG também terá uma regra de saída que nega acesso à Internet.

### Criar o Application Security Group (ASG)

1. No Azure portal, pesquise por e selecione `Application security groups`.

1. Clique em **Criar** e forneça as informações básicas.

    | Configuração | Valor |
    | -- | -- |
    | Assinatura | *sua assinatura* |
    | Grupo de recursos | **az104-rg4** |
    | Nome | `asg-web` |
    | Região | **East US**  |

1. Clique em **Revisar + criar** e então, após a validação, clique em **Criar**.

>**Observação:** Neste ponto, você associaria o ASG com máquina(s) virtual(is). Essas máquinas serão afetadas pela regra de entrada do NSG que você criará na próxima tarefa.

### Criar o Network Security Group (NSG) e associá-lo ao CoreServicesVnet

1. No Azure portal, pesquise por e selecione `Network security groups`.

>**Observação:** Você também pode localizar este recurso usando o menu do portal do Azure (ícone no canto superior esquerdo). Selecione **Criar um recurso** e então, na lâmina **Rede**, selecione **Grupo de segurança de rede (Network security group)**.

1. Selecione **+ Criar** e forneça informações na guia **Básicos**.

    | Configuração | Valor |
    | -- | -- |
    | Assinatura | *sua assinatura* |
    | Grupo de recursos | **az104-rg4** |
    | Nome | `myNSGSecure` |
    | Região | **East US**  |

1. Clique em **Revisar + criar** e então, após a validação, clique em **Criar**.

1. Após a implantação do NSG, clique em **Ir para o recurso**.

1. Em **Configurações** clique em **Sub-redes** e então **Associar**.

    | Configuração | Valor |
    | -- | -- |
    | Rede virtual | **CoreServicesVnet (az104-rg4)** |
    | Sub-rede | **SharedServicesSubnet** |

1. Clique em **OK** para salvar a associação.

### Configurar uma regra de segurança de entrada para permitir tráfego do ASG

1. Continue trabalhando com seu NSG. Na área **Configurações**, selecione **Regras de segurança de entrada**.

1. Revise as regras de entrada padrão. Observe que apenas outras redes virtuais e balanceadores de carga têm acesso permitido.

1. Selecione **+ Adicionar**.

1. No blade **Adicionar regra de segurança de entrada**, use as seguintes informações para adicionar uma regra de porta de entrada. Esta regra permite tráfego do ASG. Quando terminar, selecione **Adicionar**.

    | Configuração | Valor |
    | -- | -- |
    | Origem | **Grupo de segurança de aplicação** |
    | Grupos de segurança de aplicação de origem | **asg-web** |
    | Intervalos de portas de origem |  * |
    | Destino | **Qualquer** |
    | Serviço | **Personalizado** (observe suas outras escolhas) |
    | Intervalos de portas de destino | **80,443** |
    | Protocolo | **TCP** |
    | Ação | **Permitir** |
    | Prioridade | **100** |
    | Nome | `AllowASG` |

### Configurar uma regra de saída do NSG que nega acesso à Internet

1. Depois de criar sua regra de entrada do NSG, selecione **Regras de segurança de saída**.

1. Observe a regra **AllowInternetOutBound**. Também observe que a regra não pode ser excluída e a prioridade é 65001.

1. Selecione **+ Adicionar** e então configure uma regra de saída que negue acesso à Internet. Quando terminar, selecione **Adicionar**.

    | Configuração | Valor |
    | -- | -- |
    | Origem | **Qualquer** |
    | Intervalos de portas de origem |  * |
    | Destino | **Etiqueta de serviço** |
    | Tag de serviço de destino | **Internet** |
    | Serviço | **Personalizado** |
    | Intervalos de portas de destino | `*` |
    | Protocolo | **Qualquer** |
    | Ação | **Negar** |
    | Prioridade | **4096** |
    | Nome | `DenyInternetOutbound` |


## Tarefa 4: Configurar zonas DNS públicas e privadas no Azure

Nesta tarefa, você criará e configurará zonas DNS públicas e privadas.

### Configurar uma zona DNS pública

Você pode configurar o Azure DNS para resolver nomes de host no seu domínio público. Por exemplo, se você comprou o domínio contoso.xyz de um registrador de domínios, você pode configurar o Azure DNS para hospedar o domínio `contoso.com` e resolver www.contoso.xyz para o endereço IP do seu servidor web ou aplicativo web.

1. No portal, pesquise por e selecione `DNS zones`.

1. Selecione **+ Criar**.

1. Configure a guia **Básicos**.

    | Propriedade | Valor    |
    |:---------|:---------|
    | Assinatura | **Selecione sua assinatura** |
    | Grupo de recursos | **az104-rg4** |
    | Nome | `contoso.com` (este nome deve ser único, contoso.com está reservado então altere para outro.) |
    | Região |**East US** (revise o ícone informativo) |

1. Selecione **Revisar + criar** e então **Criar**.

1. Aguarde a zona DNS ser implantada e então selecione **Ir para o recurso**.

1. Na lâmina **Visão geral** observe os nomes dos quatro servidores de nomes (name servers) do Azure DNS atribuídos à zona. **Copie** um dos endereços do servidor de nomes. Você precisará dele em um passo futuro para o comando nslookup abaixo.

1. Expanda o blade **Gerenciamento de DNS** e selecione **Conjuntos de registros**. Clique em **+ Adicionar**.

    | Propriedade | Valor    |
    |:---------|:---------|
    | Nome | **www** |
    | Tipo | **A** |
    | Conjunto de registro alias | **Não** |
    | TTL | **1** |
    | Endereço IP | **10.1.1.4** |

>**Observação:** Em um cenário real, você inseriria o endereço IP público do seu servidor web.

1. Selecione **Adicionar** e verifique se seu domínio tem um registro A chamado **www**.

1. Abra um prompt de comando e execute o seguinte comando. Se você tiver alterado o nome de domínio, faça o ajuste.

   ```sh
   nslookup www.contosoxyz104.com <name server name you copied in step 6 above>
   ```
1. Verifique se o host www.contosoxyz104.com resolve para o endereço IP que você forneceu. Isso confirma que a resolução de nomes está funcionando corretamente.

### Configurar uma zona DNS privada

Uma zona DNS privada fornece serviços de resolução de nomes dentro de redes virtuais. Uma zona DNS privada só é acessível a partir das redes virtuais às quais ela está vinculada e não pode ser acessada pela internet.

1. No portal, pesquise por e selecione `Private dns zones`.

1. Selecione **+ Criar**.

1. Na guia **Básicos** de Criar zona DNS privada, insira as informações conforme listado na tabela abaixo:

    | Propriedade | Valor    |
    |:---------|:---------|
    | Assinatura | **Selecione sua assinatura** |
    | Grupo de recursos | **az104-rg4** |
    | Nome | `private.contoso.com` (ajuste se você teve que renomear) |
    | Região |**East US** |

1. Selecione **Revisar + criar** e então **Criar**.

1. Aguarde a zona DNS ser implantada e então selecione **Ir para o recurso**.

1. Observe na lâmina **Visão geral** que não há registros de servidores de nomes.

1. Expanda o blade **Gerenciamento de DNS** e então selecione **Vínculos de rede virtual**. Configure o vínculo.

    | Propriedade | Valor    |
    |:---------|:---------|
    | Nome do vínculo | `manufacturing-link` |
    | Rede virtual | `ManufacturingVnet` |

1. Selecione **Criar** e aguarde a criação do vínculo.

1. No blade **Gerenciamento de DNS** selecione **+ Conjuntos de registros**. Agora você adicionaria um registro para cada máquina virtual que precisa de suporte à resolução de nome privada.

    | Propriedade | Valor    |
    |:---------|:---------|
    | Nome | **sensorvm** |
    | Tipo | **A** |
    | TTL | **1** |
    | Endereço IP | **10.1.1.4** |

 >**Observação:** Em um cenário real, você inseriria o endereço IP para uma VM específica de manufatura.

## Limpar seus recursos

Se você estiver trabalhando com **sua própria assinatura**, reserve um minuto para excluir os recursos do laboratório. Isso garantirá que os recursos sejam liberados e que o custo seja minimizado. A forma mais fácil de excluir os recursos do laboratório é excluir o grupo de recursos do laboratório.

+ No Azure portal, selecione o grupo de recursos, selecione **Excluir o grupo de recursos**, **Digite o nome do grupo de recursos**, e então clique **Excluir**.
+ Usando Azure PowerShell, `Remove-AzResourceGroup -Name resourceGroupName`.
+ Usando o CLI, `az group delete --name resourceGroupName`.

## Expanda seu aprendizado com o Copilot

O Copilot pode ajudá-lo a aprender como usar as ferramentas de script do Azure. O Copilot também pode auxiliar em áreas não cobertas no laboratório ou onde você precisa de mais informações. Abra um navegador Edge e escolha Copilot (no canto superior direito) ou navegue até *copilot.microsoft.com*. Reserve alguns minutos para testar estes prompts.
+ Compartilhe as 10 melhores práticas ao implantar e configurar uma rede virtual no Azure.
+ Como usar Azure PowerShell e Azure CLI para criar uma rede virtual com um endereço IP público e uma sub-rede.
+ Explique as regras de entrada (inbound) e saída (outbound) do Azure Network Security Group e como elas são usadas.
+ Qual é a diferença entre Network Security Groups e Application Security Groups? Forneça exemplos de quando usar cada um desses grupos.
+ Forneça um guia passo a passo sobre como diagnosticar quaisquer problemas de rede enfrentados ao implantar uma rede no Azure. Também compartilhe o raciocínio usado em cada etapa do troubleshooting.

## Aprenda mais com treinamento autodirigido

+ [Introdução às Redes Virtuais do Azure](https://learn.microsoft.com/training/modules/introduction-to-azure-virtual-networks/). Desenhe e implemente a infraestrutura de redes central do Azure, como redes virtuais, IPs públicos e privados, DNS, emparelhamento de redes virtuais, roteamento e Azure Virtual NAT.
+ [Proteja e isole o acesso a recursos do Azure usando grupos de segurança de rede e pontos de extremidade de serviço](https://learn.microsoft.com/training/modules/secure-and-isolate-with-nsg-and-service-endpoints/). Grupos de segurança de rede e pontos de extremidade de serviço ajudam a proteger suas máquinas virtuais e serviços do Azure contra acesso de rede não autorizado.
+ [Hospede seu domínio no Azure DNS](https://learn.microsoft.com/training/modules/host-domain-azure-dns/). Crie uma zona DNS para o seu nome de domínio. Crie registros DNS para mapear o domínio para um endereço IP. Teste se o nome de domínio resolve para o seu servidor web.

## Principais conclusões

Parabéns por concluir o laboratório. Aqui estão os principais aprendizados deste laboratório.

+ Uma rede virtual é uma representação da sua própria rede na nuvem.
+ Ao projetar redes virtuais é uma boa prática evitar sobreposição de intervalos de endereços IP. Isso reduzirá problemas e simplificará a solução de problemas.
+ Uma sub-rede é um intervalo de endereços IP na rede virtual. Você pode dividir uma rede virtual em várias sub-redes para organização e segurança.
+ Um grupo de segurança de rede contém regras de segurança que permitem ou negam o tráfego de rede. Existem regras de entrada e saída padrão que você pode personalizar conforme suas necessidades.
+ Grupos de segurança de aplicação são usados para proteger grupos de servidores com uma função comum, como servidores web ou servidores de banco de dados.
+ Azure DNS é um serviço de hospedagem para domínios DNS que fornece resolução de nomes. Você pode configurar o Azure DNS para resolver nomes de host no seu domínio público. Você também pode usar zonas DNS privadas para atribuir nomes DNS às máquinas virtuais (VMs) em suas redes virtuais do Azure.
