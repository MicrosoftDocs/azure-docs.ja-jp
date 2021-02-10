---
title: BLOB インデクサーを構成する
titleSuffix: Azure Cognitive Search
description: Azure Cognitive Search でのフルテキスト検索操作用に BLOB コンテンツのインデックス付けを自動化するように Azure Blob インデクサーを設定します。
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 266538a7aff11bff0b6dc5c2d2b6a7a3d25c18ab
ms.sourcegitcommit: 445ecb22233b75a829d0fcf1c9501ada2a4bdfa3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/02/2021
ms.locfileid: "99474983"
---
# <a name="how-to-configure-blob-indexing-in-cognitive-search"></a>Cognitive Search で BLOB のインデックス作成を構成する方法

この記事では、Azure Cognitive Search でテキストベースのドキュメント (PDF、Microsoft Office ドキュメントなど) のインデックスを作成するための BLOB インデクサーを構成する方法について説明します。 インデクサーの概念について理解が不十分な場合は、BLOB のインデックス作成に進む前に、「[Azure Cognitive Search のインデクサー](search-indexer-overview.md)」および[検索インデクサーの作成](search-howto-create-indexers.md)に関するページを先にお読みください。

<a name="SupportedFormats"></a>

## <a name="supported-document-formats"></a>サポートされるドキュメントの形式

Azure Cognitive Search の BLOB インデクサーは、次の形式のドキュメントからテキストを抽出できます。

[!INCLUDE [search-blob-data-sources](../../includes/search-blob-data-sources.md)]

## <a name="data-source-definitions"></a>データ ソース定義

BLOB インデクサーとその他のインデクサーの違いは、インデクサーに割り当てられるデータ ソース定義です。 データ ソースは、インデックスを作成するコンテンツの種類、接続、場所を指定するすべてのプロパティをカプセル化します。

BLOB のデータ ソース定義は、次の例のようになります。

```http
{
    "name" : "my-blob-datasource",
    "type" : "azureblob",
    "credentials" : { "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>;" },
    "container" : { "name" : "my-container", "query" : "<optional-virtual-directory-name>" }
}
```

`"credentials"` プロパティは、上の例に示すような接続文字列か、次のセクションで説明する別の方法のいずれかで指定できます。 `"container"` プロパティは Azure Storage 内のコンテンツの場所を指定し、`"query"` を使用してコンテナー内のサブフォルダーを指定します。 データ ソース定義の詳細については、「[データ ソースの作成 (REST)](/rest/api/searchservice/create-data-source)」を参照してください。

<a name="Credentials"></a>

## <a name="credentials"></a>資格情報

次のいずれかの方法で BLOB コンテナーに対して資格情報を指定できます。

**マネージド ID の接続文字列**: `{ "connectionString" : "ResourceId=/subscriptions/<your subscription ID>/resourceGroups/<your resource group name>/providers/Microsoft.Storage/storageAccounts/<your storage account name>/;" }`

この接続文字列にはアカウント キーは必要ありませんが、[マネージド ID を使用して、Azure Storage アカウントへの接続を設定する](search-howto-managed-identities-storage.md)方法に関する指示に従う必要があります。

**フル アクセス ストレージ アカウントの接続文字列**: `{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }`

この接続文字列は、ストレージ アカウント ブレードに移動し、[設定]、[キー] と選択する (クラシック ストレージ アカウントの場合) か、[設定]、[アクセス キー] と選択する (Azure Resource Manager ストレージ アカウントの場合) ことで Azure Portal から取得できます。

**ストレージ アカウントの Shared Access Signature** (SAS) の接続文字列: `{ "connectionString" : "BlobEndpoint=https://<your account>.blob.core.windows.net/;SharedAccessSignature=?sv=2016-05-31&sig=<the signature>&spr=https&se=<the validity end time>&srt=co&ss=b&sp=rl;" }`

SAS にはコンテナー上およびオブジェクト (この場合は BLOB) にリストおよび読み取りアクセス許可が必要です。

