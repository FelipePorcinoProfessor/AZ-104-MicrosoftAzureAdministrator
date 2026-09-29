---
demo:
    title: 'Demonstração 07: Administrar Azure Storage'
    module: 'Administrar Azure Storage'
layout: default
---


# 07 - Administrar Azure Storage

## Configurar Storage Accounts

Nesta demonstração, criaremos uma storage account.

**Referência**: [Criar uma storage account](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)

1. Use o Azure portal.

1. Revise a finalidade das storage accounts.

1. Pesquise e selecione **Storage Accounts**.

1. Crie uma storage account básica.

	- Discuta os requisitos em torno da nomeação de uma storage account e a necessidade de o nome ser único no Azure.

	- Revise os diferentes storage kinds. Por exemplo, general-purpose v2.

	- Revise as seleções de access tier. Por exemplo, as tiers cool e hot.

	- Outras abas e configurações serão abordadas em outras demonstrações.

1. Crie a storage account e aguarde a implantação do recurso.


## Configurar Blob Storage

Nesta demonstração, exploraremos o blob storage.

**Observação:** Estes passos requerem uma storage account.

**Referência**: [Introdução rápida: Fazer upload, download e listar blobs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

1. Navegue até uma storage account no Azure portal.

1. Revise a finalidade do blob storage.

1. Crie um blob container. Revise o nível de acesso do container. Por exemplo, private (sem acesso anônimo).

1. Faça upload de um blob para o container. À medida que houver tempo, reveja as configurações avançadas. Por exemplo, blob type e blob size.

## Configurar a Segurança do Storage

Nesta demonstração, criaremos uma shared access signature.

**Observação:** Esta demonstração requer uma storage account, com um blob container, e um arquivo enviado.

**Referência**: [Criar SAS tokens para storage containers](https://learn.microsoft.com/azure/applied-ai-services/form-recognizer/create-sas-tokens?source=recommendations&view=form-recog-3.0.0)

1. Selecione um blob ou arquivo que você deseja proteger.

1. Gere uma shared access signature (SAS). Revise as permissões, os horários de início e expiração, e os protocolos permitidos.

1. Use a SAS URL para garantir que o recurso seja exibido.


## Configurar Azure Files

Nesta demonstração, trabalharemos com file shares e snapshots.

**Observação:** Estes passos requerem uma storage account.

**Referência**: [Introdução rápida para gerenciar Azure file shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-portal?tabs=azure-portal)

1. Revise a finalidade dos file shares.

1. Acesse uma storage account e clique em **Files**.

1. Crie um file share. Revise quotas, upload de arquivos e a adição de diretórios para organizar as informações.

1. Crie um snapshot de file share. Revise quando usar snapshots e como eles são diferentes de backups. Se houver tempo, faça upload de um arquivo, tire um snapshot, exclua o arquivo e restaure o snapshot.

**Referência**: [Comece com o Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

1. Instale o Storage Explorer ou use o Storage Browser.

1. Revise como navegar e criar recursos de storage. Por exemplo, adicione um blob container.

**Referência**: [Copiar ou mover dados para Azure Storage usando o AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=/azure/storage/files/toc.json)

1. Discuta quando usar o AzCopy. Veja a ajuda, **azcopy /?**.

1. Role para baixo até a seção **Samples**. Se houver tempo, experimente qualquer um dos exemplos.
