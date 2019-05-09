---
title: 'クイック スタート: Azure Data Explorer の Node ライブラリを使用してデータを取り込む'
description: このクイック スタートでは、Node.js を使用して Azure Data Explorer にデータを取り込む (読み込む) 方法について説明します。
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: quickstart
ms.date: 10/25/2018
ms.openlocfilehash: d530ed3710a3406e5cd8115d615e91d5056a5f8c
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59047655"
---
# <a name="quickstart-ingest-data-using-the-azure-data-explorer-node-library"></a>クイック スタート: Azure Data Explorer の Node ライブラリを使用してデータを取り込む

Azure Data Explorer は、ログと利用統計情報データのための高速で拡張性に優れたデータ探索サービスです。 Azure Data Explorer では、Node 用のクライアント ライブラリとして、[取り込みライブラリ](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-ingest)と[データ ライブラリ](https://github.com/Azure/azure-kusto-node/tree/master/azure-kusto-data)の 2 つが用意されています。 これらのライブラリを使用すると、クラスターにデータを取り込み (読み込み)、コードからデータのクエリを行うことができます。 このクイック スタートではまず、テスト クラスター内にテーブルとデータ マッピングを作成します。 その後、クラスターに対するインジェストをキューに入れて、結果を検証します。

Azure サブスクリプションをお持ちでない場合は、開始する前に[無料の Azure アカウント](https://azure.microsoft.com/free/)を作成してください。

## <a name="prerequisites"></a>前提条件

このクイック スタートを実行するには、Azure サブスクリプションに加え、以下が必要です。

* [テスト用のクラスターとデータベース](create-cluster-database-portal.md)

* 開発用コンピューターに [Node.js](https://nodejs.org/en/download/) がインストール済み

## <a name="install-the-data-and-ingest-libraries"></a>データ ライブラリと取り込みライブラリをインストールする

*azure-kusto-ingest* と *azure-kusto-data* をインストールします。

```bash
npm i --save azure-kusto-ingest azure-kusto-data
```

## <a name="add-import-statements-and-constants"></a>import ステートメントおよび定数を追加する

ライブラリからクラスをインポートします。

```javascript

const KustoConnectionStringBuilder = require("azure-kusto-data").KustoConnectionStringBuilder;
const KustoClient = require("azure-kusto-data").Client;
const KustoIngestClient = require("azure-kusto-ingest").IngestClient;
const IngestionProperties = require("azure-kusto-ingest").IngestionProperties;
const { DataFormat } = require("azure-kusto-ingest").IngestionPropertiesEnums;
const { BlobDescriptor } = require("azure-kusto-ingest").IngestionDescriptors;

```
Azure Data Explorer では、アプリケーションを認証するために Azure Active Directory テナント ID が使用されます。 テナント ID を検索するには、[Office 365 のテナント ID の検索](https://docs.microsoft.com/onedrive/find-your-office-365-tenant-id)に関するページの手順に従ってください。

このコードを実行する前に、`authorityId`、`kustoUri`、`kustoIngestUri`、`kustoDatabase` の値を設定します。

```javascript
const cluster = "MyCluster";
const region = "westus";
const authorityId = "microsoft.com";
const kustoUri = `https://${cluster}.${region}.kusto.windows.net:443`;
const kustoIngestUri = `https://ingest-${cluster}.${region}.kusto.windows.net:443`;
const kustoDatabase  = "Weather";
```

では、接続文字列を作成します。 この例では、デバイス認証を使用してクラスターにアクセスします。 Azure Active Directory のアプリケーション証明書とアプリケーション キー、ユーザー、パスワードを使用することもできます。

ターゲット テーブルとマッピングは後のステップで作成します。

```javascript
const kcsbIngest = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoIngestUri, authorityId);
const kcsbData = KustoConnectionStringBuilder.withAadDeviceAuthentication(kustoUri, authorityId);
const destTable = "StormEvents";
const destTableMapping = "StormEvents_CSV_Mapping";
```

## <a name="set-source-file-information"></a>ソース ファイルの情報を設定する

他のクラスをインポートし、データ ソース ファイルに対する定数を設定します。 この例では、Azure Blob Storage でホストされているサンプル ファイルを使います。 **StormEvents**サンプル データ セットには､ [National Centers for Environmental Information から入手した気象関連データが含まれています](https://www.ncdc.noaa.gov/stormevents/)｡

```javascript
const container = "samplefiles";
const account = "kustosamplefiles";
const sas = "?st=2018-08-31T22%3A02%3A25Z&se=2020-09-01T22%3A02%3A00Z&sp=r&sv=2018-03-28&sr=b&sig=LQIbomcKI8Ooz425hWtjeq6d61uEaq21UVX7YrM61N4%3D";
const filePath = "StormEvents.csv";
const blobPath = `https://${account}.blob.core.windows.net/${container}/${filePath}${sas}`;
```

## <a name="create-a-table-on-your-test-cluster"></a>テスト クラスターにテーブルを作成する

`StormEvents.csv` ファイル内のデータのスキーマと一致するテーブルを作成します。 このコードを実行すると、次のようなメッセージが返されます。"*サインインするには、Web ブラウザーを使用して https://microsoft.com/devicelogin ページを開き、認証するためのコード XXXXXXXXX を入力します*"。 この手順に従ってサインインし、元のページに戻って次のコード ブロックを実行します。 接続を行う後続のコード ブロックでは、再びサインインする必要があります。

```javascript
const kustoClient = new KustoClient(kcsbData);
const createTableCommand = `.create table ${destTable} (StartTime: datetime, EndTime: datetime, EpisodeId: int, EventId: int, State: string, EventType: string, InjuriesDirect: int, InjuriesIndirect: int, DeathsDirect: int, DeathsIndirect: int, DamageProperty: int, DamageCrops: int, Source: string, BeginLocation: string, EndLocation: string, BeginLat: real, BeginLon: real, EndLat: real, EndLon: real, EpisodeNarrative: string, EventNarrative: string, StormSummary: dynamic)`;

kustoClient.executeMgmt(kustoDatabase, createTableCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="define-ingestion-mapping"></a>インジェストのマッピングを定義する

受信した CSV データを、テーブル作成時に使用される列名とデータ型にマップします。

```javascript
const createMappingCommand = `.create table ${destTable} ingestion csv mapping '${destTableMapping}' '[{"Name":"StartTime","datatype":"datetime","Ordinal":0}, {"Name":"EndTime","datatype":"datetime","Ordinal":1},{"Name":"EpisodeId","datatype":"int","Ordinal":2},{"Name":"EventId","datatype":"int","Ordinal":3},{"Name":"State","datatype":"string","Ordinal":4},{"Name":"EventType","datatype":"string","Ordinal":5},{"Name":"InjuriesDirect","datatype":"int","Ordinal":6},{"Name":"InjuriesIndirect","datatype":"int","Ordinal":7},{"Name":"DeathsDirect","datatype":"int","Ordinal":8},{"Name":"DeathsIndirect","datatype":"int","Ordinal":9},{"Name":"DamageProperty","datatype":"int","Ordinal":10},{"Name":"DamageCrops","datatype":"int","Ordinal":11},{"Name":"Source","datatype":"string","Ordinal":12},{"Name":"BeginLocation","datatype":"string","Ordinal":13},{"Name":"EndLocation","datatype":"string","Ordinal":14},{"Name":"BeginLat","datatype":"real","Ordinal":16},{"Name":"BeginLon","datatype":"real","Ordinal":17},{"Name":"EndLat","datatype":"real","Ordinal":18},{"Name":"EndLon","datatype":"real","Ordinal":19},{"Name":"EpisodeNarrative","datatype":"string","Ordinal":20},{"Name":"EventNarrative","datatype":"string","Ordinal":21},{"Name":"StormSummary","datatype":"dynamic","Ordinal":22}]'`;

kustoClient.executeMgmt(kustoDatabase, createMappingCommand, (err, results) => {
    console.log(results.primaryResults[0][0].toString());
});
```

## <a name="queue-a-message-for-ingestion"></a>取り込みのためにメッセージをキューに入れる

BLOB ストレージからデータをプルし、そのデータを Azure Data Explorer に取り込むために、メッセージをキューに入れます。

```javascript
const defaultProps  = new IngestionProperties(kustoDatabase, destTable, DataFormat.csv, null,destTableMapping, {'ignoreFirstRecord': 'true'});
const ingestClient = new KustoIngestClient(kcsbIngest, defaultProps);
// All ingestion properties are documented here: https://docs.microsoft.com/azure/kusto/management/data-ingest#ingestion-properties

const blobDesc = new BlobDescriptor(blobPath, 10);
ingestClient.ingestFromBlob(blobDesc,null, (err) => {
    if (err) throw new Error(err);
});
```

## <a name="validate-that-table-contains-data"></a>テーブルにデータが格納されていることを確認する

データがテーブルに取り込まれたことを確認します。 キューに入れられたインジェストが取り込みをスケジュールされて、Azure Data Explorer にデータが読み込まれるまで、5 ～ 10 分待ちます。 その後、次のコードを実行して、`StormEvents` テーブル内のレコードの数を取得します。

```javascript
const query = `${destTable} | count`;

kustoClient.execute(kustoDatabase, query, (err, results) => {
    if (err) throw new Error(err);  
    console.log(results.primaryResults[0][0].toString());
});
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
| where StartedOn > ago(4h) and Database == "<DatabaseName>" and Operation == "DataIngestPull"
| summarize arg_max(LastUpdatedOn, *) by OperationId
```

## <a name="clean-up-resources"></a>リソースのクリーンアップ

他のクイック スタートやチュートリアルを行う場合は、作成したリソースをそのままにします。 行わない場合は、データベースで次のコマンドを実行して、`StormEvents` テーブルをクリーンアップします。

```Kusto
.drop table StormEvents
```

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [クエリを作成する](write-queries.md)
