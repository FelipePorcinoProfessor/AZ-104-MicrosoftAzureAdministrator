---
demo:
    title: 'Demonstração 07: Administrar Azure Storage'
    module: 'Administrar Azure Storage'
layout: default
---


# 07 - Administrar Azure Storage

## Configurar Contas de armazenamento (Storage Accounts)

Nesta demonstração, criaremos uma conta de armazenamento.

**Referência**: [Criar uma conta de armazenamento](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal)

1. Use o portal do Azure.

1. Revise a finalidade das contas de armazenamento.

1. Pesquise e selecione **Contas de armazenamento (Storage Accounts)**.

1. Crie uma conta de armazenamento básica.

	- Discuta os requisitos de nomeação de uma conta de armazenamento e a necessidade de o nome ser único no Azure.

	- Revise os diferentes tipos de conta de armazenamento (storage kinds). Por exemplo, general-purpose v2.

	- Revise as seleções de camada de acesso. Por exemplo, as camadas Cool e Hot.

	- Outras abas e configurações serão abordadas em outras demonstrações.

1. Crie a conta de armazenamento e aguarde a implantação do recurso.


## Configurar Azure Blob Storage

Nesta demonstração, exploraremos o Blob Storage.

**Observação:** Estes passos requerem uma conta de armazenamento.

**Referência**: [Introdução rápida: Fazer upload, download e listar blobs](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

1. Navegue até uma conta de armazenamento no portal do Azure.

1. Revise a finalidade do Blob Storage.

1. Crie um container de blobs. Revise o nível de acesso do container. Por exemplo, privado (sem acesso anônimo).

1. Envie (faça upload) de um blob para o container. Se houver tempo, revise as configurações avançadas. Por exemplo, tipo de blob e tamanho do blob.

## Configurar a Segurança do Storage

Nesta demonstração, criaremos uma assinatura de acesso compartilhado (Shared Access Signature, SAS).

**Observação:** Esta demonstração requer uma conta de armazenamento, com um container de blobs, e um arquivo enviado.

**Referência**: [Criar tokens SAS para containers de armazenamento](https://learn.microsoft.com/azure/applied-ai-services/form-recognizer/create-sas-tokens?source=recommendations&view=form-recog-3.0.0)

1. Selecione um blob ou arquivo que você deseja proteger.

1. Gere uma assinatura de acesso compartilhado (SAS). Revise as permissões, os horários de início e expiração e os protocolos permitidos.

1. Use a URL SAS para confirmar que o recurso está acessível.


## Configurar Azure Files

Nesta demonstração, trabalharemos com compartilhamentos de arquivos (file shares) e snapshots.

**Observação:** Estes passos requerem uma conta de armazenamento.

**Referência**: [Introdução rápida para gerenciar Azure file shares](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-portal?tabs=azure-portal)

1. Revise a finalidade dos compartilhamentos de arquivos.

1. Acesse uma conta de armazenamento e clique em **Arquivos (Files)**.

1. Crie um compartilhamento de arquivos (file share). Revise cotas (quotas), envio de arquivos e a adição de diretórios para organizar os conteúdos.

1. Crie um snapshot do compartilhamento de arquivos. Revise quando usar snapshots e como eles diferem de backups. Se houver tempo, envie um arquivo, faça um snapshot, exclua o arquivo e restaure o snapshot.

**Referência**: [Comece com o Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

1. Instale o Explorador de Armazenamento (Storage Explorer) ou use o Navegador de Armazenamento (Storage Browser).

1. Revise como navegar e criar recursos de armazenamento. Por exemplo, adicione um container de blobs.

**Referência**: [Copiar ou mover dados para Azure Storage usando o AzCopy v10](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10?toc=/azure/storage/files/toc.json)

1. Discuta quando usar o AzCopy. Consulte a ajuda: **azcopy /?**.

1. Role para baixo até a seção **Exemplos (Samples)**. Se houver tempo, experimente qualquer um dos exemplos.
