---
title: Python を使用した Azure Storage サンプル | Microsoft Docs
description: Azure Storage のサンプル コードとアプリケーションを表示、ダウンロード、実行します。 Python のストレージ クライアント ライブラリを使用して、BLOB、キュー、テーブル、ファイルのサンプルの概要について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 5d823059842bf539f00ad372127fc8b636c74b65
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70745969"
---
# <a name="azure-storage-samples-using-python"></a>Python を使用した Azure Storage サンプル

次の表は、各サンプルで扱っているサンプル リポジトリとシナリオの概要を示したものです。 リンクをクリックすると、対応するサンプル コードが GitHub で表示されます。

## <a name="blob-samples"></a>BLOB のサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| Append Blob | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L166) |
| ブロック BLOB | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L77) |
| クライアント側の暗号化 | [Python を使用した Azure Key Value でのストレージ アカウント キーの管理](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| BLOB のコピー | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L102) |
| コンテナーの作成 | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L91) |
| Delete Blob | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L114) |
| Delete Container | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L118) |
| BLOB のメタデータ/プロパティ/統計 | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L298) |
| コンテナーの ACL/メタデータ/プロパティ | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L268) |
| ページ範囲の取得 | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L151) |
| BLOB/コンテナーのリース | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L377) |
| BLOB/コンテナーのリスト | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L103) |
| ページ BLOB | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L124) |
| SAS | [Python での Shared Access Signature](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L145) |
| サービスのプロパティ | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_advanced_samples.py#L540) |
| Snapshot Blob | [Python での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-python-getting-started/blob/master/blob_basic_samples.py#L214) |

## <a name="file-samples"></a>ファイルのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| 共有/ディレクトリ/ファイルの作成 | [Python での Azure File service の概要](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L71) |
| 共有/ディレクトリ/ファイルの削除 | [Python での Azure File service の概要](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L170) |
| ディレクトリのプロパティ/メタデータ | [Python での Azure File service の概要](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L175) |
| ファイルのダウンロード | [Python での Azure File service の概要](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L138) |
| ファイルのプロパティ/メタデータ/メトリック | [Python での Azure File service の概要](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L193) |
| ファイル サービスのプロパティ | [Python での Azure File service の概要](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L125) |
| ディレクトリとファイルのリスト | [Python での Azure File service の概要](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_basic_samples.py#L153) |
| 共有のリスト | [Python での Azure File service の概要](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L82) |
| 共有のプロパティ/メタデータ/統計 | [Python での Azure File service の概要](https://github.com/Azure-Samples/storage-file-python-getting-started/blob/master/file_advanced_samples.py#L144) |

## <a name="queue-samples"></a>キューのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| メッセージの追加 | [Python での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L94) |
| クライアント側の暗号化 | [Python を使用した Azure Key Vault でのストレージ アカウント キーの管理](https://github.com/Azure-Samples/key-vault-python-storage-accounts) |
| キューの作成 | [Python での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L75) |
| メッセージ/キューの削除 | [Python での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L144) |
| メッセージのピーク | [Python での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L110) |
| キューの ACL/メタデータ/統計 | [Python での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L148) |
| キュー サービスのプロパティ | [Python での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_advanced_samples.py#L128) |
| 更新メッセージ | [Python での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-python-getting-started/blob/master/queue_basic_samples.py#L120) |

## <a name="table-samples"></a>テーブルのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| テーブルの作成 | [Python での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L46) |
| エンティティ/テーブルの削除 | [Python での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L79) |
| エンティティの挿入/マージ/置換 | [Python での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L57) |
| エンティティのクエリ | [Python での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L62) |
| テーブルの照会 | [Python での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py) |
| テーブルの ACL/プロパティ | [Python での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_advanced_samples.py#L138) |
| エンティティの更新 | [Python での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-python-getting-started/blob/master/table_basic_samples.py#L68) |

## <a name="azure-code-samples-library"></a>Azure のコード サンプル ライブラリ

完全なサンプル ライブラリを表示するには、[Azure のコード サンプル](https://azure.microsoft.com/resources/samples/?service=storage) ライブラリにアクセスしてください。このライブラリには、ダウンロードしてローカルで実行できる Azure Storage のサンプルが用意されています。 コード サンプル ライブラリでは、サンプル コードが .zip 形式で提供されます。 また、各サンプルの GitHub リポジトリを参照して複製することもできます。

[!INCLUDE [storage-python-samples-include](../../../includes/storage-python-samples-include.md)]

## <a name="getting-started-guides"></a>概要ガイド

Azure Storage ライブラリのインストール方法と概要については、以下のガイドをご覧ください。

* [Python での Azure Blob service の概要](../blobs/storage-quickstart-blobs-python.md)
* [Python での Azure Queue サービスの概要](../queues/storage-python-how-to-use-queue-storage.md)
* [Python での Azure Table service の概要](../../cosmos-db/table-storage-how-to-use-python.md)
* [Python での Azure File service の概要](../files/storage-python-how-to-use-file-storage.md)

## <a name="next-steps"></a>次の手順

他の言語のサンプルについては、以下のページをご覧ください。

* .NET:[.NET を使用した Azure Storage サンプル](storage-samples-dotnet.md)
* Java:[Java を使用した Azure Storage サンプル](storage-samples-java.md)
* その他すべての言語: [Azure Storage のサンプル](storage-samples.md)
