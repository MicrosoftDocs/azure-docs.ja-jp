---
title: "Azure Table Storage のインデックスを Azure Search で作成する"
description: "Azure Search で Azure テーブルに格納されたデータのインデックスを作成する方法について説明します"
services: search
documentationcenter: 
author: chaosrealm
manager: pablocas
editor: 
ms.assetid: 1cc27411-d0cc-40ed-8aed-c7cb9ab402b9
ms.service: search
ms.devlang: rest-api
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 01/18/2017
ms.author: eugenesh
translationtype: Human Translation
ms.sourcegitcommit: 432752c895fca3721e78fb6eb17b5a3e5c4ca495
ms.openlocfilehash: 66e6fec16aab7764b05b616efc0fccbfb2d0595e
ms.lasthandoff: 03/30/2017

---

# <a name="indexing-azure-table-storage-with-azure-search"></a>Azure Table Storage のインデックスを Azure Search で作成する
この記事では、Azure Search を使用して、Azure Table Storage に格納されているデータのインデックスを使用する方法を示します。 Azure Search の新しいテーブル インデクサーを使用すると、迅速かつシームレスに作成できます。

## <a name="setting-up-azure-table-indexing"></a>Azure テーブルのインデックス作成の設定
Azure テーブルのインデクサーの設定と構成は、「[インデクサーの操作](https://msdn.microsoft.com/library/azure/dn946891.aspx)」の説明に従い、Azure Search REST API を使用して**インデクサー**と**データ ソース**を作成、管理することによって行います。 [バージョン 2.0-preview](https://msdn.microsoft.com/library/mt761536%28v=azure.103%29.aspx) の .NET SDK を使用することもできます。 今後、Azure ポータルにテーブル インデックス作成機能が追加される予定です。

データ ソースでは、インデックスを作成するデータ、データにアクセスするために必要な資格情報、および Azure Search がデータの変更 (新しい行、変更された行、削除された行) を効率よく識別できるようにするポリシーを指定します。

インデクサーはデータ ソースからデータを読み取って、対象となる検索インデックスに読み込みます。

テーブル インデックスを設定するには、次の手順に従います。

1. データ ソースを作成する
   * `type` パラメーターを `azuretable` に設定します。
   * ストレージ アカウントの接続文字列を `credentials.connectionString` パラメーターとして渡します。 詳しくは、後述の「[資格情報を指定する方法](#Credentials)」をご覧ください。
   * `container.name` パラメーターを使用して、テーブル名を指定します。
   * 必要に応じて `container.query` パラメーターを使用して、クエリを指定します。 可能な場合は、PartitionKey でフィルターを使用して、パフォーマンスを最大限に高めます。他のクエリを実行すると、フル テーブル スキャンが発生し、大きなテーブルの場合はパフォーマンスが低下する可能性があります。
2. インデックスを作成するテーブルの列に対応するスキーマを使用して検索インデックスを作成します。
3. データ ソースを検索インデックスに接続することによって、インデクサーを作成します。

### <a name="create-data-source"></a>データ ソースの作成
    POST https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-table", "query" : "PartitionKey eq '123'" }
    }   

データ ソース作成 API の詳細については、「 [データ ソースの作成](https://msdn.microsoft.com/library/azure/dn946876.aspx)」をご覧ください。

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>資格情報を指定する方法 ####

次のいずれかの方法でテーブルに対して資格情報を指定できます。 

- **フル アクセス ストレージ アカウントの接続文字列**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>`。 この接続文字列は、ストレージ アカウント ブレードに移動し、[設定]、[キー] と選択する (クラシック ストレージ アカウントの場合) か、[設定]、[アクセス キー] と選択する (Azure Resource Manager ストレージ アカウントの場合) ことで Azure Portal から取得できます。
- **ストレージ アカウントの Shared Access Signature** (SAS) の接続文字列: `TableEndpoint=https://<your account>.table.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=t&sp=rl`。 SAS にはコンテナー (この場合はテーブル) 上およびオブジェクト (テーブル行) にリストおよび読み取りアクセス許可が必要です。
-  **テーブルの Shared Access Signature**: `ContainerSharedAccessUri=https://<your storage account>.table.core.windows.net/<table name>?tn=<table name>&sv=2016-05-31&sig=<the signature>&se=<the validity end time>&sp=r`。 SAS にはテーブル上にクエリ (読み取り) アクセス許可が必要です。

Shared Access Signature について詳しくは、「[Shared Access Signature の使用](../storage/storage-dotnet-shared-access-signature-part-1.md)」をご覧ください。

> [!NOTE]
> SAS の資格情報を使用する場合は、その有効期限が切れないように、データ ソースの資格情報を更新された署名で定期的に更新する必要があります。 SAS の資格情報の有効期限が切れると、インデクサーは「`Credentials provided in the connection string are invalid or have expired.`」のようなエラー メッセージで失敗します。  

### <a name="create-index"></a>インデックスの作成
    POST https://[service name].search.windows.net/indexes?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "key", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "SomeColumnInMyTable", "type": "Edm.String", "searchable": true }
          ]
    }

