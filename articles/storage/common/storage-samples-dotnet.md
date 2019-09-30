---
title: .NET を使用した Azure Storage サンプル | Microsoft Docs
description: Azure Storage のサンプル コードとアプリケーションを表示、ダウンロード、実行します。 .NET のストレージ クライアント ライブラリを使用して、BLOB、キュー、テーブル、ファイルのサンプルの概要について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/06/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 57249bd2fa4d3c8aefe19a85ec9a2b6b584b00d2
ms.sourcegitcommit: 86d49daccdab383331fc4072b2b761876b73510e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/06/2019
ms.locfileid: "70743949"
---
# <a name="azure-storage-samples-using-net"></a>.NET を使用した Azure Storage サンプル

次の表は、各サンプルで扱っているサンプル リポジトリとシナリオの概要を示したものです。 リンクをクリックすると、対応するサンプル コードが GitHub で表示されます。

## <a name="blob-samples"></a>BLOB のサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| Append Blob | [BLOB の概要](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs#L1144) |
| ブロック BLOB | [Azure Blob Storage のフォト ギャラリーの Web アプリケーション](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| クライアント側の暗号化 | [BLOB 暗号化サンプル](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs) |
| BLOB のコピー | [BLOB の概要](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| コンテナーの作成 | [Azure Blob Storage のフォト ギャラリーの Web アプリケーション](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Delete Blob | [Azure Blob Storage のフォト ギャラリーの Web アプリケーション](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| Delete Container | [BLOB の概要](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB のメタデータ/プロパティ/統計 | [BLOB の概要](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| コンテナーの ACL/メタデータ/プロパティ | [Azure Blob Storage のフォト ギャラリーの Web アプリケーション](https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs) |
| ページ範囲の取得 | [BLOB の概要](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB/コンテナーのリース | [BLOB の概要](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| BLOB/コンテナーのリスト | [BLOB の概要](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| ページ BLOB | [BLOB の概要](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs) |
| SAS | [BLOB の概要](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| サービスのプロパティ | [BLOB の概要](https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs) |
| Snapshot Blob | [Azure 仮想マシン ディスクを増分スナップショットでバックアップする](https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs) |

## <a name="file-samples"></a>ファイルのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| 共有/ディレクトリ/ファイルの作成 | [Azure Storage .NET File Storage のサンプル](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 共有/ディレクトリ/ファイルの削除 | [.Net での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs) |
| ディレクトリのプロパティ/メタデータ | [Azure Storage .NET File Storage のサンプル](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| ファイルのダウンロード | [Azure Storage .NET File Storage のサンプル](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| ファイルのプロパティ/メタデータ/メトリック | [Azure Storage .NET File Storage のサンプル](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| ファイル サービスのプロパティ | [Azure Storage .NET File Storage のサンプル](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| ディレクトリとファイルのリスト | [Azure Storage .NET File Storage のサンプル](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs) |
| 共有のリスト | [Azure Storage .NET File Storage のサンプル](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |
| 共有のプロパティ/メタデータ/統計 | [Azure Storage .NET File Storage のサンプル](https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs) |

## <a name="queue-samples"></a>キューのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| メッセージの追加 | [.Net での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| クライアント側の暗号化 | [Azure Storage .NET Queue のクライアント側の暗号化](https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs) |
| キューの作成 | [.Net での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| メッセージ/キューの削除 | [.Net での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| メッセージのピーク | [.Net での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |
| キューの ACL/メタデータ/統計 | [.Net での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| キュー サービスのプロパティ | [.Net での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs) |
| 更新メッセージ | [.Net での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs) |

## <a name="table-samples"></a>テーブルのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| テーブルの作成 | [Azure Storage でのコンカレンシー制御の管理 - サンプル アプリケーション](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| エンティティ/テーブルの削除 | [.Net での Azure Table Storage の概要](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| エンティティの挿入/マージ/置換 | [Azure Storage でのコンカレンシー制御の管理 - サンプル アプリケーション](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |
| エンティティのクエリ | [.Net での Azure Table Storage の概要](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| テーブルの照会 | [.Net での Azure Table Storage の概要](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs) |
| テーブルの ACL/プロパティ | [.Net での Azure Table Storage の概要](https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs) |
| エンティティの更新 | [Azure Storage でのコンカレンシー制御の管理 - サンプル アプリケーション](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262) |

## <a name="azure-code-samples-library"></a>Azure のコード サンプル ライブラリ

完全なサンプル ライブラリを表示するには、[Azure のコード サンプル](https://azure.microsoft.com/resources/samples/?service=storage) ライブラリにアクセスしてください。このライブラリには、ダウンロードしてローカルで実行できる Azure Storage のサンプルが用意されています。 コード サンプル ライブラリでは、サンプル コードが .zip 形式で提供されます。 また、各サンプルの GitHub リポジトリを参照して複製することもできます。

[!INCLUDE [storage-dotnet-samples-include](../../../includes/storage-dotnet-samples-include.md)]

## <a name="getting-started-guides"></a>概要ガイド

Azure Storage ライブラリのインストール方法と概要については、以下のガイドをご覧ください。

* [.Net での Azure BLOB サービスの概要](../blobs/storage-dotnet-how-to-use-blobs.md)
* [.Net での Azure Queue サービスの概要](../storage-dotnet-how-to-use-queues.md)
* [.Net での Azure Table サービスの概要](../../cosmos-db/table-storage-how-to-use-dotnet.md)
* [.Net での Azure File サービスの概要](../storage-dotnet-how-to-use-files.md)

## <a name="next-steps"></a>次の手順

他の言語のサンプルについては、以下のページをご覧ください。

* Java:[Java を使用した Azure Storage サンプル](storage-samples-java.md)
* Python: [Python を使用した Azure Storage サンプル](storage-samples-python.md)
* その他すべての言語: [Azure Storage のサンプル](../storage-samples.md)
