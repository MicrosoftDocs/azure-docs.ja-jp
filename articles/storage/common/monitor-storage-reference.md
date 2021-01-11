---
title: Azure Storage 監視データのリファレンス | Microsoft Docs
description: Azure Storage からのデータを監視するためのログとメトリックのリファレンス。
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 05/01/2020
ms.author: normesta
ms.subservice: logs
ms.custom: monitoring
ms.openlocfilehash: 28a127b4debeacd2562867008bc594897558d50d
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/30/2020
ms.locfileid: "87446837"
---
# <a name="azure-storage-monitoring-data-reference"></a>Azure Storage 監視データのリファレンス

Azure Storage の監視データの収集と分析の詳細については、「[Azure Storage の監視](monitor-storage.md)」を参照してください。

## <a name="metrics"></a>メトリック

次の表は、Azure Storage に関して収集されるプラットフォーム メトリックを示しています。 

### <a name="capacity-metrics"></a>容量メトリック

容量メトリックの値は 1 時間ごとに Azure Monitor に送信され、 毎日更新されます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべての容量メトリックに対して、1 時間 (PT1H) の時間グレインがサポートされます。

Azure Storage は、Azure Monitor で次の容量メトリックを提供します。

#### <a name="account-level"></a>アカウント レベル

この表は、[アカウントレベルのメトリック](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccounts)を示しています。

| メトリック | 説明 |
| ------------------- | ----------------- |
| UsedCapacity | ストレージ アカウントによって使用されているストレージの量。 Standard ストレージ アカウントについては、Blob、Table、File、および Queue で使用される容量の合計です。 Premium ストレージ アカウントと BLOB ストレージ アカウントについては、BlobCapacity と同じです。 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024 |

#### <a name="blob-storage"></a>BLOB ストレージ

この表は、[BLOB ストレージのメトリック](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsblobservices)を示しています。

