---
demo:
    title: 'Demonstração 10: Administrar Proteção de Dados'
    module: 'Administrar Proteção de Dados'
layout: default
---

# 10 - Administrar Proteção de Dados

## Fazer backup de Azure File Shares

Nesta demonstração, exploraremos como fazer backup de um file share no portal do Azure.

> **Observação:** Esta demonstração requer uma storage account com um file share.

**Referência**: [Back up Azure file shares in the Azure portal](https://docs.microsoft.com/azure/backup/backup-afs)

**Create a Recovery Services vault**

1. Use o portal do Azure.

1. Pesquise por e selecione **Recovery Services vaults**.

1. Crie um **Recovery Services Vault**. Verifique o requisito de que o vault esteja na mesma região que o file share.

1. Aguarde a criação do vault.

**Configure the Azure files backup**

1. Vá para **Backup center** e crie uma nova instância de **Backup**.

1. Analise e discuta as opções no menu suspenso **Datasource type**. Selecione **Azure files (Azure storage)**.

1. Selecione seu **vault**.

1. **Continue** configurando o backup. Selecione a storage account específica e o file share que você deseja fazer backup.

1. Em **Policy details** clique em **Edit this policy**. Discuta a finalidade das políticas de backup. Revise o **backup schedule** e o **retention range**.

1. Clique em **Enable backup** para salvar suas alterações.

1. Se houver tempo, revise como **Restore** uma **Backup instance**. Além disso, como monitorar seus **Backup jobs**.

## Fazer backup de Azure Virtual Machines

Nesta demonstração, agendaremos um backup diário de uma máquina virtual para um Recovery Services vault.

> **Observação:** Esta demonstração requer uma máquina virtual e um Recovery Services vault.

**Referência**: [Tutorial - Back up multiple Azure virtual machines](https://docs.microsoft.com/azure/backup/tutorial-backup-vm-at-scale)

1. Use o portal do Azure.

1. Vá para **Backup center** e crie uma nova instância de **Backup**.

1. Selecione **Azure Virtual machines** como o **Datasource type** e selecione o vault.

1. Revise a **DefaultPolicy**. A política padrão faz o backup da máquina virtual uma vez por dia. Os backups diários são retidos por 30 dias. Os instant recovery snapshots são retidos por dois dias.

1. Clique em **Enable backup** para salvar sua configuração.

1. Se houver tempo, revise como **Backup now**. Além disso, como revisar seus **Backup jobs**.