**コンテナーの Shared Access Signature**: `{ "connectionString" : "ContainerSharedAccessUri=https://<your storage account>.blob.core.windows.net/<container name>?sv=2016-05-31&sr=c&sig=<the signature>&se=<the validity end time>&sp=rl;" }`

SAS にはコンテナー上にリストおよび読み取りアクセス許可が必要です。 ストレージの共有アクセス署名の詳細については、「[Shared Access Signature (SAS) の使用](../storage/common/storage-sas-overview.md)」を参照してください。

> [!NOTE]
> SAS の資格情報を使用する場合は、その有効期限が切れないように、データ ソースの資格情報を更新された署名で定期的に更新する必要があります。 SAS の資格情報の有効期限が切れた場合、インデクサーは失敗し、「接続文字列で指定された資格情報が無効か期限が切れています」のようなエラー メッセージが表示されます。  

## <a name="index-definitions"></a>インデックス定義

インデックスでは、検索に使用する、ドキュメント内のフィールド、属性、およびその他の構成要素を指定します。 次の例では、[Create Index (REST API)](/rest/api/searchservice/create-index) を使用して単純なインデックスを作成します。 

```http
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
      "name" : "my-target-index",
      "fields": [
        { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
        { "name": "content", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
      ]
}
```

インデックス定義には、ドキュメント キーとして機能する 1 つのフィールドが `"fields"` コレクションに必要です。 インデックス定義には、コンテンツとメタデータのフィールドも含める必要があります。

**`content`** フィールドは、BLOB から抽出されたテキストを格納するために使用されます。 このフィールドの定義は上記のようになります。 この名前を使用する必要はありませんが、そうすることで暗黙的なフィールド マッピングを利用できます。 BLOB インデクサーは、BLOB のコンテンツをインデックス内のコンテンツ Edm.String フィールドに送信できます。フィールド マッピングは必要ありません。

