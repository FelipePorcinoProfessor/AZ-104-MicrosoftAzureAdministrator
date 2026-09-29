---

demo:
    title: 'Demonstração 04: Administrar Rede Virtual (Virtual Networking)'
    module: 'Administrar Rede Virtual (Virtual Networking)'
layout: default
---

# 04 - Administrar Rede Virtual

## Configurar Redes Virtuais

Nesta demonstração, você criará redes virtuais.

**Referência**: [Introdução rápida: Criar uma rede virtual - Portal do Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## Criar uma rede virtual no portal

1.  Entre no Portal do Azure (Azure portal) e pesquise por **Redes virtuais (Virtual Networks)**.

1.  Crie uma rede virtual (virtual network), explicando as configurações básicas conforme avança. Garanta que pelo menos uma sub-rede (subnet) seja criada.

1.  Verifique se sua rede virtual foi criada.

## Configurar Grupos de Segurança de Rede

Nesta demonstração, você explorará grupos de segurança de rede (Network Security Groups - NSGs) e endpoints de serviço (service endpoints).

**Referência**: [Tutorial: Restringir acesso a recursos PaaS - Portal do Azure](https://docs.microsoft.com/azure/virtual-network/tutorial-restrict-network-access-to-resources)

**Criar um grupo de segurança de rede**

1. Acesse o Portal do Azure (Azure portal).

1. Pesquise e selecione **Grupos de Segurança de Rede (Network Security Groups)**.

1. Crie um NSG explicando as configurações conforme avança.

1. Aguarde a implantação do novo NSG.

**Explorar regras inbound e outbound**

1. Selecione seu novo NSG.

1. Discuta como o NSG pode ser associado a sub-redes (subnets) ou interfaces de rede (network interfaces).

1. Discuta a finalidade das regras de entrada (inbound) e de saída (outbound).

1. Revise as regras padrão de entrada e de saída.

1. Crie uma nova regra, explicando as configurações conforme avança. Discuta especificamente a seleção de serviço (por exemplo, HTTPS) e as configurações de prioridade.

## Configurar Azure DNS

Nesta demonstração, você explorará o Azure DNS.

**Referência**: [Tutorial: Hospedar seu domínio e subdomínio - Azure DNS](https://docs.microsoft.com/azure/dns/dns-delegate-domain-azure-dns)


**Criar uma zona DNS**

1. Acesse o Portal do Azure (Azure portal).

1. Pesquise pelo serviço **Zonas DNS (DNS zones)**.

1. Crie uma **zona DNS (DNS zone)** e explique o propósito da zona. Como nome você pode usar contoso.internal.com.

1. Aguarde a criação da zona DNS. Pode ser necessário **Atualizar (Refresh)** a página.

**Adicionar um conjunto de registros DNS**

**Referência**: [Tutorial: Criar um registro de alias para referenciar um registro de zona - Azure DNS](https://learn.microsoft.com/azure/dns/tutorial-alias-rr)

1. Assim que sua zona DNS for criada, selecione **+Conjunto de Registros (+Record set)**.

1. Use o menu suspenso **Tipo (Type)** para ver os diferentes tipos de registros. Revise como os diferentes tipos de registro são usados. Observe como as informações do registro mudam conforme você seleciona diferentes tipos de registro.

1. Crie um registro **A** como exemplo.
