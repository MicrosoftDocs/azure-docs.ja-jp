---
title: フルテキスト検索用に Azure Table Storage のコンテンツのインデックスを作成する - Azure Search
description: Azure Table Storage に格納されているデータのインデックスを Azure Search インデクサーで作成する方法について説明します。
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: dffb0a41dbf33cd86014115b089036d69a8e4718
ms.sourcegitcommit: bb8e9f22db4b6f848c7db0ebdfc10e547779cccc
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/20/2019
ms.locfileid: "69648179"
---
# <a name="index-azure-table-storage-with-azure-search"></a>Azure Table Storage のインデックスを Azure Search で作成する
この記事では、Azure Search を使用して、Azure Table Storage に格納されているデータのインデックスを作成する方法を示します。

## <a name="set-up-azure-table-storage-indexing"></a>Azure Table Storage のインデックスを設定する

次のリソースを使用して、Azure Table Storage のインデクサーを設定できます。

* [Azure Portal](https://ms.portal.azure.com)
* Azure Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

ここでは、REST API を使用したフローについて説明します。 

### <a name="step-1-create-a-datasource"></a>手順 1:データソースの作成

データ ソースは、インデックスを作成するデータ、データにアクセスするために必要な資格情報、および Azure Search がデータの変更を効率よく識別できるようにするポリシーを指定します。

テーブルのインデックス作成では、次のプロパティがデータ ソースに必要です。

- **name** は、Search サービス内のデータ ソースの一意の名前です。
- **type** は `azuretable` である必要があります。
- **credentials** パラメーターは、ストレージ アカウントの接続文字列が含まれます。 詳しくは、「[資格情報を指定する方法](#Credentials)」セクションをご覧ください。
- **container** はテーブル名とオプションのクエリを設定します。
    - `name` パラメーターを使用して、テーブル名を指定します。
    - 必要に応じて `query` パラメーターを使用して、クエリを指定します。 

> [!IMPORTANT] 
> 可能な場合は、パフォーマンス向上のために PartitionKey でフィルターを使用してください。 その他のクエリはフル テーブル スキャンを実行するため、規模の大きなテーブルではパフォーマンスが下がります。 詳しくは、「[パフォーマンスに関する考慮事項](#Performance)」セクションをご覧ください。


データ ソースを作成するには:

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

データ ソース作成 API の詳細については、「[データ ソースの作成](https://docs.microsoft.com/rest/api/searchservice/create-data-source)」をご覧ください。

<a name="Credentials"></a>
#### <a name="ways-to-specify-credentials"></a>資格情報を指定する方法 ####

次のいずれかの方法でテーブルに対して資格情報を指定できます。 

- **フル アクセス ストレージ アカウントの接続文字列**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` この接続文字列は、**ストレージ アカウント ブレード** >  **[設定]**  >  **[キー]** と選択する (クラシック ストレージ アカウントの場合) か、 **[設定]**  >  **[アクセス キー]** と選択する (Azure Resource Manager ストレージ アカウントの場合) ことで Azure Portal から取得できます。
- **ストレージ アカウントの共有アクセス署名の接続文字列**: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl` 共有アクセス署名には、コンテナー (この場合はテーブル) 上およびオブジェクト (テーブル行) にリストおよび読み取りアクセス許可が必要です。
-  **テーブルの共有アクセス署名**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r` 共有アクセス署名には、テーブルに対するクエリ (読み取り) アクセス許可が必要です。

ストレージの共有アクセス署名の詳細については、「[Shared Access Signature (SAS) の使用](../storage/common/storage-dotnet-shared-access-signature-part-1.md)」を参照してください。

> [!NOTE]
> 共有アクセス署名の資格情報を使用する場合は、その有効期限が切れないように、データ ソースの資格情報を更新された署名で定期的に更新する必要があります。 共有アクセス署名の資格情報の有効期限が切れた場合、インデクサーは失敗し、「接続文字列で指定された資格情報が無効か期限が切れています」のようなエラー メッセージが表示されます。  

### <a name="step-2-create-an-index"></a>手順 2:インデックスを作成する
インデックスは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

インデックスを作成するには:

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

インデックスの作成の詳細については、[インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)に関する記事をご覧ください。

### <a name="step-3-create-an-indexer"></a>手順 3:インデクサーの作成
インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。 

インデックスとデータ ソースを作成した後、インデクサーを作成できます。

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

このインデクサーは、2 時間ごとに実行されます (スケジュール間隔は "PT2H"に設定されます)。インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、[インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)に関するページをご覧ください。

インデクサーのスケジュールの定義の詳細については、「[Azure Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)」を参照してください。

## <a name="deal-with-different-field-names"></a>さまざまなフィールド名を操作する
既存のインデックス内のフィールド名が、テーブルのプロパティ名と異なることがあります。 テーブルのプロパティ名は、フィールド マッピングを使用して、検索インデックス内のフィールド名に対応付けることができます。 フィールド マッピングの詳細については、「[データ ソースと検索インデックスの橋渡し役としての Azure Search インデクサー フィールド マッピング](search-indexer-field-mappings.md)」を参照してください。

## <a name="handle-document-keys"></a>ドキュメント キーを処理する
Azure Search では、ドキュメントがそのキーによって一意に識別されます。 それぞれの検索インデックスには、 `Edm.String`型のキー フィールドが 1 つだけ必要です。 キー フィールドは、インデックスに追加するドキュメントごとに必要です (実際のところ、これは唯一の必須フィールドです)。

テーブル行には複合キーがあるため、Azure Search では、パーティション キーと行キーの値が連結された `Key` と呼ばれる合成フィールドが生成されます。 たとえば、行の PartitionKey が `PK1` で、RowKey が `RK1` の場合、`Key` フィールドの値は `PK1RK1` です。

> [!NOTE]
> `Key` 値には、ドキュメント キーでは無効な文字、たとえばダッシュを含めることができます。 無効な文字を扱うには、 `base64Encode` [フィールド マッピング関数](search-indexer-field-mappings.md#base64EncodeFunction)を使用します。 これを行う場合は、Lookup などの API 呼び出しでドキュメント キーを渡す際に、必ず URL の安全な Base64 エンコードを使用する点にも注意してください。
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>インデックスの増分作成と削除の検出
スケジュールに従って実行するようにテーブルのインデクサーを設定すると、行の `Timestamp` 値に従って、新しい行または更新された行のみでインデックスが再作成されます。 変更検出ポリシーを指定する必要はありません。 増分インデックス作成が自動的に有効になります。

一部のドキュメントをインデックスを削除する必要があることを示すには、論理的な削除を使用できます。 行を削除する代わりに、プロパティを追加してそれが削除されていることを示し、データソースに論理的な削除のポリシーを設定します。 たとえば、次のポリシーは値 `"true"` が指定されたプロパティ `IsDeleted` がある行を削除されているものと見なします。

    PUT https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "<query>" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   

<a name="Performance"></a>
## <a name="performance-considerations"></a>パフォーマンスに関する考慮事項

既定では、Azure Search は `Timestamp >= HighWaterMarkValue` というクエリ フィルターを使用します。 Azure のテーブルの `Timestamp` フィールドにはセカンダリ インデックスがないため、この種類のクエリはフル テーブル スキャンを必要とし、規模の大きなテーブルでは速度が低下します。


テーブルのインデックス作成のパフォーマンスを向上させる可能性のある 2 つの方法を紹介します。 どちらの方法もテーブルのパーティションを使用します。 

- データを複数のパーティション範囲に自然に分割できる場合は、各パーティション範囲にデータソースと対応するインデクサーを作成します。 これで各インデクサーが特定のパーティション範囲のみを処理するようになるため、クエリのパフォーマンスが向上します。 インデックスを作成する必要があるデータに固定のパーティションが少数ある場合、各インデクサーはパーティションのスキャンのみを実行するため、パフォーマンスが向上します。 たとえば、キーが `000` から `100` の範囲のパーティションを処理するデータソースを作成するには、次のようなクエリを使用します。 
    ```
    "container" : { "name" : "my-table", "query" : "PartitionKey ge '000' and PartitionKey lt '100' " }
    ```

- データが時間によって分割されている (新しいパーティションを毎日または毎週作成するなどの) 場合は、次の方法を検討してください。 
    - `(PartitionKey ge <TimeStamp>) and (other filters)` の形式のクエリを使用します。 
    - インデクサーの進行状況を [Get Indexer Status API](https://docs.microsoft.com/rest/api/searchservice/get-indexer-status) を使用して監視し、直近の成功した高基準値に基づいてクエリの `<TimeStamp>` 条件を定期的に更新します。 
    - この方法では、全インデックスの再作成をトリガーする必要がある場合は、インデクサーのリセットに加えてデータソース クエリをリセットする必要があります。 


## <a name="help-us-make-azure-search-better"></a>Azure Search の品質向上にご協力ください
ご希望の機能や品質向上のアイデアがありましたら、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)にぜひお寄せください。
