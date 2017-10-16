---
title: "データ サイエンス仮想マシンのデータ取り込みツール - Azure | Microsoft Docs"
description: "データ サイエンス仮想マシンのデータ取り込みツール"
keywords: "データ サイエンス ツール,データ サイエンス仮想マシン, データ サイエンス用ツール, linux データ サイエンス"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/11/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: 564c06c5017a77431b7d6fed7b43c47141b12252
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="data-science-virtual-machine-data-ingestion-tools"></a>データ サイエンス仮想マシンのデータ取り込みツール

データ サイエンスまたは AI プロジェクトの最初の技術的ステップの 1 つは、使用するデータセットを識別し、それらを分析環境に取り込むことです。 データ サイエンス仮想マシン (DSVM) は、DSVM 上のローカルの分析データ ストレージに、あるいはクラウドまたは内部設置型のデータ プラットフォームに、さまざまなソースからデータを取り込むためのツールおよびライブラリを提供します。 

ここでは、DSVM で提供しているデータ移動ツールの一部を示します。 

## <a name="adlcopy"></a>AdlCopy

|    |           |
| ------------- | ------------- |
| 紹介   | Azure Storage BLOB から Azure Data Lake Store にデータをコピーするツール。 2 つの Azure Data Lake Store アカウント間でデータをコピーすることもできます。      |
| サポートされている DSVM バージョン      | Windows      |
| 一般的な用途      | Azure ストレージから Azure Data Lake Store に複数の BLOB をインポートします。      |
|  使用/実行方法    |   コマンド プロンプトを開き、`adlcopy` と入力してヘルプを表示します。    |
| サンプルへのリンク      | [Using AdlCopy]https://docs.microsoft.com/en-us/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob)      |
| DSVM 上の関連ツール      | AzCopy、Azure コマンド ライン     |

## <a name="azure-command-line"></a>Azure コマンド ライン

|    |           |
| ------------- | ------------- |
| 紹介   | Azure の管理ツール。 Azure ストレージ BLOB、Azure Data Lake Storage などの Azure データ プラットフォームからデータを移動するためのコマンド動詞も含まれています。     |
| サポートされている DSVM バージョン      | Windows、Linux     |
| 一般的な用途      | Azure ストレージ、Azure Data Lake Store との間でデータをインポートおよびエクスポートします。      |
|  使用/実行方法    |   コマンド プロンプトを開き、`az` と入力してヘルプを表示します。    |
| サンプルへのリンク      | [Azure CLI の使用](https://docs.microsoft.com/cli/azure/?viee-cli-latest)     |
| DSVM 上の関連ツール      | AzCopy、AdlCopy      |


## <a name="azcopy"></a>AzCopy

|    |           |
| ------------- | ------------- |
| 紹介   | ローカル ファイル、Azure ストレージ BLOB、ファイル、およびテーブルとの間でデータをコピーするためのツール。      |
| サポートされている DSVM バージョン      | Windows      |
| 一般的な用途      | BLOB ストレージにファイルをコピー、アカウント間で BLOB をコピーします。      |
|  使用/実行方法    |   コマンド プロンプトを開き、`azcopy` と入力してヘルプを表示します。    |
| サンプルへのリンク      | [Windows での AzCopy](https://docs.microsoft.com/en-us/azure/storage/common/storage-use-azcopy)      |
| DSVM 上の関連ツール      | AdlCopy     |


## <a name="azure-cosmos-db-documentdb-api-data-migration-tool"></a>Azure Cosmos DB: DocumentDB API データ移行ツール

|    |           |
| ------------- | ------------- |
| 紹介   | JSON ファイル、CSV ファイル、SQL、MongoDB、Azure Table ストレージ、Amazon DynamoDB、Azure Cosmos DB DocumentDB API コレクションなどのさまざまななソースから、Azure Cosmos DB または Azure DocumentDB にデータをインポートするツール。      |
| サポートされている DSVM バージョン      | Windows      |
| 一般的な用途      | VM から CosmosDB にファイルをインポート、Azure テーブル ストレージから CosmosDB にデータをインポート、または SQL Server データベースから CosmosDB にデータをインポートします。     |
|  使用/実行方法    |   コマンド ライン バージョンを使用するには、コマンド プロンプトを開いて `dt` と入力します。 GUI ツールを使用するには、コマンド プロンプトを開いて `dtui` と入力します。    |
| サンプルへのリンク      | [CosmosDB インポート データ](https://docs.microsoft.com/en-us/azure/cosmos-db/import-data)      |
| DSVM 上の関連ツール      | AzCopy、AdlCopy      |


## <a name="bcp"></a>bcp

|    |           |
| ------------- | ------------- |
| 紹介   | SQL Server とデータ ファイルの間でデータをコピーする SQL Server ツール。      |
| サポートされている DSVM バージョン      | Windows      |
| 一般的な用途      | CSV ファイルを SQL Server テーブルにインポート、SQL Server テーブルをファイルにエクスポートします。      |
|  使用/実行方法    |   コマンド プロンプトを開き、`bcp` と入力してヘルプを表示します。    |
| サンプルへのリンク      | [一括コピー ユーティリティ](https://docs.microsoft.com/en-us/sql/tools/bcp-utility)      |
| DSVM 上の関連ツール      | SQL Server、sqlcmd      |


## <a name="microsoft-data-management-gateway"></a>Microsoft Data Management Gateway

|    |           |
| ------------- | ------------- |
| 紹介   | 内部設置型データ ソースを、使用のためにクラウド サービスに接続するツール。      |
| サポートされている DSVM バージョン      | Windows      |
| 一般的な用途      | 内部設置型データ ソースに VM を接続します。      |
|  使用/実行方法    |   [スタート] メニューから "Microsoft Data Management Gateway" を起動します。    |
| サンプルへのリンク      | [Data Management Gateway](https://msdn.microsoft.com/library/dn879362.aspx)      |
| DSVM 上の関連ツール      | AzCopy、AdlCopy、bcp    |
