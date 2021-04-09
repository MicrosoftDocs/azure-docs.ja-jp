---
title: データ取り込みツール
titleSuffix: Azure Data Science Virtual Machine
description: Data Science Virtual Machine にプレインストールされているデータ インジェスト ツールとユーティリティについて説明します。
keywords: データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス
services: machine-learning
ms.service: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: 21a6efa8108bfd0a317eb955e8b3ffcfba0862a2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100519373"
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>データ サイエンス仮想マシンのデータ取り込みツール

データ サイエンスまたは AI プロジェクトの最初の技術的ステップの 1 つとして、使用するデータセットを識別し、それらを分析環境に取り込む必要があります。 Data Science Virtual Machine (DSVM) は、DSVM 上のローカルの分析データ ストレージに、あるいはクラウドまたはオンプレミスのデータ プラットフォームに、さまざまなソースからデータを取り込むためのツールおよびライブラリを提供します。

ここでは、DSVM で利用できるデータ移動ツールの一部を示します。

## <a name="adlcopy"></a>AdlCopy

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | Azure Blob Storage から Azure Data Lake Store にデータをコピーするツール。 2 つの Azure Data Lake Store アカウント間でデータをコピーすることもできます。      |
| サポートされている DSVM バージョン      | Windows      |
| 標準的な使用      | Azure Blob Storage から Azure Data Lake Store に複数の BLOB をインポートします。      |
|  使用/実行方法    |   コマンド プロンプトを開き、「`adlcopy`」と入力してヘルプを表示します。    |
| サンプルへのリンク      | [AdlCopy を使用](../../data-lake-store/data-lake-store-copy-data-azure-storage-blob.md)      |
| DSVM 上の関連ツール      | AzCopy、Azure CLI     |

## <a name="azure-cli"></a>Azure CLI

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | Azure の管理ツール。 Azure Blob Storage、Azure Data Lake Store などの Azure データ プラットフォームからデータを移動するためのコマンド動詞も含まれています。     |
| サポートされている DSVM バージョン      | Windows、Linux     |
| 標準的な使用      | Azure Storage と Azure Data Lake Store との間でデータをインポートおよびエクスポートします。      |
|  使用/実行方法    |   コマンド プロンプトを開き、「`az`」と入力してヘルプを表示します。    |
| サンプルへのリンク      | [Azure CLI の使用](/cli/azure)     |
| DSVM 上の関連ツール      | AzCopy、AdlCopy      |


## <a name="azcopy"></a>AzCopy

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | ローカル ファイル、Azure Blob Storage、ファイル、およびテーブルとの間でデータをコピーするためのツール。      |
| サポートされている DSVM バージョン      | Windows      |
| 標準的な使用      | Azure Blob Storage にファイルをコピーし、アカウント間で BLOB をコピーします。      |
|  使用/実行方法    |   コマンド プロンプトを開き、「`azcopy`」と入力してヘルプを表示します。    |
| サンプルへのリンク      | [Windows での AzCopy](../../storage/common/storage-use-azcopy-v10.md)      |
| DSVM 上の関連ツール      | AdlCopy     |


## <a name="azure-cosmos-db-data-migration-tool"></a>Azure Cosmos DB データ移行ツール

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | さまざまなソースからクラウド内の NoSQL データベースである Azure Cosmos DB にデータをインポートするツール。 ソースは、JSON ファイル、CSV ファイル、SQL、MongoDB、Azure Table Storage、Amazon DynamoDB、Azure Cosmos DB SQL API コレクションなどです。      |
| サポートされている DSVM バージョン      | Windows      |
| 標準的な使用      | VM から CosmosDB にファイルをインポート、Azure Table Storageから CosmosDB にデータをインポート、および Microsoft SQL Server データベースから CosmosDB にデータをインポートします。     |
|  使用/実行方法    |   コマンド ライン バージョンを使用するには、コマンド プロンプトを開いて「`dt`」と入力します。 GUI ツールを使用するには、コマンド プロンプトを開いて「`dtui`」と入力します。    |
| サンプルへのリンク      | [CosmosDB インポート データ](../../cosmos-db/import-data.md)      |
| DSVM 上の関連ツール      | AzCopy、AdlCopy      |

## <a name="azure-storage-explorer"></a>Azure ストレージ エクスプローラー

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | Azure クラウドに格納されているファイルと対話するためのグラフィカル ユーザー インターフェイス。 |
| サポートされている DSVM バージョン      | Windows      |
| 標準的な使用      | DSVM からのデータのインポートとエクスポート。    |
|  使用/実行方法    | [スタート] メニュー内で "Azure Storage Explorer" を検索します。 |
| サンプルへのリンク      | [Azure 記憶域エクスプローラー](vm-do-ten-things.md#access-azure-data-and-analytics-services)      |


## <a name="bcp"></a>bcp

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | SQL Server とデータ ファイルの間でデータをコピーする SQL Server ツール。      |
| サポートされている DSVM バージョン      | Windows      |
| 標準的な使用      | CSV ファイルを SQL Server テーブルにインポートし、SQL Server テーブルをファイルにエクスポートします。      |
|  使用/実行方法    |   コマンド プロンプトを開き、「`bcp`」と入力してヘルプを表示します。    |
| サンプルへのリンク      | [bcp ユーティリティ](/sql/tools/bcp-utility)      |
| DSVM 上の関連ツール      | SQL Server、sqlcmd      |

## <a name="blobfuse"></a>blobfuse

| カテゴリ | 値 |
| ------------- | ------------- |
| 紹介   | Linux ファイル システムで Azure Blob Storage コンテナーをマウントするツール。      |
| サポートされている DSVM バージョン      | Linux      |
| 標準的な使用      | コンテナー内の BLOB に対する読み取りと書き込み。      |
|  使用と実行の方法    |   端末で _blobfuse_ を実行します。    |
| サンプルへのリンク      | [GitHub の blobfuse](https://github.com/Azure/azure-storage-fuse)      |
| DSVM 上の関連ツール      | Azure CLI      |