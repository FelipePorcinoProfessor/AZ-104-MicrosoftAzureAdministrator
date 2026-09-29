---
demo:
    title: 'Demonstração 08: Administrar Azure Virtual Machines'
    module: 'Administrar Azure Virtual Machines'
layout: default
---


# 08 - Administrar Azure Virtual Machines

## Demonstração -- Criar Máquinas Virtuais (Virtual machines) no portal

Nesta demonstração, criaremos e acessaremos uma Máquina Virtual do Azure no portal.

**Referências**

[Início rápido - Criar uma Windows VM no Azure portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

[Início rápido - Criar uma Linux VM no Azure portal](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-portal)

[Conectar-se a uma Virtual Machine com Bastion](https://learn.microsoft.com/azure/bastion/tutorial-create-host-portal#connect)

**Criar a máquina virtual**

**Observação:** Estes passos cobrem apenas alguns parâmetros da máquina virtual. Sinta-se à vontade para explorar e abordar outras áreas. Você pode criar uma máquina virtual Windows ou Linux, dependendo do seu público.

1. Use o portal do Azure.

1. Pesquise por **Máquinas Virtuais (Virtual machines)**.

1. Crie uma máquina virtual básica. Revise as opções de disponibilidade, imagens e regras de entrada.

1. Discuta a importância de criar uma conta de administrador segura.

1. Crie a máquina virtual e aguarde o recurso ser implantado.

**Conectar-se à máquina virtual**

1. Há várias maneiras de **conectar-se** à máquina virtual.

1. Para um servidor Windows, você pode usar **RDP**, conforme mostrado no Início rápido.

1. Para um servidor Linux, você pode usar **SSH**, conforme mostrado no Início rápido.

1. Para qualquer um dos servidores, você pode conectar-se com o serviço **Bastion** (Início rápido). Reveja por que o Bastion é preferido em relação ao RDP ou SSH.

## Configurar disponibilidade da Máquina Virtual

Nesta demonstração, exploraremos opções de escalonamento para Máquinas Virtuais (Virtual machines).

**Referências**

[Criar virtual machines em um scale set usando o Azure portal](https://learn.microsoft.com/azure/virtual-machine-scale-sets/flexible-virtual-machine-scale-sets-portal)

1. Use o portal do Azure.

1. Pesquise e selecione **Conjuntos de Dimensionamento de Máquinas Virtuais (Virtual Machine Scale Sets)**.

1. Crie um **Conjunto de Dimensionamento de Máquinas Virtuais (Virtual Machine Scale Set)**. Revise o propósito dos **Conjuntos de Dimensionamento de Máquinas Virtuais (Virtual Machine Scale Sets)**. Revise a diferença entre os modos de orquestração **Uniform** e **Flexible**. Explique que sua seleção pode afetar suas opções de escalonamento.

1. Vá para a guia **Escalonamento (Scaling)**.

1. Revise como **Escalonamento manual (Manual scale)** e **Política de redução (Scale-in policy)** são usados.

1. Altere para uma política de escalonamento **Personalizada (Custom)**.

1. Revise como **Limiar de CPU (%) (CPU threshold (%))** é usado para aumentar (scale out) e reduzir (scale in) o número de instâncias da máquina virtual.
