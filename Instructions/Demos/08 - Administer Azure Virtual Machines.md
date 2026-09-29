---
demo:
    title: 'Demonstração 08: Administrar Azure Virtual Machines'
    module: 'Administrar Azure Virtual Machines'
layout: default
---


# 08 - Administrar Azure Virtual Machines

## Demonstração -- Criar Virtual Machines no portal

Nesta demonstração, criaremos e acessaremos uma Virtual Machine do Azure no portal.

**Referências**

[Início rápido - Criar uma Windows VM no Azure portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

[Início rápido - Criar uma Linux VM no Azure portal](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)

[Conectar-se a uma Virtual Machine com Bastion](https://learn.microsoft.com/azure/bastion/tutorial-create-host-portal#connect)

**Criar a virtual machine**

**Observação:** Estes passos cobrem apenas alguns parâmetros da virtual machine. Sinta-se à vontade para explorar e abordar outras áreas. Você pode criar uma virtual machine Windows ou Linux, dependendo do seu público.

1. Use o Azure portal.

1. Pesquise por **Virtual machines**.

1. Crie uma virtual machine básica. Revise as opções de disponibilidade, imagens e regras de entrada.

1. Discuta a importância de criar uma conta de administrador segura.

1. Crie a virtual machine e aguarde o recurso ser implantado.

**Conectar-se à virtual machine**

1. Há várias maneiras de **Conectar-se** à virtual machine.

1. Para um servidor Windows, você pode usar **RDP**, conforme mostrado no Início rápido.

1. Para um servidor Linux, você pode usar **SSH**, conforme mostrado no Início rápido.

1. Para qualquer um dos servidores, você pode conectar-se com o serviço **Bastion** (Início rápido). Reveja por que o Bastion é preferido em relação ao RDP ou SSH.

## Configurar disponibilidade da Virtual Machine

Nesta demonstração, exploraremos opções de escalonamento para virtual machines.

**Referências**

[Criar virtual machines em um scale set usando o Azure portal](https://learn.microsoft.com/azure/virtual-machine-scale-sets/flexible-virtual-machine-scale-sets-portal)

1. Use o Azure Portal.

1. Pesquise e selecione **Virtual Machine Scale Sets**.

1. Crie um **Virtual Machine Scale Sets**. Revise o propósito dos Virtual Machine Scale Sets. Revise a diferença entre os modos de orquestração **Uniform** e **Flexible**. Explique que sua seleção pode afetar suas opções de escalonamento.

1. Vá para a guia **Scaling**.

1. Reveja como **Manual scale** e **Scale-in policy** são usados.

1. Altere para uma política de escalonamento **Custom**.

1. Reveja como **CPU threshold (%)** é usado para scale out e scale in das instâncias de virtual machine.
