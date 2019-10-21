---
title: JavaScript を使用した Azure Storage サンプル | Microsoft Docs
description: Azure Storage のサンプル コードとアプリケーションを表示、ダウンロード、実行します。 JavaScript/Node.js のストレージ クライアント ライブラリを使用して、BLOB、キュー、テーブル、ファイルのサンプルの概要について説明します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/26/2019
ms.service: storage
ms.subservice: common
ms.topic: sample
ms.openlocfilehash: 363739bbab67b92e78e0cdb56cf98cdb8e1446eb
ms.sourcegitcommit: 7f6d986a60eff2c170172bd8bcb834302bb41f71
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/27/2019
ms.locfileid: "71350891"
---
# <a name="azure-storage-samples-using-javascript"></a>JavaScript を使用した Azure Storage サンプル

次の表は、各サンプルで扱っているサンプル リポジトリとシナリオの概要を示したものです。 リンクをクリックすると、対応するサンプル コードが GitHub で表示されます。

## <a name="blob-samples"></a>BLOB のサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| ブロック BLOB | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L43) |
| クライアント側の暗号化 | [JavaScript を使用した Azure Key Value でのストレージ アカウント キーの管理](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| BLOB のコピー | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L73) |
| コンテナーの作成 | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L54) |
| Delete Blob | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L103) |
| Delete Container | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L110) |
| BLOB のメタデータ | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L538) |
| BLOB のプロパティ | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L478) |
| コンテナー ACL | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L444) |
| コンテナー メタデータ | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L409) |
| コンテナーのプロパティ | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L377) |
| ページ範囲の取得 | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L170) |
| Lease Blob | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L216) |
| Lease Container | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L185) |
| BLOB/コンテナーのリスト | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L134) |
| ページ BLOB | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L129) |
| SAS | [JavaScript での Shared Access Signature](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L257) |
| サービスのプロパティ | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L308) |
| CORS ルールを設定する | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/advanced.js#L152) |
| Snapshot Blob | [JavaScript での Azure Blob service の概要](https://github.com/Azure-Samples/storage-blob-node-getting-started/blob/master/basic.js#L79) |

## <a name="file-samples"></a>ファイルのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| 共有/ディレクトリ/ファイルの作成 | [JavaScript での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L97) |
| 共有/ディレクトリ/ファイルの削除 | [JavaScript での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L135) |
| ファイルのダウンロード | [JavaScript での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L128) |
| ディレクトリとファイルのリスト | [JavaScript での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L115) |
| 共有のリスト | [JavaScript での Azure File サービスの概要](https://github.com/Azure-Samples/storage-file-node-getting-started/blob/master/fileSample.js#L187) |

## <a name="queue-samples"></a>キューのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| メッセージの追加 | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L142) |
| クライアント側の暗号化 | [JavaScript を使用した Azure Key Vault でのストレージ アカウント キーの管理](https://github.com/Azure-Samples/key-vault-node-storage-accounts) |
| キューの作成 | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L57) |
| メッセージの削除 | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L164) |
| キューの削除 | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L203) |
| キューを一覧表示する | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L111) |
| メッセージのピーク | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L170) |
| キューの ACL | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L192) |
| キューの CORS ルール | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L55) |
| キューのメタデータ | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L161) |
| キュー サービスのプロパティ | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L94) |
| キューの統計 | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/advanced.js#L149) |
| 更新メッセージ | [JavaScript での Azure Queue サービスの概要](https://github.com/Azure-Samples/storage-queue-node-getting-started/blob/master/basic.js#L176) |

## <a name="table-samples"></a>テーブルのサンプル

| **シナリオ** | **サンプル コード** |
|--------------|-----------------|
| Batch のエンティティ | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L87) |
| テーブルの作成 | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L41) |
| エンティティ/テーブルの削除 | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L67) |
| エンティティの挿入/マージ/置換 | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |
| テーブルのリスト | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L63) |
| エンティティのクエリ | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L59) |
| テーブルの照会 | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L140) |
| 範囲クエリ | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L102) |
| SAS | [JavaScript での Shared Access Signature](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L87) |
| テーブルの ACL | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L255) |
| テーブルの CORS ルール | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L149) |
| テーブルのプロパティ | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L188) |
| テーブルの統計 | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/advanced.js#L243) |
| エンティティの更新 | [JavaScript での Azure Table service の概要](https://github.com/Azure-Samples/storage-table-node-getting-started/blob/master/basic.js#L49) |

## <a name="azure-code-samples-library"></a>Azure のコード サンプル ライブラリ

完全なサンプル ライブラリを表示するには、[Azure のコード サンプル](https://azure.microsoft.com/resources/samples/?service=storage) ライブラリにアクセスしてください。このライブラリには、ダウンロードしてローカルで実行できる Azure Storage のサンプルが用意されています。 コード サンプル ライブラリでは、サンプル コードが .zip 形式で提供されます。 また、各サンプルの GitHub リポジトリを参照して複製することもできます。

[!INCLUDE [storage-node-samples-include](../../../includes/storage-node-samples-include.md)]

## <a name="getting-started-guides"></a>概要ガイド

Azure Storage ライブラリのインストール方法と概要については、以下のガイドをご覧ください。

* [JavaScript での Azure Blob service の概要](../blobs/storage-quickstart-blobs-nodejs.md)
* [JavaScript での Azure Queue サービスの概要](../queues/storage-nodejs-how-to-use-queues.md)
* [JavaScript での Azure Table service の概要](../../cosmos-db/table-storage-how-to-use-nodejs.md)

## <a name="next-steps"></a>次の手順

他の言語のサンプルについては、以下のページをご覧ください。

* .NET:[.NET を使用した Azure Storage サンプル](storage-samples-dotnet.md)
* Java:[Java を使用した Azure Storage サンプル](storage-samples-java.md)
* Python: [Python を使用した Azure Storage サンプル](storage-samples-python.md)
* その他すべての言語: [Azure Storage のサンプル](storage-samples.md)
