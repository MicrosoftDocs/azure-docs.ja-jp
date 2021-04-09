---
title: AzCopy v10 を使用して Azure Blob Storage と同期する | Microsoft Docs
description: この記事には、Azure Blob Storage と同期するのに役立つ AzCopy サンプル コマンドのコレクションが含まれています。
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 12/08/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: dineshm
ms.openlocfilehash: ab1da88899ba2b90e303da107631e3878b3a8b58
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102635877"
---
# <a name="synchronize-with-azure-blob-storage-by-using-azcopy-v10"></a>AzCopy v10 を使用して Azure Blob Storage と同期する

AzCopy v10 コマンドライン ユーティリティを使用して、Azure Blob Storage とローカル ストレージを同期できます。 

ローカル ファイル システムのコンテンツを BLOB コンテナーに同期できます。 また、コンテナーと仮想ディレクトリを相互に同期することもできます。 同期は一方向です。 言い換えると、2 つのエンドポイントのいずれかを同期元として、いずれかを同期先として選択します。 同期にもサーバー間 API が使用されます。 このセクションに示されている例は、階層型名前空間があるアカウントでも機能します。 

> [!NOTE]
> 現行版の AzCopy では、他のコピー元とコピー先の間では同期されません (例:ファイル ストレージまたはアマゾン ウェブ サービス (AWS) S3 バケット)。

ファイルのアップロード、BLOB のダウンロード、およびアカウント間での BLOB のコピーなど、他の種類のタスクの例については、この記事の「[次のステップ](#next-steps)」セクションに記載されているリンクを参照してください。

## <a name="get-started"></a>はじめに

AzCopy のダウンロード方法と、ストレージ サービスに認証資格情報を与える方法については、[AzCopy の作業開始](storage-use-azcopy-v10.md)に関するページをご覧ください。

> [!NOTE] 
> この記事の例では、Azure Active Directory (Azure AD) を使用して認証資格情報を指定していることを前提としています。
>
> SAS トークンを使用して BLOB データへのアクセスを承認する場合、各 AzCopy コマンドのリソース URL の先頭にそのトークンを追加できます。 (例: `'https://<storage-account-name>.blob.core.windows.net/<container-name><SAS-token>'`)。

## <a name="guidelines"></a>ガイドライン

- [sync](storage-ref-azcopy-sync.md) コマンドでは、ファイル名と最後に変更されたタイムスタンプが比較されます。 省略可能な `--delete-destination` フラグの値を `true` または `prompt` に設定すると、コピー元のディレクトリにファイルがもう存在しなくなると、コピー先のディレクトリからそれらのファイルが削除されます。

- `--delete-destination` フラグを `true` に設定すると、AzCopy では、プロンプトが表示されずにファイルが削除されます。 AzCopy でファイルが削除される前にプロンプトを表示する場合、`--delete-destination` フラグを `prompt` に設定します。

- 誤削除を防ぐために、`--delete-destination=prompt|true` フラグを使用する前に[論理的な削除](../blobs/soft-delete-blob-overview.md)機能を有効にしてください。

## <a name="update-a-container-with-changes-to-a-local-file-system"></a>ローカル ファイル システムへの変更を使用してコンテナーを更新する

この場合、コンテナーが宛先であり、ローカル ファイル システムがソースです。 

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync '<local-directory-path>' 'https://<storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **例** | `azcopy sync 'C:\myDirectory' 'https://mystorageaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-local-file-system-with-changes-to-a-container"></a>コンテナーへの変更を使用してローカル ファイル システムを更新する

この場合、ローカル ファイル システムが宛先であり、コンテナーがソースです。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync 'https://<storage-account-name>.blob.core.windows.net/<container-name>' 'C:\myDirectory' --recursive` |
| **例** | `azcopy sync 'https://mystorageaccount.blob.core.windows.net/mycontainer' 'C:\myDirectory' --recursive` |

## <a name="update-a-container-with-changes-in-another-container"></a>別のコンテナーへの変更を使用してコンテナーを更新する

このコマンドに表示される最初のコンテナーがソースです。 2 つ目が宛先です。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>' --recursive` |
| **例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/mycontainer' 'https://mydestinationaccount.blob.core.windows.net/mycontainer' --recursive` |

## <a name="update-a-directory-with-changes-to-a-directory-in-another-container"></a>別のコンテナー内のディレクトリへの変更を使用してディレクトリを更新する

このコマンドに表示される最初のディレクトリがソースです。 2 つ目が宛先です。

> [!TIP]
> この例では、パス引数を単一引用符 ('') で囲んでいます。 Windows コマンド シェル (cmd.exe) を除き、すべてのコマンド シェルで単一引用符を使用します。 Windows コマンド シェル (cmd.exe) を使用している場合は、単一引用符 ('') ではなく、二重引用符 ("") でパス引数を囲みます。

|    |     |
|--------|-----------|
| **構文** | `azcopy sync 'https://<source-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' 'https://<destination-storage-account-name>.blob.core.windows.net/<container-name>/<directory-name>' --recursive` |
| **例** | `azcopy sync 'https://mysourceaccount.blob.core.windows.net/<container-name>/myDirectory' 'https://mydestinationaccount.blob.core.windows.net/mycontainer/myDirectory' --recursive` |

## <a name="synchronize-with-optional-flags"></a>オプションのフラグに同期させる

オプションのフラグを使用して、同期操作を調整できます。 以下にいくつか例を示します。

|シナリオ|フラグ|
|---|---|
|ダウンロードの際に MD5 ハッシュを検証する厳密さを指定します。|**--check-md5**=\[NoCheck\|LogOnly\|FailIfDifferent\|FailIfDifferentOrMissing\]|
|パターンに基づいてファイルを除外します。|**--exclude-path**|
|同期に関連するログ エントリの詳細レベルを指定します。|**--log-level**=\[WARNING\|ERROR\|INFO\|NONE\]|

完全な一覧については、「[オプション](storage-ref-azcopy-sync.md#options)」を参照してください。

## <a name="next-steps"></a>次のステップ

他の例については、次の記事を参照してください。

- [例:アップロード](storage-use-azcopy-blobs-upload.md)
- [例:ダウンロード](storage-use-azcopy-blobs-download.md)」をご覧ください
- [例:アカウント間のコピー](storage-use-azcopy-blobs-copy.md)
- [例:Amazon S3 バケット](storage-use-azcopy-s3.md)
- [例:Azure Files](storage-use-azcopy-files.md)
- [チュートリアル:AzCopy を使用したオンプレミス データのクラウド ストレージへの移行](storage-use-azcopy-migrate-on-premises-data.md)
- [AzCopy の構成、最適化、トラブルシューティング](storage-use-azcopy-configure.md)