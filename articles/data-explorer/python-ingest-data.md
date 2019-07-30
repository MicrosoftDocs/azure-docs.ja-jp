---
title: Azure Data Explorer の Python ライブラリを使用してデータを取り込む
description: この記事では、Python を使用して Azure Data Explorer にデータを取り込む (読み込む) 方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: f109f2dd45fe90884d3947b244b3dafffd547725
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/19/2019
ms.locfileid: "68355935"
---
# <a name="ingest-data-using-the-azure-data-explorer-python-library"></a>Azure Data Explorer の Python ライブラリを使用してデータを取り込む

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 Azure Data Explorer では、Python 用のクライアント ライブラリとして、[取り込みライブラリ](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-ingest)と[データ ライブラリ](https://github.com/Azure/azure-kusto-python/tree/master/azure-kusto-data)の 2 つが用意されています。 これらのライブラリを使用すると、クラスターにデータを取り込み (読み込み)、コードからデータのクエリを行うことができます。 この記事ではまず、クラスター内にテーブルとデータ マッピングを作成します。 その後、クラスターに対するインジェストをキューに入れて、結果を検証します。

この記事は [Azure Notebook](https://notebooks.azure.com/ManojRaheja/libraries/KustoPythonSamples/html/QueuedIngestSingleBlob.ipynb) でも利用できます。

## <a name="prerequisites"></a>前提条件

* Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

* [クラスターとデータベース](create-cluster-database-portal.md)

* 開発用コンピューターに [Python](https://www.python.org/downloads/) がインストール済み

## <a name="install-the-data-and-ingest-libraries"></a>データ ライブラリと取り込みライブラリをインストールする

*azure-kusto-data* と *azure-kusto-ingest* をインストールします。

```
pip install azure-kusto-data
pip install azure-kusto-ingest
```

## <a name="add-import-statements-and-constants"></a>import ステートメントおよび定数を追加する

azure-kusto-data からクラスをインポートします。

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
from azure.kusto.data.exceptions import KustoServiceError
from azure.kusto.data.helpers import dataframe_from_result_table
```

Azure データ エクスプ ローラーでは、アプリケーションを認証するために AAD テナント ID が使用されます。 テナント ID を検索するには、次の URL を使用し、ドメインを *YourDomain* に置き換えます。

```
https://login.windows.net/<YourDomain>/.well-known/openid-configuration/
```

たとえば、ドメインが *contoso.com* の場合、URL は [https://login.windows.net/contoso.com/.well-known/openid-configuration/](https://login.windows.net/contoso.com/.well-known/openid-configuration/) になります。 結果を表示するには、この URL をクリックします。最初の行は次のとおりです。 

```
"authorization_endpoint":"https://login.windows.net/6babcaad-604b-40ac-a9d7-9fd97c0b779f/oauth2/authorize"
```

この場合のテナント ID は `6babcaad-604b-40ac-a9d7-9fd97c0b779f` です。 このコードを実行する前に、AAD_TENANT_ID、KUSTO_URI、KUSTO_INGEST_URI、KUSTO_DATABASE の値を設定します。

```python
AAD_TENANT_ID = "<TenantId>"
KUSTO_URI = "https://<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_INGEST_URI = "https://ingest-<ClusterName>.<Region>.kusto.windows.net:443/"
KUSTO_DATABASE = "<DatabaseName>"
```

では、接続文字列を作成します。 この例では、デバイス認証を使用してクラスターにアクセスします。 [AAD アプリケーション証明書](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L24)、[AAD アプリケーション キー](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L20)、および [AAD ユーザーとパスワード](https://github.com/Azure/azure-kusto-python/blob/master/azure-kusto-data/tests/sample.py#L34)を使用することもできます。

ターゲット テーブルとマッピングは後のステップで作成します。

```python
KCSB_INGEST = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_INGEST_URI, AAD_TENANT_ID)

KCSB_DATA = KustoConnectionStringBuilder.with_aad_device_authentication(
    KUSTO_URI, AAD_TENANT_ID)

DESTINATION_TABLE = "StormEvents"
DESTINATION_TABLE_COLUMN_MAPPING = "StormEvents_CSV_Mapping"
```

## <a name="set-source-file-information"></a>ソース ファイルの情報を設定する

他のクラスをインポートし、データ ソース ファイルに対する定数を設定します。 この例では、Azure Blob Storage でホストされているサンプル ファイルを使います。 **StormEvents**サンプル データ セットには､ [National Centers for Environmental Information から入手した気象関連データが含まれています](https://www.ncdc.noaa.gov/stormevents/)｡

```python
from azure.storage.blob import BlockBlobService
from azure.kusto.ingest import KustoIngestClient, IngestionProperties, FileDescriptor, BlobDescriptor, DataFormat, ReportLevel, ReportMethod

CONTAINER = "samplefiles"
ACCOUNT_NAME = "kustosamplefiles"
SAS_TOKEN = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D"
FILE_PATH = "StormEvents.csv"
FILE_SIZE = 64158321    # in bytes

BLOB_PATH = "https://" + ACCOUNT_NAME + ".blob.core.windows.net/" + \
    CONTAINER + "/" + FILE_PATH + SAS_TOKEN
```

## <a name="create-a-table-on-your-cluster"></a>クラスターにテーブルを作成する

StormEvents.csv ファイル内のデータのスキーマと一致するテーブルを作成します。 このコードを実行すると、次のようなメッセージが返されます。"*サインインするには、Web ブラウザーを使用して https://microsoft.com/devicelogin ページを開き、認証するためのコード F3W4VWZDM を入力します*。" この手順に従ってサインインし、元のページに戻って次のコード ブロックを実行します。 接続を行う後続のコード ブロックでは、再びサインインする必要があります。

```python
KUSTO_CLIENT = KustoClient(KCSB_DATA)
CREATE_TABLE_COMMAND = ".create table StormEvents (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)"

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_TABLE_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="define-ingestion-mapping"></a>インジェストのマッピングを定義する

受信した CSV データを、テーブル作成時に使用される列名とデータ型にマップします。 これにより、ソース データのフィールドがターゲットのテーブル列にマップされます。

```python
CREATE_MAPPING_COMMAND = """.create table StormEvents ingestion csv mapping 'StormEvents_CSV_Mapping' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'"""

RESPONSE = KUSTO_CLIENT.execute_mgmt(KUSTO_DATABASE, CREATE_MAPPING_COMMAND)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="queue-a-message-for-ingestion"></a>取り込みのためにメッセージをキューに入れる

BLOB ストレージからデータをプルし、そのデータを Azure Data Explorer に取り込むために、メッセージをキューに入れます。

```python
INGESTION_CLIENT = KustoIngestClient(KCSB_INGEST)

# All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties
INGESTION_PROPERTIES = IngestionProperties(database=KUSTO_DATABASE, table=DESTINATION_TABLE, dataFormat=DataFormat.csv,
                                           mappingReference=DESTINATION_TABLE_COLUMN_MAPPING, additionalProperties={'ignoreFirstRecord': 'true'})
# FILE_SIZE is the raw size of the data in bytes
BLOB_DESCRIPTOR = BlobDescriptor(BLOB_PATH, FILE_SIZE)
INGESTION_CLIENT.ingest_from_blob(
    BLOB_DESCRIPTOR, ingestion_properties=INGESTION_PROPERTIES)

print('Done queuing up ingestion with Azure Data Explorer')
```

## <a name="query-data-that-was-ingested-into-the-table"></a>テーブルに取り込まれたデータを照会する

キューに入れられたインジェストが取り込みをスケジュールされて、Azure Data Explorer にデータが読み込まれるまで、5 ～ 10 分待ちます。 その後、次のコードを実行して、StormEvents テーブル内のレコードの数を取得します。

```python
QUERY = "StormEvents | count"

RESPONSE = KUSTO_CLIENT.execute_query(KUSTO_DATABASE, QUERY)

dataframe_from_result_table(RESPONSE.primary_results[0])
```

## <a name="run-troubleshooting-queries"></a>トラブルシューティングのクエリを実行する

[https://dataexplorer.azure.com](https://dataexplorer.azure.com) にサインインして、クラスターに接続します。 データベースで次のコマンドを実行し、過去 4 時間以内にインジェスト エラーがあったかどうかを調べます。 実行する前にデータベース名を置き換えてください。

```Kusto
.show ingestion failures
| where FailedOn > ago(4h) and Database == "<DatabaseName>"
```

次のコマンドを実行し、過去 4 時間以内のすべてのインジェスト操作の状態を表示します。 実行する前にデータベース名を置き換えてください。

```Kusto
.show operations
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Table == "StormEvents" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

他の記事に進む場合は、作成したリソースをそのままにします。 行わない場合は、データベースで次のコマンドを実行して、StormEvents テーブルをクリーンアップします。

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>次の手順

* [Python を使用したデータのクエリ](python-query-data.md)
