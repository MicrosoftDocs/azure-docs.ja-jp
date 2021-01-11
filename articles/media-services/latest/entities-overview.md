---
title: Media Services エンティティのフィルター処理、順序付け、およびページング
titleSuffix: Azure Media Services
description: Azure Media Services v3 エンティティのフィルター処理、順序付け、およびページングについて説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: overview
ms.date: 08/31/2020
ms.author: inhenkel
ms.custom: seodec18, devx-track-csharp
ms.openlocfilehash: 9a8cff3685cdaad011332adf58dc76f74976cd44
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89300189"
---
# <a name="filtering-ordering-and-paging-of-media-services-entities"></a>Media Services エンティティのフィルター処理、順序付け、およびページング

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

このトピックでは、Azure Media Services v3 エンティティを一覧表示するときに使用できる OData クエリ オプションと改ページ位置の自動調整のサポートについて説明します。

## <a name="considerations"></a>考慮事項

* `Datetime` 型のエンティティのプロパティは、常に UTC 形式です。
* クエリ文字列内の空白文字は、要求を送信する前に URL エンコードする必要があります。

## <a name="comparison-operators"></a>比較演算子

次の演算子を使用して、フィールドを定数値と比較できます。

等価演算子:

- `eq`:フィールドが定数値 "*と等しい*" かどうかをテストします。
- `ne`:フィールドが定数値 "*と等しくない*" かどうかをテストします。

範囲演算子:

- `gt`:フィールドが定数値 "*より大きい*" かどうかをテストします。
- `lt`:フィールドが定数値 "*より小さい*" かどうかをテストします。
- `ge`:フィールドが定数値*以上である*かどうかをテストします。
- `le`:フィールドが定数値 "*以下である*" かどうかをテストします。

## <a name="filter"></a>Assert

`$filter` を使用して OData フィルター パラメーターを指定し、関心のあるオブジェクトのみを検索します。

次の REST の例では、アセットの `alternateId` 値に対してフィルター処理を行います。

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$filter=properties/alternateId%20eq%20'unique identifier'
```

次の C# の例は、アセットの作成日に基づいてフィルター処理を実行しています。

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

## <a name="order-by"></a>Order by (並べ替え)

`$orderby` を使用して、指定したパラメーターによって返されたオブジェクトを並べ替えます。 次に例を示します。  

```
GET https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01$orderby=properties/created%20gt%202018-05-11T17:39:08.387Z
```

結果を昇順または降順で並べ替えるには、フィールド名に `asc` または `desc` をスペースで区切って追加します。 (例: `$orderby properties/created desc`)。

## <a name="skip-token"></a>スキップ トークン

クエリ応答に多数の項目が含まれている場合、サービスでは結果の次のページを取得するために使用する `$skiptoken` (`@odata.nextLink`) 値を返します。 それを利用して、結果セット全体をページングします。

Media Services v3 では、ページ サイズを構成することはできません。 ページ サイズは、エンティティの種類によって異なります。 詳細については、以降の各セクションを参照してください。

コレクションのページング中にエンティティが作成または削除された場合、その変更は返される結果に反映されます (これらの変更が、まだダウンロードされていないコレクションの一部である場合)。

> [!TIP]
> 常に `nextLink` を使用してコレクションを列挙します。特定のページ サイズに依存しないでください。
>
> `nextLink` 値は、エンティティのページが複数存在する場合にのみ表示されます。

`$skiptoken` が使用されている次の例について考えてみましょう。 必ず、*amstestaccount* を自分のアカウント名に置き換え、*api-version* の値を最新バージョンに設定してください。

次のようにアセットの一覧を要求すると、

```
GET  https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

次のような応答が返されます。

```
HTTP/1.1 200 OK

{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-00000000-0000-0000-0000-0000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-0000-0000-0000-000000000000","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-00000000-0000-0000-0000-000000000000","storageAccountName":"amsacctname","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

その後、次の get 要求を送信して、次のページを要求します。

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

次の C# の例は、アカウント内のすべてのストリーミング ロケーターを列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

## <a name="using-logical-operators-to-combine-query-options"></a>論理演算子を使用したクエリ オプションの結合

Media Services v3 では、**OR** および **AND** 論理演算子がサポートされています。 

次の REST の例では、ジョブの状態を確認します。

```
https://management.azure.com/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/qbtest/providers/Microsoft.Media/mediaServices/qbtest/transforms/VideoAnalyzerTransform/jobs?$filter=properties/state%20eq%20Microsoft.Media.JobState'Scheduled'%20or%20properties/state%20eq%20Microsoft.Media.JobState'Processing'&api-version=2018-07-01
```

同じクエリを C# で次のように作成します。 

```csharp
var odataQuery = new ODataQuery<Job>("properties/state eq Microsoft.Media.JobState'Scheduled' or properties/state eq Microsoft.Media.JobState'Processing'");
client.Jobs.List(config.ResourceGroup, config.AccountName, VideoAnalyzerTransformName, odataQuery);
```

## <a name="filtering-and-ordering-options-of-entities"></a>エンティティのフィルター処理および順序付けオプション

次の表は、フィルター処理および順序付けオプションをさまざまなエンティティに適用できる方法を示しています。

|エンティティ名|プロパティ名|Assert|Order|
|---|---|---|---|
|[アセット](/rest/api/media/assets/)|name|`eq`、`gt`、`lt`、`ge`、`le`|`asc` および `desc`|
||properties.alternateId |`eq`||
||properties.assetId |`eq`||
||properties.created| `eq`、`gt`、`lt`| `asc` および `desc`|
|[コンテンツ キー ポリシー](/rest/api/media/contentkeypolicies)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` および `desc`|
||properties.created    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` および `desc`|
||properties.description    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`||
||properties.lastModified|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` および `desc`|
||properties.policyId|`eq`, `ne`||
|[ジョブ](/rest/api/media/jobs)| name  | `eq`            | `asc` および `desc`|
||properties.state        | `eq`, `ne`        |                         |
||properties.created      | `gt`、`ge`、`lt`, `le`| `asc` および `desc`|
||properties.lastModified | `gt`、`ge`、`lt`, `le` | `asc` および `desc`| 
|[ストリーミング ロケーター](/rest/api/media/streaminglocators)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` および `desc`|
||properties.created    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` および `desc`|
||properties.endTime    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` および `desc`|
|[ストリーミング ポリシー](/rest/api/media/streamingpolicies)|name|`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` および `desc`|
||properties.created    |`eq`、`ne`、`ge`、`le`、`gt`、`lt`|`asc` および `desc`|
|[変換](/rest/api/media/transforms)| name | `eq`            | `asc` および `desc`|
|| properties.created      | `gt`、`ge`、`lt`, `le`| `asc` および `desc`|
|| properties.lastModified | `gt`、`ge`、`lt`, `le`| `asc` および `desc`|

## <a name="next-steps"></a>次のステップ

* [アセットを一覧表示する](/rest/api/media/assets/list)
* [コンテンツ キー ポリシーを一覧表示する](/rest/api/media/contentkeypolicies/list)
* [ジョブを一覧表示する](/rest/api/media/jobs/list)
* [ストリーミング ポリシーを一覧表示する](/rest/api/media/streamingpolicies/list)
* [ストリーミング ロケーターを一覧表示する](/rest/api/media/streaminglocators/list)
* [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
* [クォータと制限](limits-quotas-constraints.md)
