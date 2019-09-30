---
title: Java を使用した Azure Storage サンプル | Microsoft Docs
description: Azure Storage のサンプル コードとアプリケーションを表示、ダウンロード、実行します。 Java のストレージ クライアント ライブラリを使用して、BLOB、キュー、テーブル、ファイルのサンプルの概要について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: b295c5a945f41059ca98e96d742bc5e1f6b4bf9f
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70744801"
---
# <a name="azure-storage-samples-using-java"></a>Java を使用した Azure Storage サンプル

次の表は、各サンプルで扱っているサンプル リポジトリとシナリオの概要を示したものです。 リンクをクリックすると、対応するサンプル コードが GitHub で表示されます。

## <a name="blob-samples"></a>BLOB のサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| Append Blob | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| ブロック BLOB | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| クライアント側の暗号化 | [Java での Azure クライアント側暗号化の概要](https://github.com/Azure-Samples/storage-java-client-side-encryption) |
| BLOB のコピー | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| コンテナーの作成 | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Delete Blob | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| Delete Container | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| BLOB のメタデータ/プロパティ/統計 | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| コンテナーの ACL/メタデータ/プロパティ | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| ページ範囲の取得 | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java#L399) |
| BLOB/コンテナーのリース | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| BLOB/コンテナーのリスト | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| ページ BLOB | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |
| SAS | [SAS テストのサンプル](https://github.com/Azure/azure-storage-java/blob/89540f018f1160ce55619c6fe7b5f5ff57d0ce10/src/test/java/com/microsoft/azure/storage/Samples.java#L513) |
| サービスのプロパティ | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobAdvanced.java) |
| Snapshot Blob | [Java での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-java-getting-started/blob/master/src/BlobBasics.java) |

## <a name="file-samples"></a>ファイルのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| 共有/ディレクトリ/ファイルの作成 | [Java での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 共有/ディレクトリ/ファイルの削除 | [Java での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| ディレクトリのプロパティ/メタデータ | [Java での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| ファイルのダウンロード | [Java での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| ファイルのプロパティ/メタデータ/メトリック | [Java での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| ファイル サービスのプロパティ | [Java での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |
| ディレクトリとファイルのリスト | [Java での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 共有のリスト | [Java での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileBasics.java) |
| 共有のプロパティ/メタデータ/統計 | [Java での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-java-getting-started/blob/master/src/FileAdvanced.java) |

## <a name="queue-samples"></a>キューのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| メッセージの追加 | [Java での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java#L63) |
| クライアント側の暗号化 | [Java での Azure クライアント側暗号化の概要](https://github.com/Azure-Samples/storage-java-client-side-encryption/blob/master/src/gettingstarted/KeyVaultGettingStarted.java) |
| キューの作成 | [Java での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| メッセージ/キューの削除 | [Java での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| メッセージのピーク | [Java での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java) |
| キューの ACL/メタデータ/統計 | [Java での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| キュー サービスのプロパティ | [Java での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueAdvanced.java) |
| 更新メッセージ | [Java での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-java-getting-started/blob/master/src/QueueBasics.java)
|
## <a name="table-samples"></a>テーブルのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| テーブルの作成 | [Java での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| エンティティ/テーブルの削除 | [Java での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| エンティティの挿入/マージ/置換 | [Java での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| エンティティのクエリ | [Java での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| テーブルの照会 | [Java での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
| テーブルの ACL/プロパティ | [Java での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableAdvanced.java) |
| エンティティの更新 | [Java での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-java-getting-started/blob/master/src/main/java/com/microsoft/azure/cosmosdb/tablesample/TableBasics.java) |
## <a name="azure-code-samples-library"></a>Azure のコード サンプル ライブラリ

完全なサンプル ライブラリを表示するには、[Azure のコード サンプル](https://azure.microsoft.com/resources/samples/?service=storage) ライブラリにアクセスしてください。このライブラリには、ダウンロードしてローカルで実行できる Azure Storage のサンプルが用意されています。 コード サンプル ライブラリでは、サンプル コードが .zip 形式で提供されます。 また、各サンプルの GitHub リポジトリを参照して複製することもできます。

[!INCLUDE [storage-java-samples-include](../../../includes/storage-java-samples-include.md)]

## <a name="getting-started-guides"></a>概要ガイド

Azure Storage ライブラリのインストール方法と概要については、以下のガイドをご覧ください。

* [Java での Azure Blob service の概要](../blobs/storage-quickstart-blobs-java.md)
* [Java での Azure Queue サービスの概要](../queues/storage-java-how-to-use-queue-storage.md)
* [Java での Azure Table service の概要](../../cosmos-db/table-storage-how-to-use-java.md)
* [Java での Azure File サービスの概要](../files/storage-java-how-to-use-file-storage.md)

## <a name="next-steps"></a>次の手順

他の言語のサンプルについては、以下のページをご覧ください。

* .NET:[.NET を使用した Azure Storage サンプル](storage-samples-dotnet.md)
* Python: [Python を使用した Azure Storage サンプル](storage-samples-python.md)
* その他すべての言語: [Azure Storage のサンプル](storage-samples.md)
