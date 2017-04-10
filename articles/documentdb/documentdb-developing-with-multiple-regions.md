---
title: "DocumentDB での複数リージョンを使用した開発 | Microsoft Docs"
description: "完全に管理された NoSQL データベース サービスである Azure DocumentDB で複数のリージョンにあるデータにアクセスする方法について説明します。"
services: documentdb
documentationcenter: 
author: mimig1
manager: jhubbard
editor: 
ms.assetid: d4579378-0b3a-44a5-9f5b-630f1fa4c66d
ms.service: documentdb
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: mimig
translationtype: Human Translation
ms.sourcegitcommit: 240f45383639f1eb8a4a9b7c4e4489591be23dba
ms.openlocfilehash: aac8c26cd9af94659abc80b2ae260dc380cf38c9
ms.lasthandoff: 02/10/2017


---
# <a name="developing-with-multi-region-documentdb-accounts"></a>複数リージョンの DocumentDB アカウントを使用した開発

複数リージョンの DocumentDB アカウントについては、Scott Hanselman とプリンシパル エンジニアリング マネージャー Karthik Raman による次の Azure Friday ビデオをご覧ください。

>[!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Planet-Scale-NoSQL-with-DocumentDB/player]  

## <a name="introduction"></a>はじめに

[グローバル配布](documentdb-distribute-data-globally.md)を活用するために、クライアント アプリケーションでは、ドキュメントの操作の実行に使用するリージョンの順序付き優先リストを指定できます。 これは、接続ポリシーを設定して行います。 Azure DocumentDB アカウント構成、現在のリージョンの可用性、指定された優先リストに基づいて、書き込み操作と読み取り操作を実行する SDK によって最適なエンドポイントが選択されます。

この優先リストは、DocumentDB クライアント SDK を使用して接続を初期化する際に指定されます。 SDK は、Azure リージョンの順序付きリストである省略可能なパラメーター "PreferredLocations" を受け取ります。

SDK は、すべての書き込みを現在の書き込みリージョンに自動的に送信します。

すべての読み取りは、PreferredLocations リストの最初の利用可能なリージョンに送信されます。 要求が失敗すると、クライアントはリストにある次のリージョンを試します。これが繰り返されます。

クライアント SDK は、PreferredLocations で指定されたリージョンからの読み取りのみを試みます。 このため、3 つのリージョンでデータベース アカウントが利用できるものの、クライアントが PreferredLocations の非書き込みリージョンを&2; つだけ指定している場合などには、書き込みリージョンの外で読み取りが処理されません。これはフェールオーバーの場合にもあてはまります。

アプリケーションは、WriteEndpoint と ReadEndpoint の 2 つのプロパティをチェックすることで、SDK によって選択された現在の書き込みエンドポイントと読み取りエンドポイントを確認できます。これらのプロパティは SDK バージョン 1.8 以上で利用可能です。

PreferredLocations プロパティが設定されていない場合、すべての要求が現在の書き込みリージョンから処理されます。

## <a name="net-sdk"></a>.NET SDK
SDK はコードに変更を加えることなく使用できます。 この場合、SDK は読み取りと書き込みの両方を現在の書き込みリージョンに自動的に転送します。

.NET SDK のバージョン 1.8 以降では、DocumentClient コンストラクターの ConnectionPolicy パラメーターに Microsoft.Azure.Documents.ConnectionPolicy.PreferredLocations という名前のプロパティがあります。 このプロパティは、コレクション型 `<string>` であり、リージョン名のリストを含んでいる必要があります。 文字列値は、「[Azure のリージョン][regions]」ページのリージョン名の列ごとに書式設定されます。先頭と末尾の文字のそれぞれ前後にはスペースはありません。

現在の書き込みエンドポイントと読み取りエンドポイントはそれぞれ、DocumentClient.WriteEndpoint と DocumentClient.ReadEndpoint で使用できます。

> [!NOTE]
> エンドポイントの URL は、有効期間が長い定数と見なさないでください。 これらは任意のタイミングでサービスによって更新される可能性があります。 SDK はこの変更を自動的に処理します。
>
>

    // Getting endpoints from application settings or other configuration location
    Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
    string accountKey = Properties.Settings.Default.GlobalDatabaseKey;
    
    ConnectionPolicy connectionPolicy = new ConnectionPolicy();

    //Setting read region selection preference
    connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
    connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
    connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

    // initialize connection
    DocumentClient docClient = new DocumentClient(
        accountEndPoint,
        accountKey,
        connectionPolicy);

    // connect to DocDB
    await docClient.OpenAsync().ConfigureAwait(false);


