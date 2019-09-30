---
title: フルテキスト検索用に Azure Blob Storage コンテンツのインデックスを作成する - Azure Search
description: Azure Search で Azure Blob Storage のインデックスを作成し、ドキュメントからテキストを抽出する方法について説明します。
ms.date: 05/02/2019
author: mgottein
manager: nitinme
ms.author: magottei
services: search
ms.service: search
ms.devlang: rest-api
ms.topic: conceptual
ms.custom: seodec2018
ms.openlocfilehash: 03f828be603720871672b9b5d90eb87dd283c002
ms.sourcegitcommit: adc1072b3858b84b2d6e4b639ee803b1dda5336a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/10/2019
ms.locfileid: "70842536"
---
# <a name="indexing-documents-in-azure-blob-storage-with-azure-search"></a>Azure Blob Storage 内ドキュメントのインデックスを Azure Search で作成する
この記事では、Azure Search を使用して、Azure Blob Storage に格納されているドキュメント (PDF や Microsoft Office ドキュメント、その他のよく使用されている形式など) のインデックスを作成する方法を説明します。 まず、BLOB インデクサーの設定と構成の基礎を説明します。 次に、発生する可能性のある動作とシナリオについて詳しく説明します。

## <a name="supported-document-formats"></a>サポートされるドキュメントの形式
BLOB インデクサーは、次の形式のドキュメントからテキストを抽出できます。

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="setting-up-blob-indexing"></a>BLOB インデックスの設定
Azure Blob Storage インデクサーを設定するには、以下を使用します。

