---
demo:
    title: 'Demonstração 10: Administrar Proteção de Dados'
    module: 'Administrar Proteção de Dados'
layout: default
---

# 10 - Administrar Proteção de Dados

## Fazer backup de compartilhamentos de arquivos do Azure

Nesta demonstração, exploraremos como fazer backup de um compartilhamento de arquivos no portal do Azure.

> **Observação:** Esta demonstração requer uma conta de armazenamento (storage account) com um compartilhamento de arquivos (file share).

**Referência**: [Fazer backup de compartilhamentos de arquivos do Azure no portal do Azure](https://docs.microsoft.com/azure/backup/backup-afs)

**Criar um cofre do Recovery Services (Recovery Services vault)**

1. Use o portal do Azure.

1. Pesquise e selecione **Cofres do Recovery Services (Recovery Services vaults)**.

1. Crie um **cofre do Recovery Services (Recovery Services vault)**. Verifique o requisito de que o cofre esteja na mesma região que o compartilhamento de arquivos.

1. Aguarde a criação do cofre.

**Configurar o backup do Azure Files (Azure Files backup)**

1. Vá para **Central de Backup (Backup center)** e crie uma nova instância de **Backup**.

1. Analise e discuta as opções no menu suspenso **Tipo de origem de dados (Datasource type)**. Selecione **Azure Files (Azure storage)**.

1. Selecione seu cofre.

1. Clique em **Continuar (Continue)** para configurar o backup. Selecione a conta de armazenamento (storage account) específica e o compartilhamento de arquivos (file share) que você deseja fazer backup.

1. Em **Detalhes da política (Policy details)** clique em **Editar esta política (Edit this policy)**. Discuta a finalidade das políticas de backup. Revise a **agenda de backup (backup schedule)** e o **período de retenção (retention range)**.

1. Clique em **Habilitar backup (Enable backup)** para salvar suas alterações.

1. Se houver tempo, revise como **Restaurar (Restore)** uma **instância de backup (Backup instance)**. Além disso, reveja como monitorar seus **jobs de backup (Backup jobs)**.

## Fazer backup de máquinas virtuais do Azure

Nesta demonstração, agendaremos um backup diário de uma máquina virtual para um cofre do Recovery Services (Recovery Services vault).

> **Observação:** Esta demonstração requer uma máquina virtual e um cofre do Recovery Services (Recovery Services vault).

**Referência**: [Tutorial - Fazer backup de várias máquinas virtuais do Azure](https://docs.microsoft.com/azure/backup/tutorial-backup-vm-at-scale)

1. Use o portal do Azure.

1. Vá para **Central de Backup (Backup center)** e crie uma nova instância de **Backup**.

1. Selecione **Máquinas Virtuais do Azure (Azure Virtual machines)** como o **Tipo de origem de dados (Datasource type)** e selecione o cofre.

1. Revise a política **DefaultPolicy**. A política padrão faz o backup da máquina virtual uma vez por dia. Os backups diários são retidos por 30 dias. Os snapshots de recuperação instantânea são retidos por dois dias.

1. Clique em **Habilitar backup (Enable backup)** para salvar sua configuração.

1. Se houver tempo, revise como **Fazer backup agora (Backup now)**. Além disso, reveja como verificar seus **jobs de backup (Backup jobs)**.