## <a name="nodejs-javascript-and-python-sdks"></a>NodeJS SDK、JavaScript SDK、Python SDK
SDK はコードに変更を加えることなく使用できます。 この場合、SDK は読み取りと書き込みの両方を現在の書き込みリージョンに自動的に転送します。

各 SDK のバージョン 1.8 以降では、DocumentClient コンストラクターの ConnectionPolicy パラメーターに DocumentClient.ConnectionPolicy.PreferredLocations という名前の新しいプロパティがあります。 このパラメーターは、リージョン名のリストを受け取る文字列の配列です。 この名前は「[Azure のリージョン][regions]」ページのリージョン名の列ごとに書式設定されます。 便利なオブジェクト AzureDocuments.Regions で事前定義された定数を使用することもできます。

現在の書き込みエンドポイントと読み取りエンドポイントはそれぞれ、DocumentClient.getWriteEndpoint と DocumentClient.getReadEndpoint で使用できます。

> [!NOTE]
> エンドポイントの URL は、有効期間が長い定数と見なさないでください。 これらは任意のタイミングでサービスによって更新される可能性があります。 SDK はこの変更を自動的に処理します。
>
>

NodeJS/Javascript のコード例を以下に示します。 Python と Java では同じパターンを使用します。

    // Creating a ConnectionPolicy object
    var connectionPolicy = new DocumentBase.ConnectionPolicy();

    // Setting read region selection preference, in the following order -
    // 1 - West US
    // 2 - East US
    // 3 - North Europe
    connectionPolicy.PreferredLocations = ['West US', 'East US', 'North Europe'];

    // initialize the connection
    var client = new DocumentDBClient(host, { masterKey: masterKey }, connectionPolicy);


## <a name="rest"></a>REST ()
データベース アカウントが複数リージョンで利用できるようになったら、クライアントは次の URI に対して GET 要求を実行してその可用性を照会できます。

    https://{databaseaccount}.documents.azure.com/

リージョンのほか、そのリージョンに対応するレプリカの DocumentDB エンドポイント URI がリストとしてサービスによって返されます。 現在の書き込みリージョンが応答に示されます。 クライアントはその後、次のようにそれ以降のすべての REST API 要求に対して適切なエンドポイントを選択できます。

応答の例

    {
        "_dbs": "//dbs/",
        "media": "//media/",
        "writableLocations": [
            {
                "Name": "West US",
                "DatabaseAccountEndpoint": "https://globaldbexample-westus.documents.azure.com:443/"
            }
        ],
        "readableLocations": [
            {
                "Name": "East US",
                "DatabaseAccountEndpoint": "https://globaldbexample-eastus.documents.azure.com:443/"
            }
        ],
        "MaxMediaStorageUsageInMB": 2048,
        "MediaStorageUsageInMB": 0,
        "ConsistencyPolicy": {
            "defaultConsistencyLevel": "Session",
            "maxStalenessPrefix": 100,
            "maxIntervalInSeconds": 5
        },
        "addresses": "//addresses/",
        "id": "globaldbexample",
        "_rid": "globaldbexample.documents.azure.com",
        "_self": "",
        "_ts": 0,
        "_etag": null
    }


* PUT 要求、POST 要求、DELETE 要求はすべて、示された書き込み URI に送信されます。
* すべての GET 要求とその他の読み取り専用の要求 (クエリなど) は、クライアントによって選択された任意のエンドポイントに送信できます。

読み取り専用のリージョンへの書き込み要求は失敗し、HTTP エラー コード 403 ("許可されていません") が表示されます。

クライアントの最初の検出フェーズの後に書き込みリージョンが変更された場合、以前の書き込みリージョンへの後続の書き込みは失敗し、HTTP エラー コード 403 ("許可されていません") が表示されます。 クライアントはその後、更新された書き込みリージョンを把握するためにリージョンのリストをもう一度取得する必要があります。

## <a name="next-steps"></a>次のステップ
DocumentDB を使用したデータのグローバル分散の詳細については、次の記事を参照してください。

* [DocumentDB を使用したデータのグローバル分散](documentdb-distribute-data-globally.md)
* [一貫性レベル](documentdb-consistency-levels.md)
* [Azure ポータルを使用したリージョンの追加](documentdb-portal-global-replication.md)

[regions]: https://azure.microsoft.com/regions/