インデックスに必要な任意の BLOB メタデータ用のフィールドを追加することもできます。 インデクサーは、カスタム メタデータ プロパティ、[標準メタデータ](#indexing-blob-metadata) プロパティ、[コンテンツ固有メタデータ](search-blob-metadata-properties.md) プロパティを読み取ることができます。 インデックスの詳細については、「[インデックスを作成する](search-what-is-an-index.md)」を参照してください。

<a name="DocumentKeys"></a>

### <a name="defining-document-keys-and-field-mappings"></a>ドキュメント キーとフィールド マッピングの定義

検索インデックスでは、各ドキュメントがそのキーによって一意に識別されます。 選択するフィールドは `Edm.String` 型である必要があります。 BLOB コンテンツの場合、ドキュメントキーの候補として最適なのは、BLOB のメタデータ プロパティです。

+ **`metadata_storage_name`** - このプロパティは候補ですが、インデックスを作成しているすべてのコンテナーおよびフォルダー間で名前が一意である場合に限られます。 BLOB の場所に関係なく、最終的な結果としては、すべてのコンテンツのインデックスが作成された後に、検索インデックス内でドキュメント キー (名前) が一意である必要があります。 

  ストレージ名に関するもう 1 つの潜在的な問題は、ドキュメント キーでは無効なダッシュなどの文字が含まれている可能性があることです。 無効な文字を処理するには、`base64Encode` [フィールド マッピング関数](search-indexer-field-mappings.md#base64EncodeFunction)を使用します。 これを行う場合は、[Lookup Document (REST)](/rest/api/searchservice/lookup-document) などの API 呼び出しでドキュメント キーを渡すときにそれらのキーをエンコードすることも忘れないでください。 .NET では、[UrlTokenEncode メソッド](/dotnet/api/system.web.httpserverutility.urltokenencode)を使用して文字をエンコードできます。

+ **`metadata_storage_path`** - 完全パスであれば一意性は保証されます。ただし、パスに使われる `/` 文字は、[ドキュメント キーでは無効](/rest/api/searchservice/naming-rules)です。 前述のように、`base64Encode` [関数](search-indexer-field-mappings.md#base64EncodeFunction)を使用して文字をエンコードできます。

+ 3 番目のオプションは、カスタム メタデータ プロパティを BLOB に追加する方法です。 この方法を選んだ場合、BLOB のアップロード プロセスで、該当するメタデータのプロパティをすべての BLOB に追加する必要があります。 キーは必須のプロパティであるため、値が欠落している BLOB については、インデックスが一切作成されません。

> [!IMPORTANT]
> インデックス内のキー フィールドに対して明示的なマッピングが存在しない場合、Azure Cognitive Search は自動的に `metadata_storage_path` をキーおよび Base-64 エンコード キー値として使用します (上記の 2 つ目の選択肢)。
>

#### <a name="example"></a>例

次の例では、ドキュメント キーとして `metadata_storage_name` を示しています。 `key` という名前のキー フィールドと、ドキュメントのサイズを格納するための `fileSize` という名前の別のフィールドがインデックスに存在するものとします。 インデクサー定義の[フィールド マッピング](search-indexer-field-mappings.md)はフィールドの関連付けを確立し、`metadata_storage_name` には、サポートされていない文字を処理するための [ `base64Encode`フィールド マッピング関数](search-indexer-field-mappings.md#base64EncodeFunction)があります。

```http
PUT https://[service name].search.windows.net/indexers/my-blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : "my-blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_name", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_size", "targetFieldName" : "fileSize" }
  ]
}
```

#### <a name="how-to-make-an-encoded-field-searchable"></a>エンコードされたフィールドを "検索可能" にする方法

`metadata_storage_path` のようなフィールドのエンコードされたバージョンをキーとして使用する必要がある一方、そのフィールドを検索インデックスで (エンコードせずに) 検索可能にする必要もある場合があります。 両方のユース ケースをサポートするために、`metadata_storage_path` を 2 つのフィールドにマップできます。(エンコードされた) キー用のフィールドと、インデックス スキーマで "検索可能" に属すると想定できるパス フィールド用のフィールドです。 次の例は、`metadata_storage_path` の 2 つのフィールド マッピングを示しています。

```http
PUT https://[service name].search.windows.net/indexers/blob-indexer?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  "dataSourceName" : " blob-datasource ",
  "targetIndexName" : "my-target-index",
  "schedule" : { "interval" : "PT2H" },
  "fieldMappings" : [
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "key", "mappingFunction" : { "name" : "base64Encode" } },
    { "sourceFieldName" : "metadata_storage_path", "targetFieldName" : "path" }
  ]
}
```

<a name="PartsOfBlobToIndex"></a>

## <a name="index-content-and-metadata"></a>インデックスのコンテンツとメタデータ

BLOB にはコンテンツとメタデータが含まれます。 BLOB のどの部分にインデックスを作成するかは、`dataToExtract` 構成パラメーターを使用して制御できます。 次の値を使用できます。

+ `contentAndMetadata` - すべてのメタデータと、BLOB から抽出されたテキスト コンテンツにインデックスを作成するように指定します。 これが既定値です。

+ `storageMetadata` - [標準的な BLOB のプロパティおよびユーザー指定のメタデータ](../storage/blobs/storage-blob-container-properties-metadata.md)のみにインデックスを作成するように指定します。

+ `allMetadata` - 標準の BLOB プロパティと、[見つかったコンテンツの種類に対するメタデータ](search-blob-metadata-properties.md)を BLOB のコンテンツから抽出し、インデックスを作成するように指定します。

たとえば、ストレージ メタデータのみにインデックスを作成するには、次のように使用します。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "dataToExtract" : "storageMetadata" } }
}
```

<a name="how-azure-search-indexes-blobs"></a>

### <a name="indexing-blob-content"></a>BLOB コンテンツのインデックス作成

既定では、JSON や CSV などの構造化コンテンツを持つ BLOB には、1 つのテキスト チャンクとしてインデックスが作成されます。 ただし、JSON または CSV ドキュメントに内部構造 (区切り記号) がある場合は、解析モードを割り当てて、行または要素ごとに個別の検索ドキュメントを生成できます。 詳細については、「[JSON BLOB のインデックス作成](search-howto-index-json-blobs.md)」および「[CSV BLOB のインデックス作成](search-howto-index-csv-blobs.md)」を参照してください。

複合ドキュメントや埋め込みドキュメント (ZIP アーカイブ、添付ファイルを含む Outlook メールが埋め込まれた Word 文書、添付ファイルを含む .MSG ファイルなど) も、1 つのドキュメントとしてインデックスが作成されます。 例えば、.MSG ファイルの添付ファイルから抽出されたすべての画像が normalized_images フィールドに返されます。

ドキュメントのテキスト コンテンツが、`content` という名前の文字列フィールドに抽出されます。

  > [!NOTE]
  > どれだけのテキストが抽出されるかは、価格レベルに応じて Azure Cognitive Search によって制限されます。 現在の[サービスの制限](search-limits-quotas-capacity.md#indexer-limits)は、Free レベルの場合は 32,000 文字、Basic の場合は 64,000 文字、Standard の場合は 400 万文字、Standard S2 の場合は 800 万文字、Standard S3 の場合は 1,600 万文字です。 切り捨てられたドキュメントについては、インデクサーの状態の応答に警告が含められます。  

<a name="indexing-blob-metadata"></a>

### <a name="indexing-blob-metadata"></a>BLOB メタデータのインデックス作成

インデクサーでは、BLOB メタデータのインデックスを作成することもできます。 まず、ユーザーが指定したメタデータ プロパティは、そのまま抽出できます。 値を受け取るには、`Edm.String` 型の検索インデックスに、BLOB のメタデータ キーと同じ名前のフィールドを定義する必要があります。 たとえば、BLOB に値が `High` のメタデータ キー `Sensitivity` がある場合、検索インデックスで `Sensitivity` という名前のフィールドが定義されている必要があり、値 `High` が設定されます。

次に、標準的な BLOB メタデータ プロパティを下記のフィールドに抽出できます。 BLOB インデクサーにより、これらの BLOB メタデータ プロパティの内部フィールド マッピングが自動的に作成されます。 なお、インデックス定義を使用するには、必要なフィールドを追加する必要がありますが、インデクサーでのフィールド マッピングの作成は省略できます。

  + **metadata_storage_name** (`Edm.String`) - BLOB のファイル名。 たとえば、/my-container/my-folder/subfolder/resume.pdf という BLOB がある場合、このフィールドの値は `resume.pdf` になります。

  + **metadata_storage_path** (`Edm.String`) - ストレージ アカウントを含む、BLOB の完全な URI。 たとえば、`https://myaccount.blob.core.windows.net/my-container/my-folder/subfolder/resume.pdf`

  + **metadata_storage_content_type** (`Edm.String`) - BLOB をアップロードするためのコードで指定したコンテンツ タイプ。 たとえば、「 `application/octet-stream` 」のように入力します。

  + **metadata_storage_last_modified** (`Edm.DateTimeOffset`) - 前回変更時の BLOB のタイムスタンプ。 インデックスの初回作成後に最初から作成し直さなくても済むよう、変更された BLOB を Azure Cognitive Search が特定するために、このタイムスタンプが使用されます。

  + **metadata_storage_size** (`Edm.Int64`) - BLOB のサイズ (バイト単位)。

  + **metadata_storage_content_md5** (`Edm.String`) - BLOB コンテンツの MD5 ハッシュ (利用可能な場合)。

  + **metadata_storage_sas_token** (`Edm.String`) - BLOB に対してアクセスするために、[カスタム スキル](cognitive-search-custom-skill-interface.md)で使用できる一時的な SAS トークン。 このトークンは、有効期限が切れる可能性があるため、後で使用するために保存しないでください。

最後に、インデックスを作成している BLOB のドキュメント形式に固有のメタデータ プロパティも、インデックス スキーマで表すことができます。 コンテンツ固有のメタデータの詳細については、[コンテンツのメタデータ プロパティ](search-blob-metadata-properties.md)に関するページを参照してください。

重要な指摘点としては、検索インデックスに対し、ここに挙げたすべてのプロパティのフィールドを定義する必要はありません。実際のアプリケーションで必要となるプロパティだけを取り込んでください。

<a name="WhichBlobsAreIndexed"></a>

## <a name="how-to-control-which-blobs-are-indexed"></a>インデックスが作成される BLOB の制御方法

インデックスを作成する BLOB とスキップする BLOB は、BLOB のファイルの種類によって、または BLOB 自体にプロパティを設定してインデクサーに BLOB をスキップさせることによって制御できます。

### <a name="include-specific-file-extensions"></a>特定のファイル拡張子を含める

`indexedFileNameExtensions` を使用して、インデックスを作成するファイル拡張子のコンマ区切りリストを指定します (先頭にドットを付けます)。 たとえば、.PDF や .DOCX の BLOB のみのインデックスを作成する場合は、この操作を行います。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "indexedFileNameExtensions" : ".pdf,.docx" } }
}
```

### <a name="exclude-specific-file-extensions"></a>特定のファイル拡張子を除外する

`excludedFileNameExtensions` を使用して、スキップするファイル拡張子のコンマ区切りリストを指定します (同じく、先頭にドットを付けます)。 たとえば、.PNG と .JPEG の拡張子を持つ BLOB を除くすべての BLOB のインデックスを作成する場合は、この操作を行います。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "excludedFileNameExtensions" : ".png,.jpeg" } }
}
```