* [Azure Portal](https://ms.portal.azure.com)
* Azure Search [REST API](https://docs.microsoft.com/rest/api/searchservice/Indexer-operations)
* Azure Search [.NET SDK](https://aka.ms/search-sdk)

> [!NOTE]
> フィールド マッピングなど、機能によってはまだポータルで使用できないものがあります。こうした機能についてはプログラムで使用する必要があります。
>

ここでは、REST API を使用したフローについて説明します。

### <a name="step-1-create-a-data-source"></a>手順 1:データ ソースを作成する
データ ソースでは、インデックスを作成するデータ、データにアクセスするために必要な資格情報、およびデータの変更 (新しい行、変更された行、削除された行) を効率よく識別するためのポリシーを指定します。 データ ソースは、同じ Search サービス内の複数のインデクサーで使用できます。

BLOB インデックス作成の場合は、次の必須プロパティがデータ ソースに必要です。

* **name** は、Search サービス内のデータ ソースの一意の名前です。
* **type** は `azureblob` である必要があります。
* **credentials** は、ストレージ アカウントの接続文字列を `credentials.connectionString` パラメーターとして提供します。 詳しくは、後述の「[資格情報を指定する方法](#Credentials)」をご覧ください。
* **container** は、ストレージ アカウントにあるコンテナーを指定します。 既定では、コンテナー内のすべての BLOB を取得できます。 特定の仮想ディレクトリにある BLOB についてのみインデックスを作成する場合は、オプションの **query** パラメーターを使用してそのディレクトリを指定できます。

データ ソースを作成する方法を次に示します。

    POST https://[service name].search.windows.net/datasources?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
        "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
    }   

データ ソース作成 API の詳細については、「 [データ ソースの作成](https://docs.microsoft.com/rest/api/searchservice/create-data-source)」をご覧ください。

<a name="Credentials"></a>
#### <a name="how-to-specify-credentials"></a>資格情報を指定する方法 ####

次のいずれかの方法で BLOB コンテナーに対して資格情報を指定できます。

- **フル アクセス ストレージ アカウントの接続文字列**: `DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>` この接続文字列は、ストレージ アカウント ブレードに移動し、[設定] > [キー] と選択する (クラシック ストレージ アカウントの場合) か、[設定] > [アクセス キー] と選択する (Azure Resource Manager ストレージ アカウントの場合) ことで Azure portal から取得できます。
- **ストレージ アカウントの Shared Access Signature** (SAS) の接続文字列:`BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl`SAS にはコンテナー上およびオブジェクト (この場合は BLOB) にリストおよび読み取りアクセス許可が必要です。
-  **コンテナーの Shared Access Signature**:`ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl`SAS にはコンテナー上にリストおよび読み取りアクセス許可が必要です。

Shared Access Signature について詳しくは、「[Shared Access Signature の使用](../storage/common/storage-dotnet-shared-access-signature-part-1.md)」をご覧ください。

> [!NOTE]
> SAS の資格情報を使用する場合は、その有効期限が切れないように、データ ソースの資格情報を更新された署名で定期的に更新する必要があります。 SAS の資格情報の有効期限が切れると、インデクサーは「`Credentials provided in the connection string are invalid or have expired.`」のようなエラー メッセージで失敗します。  

### <a name="step-2-create-an-index"></a>手順 2:インデックスを作成する
インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。

ここでは、BLOB から抽出されたテキストを格納するために、検索可能な `content` フィールドを含むインデックスを作成する方法を示します。   

    POST https://[service name].search.windows.net/indexes?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
          "name" : "my-target-index",
          "fields": [
            { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
            { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
          ]
    }

インデックスの作成の詳細については、[インデックスの作成](https://docs.microsoft.com/rest/api/searchservice/create-index)に関する記事をご覧ください。

### <a name="step-3-create-an-indexer"></a>手順 3:インデクサーの作成
インデクサーはデータ ソースをターゲットの検索インデックスに接続し、データ更新を自動化するスケジュールを提供します。

インデックスとデータ ソースを作成したら、インデクサーを作成できます。

    POST https://[service name].search.windows.net/indexers?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "blob-indexer",
      "dataSourceName" : "blob-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }

このインデクサーは 2 時間ごとに実行されます (スケジュールの間隔が "PT2H" に設定されています)。 インデクサーを 30 分ごとに実行するには、間隔を "PT30M" に設定します。 サポートされている最短の間隔は 5 分です。 スケジュールは省略可能です。省略した場合、インデクサーは作成時に一度だけ実行されます。 ただし、いつでもオンデマンドでインデクサーを実行できます。   

インデクサー作成 API の詳細については、「 [インデクサーの作成](https://docs.microsoft.com/rest/api/searchservice/create-indexer)」をご覧ください。

インデクサーのスケジュールの定義の詳細については、「[Azure Search のインデクサーのスケジュールを設定する方法](search-howto-schedule-indexers.md)」を参照してください。

## <a name="how-azure-search-indexes-blobs"></a>Azure Search で BLOB のインデックスを作成する方法

[インデクサー構成](#PartsOfBlobToIndex)に応じて、BLOB インデクサーでは、ストレージ メタデータのみ (メタデータのみに注意すればよく、BLOB のコンテンツにインデックスを作成する必要がないときに便利です)、ストレージとコンテンツ メタデータ、またはメタデータとテキスト コンテンツの両方にインデックスを作成することができます。 インデクサーは、既定では、メタデータとコンテンツの両方を抽出します。

> [!NOTE]
> 既定では、JSON や CSV などの構造化コンテンツを持つ BLOB には、1 つのテキスト チャンクとしてインデックスが作成されます。 構造化された方法で JSON および CSV の BLOB のインデックスを作成する場合は、[JSON BLOB のインデックス作成に関するページ](search-howto-index-json-blobs.md)と [CSV BLOB のインデックス作成に関するページ](search-howto-index-csv-blobs.md)で詳細を確認してください。
>
> 複合ドキュメントや埋め込みドキュメント (ファイルが添付された Outlook 電子メールを埋め込んだ Word 文書、ZIP アーカイブなど) も、1 つのドキュメントとしてインデックスが作成されます。

* ドキュメントのテキスト コンテンツが、`content` という名前の文字列フィールドに抽出されます。

> [!NOTE]
> どれだけのテキストが抽出されるかは、価格レベルに応じて Azure Search によって制限されます。Free レベルの場合は 32,000 文字、Basic レベルの場合は 64,000 文字、Standard レベル、Standard S2 レベル、および Standard S3 レベルの場合は 400 万文字です。 切り捨てられたドキュメントについては、インデクサーの状態の応答に警告が含められます。  

* ユーザー指定のメタデータのプロパティが BLOB に存在する場合、それらのプロパティは、そのまま抽出されます。
* 標準的な BLOB のメタデータのプロパティは、次のフィールドに抽出されます。

  * **metadata\_storage\_name** (Edm.String) - BLOB のファイル名。 たとえば、/my-container/my-folder/subfolder/resume.pdf という BLOB がある場合、このフィールドの値は `resume.pdf` になります。
  * **metadata\_storage\_path** (Edm.String) - ストレージ アカウントを含む、BLOB の完全な URI。 たとえば、 `https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`
  * **metadata\_storage\_content\_type** (Edm.String) - BLOB をアップロードするためのコードで指定したコンテンツ タイプ。 たとえば、「 `application/octet-stream`」のように入力します。
  * **metadata\_storage\_last\_modified** (Edm.DateTimeOffset) - 前回変更時の BLOB のタイムスタンプ。 インデックスの初回作成後に最初から作成し直さなくても済むよう、変更された BLOB を Azure Search が特定するために、このタイムスタンプが使用されます。
  * **metadata\_storage\_size** (Edm.Int64) - BLOB のサイズ (バイト単位)。
  * **metadata\_storage\_content\_md5** (Edm.String) - BLOB コンテンツの MD5 ハッシュ (利用可能な場合)。
  * **metadata\_storage\_sas\_token** (Edm.String) - BLOB に対してアクセスするために、[カスタム スキル](cognitive-search-custom-skill-interface.md)で使用できる一時的な SAS トークン。 このトークンは、有効期限が切れる可能性があるため、後で使用するために保存しないでください。

* 各ドキュメント形式に固有のメタデータのプロパティは、[こちら](#ContentSpecificMetadata)に記載したフィールドに抽出されます。

検索インデックスに対し、ここに挙げたすべてのプロパティのフィールドを定義する必要はありません。実際のアプリケーションで必要となるプロパティだけを取り込んでください。

> [!NOTE]
> 既存のインデックス内のフィールド名が、ドキュメントの抽出過程で生成されたフィールド名と異なることは少なくありません。 Azure Search によって出力されたプロパティ名は、**フィールドのマッピング**を使用して、検索インデックス内のフィールド名に対応付けることができます。 フィールドのマッピングの例を次に示します。
>
>

<a name="DocumentKeys"></a>
### <a name="defining-document-keys-and-field-mappings"></a>ドキュメント キーとフィールド マッピングの定義
Azure Search では、ドキュメントがそのキーによって一意に識別されます。 それぞれの検索インデックスに、Edm.String 型のキー フィールドが 1 つだけ存在している必要があります。 キー フィールドは、インデックスに追加するドキュメントごとに必要となります (唯一の必須フィールド)。  

抽出されたフィールドとインデックスのキー フィールドとのマッピングは、慎重に検討する必要があります。 その例を次に示します。

* **metadata\_storage\_name** - 名前をキーにできればそれに越したことはありませんが、1) 同じ名前の BLOB が別のフォルダーに存在し、名前が重複する可能性があること、2) ドキュメント キーに無効な文字 (ダッシュなど) が名前に含まれている可能性があることに注意する必要があります。 無効な文字は、`base64Encode` [フィールド マッピング関数](search-indexer-field-mappings.md#base64EncodeFunction)を使用して処理できます。その場合、API 呼び出し (Lookup など) にドキュメント キーを渡すとき、必ずエンコードしてください (たとえば、.NET であれば [UrlTokenEncode](https://msdn.microsoft.com/library/system.web.httpserverutility.urltokenencode.aspx) メソッドを利用できます)。
* **metadata\_storage\_path** - 完全パスであれば一意性は保証されます。ただし、パスに使われる `/` 文字は、[ドキュメント キーでは無効](https://docs.microsoft.com/rest/api/searchservice/naming-rules)です。  この場合も、`base64Encode` [関数](search-indexer-field-mappings.md#base64EncodeFunction)を使用してキーをエンコーディングすることができます。
* いずれの選択肢も利用できない場合は、独自のメタデータ プロパティを BLOB に追加できます。 ただし、この方法を選んだ場合、BLOB のアップロード プロセスで、該当するメタデータのプロパティをすべての BLOB に追加する必要があります。 キーは必須のプロパティであるため、そのプロパティを持たない BLOB については、インデックスが一切作成されません。

> [!IMPORTANT]
> インデックス内のキー フィールドに対して明示的なマッピングが存在しない場合、Azure Search は自動的に `metadata_storage_path` をキーおよび base-64 エンコード キー値として使用します (上記の 2 つ目の選択肢)。
>
>

この例では、`metadata_storage_name` フィールドをドキュメント キーにしましょう。 また、既存のインデックスには、`key` という名前のキー フィールドと、ドキュメントのサイズを格納するための `fileSize` フィールドが存在するものとします。 それらを適切に対応付けるために、インデクサーを作成または更新するときに、次のフィールド マッピングを指定します。

    "fieldMappings" : [
      { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
      { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
    ]

以下に示したのは、それらを反映したコードです。既存のインデクサーに対してフィールドのマッピングを追加し、キーの base-64 エンコーディングを有効にしています。

    PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      "dataSourceName" : " blob-datasource ",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" },
      "fieldMappings" : [
        { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
        { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
      ]
    }

> [!NOTE]
> フィールド マッピングの詳細については、[こちらの記事](search-indexer-field-mappings.md)を参照してください。
>
>

<a name="WhichBlobsAreIndexed"></a>
## <a name="controlling-which-blobs-are-indexed"></a>インデックスが作成される BLOB の制御
インデックスが作成される BLOB とスキップされる BLOB を制御できます。

### <a name="index-only-the-blobs-with-specific-file-extensions"></a>特定のファイル拡張子を持つ BLOB のみのインデックスを作成する
`indexedFileNameExtensions` インデクサー構成パラメーターを使用すると、指定したファイル名拡張子を持つ BLOB のインデックスだけを作成できます。 値は、(先頭にピリオドが付いた) ファイル拡張子のコンマ区切りの一覧を含む文字列です。 たとえば、.PDF や .DOCX の BLOB のみのインデックスを作成する場合は、この操作を行います。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
    }

### <a name="exclude-blobs-with-specific-file-extensions"></a>特定のファイル拡張子を持つ BLOB を除外する
`excludedFileNameExtensions` 構成パラメーターを使用すると、特定のファイル名拡張子を持つ BLOB をインデックス作成から除外できます。 値は、(先頭にピリオドが付いた) ファイル拡張子のコンマ区切りの一覧を含む文字列です。 たとえば、.PNG と .JPEG の拡張子を持つ BLOB を除くすべての BLOB のインデックスを作成する場合は、この操作を行います。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
    }

`indexedFileNameExtensions` と `excludedFileNameExtensions` の両方のパラメーターがある場合、Azure Search では最初に `indexedFileNameExtensions` を調べ、次に `excludedFileNameExtensions` を調べます。 つまり、同じファイル拡張子が両方の一覧に存在する場合、インデックス作成から除外されます。

<a name="PartsOfBlobToIndex"></a>
## <a name="controlling-which-parts-of-the-blob-are-indexed"></a>インデックスが作成される BLOB の部分の制御

BLOB のどの部分にインデックスを作成するかは、`dataToExtract` 構成パラメーターを使用して制御できます。 次の値を使用できます。

* `storageMetadata` - [標準的な BLOB のプロパティおよびユーザー指定のメタデータ](../storage/blobs/storage-properties-metadata.md)のみにインデックスを作成するように指定します。
* `allMetadata` - ストレージ メタデータと、BLOB コンテンツから抽出された[コンテンツの種類固有のメタデータ](#ContentSpecificMetadata)にインデックスを作成するように指定します。
* `contentAndMetadata` - すべてのメタデータと、BLOB から抽出されたテキスト コンテンツにインデックスを作成するように指定します。 これが既定値です。

たとえば、ストレージ メタデータのみにインデックスを作成するには、次のように使用します。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
    }

### <a name="using-blob-metadata-to-control-how-blobs-are-indexed"></a>BLOB のメタデータを使用した BLOB インデックスの作成方法の制御

上記の構成パラメーターは、すべての BLOB に適用されます。 ときには、"*個々の BLOB*" のインデックスの作成方法を制御することが必要になる場合があります。 これを行うには、次の BLOB メタデータのプロパティと値を追加します。

| プロパティ名 | プロパティ値 | 説明 |
| --- | --- | --- |
| AzureSearch_Skip |"true" |BLOB を完全にスキップするように BLOB インデクサーに指示します。 メタデータとコンテンツのどちらの抽出も行われません。 特定の BLOB で何度もエラーが発生し、インデックス作成プロセスが中断されるときに利用できます。 |
| AzureSearch_SkipContent |"true" |これは、[前](#PartsOfBlobToIndex)に説明した、特定の BLOB を対象とする `"dataToExtract" : "allMetadata"` 設定と同じです。 |

<a name="DealingWithErrors"></a>
## <a name="dealing-with-errors"></a>エラーへの対処

既定では、BLOB インデクサーは、サポートされていないコンテンツの種類 (画像など) が含まれる BLOB を検出するとすぐに停止されます。 もちろん、`excludedFileNameExtensions` パラメーターを使用して特定のコンテンツの種類をスキップすることもできますが、 存在する可能性のあるすべてのコンテンツの種類が事前にわからないまま BLOB のインデックスを作成する必要がある場合もあります。 サポートされていないコンテンツの種類が検出されたときにインデックス作成を続行するには、`failOnUnsupportedContentType` 構成パラメーターを `false` に設定します。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false } }
    }

一部の BLOB では、Azure Search はコンテンツの種類を判別できないか、他の種類ではサポートされているコンテンツの種類のドキュメントを処理できない場合があります。 この障害モードを無視するには、`failOnUnprocessableDocument` 構成パラメーターを false に設定します。

      "parameters" : { "configuration" : { "failOnUnprocessableDocument" : false } }

Azure Search では、インデックスを付ける BLOB のサイズが制限されます。 これらの制限は、「[Azure Search サービスの制限](https://docs.microsoft.com/azure/search/search-limits-quotas-capacity)」に記載されています。 サイズが大きい BLOB は、既定ではエラーとして扱われます。 ただし、`indexStorageMetadataOnlyForOversizedDocuments` 構成パラメーターを true に設定した場合、サイズが大きい BLOB のストレージ メタデータには引き続きインデックスを付けることができます。 

    "parameters" : { "configuration" : { "indexStorageMetadataOnlyForOversizedDocuments" : true } }

BLOB の解析中またはインデックスへのドキュメントの追加中、処理のどこかの時点でエラーが発生した場合に、インデックス付けを続行することもできます。 特定数のエラーを無視するには、構成パラメーター `maxFailedItems` と `maxFailedItemsPerBatch` を望ましい値に設定します。 例:

    {
      ... other parts of indexer definition
      "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
    }

## <a name="incremental-indexing-and-deletion-detection"></a>インデックスの増分作成と削除の検出
スケジュールに従って実行するように BLOB のインデクサーを設定すると、その BLOB の `LastModified` タイムスタンプから判断された変更済みの BLOB のみインデックスが再構築されます。

> [!NOTE]
> 変更検出ポリシーを独自に指定する必要はありません。インデックスの増分作成は自動的に有効になります。

ドキュメントの削除をサポートするには、"論理削除" 方式を使用してください。 BLOB を完全に削除すると、対応するドキュメントが Search インデックスから削除されません。 代わりに、次の手順を実行します。  

1. 独自のメタデータ プロパティを BLOB に追加して、ドキュメントが論理的に削除されていることを Azure Search に示します
2. データ ソースで論理削除の検出ポリシーを構成します
3. インデクサーが BLOB を処理したら (インデクサーの状態 API によって示されます)、BLOB を物理的に削除できます

たとえば、次のポリシーでは、BLOB のメタデータ プロパティ `IsDeleted` の値が `true` のときに、その BLOB が削除されるものと見なされます。

    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",     
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }   

## <a name="indexing-large-datasets"></a>大規模なデータセットのインデックス作成

BLOB のインデックス作成プロセスは、時間がかかる場合があります。 インデックスを作成する BLOB が数百万ある場合は、データをパーティション分割し、複数のインデクサーを使用してデータを並列で処理することで、インデックス作成を高速に処理できます。 設定方法は次のとおりです。

- 複数の BLOB コンテナーまたは仮想フォルダーにデータをパーティション分割します。
- コンテナーまたはフォルダーごとに 1 つずつ、Azure Search データ ソースを設定します。 BLOB フォルダーをポイントするには、`query` パラメーターを使用します。

    ```
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

- データ ソースごとに対応するインデクサーを作成します。 すべてのインデクサーから、同じターゲット検索インデックスをポイントできます。  

- サービス内の 1 つの検索単位は、特定の時点で 1 つのインデクサーを実行できます。 上記のように、複数のインデクサーの作成は、これらを実際に並行して実行する場合のみ有用です。 複数のインデクサーを並行して実行するには、適切な数のパーティションとレプリカを作成して、検索サービスをスケールアウトします。 たとえば、検索サービスに 6 つの検索単位がある場合 (たとえば、2 つのパーティション x 3 つのレプリカ)、6 つのインデクサーを同時に実行でき、インデックス作成のスループットが 6 倍になります。 スケーリングと容量計画について詳しくは、「[Azure Search でクエリとインデックス作成のワークロードに応じてリソース レベルをスケールする](search-capacity-planning.md)」をご覧ください。

## <a name="indexing-documents-along-with-related-data"></a>ドキュメントと関連データを併せたインデックスを作成する

インデックスの複数のソースからドキュメントを「アセンブル」できます。 たとえば、Cosmos DB に格納された他のメタデータを使用して BLOB からテキストをマージすることもできます。 プッシュ インデックス作成 API を各種インデクサーとともに使用して、複数のパーツから検索ドキュメントを構築することもできます。 

これが機能するには、すべてのインデクサーと他のコンポーネントがドキュメント キーに同意する必要があります。 このトピックの詳細については、[複数の Azure データ ソースのインデックスを作成する](https://docs.microsoft.com/azure/search/tutorial-multiple-data-sources)方法に関するページを参照してください。 このソリューションのチュートリアルについて詳しくは、外部資料「[Combine documents with other data in Azure Search (ドキュメントを Azure Search の他のデータと組み合わせる)](https://blog.lytzen.name/2017/01/combine-documents-with-other-data-in.html)」をご覧ください。

<a name="IndexingPlainText"></a>
## <a name="indexing-plain-text"></a>プレーンテキストのインデックス作成 

すべての BLOB に同じエンコードのプレーンテキストが含まれている場合、**テキスト解析モード**を使用してインデックス作成のパフォーマンスを大幅に改善できます。 テキスト解析モードを使用するには、`parsingMode` 構成プロパティを `text` に設定します。

    PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2019-05-06
    Content-Type: application/json
    api-key: [admin key]

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text" } }
    }

既定では、`UTF-8` エンコードが想定されます。 別のエンコードを指定するには、`encoding` 構成プロパティを使用します。 

    {
      ... other parts of indexer definition
      "parameters" : { "configuration" : { "parsingMode" : "text", "encoding" : "windows-1252" } }
    }


<a name="ContentSpecificMetadata"></a>
## <a name="content-type-specific-metadata-properties"></a>コンテンツの種類ごとのメタデータのプロパティ
以下の表は、各ドキュメント形式に関して実行される処理と、Azure Search によって抽出されるメタデータのプロパティをまとめたものです。

| ドキュメントの形式/コンテンツの種類 | コンテンツの種類ごとのメタデータのプロパティ | 処理の詳細 |
| --- | --- | --- |
| HTML (text/html) |`metadata_content_encoding`<br/>`metadata_content_type`<br/>`metadata_language`<br/>`metadata_description`<br/>`metadata_keywords`<br/>`metadata_title` |HTML マークアップを削除し、テキストを抽出します。 |
| PDF (application/pdf) |`metadata_content_type`<br/>`metadata_language`<br/>`metadata_author`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります (画像を除く)。 |
| DOCX (application/vnd.openxmlformats-officedocument.wordprocessingml.document) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| DOC (application/msword) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| DOCM (application/vnd.ms-word.document.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| WORD XML (application/vnd.ms-word2006ml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |XML マークアップを削除し、テキストを抽出します。 |
| WORD 2003 XML (application/vnd.ms-wordml) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date` |XML マークアップを削除し、テキストを抽出します。 |
| XLSX (application/vnd.openxmlformats-officedocument.spreadsheetml.sheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| XLS (application/vnd.ms-excel) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| XLSM (application/vnd.ms-excel.sheet.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| PPTX (application/vnd.openxmlformats-officedocument.presentationml.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| PPT (application/vnd.ms-powerpoint) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| PPTM (application/vnd.ms-powerpoint.presentation.macroenabled.12) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_slide_count`<br/>`metadata_title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| MSG (application/vnd.ms-outlook) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_from_email`<br/>`metadata_message_to`<br/>`metadata_message_to_email`<br/>`metadata_message_cc`<br/>`metadata_message_cc_email`<br/>`metadata_message_bcc`<br/>`metadata_message_bcc_email`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_subject` |テキストを抽出します。添付ファイルも対象となります。 |
| ODT (application/vnd.oasis.opendocument.text) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`metadata_page_count`<br/>`metadata_word_count` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| ODS (application/vnd.oasis.opendocument.spreadsheet) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| ODP (application/vnd.oasis.opendocument.presentation) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_last_modified`<br/>`title` |テキストを抽出します。埋め込みドキュメントも対象となります。 |
| ZIP (application/zip) |`metadata_content_type` |アーカイブ内のすべてのドキュメントからテキストを抽出します。 |
| GZ (application/gzip) |`metadata_content_type` |アーカイブ内のすべてのドキュメントからテキストを抽出します。 |
| EPUB (application/epub+zip) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_creation_date`<br/>`metadata_title`<br/>`metadata_description`<br/>`metadata_language`<br/>`metadata_keywords`<br/>`metadata_identifier`<br/>`metadata_publisher` |アーカイブ内のすべてのドキュメントからテキストを抽出します。 |
| XML (application/xml) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> |XML マークアップを削除し、テキストを抽出します。 |
| JSON (application/json) |`metadata_content_type`<br/>`metadata_content_encoding` |テキストを抽出します<br/>注:JSON BLOB から複数のドキュメント フィールドを抽出する必要がある場合、詳細については、[JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)に関する記事をご覧ください |
| EML (message/rfc822) |`metadata_content_type`<br/>`metadata_message_from`<br/>`metadata_message_to`<br/>`metadata_message_cc`<br/>`metadata_creation_date`<br/>`metadata_subject` |テキストを抽出します。添付ファイルも対象となります。 |
| RTF (アプリケーション/rtf) |`metadata_content_type`<br/>`metadata_author`<br/>`metadata_character_count`<br/>`metadata_creation_date`<br/>`metadata_page_count`<br/>`metadata_word_count`<br/> | テキストを抽出します|
| プレーン テキスト (text/plain) |`metadata_content_type`<br/>`metadata_content_encoding`<br/> | テキストを抽出します|


## <a name="help-us-make-azure-search-better"></a>Azure Search の品質向上にご協力ください
ご希望の機能や品質向上のアイデアがありましたら、[UserVoice サイト](https://feedback.azure.com/forums/263029-azure-search/)までお寄せください。
