---
title: Azure Blob Storage 監視データのリファレンス | Microsoft Docs
description: Azure Blob Storage からのデータを監視するためのログとメトリックのリファレンス。
author: normesta
services: azure-monitor
ms.service: azure-monitor
ms.topic: reference
ms.date: 10/02/2020
ms.author: normesta
ms.subservice: logs
ms.custom: subject-monitoring
ms.openlocfilehash: b37586f66106e33b2a8dad034a6e7c131484be73
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100571617"
---
# <a name="azure-blob-storage-monitoring-data-reference"></a>Azure Blob Storage 監視データのリファレンス

Azure Storage の監視データの収集と分析の詳細については、「[Azure Storage の監視](monitor-blob-storage.md)」を参照してください。

## <a name="metrics"></a>メトリック

次の表は、Azure Storage に関して収集されるプラットフォーム メトリックを示しています。 

### <a name="capacity-metrics"></a>容量メトリック

容量メトリックの値は毎日 (最大 24 時間) 更新されます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべての容量メトリックに対して、1 時間 (PT1H) の時間グレインがサポートされます。

Azure Storage は、Azure Monitor で次の容量メトリックを提供します。

#### <a name="account-level"></a>アカウント レベル

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-account-capacity-metrics.md)]

#### <a name="blob-storage"></a>BLOB ストレージ

この表は、[BLOB ストレージのメトリック](../../azure-monitor/essentials/metrics-supported.md#microsoftstoragestorageaccountsblobservices)を示しています。

| メトリック | 説明 |
| ------------------- | ----------------- |
| BlobCapacity | ストレージ アカウントで使用されている Blob Storage の合計。 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024 <br/> 寸法: **BlobType**、および **BlobTier** ([定義](#metrics-dimensions)) |
| BlobCount    | ストレージ アカウントに格納されている BLOB オブジェクトの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 <br/> 寸法: **BlobType**、および **BlobTier** ([定義](#metrics-dimensions)) |
| BlobProvisionedSize | ストレージ アカウントでプロビジョニングされたストレージの総量。 このメトリックは、Premium Storage アカウントのみに適用されます。 <br/><br/> 単位: バイト <br/> 集計の種類:Average |
| ContainerCount    | ストレージ アカウントのコンテナーの数。 <br/><br/> 単位:Count <br/> 集計の種類:Average <br/> 値の例:1024 |
| IndexCapacity     | ADLS Gen2 階層構造のインデックスで使用される記憶域の量 <br/><br/> 単位:バイト <br/> 集計の種類:Average <br/> 値の例:1024 |

### <a name="transaction-metrics"></a>トランザクション メトリック

トランザクション メトリックは、ストレージ アカウントへの要求ごとに、Azure Storage から Azure Monitor に出力されます。 ストレージ アカウントにアクティビティがない場合、その間はトランザクション メトリックのデータは存在しません。 すべてのトランザクション メトリックは、アカウントと Blob Storage の両方のサービス レベルで使用できます。 時間グレインは、メトリック値が提供される時間間隔を定義します。 すべてのトランザクション メトリックに対してサポートされている時間グレインは PT1H と PT1M です。

[!INCLUDE [Transaction metrics](../../../includes/azure-storage-account-transaction-metrics.md)]

<a id="metrics-dimensions"></a>

## <a name="metrics-dimensions"></a>メトリックのディメンション

Azure Storage では、Azure Monitor の次のメトリック ディメンションがサポートされます。

### <a name="dimensions-available-to-all-storage-services"></a>すべてのストレージ サービスで使用可能なディメンション

[!INCLUDE [Metrics dimensions](../../../includes/azure-storage-account-metrics-dimensions.md)]

### <a name="dimensions-specific-to-blob-storage"></a>Blob Storage 固有のディメンション

| ディメンション名 | 説明 |
| ------------------- | ----------------- |
| **BlobType** | BLOB メトリックの BLOB の種類のみ。 サポートされる値は、**BlockBlob**、**PageBlob**、**Azure Data Lake Storage** です。 追加 BLOB は **BlockBlob** に含まれます。 |
| **BlobTier** | Azure Storage からはさまざまなアクセス層が提供され、最もコスト効果の高い方法で BLOB オブジェクト データを格納できます。 詳細については、[Azure Storage の BLOB 層](../blobs/storage-blob-storage-tiers.md)に関する記事を参照してください。 サポートされる値は次のとおりです。 <br/> <li>**Hot**:ホット層</li> <li>**Cool**:クール層</li> <li>**アーカイブ**: アーカイブ層</li> <li>**Premium**:ブロック BLOB 用 Premium 層</li> <li>**P4/P6/P10/P15/P20/P30/P40/P50/P60**:Premium ページ BLOB 用の層の種類</li> <li>**Standard**:Standard ページ BLOB 用の層の種類</li> <li>**Untiered**:汎用 v1 ストレージ アカウントの層の種類</li> |

メトリック サポート ディメンションの場合、対応するメトリック値を表示するには、ディメンション値を指定する必要があります。 たとえば、成功した応答の **Transaction** 値を確認する場合は、**ResponseType** ディメンション を **Success** でフィルター処理する必要があります。 ブロック BLOB の **BlobCount** 値を確認する場合は、**BlobType** ディメンションを **BlockBlob** でフィルター処理する必要があります。

## <a name="resource-logs-preview"></a>リソース ログ (プレビュー)

> [!NOTE]
> Azure Monitor の Azure Storage ログはパブリック プレビュー段階にあり、すべてのパブリック クラウド リージョンでプレビュー テスト用に使用できます。 このプレビューでは、汎用 v1 と汎用 v2 ストレージ アカウントの BLOB (Azure Data Lake Storage Gen2 を含む)、ファイル、キュー、テーブル、Premium ストレージ アカウントに対してログが有効になります。 クラシック ストレージ アカウントはサポートされていません。

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

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-operation.md)]

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

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-authentication.md)]

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

[!INCLUDE [Account level capacity metrics](../../../includes/azure-storage-logs-properties-service.md)]

## <a name="see-also"></a>関連項目

- Azure Storage 監視の詳細については、「[Azure Storage の監視](monitor-blob-storage.md)」を参照してください。
- Azure リソースの監視の詳細については、「[Azure Monitor を使用した Azure リソースの監視](../../azure-monitor/essentials/monitor-azure-resource.md)」を参照してください。