| メトリック | 説明 |
| ------------------- | ----------------- |
| BlobCapacity | ストレージ アカウントで使用されている Blob Storage の合計。 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024 <br/> 寸法: **BlobType**、および **BlobTier** ([定義](#metrics-dimensions)) |
| BlobCount    | ストレージ アカウントに格納されている BLOB オブジェクトの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 <br/> 寸法: **BlobType**、および **BlobTier** ([定義](#metrics-dimensions)) |
| BlobProvisionedSize | ストレージ アカウントでプロビジョニングされたストレージの総量。 このメトリックは、Premium Storage アカウントのみに適用されます。 <br/><br/> 単位: バイト <br/> 集計の種類:Average |
| ContainerCount    | ストレージ アカウントのコンテナーの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |
| IndexCapacity     | ADLS Gen2 階層構造のインデックスで使用される記憶域の量 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024 |

#### <a name="table-storage"></a>テーブル ストレージ

この表は、[Table ストレージのメトリック](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountstableservices)を示しています。

| メトリック | 説明 |
| ------------------- | ----------------- |
| TableCapacity | ストレージ アカウントによって使用されている Table Storage の量。 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024 |
| TableCount   | ストレージ アカウントのテーブルの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |
| TableEntityCount | ストレージ アカウントのテーブル エンティティの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |

#### <a name="queue-storage"></a>ストレージ

この表は、[Queue storage のメトリック](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsfileservices)を示しています。

| メトリック | 説明 |
| ------------------- | ----------------- |
| QueueCapacity | ストレージ アカウントによって使用されている Queue ストレージの量。 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024 |
| QueueCount   | ストレージ アカウントのキューの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |
| QueueMessageCount | ストレージ アカウントの Queue サービス内のキュー メッセージの概数。 <br/><br/>単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |

#### <a name="file-storage"></a>File Storage

この表は、[File Storage のメトリック](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported#microsoftstoragestorageaccountsqueueservices)を示しています。

| メトリック | 説明 |
| ------------------- | ----------------- |
| FileCapacity | ストレージ アカウントによって使用されている File ストレージの量。 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024 |
| FileCount   | ストレージ アカウントのファイルの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |
| FileShareCount | ストレージ アカウントのファイル共有の数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |
| FileShareProvisionedIOPS | ファイル共有でプロビジョニングされた IOPS の数。 このメトリックは、Premium ファイル ストレージにのみ適用されます。 <br/><br/> 単位: バイト <br/> 集計の種類:Average |

### <a name="transaction-metrics"></a>トランザクション メトリック

トランザクション メトリックは、ストレージ アカウントへの要求ごとに、Azure Storage から Azure Monitor に出力されます。 ストレージ アカウントにアクティビティがない場合、その間はトランザクション メトリックのデータは存在しません。 すべてのトランザクション メトリックを、アカウント レベルとサービス レベルの両方 (Blob Storage、Table Storage、Azure Files、および Queue ストレージ) で使用することができます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべてのトランザクション メトリックに対してサポートされている時間グレインは PT1H と PT1M です。

Azure Storage は、Azure Monitor で次のトランザクション メトリックを提供します。

| メトリック | 説明 |
| ------------------- | ----------------- |
| トランザクション | ストレージ サービスまたは指定された API 操作に対して行われた要求の数。 この数には、成功した要求と失敗した要求およびエラーが発生した要求が含まれます。 <br/><br/> 単位:Count <br/> 集計の種類:合計 <br/> 適用可能なディメンション:ResponseType、GeoType、ApiName、Authentication ([定義](#metrics-dimensions))<br/> 値の例:1024 |
| イングレス | イングレス データの量。 この値には、外部クライアントから Azure Storage へのイングレスおよび Azure 内でのイングレスが含まれます。 <br/><br/> 単位:バイト <br/> 集計の種類:合計 <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| エグレス | エグレス データの量。 この値には、外部クライアントから Azure Storage へのエグレスおよび Azure 内でのエグレスが含まれます。 したがって、この値には、課金対象のエグレスが反映されません。 <br/><br/> 単位:バイト <br/> 集計の種類:合計 <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| SuccessServerLatency | Azure Storage による成功した要求の平均処理時間。 この値には、SuccessE2ELatency で指定されているネットワーク待機時間は含まれません。 <br/><br/> 単位:ミリ秒 <br/> 集計の種類:Average <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| SuccessE2ELatency | ストレージ サービスまたは指定された API 操作に対して行われた成功した要求の平均エンド ツー エンド待機時間。 この値には、要求の読み取り、応答の送信、および応答の受信確認を受け取るために Azure Storage 内で必要な処理時間が含まれます。 <br/><br/> 単位:ミリ秒 <br/> 集計の種類:Average <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:1024 |
| 可用性 | ストレージ サービスまたは指定された API 操作の可用性の割合。 可用性は、合計課金対象要求数の値を取得し、それを該当する要求の数 (予期しないエラーになった要求を含む) で割ることによって、計算されます。 予期しないエラーすべてが、ストレージ サービスまたは指定された API 操作の可用性の低下をもたらします。 <br/><br/> 単位:Percent <br/> 集計の種類:Average <br/> 適用可能なディメンション:GeoType、ApiName、Authentication ([定義](#metrics-dimensions)) <br/> 値の例:99.99 |

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>メトリックのディメンション

Azure Storage では、Azure Monitor の次のメトリック ディメンションがサポートされます。

| ディメンション名 | 説明 |
| ------------------- | ----------------- |
| **BlobType** | BLOB メトリックの BLOB の種類のみ。 サポートされる値は、**BlockBlob**、**PageBlob**、**Azure Data Lake Storage** です。 追加 BLOB は **BlockBlob** に含まれます。 |
| **BlobTier** | Azure Storage からはさまざまなアクセス層が提供され、最もコスト効果の高い方法で BLOB オブジェクト データを格納できます。 詳細については、[Azure Storage の BLOB 層](../blobs/storage-blob-storage-tiers.md)に関する記事を参照してください。 サポートされる値は次のとおりです。 <br/> <li>**Hot**:ホット層</li> <li>**Cool**:クール層</li> <li>**アーカイブ**: アーカイブ層</li> <li>**Premium**:ブロック BLOB 用 Premium 層</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**:Premium ページ BLOB 用の層の種類</li> <li>**Standard**:Standard ページ BLOB 用の層の種類</li> <li>**Untiered**:汎用 v1 ストレージ アカウントの層の種類</li> |
| **GeoType** | プライマリ クラスターまたはセカンダリ クラスターからのトランザクション。 使用できる値には、**Primary** と **Secondary** が含まれています。 セカンダリ テナントからオブジェクトを読み取るときに、読み取りアクセス geo 冗長ストレージ (RA-GRS) に適用されます。 |
| **ResponseType** | トランザクション応答の種類。 次の値をご利用いただけます。 <br/><br/> <li>**ServerOtherError**:記述されていない、その他すべてのサーバー側エラー </li> <li>**ServerBusyError**:HTTP 503 ステータス コードを返した認証済み要求。 </li> <li>**ServerTimeoutError**:HTTP 500 ステータス コードを返した、タイムアウトした認証済み要求。 タイムアウトは、サーバー エラーが原因で発生しました。 </li> <li>**AuthorizationError**:データの不正アクセスまたは承認エラーが原因で失敗した認証済み要求。 </li> <li>**NetworkError**:ネットワーク エラーが原因で失敗した認証済み要求。 クライアントがタイムアウト期限が切れる前に途中で接続を終了したときによく発生します。 </li><li>**ClientAccountBandwidthThrottlingError**:この要求は、[ストレージ アカウントのスケーラビリティの制限を超えた](scalability-targets-standard-account.md)ため、帯域幅が調整されます。</li><li>**ClientAccountRequestThrottlingError**:この要求は、[ストレージ アカウントのスケーラビリティの制限を超えた](scalability-targets-standard-account.md)ため、要求レートが調整されます。<li>**ClientThrottlingError**:その他のクライアント側の調整エラー。 ClientAccountBandwidthThrottlingError と ClientAccountRequestThrottlingError は除外されます。</li> <li>**ClientTimeoutError**:HTTP 500 ステータス コードを返した、タイムアウトした認証済み要求。 クライアントのネットワーク タイムアウトまたは要求タイムアウトが、ストレージ サービスで予期される値よりも低く設定されている場合、これは予期されるタイムアウトです。 それ以外の場合は、ServerTimeoutError としてレポートされます。 </li> <li>**ClientOtherError**:記述されていない、その他すべてのクライアント側エラー。 </li> <li>**成功**:成功した要求</li> <li> **SuccessWithThrottling**: 最初に試みたときは SMB クライアントが調整されたが再試行後に成功したときの、成功した要求。</li> |
| **ApiName** | 操作の名前。 次に例を示します。 <br/> <li>**CreateContainer**</li> <li>**DeleteBlob**</li> <li>**GetBlob**</li> すべての操作名については、こちらの[ドキュメント](/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)を参照してください。 |
| **認証** | トランザクションで使用される認証の種類。 次の値をご利用いただけます。 <br/> <li>**AccountKey**:トランザクションは、ストレージ アカウント キーを使って認証されます。</li> <li>**SAS**:トランザクションは、Shared Access Signature を使って認証されます。</li> <li>**OAuth**:トランザクションは、OAuth アクセス トークンを使って認証されます。</li> <li>**Anonymous**:トランザクションは匿名で要求されます。 プリフライト要求は含まれません。</li> <li>**AnonymousPreflight**:トランザクションは、プリフライト要求です。</li> |

メトリック サポート ディメンションの場合、対応するメトリック値を表示するには、ディメンション値を指定する必要があります。 たとえば、成功した応答の **Transaction** 値を確認する場合は、**ResponseType** ディメンション を **Success** でフィルター処理する必要があります。 また、ブロック BLOB の **BlobCount** 値を確認する場合は、**BlobType** ディメンションを **BlockBlob** でフィルター処理する必要があります。

## <a name="resource-logs-preview"></a>リソース ログ (プレビュー)

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 プレビューに登録するには、[こちらのページ](https://forms.microsoft.com/Pages/ResponsePage.aspx?id=v4j5cvGGr0GRqy180BHbRxW65f1VQyNCuBHMIMBV8qlUM0E0MFdPRFpOVTRYVklDSE1WUTcyTVAwOC4u)を参照してください。  このプレビューでは、汎用 v1 と汎用 v2 ストレージ アカウントの BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、テーブル、Premium ストレージ アカウントに対してログが有効になります。 クラシック ストレージ アカウントはサポートされていません。

次の表に、Azure Monitor ログまたは Azure Storage で Azure Storage リソース ログが収集される場合のそれらのプロパティを示します。 プロパティによって、操作、サービス、操作の実行に使用された承認の種類が示されます。

### <a name="fields-that-describe-the-operation"></a>操作を説明するフィールド

```json
{
    "time": "2019-02-28T19:10:21.2123117Z",
    "resourceId": "/subscriptions/12345678-2222-3333-4444-555555555555/resourceGroups/mytestrp/providers/Microsoft.Storage/storageAccounts/testaccount1/blobServices/default",
    "category": "StorageWrite",
    "operationName": "PutBlob",
    "operationVersion": "2017-04-17",
    "schemaVersion": "1.0",
    "statusCode": 201,
    "statusText": "Success",
    "durationMs": 5,
    "callerIpAddress": "192.168.0.1:11111",
    "correlationId": "ad881411-201e-004e-1c99-cfd67d000000",
    "location": "uswestcentral",
    "uri": "http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10"
}
```

| プロパティ | 説明 |
|:--- |:---|
|**time** | ストレージで要求が受信された協定世界時 (UTC) での時刻。 (例: `2018/11/08 21:09:36.6900118`)。|
|**resourceId** | ストレージ アカウントのリソース ID。 例: `/subscriptions/208841be-a4v3-4234-9450-08b90c09f4/resourceGroups/`<br>`myresourcegroup/providers/Microsoft.Storage/storageAccounts/mystorageaccount/storageAccounts/blobServices/default`|
|**category** | 要求された操作のカテゴリ。 例: `StorageRead`、`StorageWrite`、または`StorageDelete`。|
|**operationName** | 実行された REST 操作の種類。 <br> 操作の完全な一覧については、[「ストレージ分析のログに記録された操作とステータスメッセージ」のトピック](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)を参照してください。 |
|**operationVersion** | 要求が行われたときに指定されたストレージ サービスのバージョン。 これは、**x-ms-version** ヘッダーの値と同じです (例: `2017-04-17`)。|
|**schemaVersion** | ログのスキーマ バージョン (例: `1.0`)。|
|**statusCode** | 要求の HTTP ステータス コード。 要求が中断された場合、この値は `Unknown` に設定される可能性があります。 <br> 例: `206` |
|**statusText** | 要求された操作のステータス。  ステータス メッセージの完全な一覧については、[「ストレージ分析のログに記録された操作とステータスメッセージ」のトピック](https://docs.microsoft.com/rest/api/storageservices/storage-analytics-logged-operations-and-status-messages)を参照してください。 バージョン 2017-04-17 以降では、ステータス メッセージ `ClientOtherError` は使用されません。 代わりに、このフィールドにはエラー コードが含まれています。 例: `SASSuccess`  |
|**durationMs** | 要求された操作の実行に要した合計の時間 (ミリ秒単位)。 これには、受信要求を読み取り、要求元に応答を送信する時間が含まれます (例: `12`)。|
|**callerIpAddress** | ポート番号を含む要求元の IP アドレス (例: `192.100.0.102:4362`)。 |
|**correlationId** | リソース間でログを関連付けるために使用される ID (例: `b99ba45e-a01e-0042-4ea6-772bbb000000`)。 |
|**location** | ストレージ アカウントの場所 (例: `North Europe`)。 |
|**protocol**|操作で使用されるプロトコル。 例: `HTTP`、`HTTPS`、`SMB`、`NFS`|
| **uri** | 要求された Uniform Resource Identifier (例: `http://myaccountname.blob.core.windows.net/cont1/blobname?timeout=10`)。 |

### <a name="fields-that-describe-how-the-operation-was-authenticated"></a>操作がどのように認証されたかを説明するフィールド

```json
{
    "identity": {
        "authorization": [
            {
                "action": "Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read",
                "principals": [
                    {
                        "id": "fde5ba15-4355-4223-b811-cccccccccccc",
                        "type": "User"
                    }
                ],
                "roleAssignmentId": "ecf75cb8-491c-4a25-ad6e-aaaaaaaaaaaa",
                "roleDefinitionId": "b7e6dc6d-f1e8-4753-8033-ffffffffffff"
            }
        ],
        "requester": {
            "appId": "691458b9-1327-4635-9f55-bbbbbbbbbbbb",
            "audience": "https://storage.azure.com/",
            "objectId": "fde5ba15-4355-4223-b811-cccccccccccc",
            "tenantId": "72f988bf-86f1-41af-91ab-dddddddddddd",
            "tokenIssuer": "https://sts.windows.net/72f988bf-86f1-41af-91ab-eeeeeeeeeeee/"
           },
        "type": "OAuth"
    },
}

```

| プロパティ | 説明 |
|:--- |:---|
|**identity / type** | 要求の作成に使用された認証の種類。 例: `OAuth`、`SAS Key`、`Account Key`、`Anonymous` |
|**identity / tokenHash**|このフィールドは、内部使用専用として予約されています。 |
|**authorization / action** | 要求に割り当てられているアクション。 例: `Microsoft.Storage/storageAccounts/blobServices/containers/blobs/read` |
|**authorization / roleAssignmentId** | ロールの割り当て ID (例: `4e2521b7-13be-4363-aeda-111111111111`)。|
|**authorization / roleDefinitionId** | ロール定義 ID (例: `ba92f5b4-2d11-453d-a403-111111111111"`)。|
|**principals / id** | セキュリティ プリンシパルの ID (例: `a4711f3a-254f-4cfb-8a2d-111111111111`)。|
|**principals / type** | セキュリティ プリンシパルの種類 (例: `ServicePrincipal`)。 |
|**requester / appID** | 要求元として使用される Open Authorization (OAuth) アプリケーション ID <br> (例: `d3f7d5fe-e64a-4e4e-871d-333333333333`)。|
|**requester / audience** | 要求の OAuth 対象ユーザー (例: `https://storage.azure.com`)。 |
|**requester / objectId** | 要求元の OAuth オブジェクト ID。 Kerberos 認証の場合は、Kerberos で認証されたユーザーのオブジェクト識別子を表します (例: `0e0bf547-55e5-465c-91b7-2873712b249c`)。 |
|**requester / tenantId** | 識別子の OAuth テナント ID (例: `72f988bf-86f1-41af-91ab-222222222222`)。|
|**requester / tokenIssuer** | OAuth トークン発行者 (例: `https://sts.windows.net/72f988bf-86f1-41af-91ab-222222222222/`)。|
|**requester / upn** | 要求元のユーザー プリンシパル名 (UPN) (例: `someone@contoso.com`)。 |
|**requester / userName** | このフィールドは、内部使用専用として予約されています。|

### <a name="fields-that-describe-the-service"></a>サービスを説明するフィールド

```json
{
    "properties": {
        "accountName": "testaccount1",
        "requestUrl": "https://testaccount1.blob.core.windows.net:443/upload?restype=container&comp=list&prefix=&delimiter=%2F&marker=&maxresults=30&include=metadata&_=1551405598426",
        "userAgentHeader": "Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/64.0.3282.140 Safari/537.36 Edge/17.17134",
        "referrerHeader": "blob:https://ms.portal.azure.com/6f50025f-3b88-488d-b29e-3c592a31ddc9",
        "clientRequestId": "",
        "etag": "",
        "serverLatencyMs": 63,
        "serviceType": "blob",
        "operationCount": 0,
        "requestHeaderSize": 2658,
        "requestBodySize": 0,
        "responseHeaderSize": 295,
        "responseBodySize": 2018,
        "contentLengthHeader": 0,
        "requestMd5": "",
        "serverMd5": "",
        "lastModifiedTime": "",
        "conditionsUsed": "",
        "smbTreeConnectID" : "0x3",
        "smbPersistentHandleID" : "0x6003f",
        "smbVolatileHandleID" : "0xFFFFFFFF00000065",
        "smbMessageID" : "0x3b165",
        "smbCreditsConsumed" : "0x3",
        "smbCommandDetail" : "0x2000 bytes at offset 0xf2000",
        "smbFileId" : " 0x9223442405598953",
        "smbSessionID" : "0x8530280128000049",
        "smbCommandMajor" : "0x6",
        "smbCommandMinor" : "DirectoryCloseAndDelete"
    }

}
```

| プロパティ | 説明 |
|:--- |:---|
|**accountName** | ストレージ アカウントの名前。 (例: `mystorageaccount`)。  |
|**requestUrl** | 要求された URL (例: `http://mystorageaccount.blob.core.windows.net/cont1/blobname?timeout=10`)。|
|**userAgentHeader** | 引用符で囲んだ **User-Agent ヘッダー**値 (例: `WA-Storage/6.2.0 (.NET CLR 4.0.30319.42000; Win32NT 6.2.9200.0)`)。|
|**referrerHeader** | **Referrer** ヘッダー値 (例: `http://contoso.com/about.html`)。|
|**clientRequestId** | 要求の **x-ms-client-request-id** ヘッダー値 (例: `360b66a6-ad4f-4c4a-84a4-0ad7cb44f7a6`)。 |
|**etag** | 引用符で囲まれている、返されたオブジェクトの ETag 識別子 (例: `0x8D101F7E4B662C4`)。  |
|**serverLatencyMs** | 要求された操作の実行に要した合計の時間 (ミリ秒単位)。 この値には、ネットワークの遅延 (受信要求を読み取り、要求元に応答を送信する時間) は含まれません (例: `22`)。 |
|**serviceType** | この要求に関連付けられたサービス。 例: `blob`、`table`、`files`、`queue`。 |
|**operationCount** | 要求に関連する、ログに記録された各操作の数。 このカウントはインデックス `0` で始まります。 要求によっては、BLOB のコピー要求など、複数の操作が必要になる場合があります。 ほとんどの要求では 1 つの操作だけが実行されます (例: `1`)。 |
|**requestHeaderSize** | 要求ヘッダーのサイズ (バイト単位) (例: `578`)。 <br>要求が成功しなかった場合、この値は空になることがあります。 |
|**requestBodySize** | ストレージ サービスによって読み取られた要求パケットのサイズ (バイト単位) <br> (例: `0`)。 <br>要求が成功しなかった場合、この値は空になることがあります。  |
|**responseHeaderSize** | 応答ヘッダーのサイズ (バイト単位) (例: `216`)。 <br>要求が成功しなかった場合、この値は空になることがあります。  |
|**responseBodySize** | ストレージ サービスによって書き込まれた応答パケットのサイズ (バイト単位)。 要求が成功しなかった場合、この値は空になることがあります (例: `216`)。  |
|**requestMd5** | 要求の **Content-MD5** ヘッダーまたは **x-ms-content-md5** ヘッダーの値。 このフィールドに指定された MD5 ハッシュ値は、要求の内容を表します (例: `788815fd0198be0d275ad329cafd1830`)。 <br>このフィールドは空になる場合があります。  |
|**serverMd5** | ストレージ サービスによって計算された MD5 ハッシュの値 (例: `3228b3cf1069a5489b298446321f8521`)。 <br>このフィールドは空になる場合があります。  |
|**lastModifiedTime** | 返されたオブジェクトの最終更新日時 (LMT)  (例: `Tuesday, 09-Aug-11 21:13:26 GMT`)。 <br>複数のオブジェクトを返すことができる操作に対しては、このフィールドは空になります。 |
|**conditionsUsed** | 条件を表すキーと値のペアをセミコロンで区切った一覧。 条件は次のいずれかになります。 <li> If-Modified-Since <li> If-Unmodified-Since <li> If-Match <li> If-None-Match  <br> (例: `If-Modified-Since=Friday, 05-Aug-11 19:11:54 GMT`)。 |
|**contentLengthHeader** | ストレージ サービスに送信された要求の Content-Length ヘッダーの値。 要求が成功した場合、この値は requestBodySize と同じです。 要求が成功しなかった場合、この値は requestBodySize と同じ値にならないことや、空になることがあります。 |
|**tlsVersion** | 要求の接続で使用される TLS のバージョン (例: `TLS 1.2`)。 |
|**smbTreeConnectID** | ツリー接続時に確立したサーバー メッセージ ブロック (SMB) の **treeConnectId**。 例: `0x3` |
|**smbPersistentHandleID** | ネットワーク再接続を維持する SMB2 CREATE 要求の永続的ハンドル ID。  [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 では **SMB2_FILEID.Persistent** として参照されます。 例: `0x6003f` |
|**smbVolatileHandleID** | ネットワーク再接続時に再利用される SMB2 CREATE 要求の揮発性ハンドル ID。  [MS-SMB2](https://docs.microsoft.com/openspecs/windows_protocols/ms-smb2/f1d9b40d-e335-45fc-9d0b-199a31ede4c3) 2.2.14.1 では **SMB2_FILEID.Volatile**として参照されます。 例: `0xFFFFFFFF00000065` |
|**smbMessageID** | 接続の相対的な **MessageId**。 例: `0x3b165` |
|**smbCreditsConsumed** | 要求で消費されるイングレスまたはエグレス (64k 単位)。 例: `0x3` |
|**smbCommandDetail** | 一般的な種類の要求ではなく、この特定の要求に関する詳細情報。 例: `0x2000 bytes at offset 0xf2000` |
|**smbFileId** | ファイルまたはディレクトリに関連付けられている **FileId**。  NTFS FileId とほぼ同じです。 例: `0x9223442405598953` |
|**smbSessionID** | セッションのセットアップ時に確立される SMB2 の **SessionId**。 例: `0x8530280128000049` |
|**smbCommandMajor  uint32** | **SMB2_HEADER.Command** の値。 現在、これは 0 から 18 まで (両端を含む) の数値です。 例: `0x6` |
|**smbCommandMinor** | 必要に応じて **SmbCommandMajor** のサブクラス。 例: `DirectoryCloseAndDelete` |

## <a name="see-also"></a>関連項目

- Azure Storage 監視の詳細については、「[Azure Storage の監視](monitor-storage.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/insights/monitor-azure-resource.md)」を参照してください。