`indexedFileNameExtensions` と `excludedFileNameExtensions` の両方のパラメーターがある場合、インデクサーでは最初に `indexedFileNameExtensions` を調べ、次に `excludedFileNameExtensions` を調べます。 同じファイル拡張子が両方の一覧にある場合、インデックス作成から除外されます。

### <a name="add-skip-metadata-the-blob"></a>"スキップ" メタデータを BLOB に追加する

インデクサーの構成パラメーターは、コンテナーまたはフォルダー内のすべての BLOB に適用されます。 ときには、"*個々の BLOB*" のインデックスの作成方法を制御することが必要です。 これを行うには、次のメタデータのプロパティと値を BLOB ストレージ内の BLOB に追加します。 インデクサーでこのプロパティが検出されると、インデックス作成の実行において BLOB またはそのコンテンツがスキップされます。

| プロパティ名 | プロパティ値 | 説明 |
| ------------- | -------------- | ----------- |
| `AzureSearch_Skip` |`"true"` |BLOB を完全にスキップするように BLOB インデクサーに指示します。 メタデータとコンテンツのどちらの抽出も行われません。 特定の BLOB で何度もエラーが発生し、インデックス作成プロセスが中断されるときに利用できます。 |
| `AzureSearch_SkipContent` |`"true"` |これは、[前](#PartsOfBlobToIndex)に説明した、特定の BLOB を対象とする `"dataToExtract" : "allMetadata"` 設定と同じです。 |

## <a name="index-large-datasets"></a>大規模なデータセットにインデックスを付ける

BLOB のインデックス作成プロセスは、時間がかかる場合があります。 インデックスを作成する BLOB が数百万ある場合は、データをパーティション分割し、複数のインデクサーを使用して[データを並列で処理](search-howto-large-index.md#parallel-indexing)することで、インデックス作成を高速に処理できます。 設定方法は次のとおりです。

+ 複数の BLOB コンテナーまたは仮想フォルダーにデータをパーティション分割します。

+ コンテナーまたはフォルダーごとに 1 つずつ、データ ソースを設定します。 BLOB フォルダーをポイントするには、`query` パラメーターを使用します。

    ```json
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : "my-folder" }
    }
    ```

+ データ ソースごとに対応するインデクサーを作成します。 すべてのインデクサーから、同じターゲット検索インデックスをポイントする必要があります。  

+ サービス内の 1 つの検索単位は、特定の時点で 1 つのインデクサーを実行できます。 上記のように、複数のインデクサーの作成は、これらを実際に並行して実行する場合のみ有用です。

  複数のインデクサーを並行して実行するには、適切な数のパーティションとレプリカを作成して、検索サービスをスケールアウトします。 たとえば、検索サービスに 6 つの検索単位がある場合 (たとえば、2 つのパーティション x 3 つのレプリカ)、6 つのインデクサーを同時に実行でき、インデックス作成のスループットが 6 倍になります。 スケーリングと容量計画の詳細については、[Azure Cognitive Search サービスの容量を調整する](search-capacity-planning.md)方法に関するページを参照してください。

<a name="DealingWithErrors"></a>

## <a name="handling-errors"></a>エラーの処理

インデックス作成中に通常発生するエラーには、サポートされていないコンテンツの種類、コンテンツの欠落、BLOB のサイズ超過などがあります。

既定では、BLOB インデクサーは、サポートされていないコンテンツの種類 (画像など) が含まれる BLOB を検出するとすぐに停止されます。 `excludedFileNameExtensions` パラメーターを使用して特定のコンテンツの種類をスキップできます。 ただし、エラーが発生した場合でもインデックスの作成を継続し、後から個々のドキュメントをデバッグすることができます。 インデクサーのエラーの詳細については、「[インデクサーの一般的な問題のトラブルシューティング](search-indexer-troubleshooting.md)」および「[インデクサーのエラーと警告](cognitive-search-common-errors-warnings.md)」を参照してください。

### <a name="respond-to-errors"></a>エラーに応答する

エラーが発生したときのインデクサーの応答を制御する 4 つのインデクサー プロパティがあります。 次の例は、インデクサー定義でこれらのプロパティを設定する方法を示しています。 インデクサーが既に存在する場合は、ポータルで定義を編集することによって、これらのプロパティを追加できます。

#### <a name="maxfaileditems-and-maxfaileditemsperbatch"></a>`"maxFailedItems"` および `"maxFailedItemsPerBatch"`

BLOB の解析中またはインデックスへのドキュメントの追加中、処理のどこかの時点でエラーが発生した場合に、インデックス付けを続行します。 これらのプロパティには、許容できるエラーの数を設定します。 値を `-1` に設定すると、どれだけエラーが発生しても処理を継続します。 それ以外の場合、値は正の整数です。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "maxFailedItems" : 10, "maxFailedItemsPerBatch" : 10 }
}
```

#### <a name="failonunsupportedcontenttype-and-failonunprocessabledocument"></a>`"failOnUnsupportedContentType"` および `"failOnUnprocessableDocument"` 

一部の BLOB では、Azure Cognitive Search でコンテンツの種類を判別できないか、他の種類ではサポートされているコンテンツの種類のドキュメントを処理できない場合があります。 これらのエラー状態を無視するには、構成パラメーターを `false` に設定します。

```http
PUT https://[service name].search.windows.net/indexers/[indexer name]?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
  ... other parts of indexer definition
  "parameters" : { "configuration" : { "failOnUnsupportedContentType" : false, "failOnUnprocessableDocument" : false } }
}
```

### <a name="relax-indexer-constraints"></a>インデクサーの制約を緩和する

エラー状態が存在するかどうかを効果的に判別する [BLOB 構成プロパティ](/rest/api/searchservice/create-indexer#blob-configuration-parameters)を設定することもできます。 次のプロパティによって、制約を緩和し、緩和しなければ発生するエラーを抑制することができます。

+ `"indexStorageMetadataOnlyForOversizedDocuments"` は、大きすぎて処理できない BLOB コンテンツのストレージ メタデータに対してインデックスを作成します。 サイズが大きい BLOB は、既定ではエラーとして扱われます。 BLOB サイズの制限については、「[サービスの制限](search-limits-quotas-capacity.md)」を参照してください。

## <a name="see-also"></a>関連項目

+ [Azure Cognitive Search のインデクサー](search-indexer-overview.md)
+ [インデクサーの作成](search-howto-create-indexers.md)
+ [BLOB での AI エンリッチメントの概要](search-blob-ai-integration.md)
+ [BLOB 検索の概要](search-blob-storage-integration.md)