インデックス作成 API の詳細については、「 [Create Index](https://msdn.microsoft.com/library/dn798941.aspx)

### <a name="create-indexer"></a>インデクサーの作成
最後に、データ ソースとターゲット インデックスとを参照するインデクサーを作成します。 次に例を示します。

    POST https://[service name].search.windows.net/indexers?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "table-indexer",
      "dataSourceName" : "table-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

インデクサー作成 API の詳細については、「 [インデクサーの作成](https://msdn.microsoft.com/library/azure/dn946899.aspx)」をご覧ください。

これですべて完了です。是非お試しください。

## <a name="dealing-with-different-field-names"></a>さまざまなフィールド名の操作
既存のインデックス内のフィールド名が、テーブルのプロパティ名と異なることは少なくありません。 テーブルのプロパティ名は、 **フィールド マッピング** を使用して、検索インデックス内のフィールド名に対応付けることができます。 フィールド マッピングの詳細については、「 [データ ソースと検索インデックスの橋渡し役としての Azure Search インデクサー フィールド マッピング](search-indexer-field-mappings.md)」を参照してください。

## <a name="handling-document-keys"></a>ドキュメント キーの処理
Azure Search では、ドキュメントがそのキーによって一意に識別されます。 それぞれの検索インデックスには、 `Edm.String`型のキー フィールドが 1 つだけ必要です。 キー フィールドは、インデックスに追加するドキュメントごとに必要です (実際のところ、これは唯一の必須フィールドです)。

テーブル行には複合キーがあるため、Azure Search では、パーティション キーと行キーの値が連結された `Key` と呼ばれる合成フィールドが生成されます。 たとえば、行の PartitionKey が `PK1` で、RowKey が `RK1` の場合、`Key` フィールドの値は `PK1RK1` になります。

> [!NOTE]
> `Key` 値には、ドキュメント キーでは無効な文字、たとえばダッシュを含めることができます。 無効な文字を扱うには、 `base64Encode` [フィールド マッピング関数](search-indexer-field-mappings.md#base64EncodeFunction)を使用します。 これを行う場合は、Lookup などの API 呼び出しでドキュメント キーを渡す際に、必ず URL の安全な Base64 エンコードを使用する点にも注意してください。
>
>

## <a name="incremental-indexing-and-deletion-detection"></a>インデックスの増分作成と削除の検出
スケジュールに従って実行するようにテーブルのインデクサーを設定すると、行の `Timestamp` 値に従って、新しい行または更新された行のみでインデックスが再作成されます。 変更検出ポリシーを独自に指定する必要はありません。インデックスの増分作成は自動的に有効になります。

特定のドキュメントをインデックスから削除するよう指定する場合は、論理削除方式を使用できます。つまり、行を削除するのではなく、その行が削除されたことを示すためのプロパティを追加し、データソースで論理削除の検出ポリシーを設定します。 たとえば、以下に示すポリシーでは、プロパティ `IsDeleted` の値が `"true"` であるとき、行が削除されたと見なされます。

    PUT https://[service name].search.windows.net/datasources?api-version=2016-09-01
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "my-table-datasource",
        "type" : "azuretable",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "table name", "query" : "query" },
        "dataDeletionDetectionPolicy" : { "@odata.type" : "#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy", "softDeleteColumnName" : "IsDeleted", "softDeleteMarkerValue" : "true" }
    }   


## <a name="help-us-make-azure-search-better"></a>Azure Search の品質向上にご協力ください
ご希望の機能や品質向上のアイデアがありましたら、 [UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)にぜひお寄せください。

