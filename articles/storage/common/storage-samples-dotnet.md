---
title: .NET を使用した Azure Storage サンプル | Microsoft Docs
description: Azure Storage のサンプル コードとアプリケーションを表示、ダウンロード、実行します。 .NET のストレージ クライアント ライブラリを使用して、BLOB、キュー、テーブル、ファイルのサンプルの概要について説明します。
services: storage
author: seguler
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 01/12/2017
ms.author: seguler
ms.subservice: common
ms.openlocfilehash: c4dcb3f42d97624e66258228ce27e130a05cac70
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/31/2019
ms.locfileid: "55456138"
---
# <a name="azure-storage-samples-using-net"></a>.NET を使用した Azure Storage サンプル

## <a name="net-sample-index"></a>.NET サンプルのインデックス

次の表は、各サンプルで扱っているサンプル リポジトリとシナリオの概要を示したものです。 リンクをクリックすると、対応するサンプル コードが GitHub で表示されます。

<table style="font-size:90%"><thead><tr><th style="font-size:110%">エンドポイント</th><th style="font-size:110%">シナリオ</th><th style="font-size:110%">サンプル コード</th></tr></thead><tbody> 
<tr> 
<td rowspan="16"><b>BLOB</b></td>
<td>Append Blob</td> 
<td><a href="https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.cloudblobcontainer.getappendblobreference.aspx">CloudBlobContainer.GetAppendBlobReference メソッドの例</a></td> 
</tr> 
<tr> 
<td>ブロック BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage のフォト ギャラリーの Web アプリケーション</a></td>
</tr> 
<tr> 
<td>クライアント側の暗号化</td>
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/BlobGettingStarted/Program.cs">BLOB 暗号化サンプル</a></td>
</tr> 
<tr> 
<td>BLOB のコピー</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB の概要</a></td>
</tr> 
<tr> 
<td>コンテナーの作成</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage のフォト ギャラリーの Web アプリケーション</a></td>
</tr> 
<tr> 
<td>Delete Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage のフォト ギャラリーの Web アプリケーション</a></td>
</tr> 
<tr> 
<td>Delete Container</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB の概要</a></td>
</tr> 
<tr> 
<td>BLOB のメタデータ/プロパティ/統計</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB の概要</a></td>
</tr> 
<tr> 
<td>コンテナーの ACL/メタデータ/プロパティ</td>
<td><a href="https://github.com/Azure-Samples/storage-blobs-dotnet-webapp/blob/master/WebApp-Storage-DotNet/Controllers/HomeController.cs">Azure Blob Storage のフォト ギャラリーの Web アプリケーション</a></td>
</tr> 
<tr> 
<td>ページ範囲の取得</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB の概要</a></td>
</tr> 
<tr> 
<td>BLOB/コンテナーのリース</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB の概要</a></td>
</tr> 
<tr> 
<td>BLOB/コンテナーのリスト</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">BLOB の概要</a></td>
</tr> 
<tr> 
<td>ページ BLOB</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/GettingStarted.cs">BLOB の概要</a></td>
</tr>
<tr> 
<td>SAS</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB の概要</a></td>
</tr>   
<tr> 
<td>サービスのプロパティ</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-getting-started/blob/master/BlobStorage/Advanced.cs">BLOB の概要</a></td>
</tr>           
<tr> 
<td>Snapshot Blob</td>
<td><a href="https://github.com/Azure-Samples/storage-blob-dotnet-back-up-with-incremental-snapshots/blob/master/Program.cs">Azure 仮想マシン ディスクを増分スナップショットでバックアップする</a></td>
</tr> 
<tr> 
<td rowspan="9"><b>ファイル</b></td>
<td>共有/ディレクトリ/ファイルの作成</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage のサンプル</a></td> 
</tr>
<tr> 
<td>共有/ディレクトリ/ファイルの削除</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/master/FileStorage/GettingStarted.cs">.Net での Azure File サービスの概要</a></td> 
</tr> 
<tr> 
<td>ディレクトリのプロパティ/メタデータ</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage のサンプル</a></td> 
</tr> 
<tr> 
<td>ファイルのダウンロード</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage のサンプル</a></td> 
</tr> 
<tr> 
<td>ファイルのプロパティ/メタデータ/メトリック</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage のサンプル</a></td> 
</tr> 
<tr> 
<td>ファイル サービスのプロパティ</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage のサンプル</a></td> 
</tr> 
<tr> 
<td>ディレクトリとファイルのリスト</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/VisualStudioQuickStarts/DataFileStorage/Program.cs">Azure Storage .NET File Storage のサンプル</a></td> 
</tr>
<tr> 
<td>共有のリスト</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage のサンプル</a></td> 
</tr>
<tr> 
<td>共有のプロパティ/メタデータ/統計</td> 
<td><a href="https://github.com/Azure-Samples/storage-file-dotnet-getting-started/blob/9f12304b2f5f5472a1c87c1e21be4af5661ac043/FileStorage/Advanced.cs">Azure Storage .NET File Storage のサンプル</a></td> 
</tr>
<tr> 
<td rowspan="8"><b>キュー</b></td>
<td>メッセージの追加</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">.Net での Azure Queue サービスの概要</a></td> 
</tr> 
<tr> 
<td>クライアント側の暗号化</td> 
<td><a href="https://github.com/Azure/azure-storage-net/blob/master/Samples/GettingStarted/EncryptionSamples/QueueGettingStarted/Program.cs">Azure Storage .NET Queue のクライアント側の暗号化</a></td> 
</tr> 
<tr> 
<td>キューの作成</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">.Net での Azure Queue サービスの概要</a></td> 
</tr> 
<tr> 
<td>メッセージ/キューの削除</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">.Net での Azure Queue サービスの概要</a></td> 
</tr> 
<tr> 
<td>メッセージのピーク</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">.Net での Azure Queue サービスの概要</a></td> 
</tr> 
<tr> 
<td>キューの ACL/メタデータ/統計</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">.Net での Azure Queue サービスの概要</a></td> 
</tr> 
<tr> 
<td>キュー サービスのプロパティ</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/Advanced.cs">.Net での Azure Queue サービスの概要</a></td> 
</tr> 
<tr> 
<td>更新メッセージ</td> 
<td><a href="https://github.com/Azure-Samples/storage-queue-dotnet-getting-started/blob/master/QueueStorage/GettingStarted.cs">.Net での Azure Queue サービスの概要</a></td> 
</tr> 
<tr> 
<td rowspan="7"><b>テーブル</b></td>
<td>テーブルの作成</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Azure Storage でのコンカレンシー制御の管理 - サンプル アプリケーション</a></td> 
</tr> 
<tr> 
<td>エンティティ/テーブルの削除</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">.Net での Azure Table Storage の概要</a></td> 
</tr> 
<tr> 
<td>エンティティの挿入/マージ/置換</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Azure Storage でのコンカレンシー制御の管理 - サンプル アプリケーション</a></td> 
</tr> 
<tr> 
<td>エンティティのクエリ</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">.Net での Azure Table Storage の概要</a></td> 
</tr> 
<tr> 
<td>テーブルの照会</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/BasicSamples.cs">.Net での Azure Table Storage の概要</a></td> 
</tr> 
<tr> 
<td>テーブルの ACL/プロパティ</td> 
<td><a href="https://github.com/Azure-Samples/storage-table-dotnet-getting-started/blob/master/TableStorage/AdvancedSamples.cs">.Net での Azure Table Storage の概要</a></td> 
</tr> 
<tr> 
<td>エンティティの更新</td> 
<td><a href="https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114/sourcecode?fileId=123913&pathId=50196262">Azure Storage でのコンカレンシー制御の管理 - サンプル アプリケーション</a></td> 
</tr> 
</tbody> 
</table>
<br/>

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
* その他すべての言語: [Azure Storage のサンプル](../storage-samples.md)
