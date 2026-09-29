---

demo:
    title: 'Demonstração 04: Administrar Virtual Networking'
    module: 'Administrar Virtual Networking'
layout: default
---

# 04 - Administrar Virtual Networking

## Configurar Virtual Networks

Nesta demonstração, você criará virtual networks.

**Referência**: [Quickstart: Create a virtual network - Azure portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## Criar uma virtual network no portal

1.  Entre no Azure portal e pesquise por **Virtual Networks**.

1.  Crie uma virtual network, explicando as configurações básicas conforme avança. Garanta que pelo menos uma subnet seja criada.

1.  Verifique se sua virtual network foi criada.

## Configurar Network Security Groups

Nesta demonstração, você explorará NSGs e service endpoints.

**Referência**: [Restrict access to PaaS resources - tutorial - Azure portal](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)

**Criar um network security group**

1. Acesse o Azure Portal.

1. Pesquise e selecione **Network Security Groups**.

1. Crie um NSG explicando as configurações conforme avança.

1. Aguarde a implantação do novo NSG.

**Explorar inbound and outbound rules**

1. Selecione seu novo NSG.

1. Discuta como o NSG pode ser associado a subnets ou network interfaces.

1. Discuta a finalidade das regras inbound e outbound.

1. Revise as regras inbound e outbound padrão.

1. Crie uma nova regra, explicando as configurações conforme avança. Discuta especificamente a seleção de serviço (por exemplo, HTTPS) e as configurações de prioridade.

## Configurar Azure DNS

Nesta demonstração, você explorará Azure DNS.

**Referência**: [Tutorial: Host your domain and subdomain - Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)


**Criar uma DNS zone**

1. Acesse o Azure Portal.

1. Pesquise pelo serviço **DNS zones**.

1. Crie uma **DNS zone** e explique o propósito da zone. Como nome você pode usar contoso.internal.com.

1. Aguarde a criação da DNS zone. Pode ser necessário **Refresh** a página.

**Adicionar um DNS record set**

**Referência**: [Tutorial: Create an alias record to refer to a zone resource record](https://learn.microsoft.com/azure/dns/tutorial-alias-rr)

1. Assim que sua DNS zone for criada, selecione **+Record Set**.

1. Use o menu suspenso **Type** para ver os diferentes tipos de registros. Revise como os diferentes tipos de registro são usados. Observe como as informações do registro mudam conforme você seleciona diferentes tipos de registro.

1. Crie um registro **A** como exemplo.
