---
title: Azure 上の Jupyter Notebook からデータ リソースにアクセスする
description: Jupyter Notebook からファイル、REST API、データベース、さまざまな Azure Storage リソースにアクセスする方法です。
services: app-service
documentationcenter: ''
author: kraigb
manager: douge
ms.assetid: ee867303-a5e5-4686-b2da-8a0108247d18
ms.service: azure-notebooks
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2018
ms.author: kraigb
ms.openlocfilehash: 3dd1dde067a2ec745d7d60a78a50ab34ea30d0dd
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359807"
---
# <a name="access-cloud-data-in-a-notebook"></a>ノートブックでクラウド データにアクセスする

Jupyter Notebook で面白い仕事を行うには、データが必要です。 実際、データは Notebook に不可欠なものです。

もちろん、[データ ファイルをプロジェクトにインポートする](work-with-project-data-files.md)こともできます。`curl` などのコマンドを使用してノートブック内から直接ファイルをダウンロードすることさえできます。 ただし、REST API、リレーショナル データベース、クラウド ストレージ (Azure テーブルなど) といったファイル以外のソースから利用可能なさらに広範なデータを使用することが必要になる可能性があります。

この記事では、これらのさまざまなオプションについて簡単に説明します。 データ アクセスは動いているところを見るのが最もよくわかるので、「[Azure Notebooks Samples - Access your data](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb)」(Azure Notebooks サンプル - データにアクセスする) の実行可能なコードをご覧ください。

## <a name="rest-apis"></a>REST API

一般に、インターネットから使用できる膨大な量のデータには、ファイルではなく、REST API を介してアクセスします。 さいわい、ノートブックのセルにはどのようなコードでも含めることができるので、コードを使用して、要求を送信し、JSON データを受信することができます。 その後、その JSON を、Pandas データフレームなどの任意の形式に変換して使用できます。

REST API を使用してデータにアクセスするには、他の任意のアプリケーションで使用するのと同じコードをノートブックのコード セルで使用します。 要求ライブラリを使用する一般的な構造は次のとおりです。

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-databases"></a>Azure SQL データベース

pyodbc または pymssql ライブラリを利用して、SQL Server データベースにアクセスできます。

「[Python を使用して Azure SQL データベースに照会する](https://docs.microsoft.com/azure/sql-database/sql-database-connect-query-python)」では、AdventureWorks のデータを含むデータベースを作成する方法と、そのデータのクエリを行う方法が示されています。 この記事のサンプルのノートブックには同じコードがあります。

## <a name="azure-storage"></a>Azure Storage

Azure Storage では、データの種類と、必要なアクセス方法に応じて、複数の異なる非リレーショナル ストレージが提供されています。

- Table Storage: 収集されたセンサー ログや診断ログなど、表形式データ用に低コストで大量のストレージを提供します。
- Blob Storage: 任意の種類のデータ用に、ファイルのようなストレージを提供します。

サンプルのノートブックではテーブルと BLOB 両方の操作が示されており、BLOB の読み取り専用アクセスを許可する共有アクセス署名の使用方法が含まれます。

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB では、JSON ドキュメント用に完全にインデックス付けされた NoSQL ストアが提供されています。 次の記事では、Python から Cosmos DB を使用するさまざまな方法が示されています。

- [Python で SQL API アプリを作成する](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-python)
- [Azure Cosmos DB の MongoDB 用 API による Flask アプリの構築](https://docs.microsoft.com/azure/cosmos-db/create-mongodb-flask)
- [Python と Gremlin API を使用してグラフ データベースを作成する](https://docs.microsoft.com/azure/cosmos-db/create-graph-python)
- [Python と Azure Cosmos DB で Cassandra アプリを構築する](https://docs.microsoft.com/azure/cosmos-db/create-cassandra-python)
- [Python と Azure Cosmos DB で Table API アプリを構築する](https://docs.microsoft.com/azure/cosmos-db/create-table-python)

Cosmos DB を使用するときは、[azure-cosmosdb-table](https://pypi.org/project/azure-cosmosdb-table/) ライブラリを使用できます。

## <a name="other-azure-databases"></a>他の Azure データベース

Azure で使用できるデータベースが他にもいくつか提供されています。 以下の記事では、Python からそれらのデータベースにアクセスするためのガイダンスが示されています。

- [Azure Database for PostgreSQL:Python を使って接続とデータの照会を行う](https://docs.microsoft.com/azure/postgresql/connect-python)
- [クイック スタート:Python で Azure Redis Cache を使用する](https://docs.microsoft.com/azure/redis-cache/cache-python-get-started)
- [Azure Database for MySQL:Python を使って接続とデータの照会を行う](https://docs.microsoft.com/azure/mysql/connect-python)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Azure Data Factory のコピー ウィザード](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>次の手順

- [方法:プロジェクト データ ファイルを操作する](work-with-project-data-files.md)
