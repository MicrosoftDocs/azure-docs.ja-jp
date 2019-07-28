---
title: Media Services エンティティのフィルター処理、順序付け、ページング - Azure | Microsoft Docs
description: この記事では、Azure Media Services エンティティのフィルター処理、順序付け、ページングについて説明します。
services: media-services
documentationcenter: ''
author: juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 04/08/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: 28c880e8709074d808a41d9920361eaa2b20ecc4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "60732367"
---
# <a name="filtering-ordering-paging-of-media-services-entities"></a>Media Services エンティティのフィルター処理、順序付け、ページング

Media Services では、Media Services v3 エンティティ用の次の OData クエリ オプションをサポートします。 

* $filter 
* $orderby 
* $top 
* $skiptoken 

演算子の説明:

* Eq = 次の値と等しい
* Ne = 次の値と等しくない
* Ge = 次の値以上
* Le = 次の値以下
* Gt = より大きい
* Lt = より小さい

Datetime 型のエンティティのプロパティは、常に UTC 形式です。

## <a name="page-results"></a>ページ結果

クエリ応答に多数の項目が含まれている場合、サービスは "\@odata.nextLink" プロパティを返して結果の次のページを取得します。 この方法を利用して、結果セット全体のページングを実行できます。 ページ サイズを構成することはできません。 ページ サイズは、エンティティの種類によって異なります。詳細については、以下の個々のセクションをお読みください。

コレクションのページング中にエンティティが作成または削除された場合、(コレクションの中で、ダウンロードされていない部分に対する変更の場合) その変更は返される結果に反映されます。 

> [!TIP]
> 常に次のリンクを使用してコレクションを列挙する必要があります。特定のページ サイズに依存しないでください。

## <a name="assets"></a>アセット

### <a name="filteringordering"></a>フィルター処理/順序付け

次の表は、[アセット](https://docs.microsoft.com/rest/api/media/assets) プロパティに適用できるオプションをフィルター処理および順序付けする方法をまとめたものです。 

|Name|filter|順序|
|---|---|---|
|id|||
|name|eq、gt、lt| 昇順および降順|
|properties.alternateId |eq||
|properties.assetId |eq||
|properties.container |||
|properties.created| eq、gt、lt| 昇順および降順|
|properties.description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

次の C# 例は、作成日に基づいてフィルター処理を実行しています。

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>改ページ位置の自動修正 

改ページ位置の自動修正は、4 つの有効な各並べ替え順序でサポートされています。 現時点では、ページ サイズは 1000 です。

#### <a name="c-example"></a>C# の例

次の C# 例は、アカウント内のすべてのアセットを列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

#### <a name="rest-example"></a>REST の例

たとえば、$skiptoken が使用されている次の例について考えてみましょう。 必ず、*amstestaccount* を自分のアカウント名に置き換え、*api-version* の値を最新バージョンに設定してください。

次のようにアセットの一覧を要求すると、

```
GET  https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01 HTTP/1.1
x-ms-client-request-id: dd57fe5d-f3be-4724-8553-4ceb1dbe5aab
Content-Type: application/json; charset=utf-8
```

次のような応答が返されます。

```
HTTP/1.1 200 OK
 
{
"value":[
{
"name":"Asset 0","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 0","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-5a4f-470a-9d81-6037d7c23eff","created":"2018-12-11T22:12:44.98Z","lastModified":"2018-12-11T22:15:48.003Z","container":"asset-98d07299-5a4f-470a-9d81-6037d7c23eff","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
},
// lots more assets
{
"name":"Asset 517","id":"/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaservices/amstestaccount/assets/Asset 517","type":"Microsoft.Media/mediaservices/assets","properties":{
"assetId":"00000000-912e-447b-a1ed-0f723913b20d","created":"2018-12-11T22:14:08.473Z","lastModified":"2018-12-11T22:19:29.657Z","container":"asset-fd05a503-912e-447b-a1ed-0f723913b20d","storageAccountName":"amsdevc1stoaccount11","storageEncryptionFormat":"None"
}
}
],"@odata.nextLink":"https:// management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517"
}
```

その後、次の get 要求を送信して、次のページを要求します。

```
https://management.azure.com/subscriptions/00000000-3761-485c-81bb-c50b291ce214/resourceGroups/mediaresources/providers/Microsoft.Media/mediaServices/amstestaccount/assets?api-version=2018-07-01&$skiptoken=Asset+517
```

REST の他の例については、「[Assets - List (アセット - リスト)](https://docs.microsoft.com/rest/api/media/assets/list)」をご覧ください。

## <a name="content-key-policies"></a>コンテンツ キー ポリシー

### <a name="filteringordering"></a>フィルター処理/順序付け

次の表は、これらのオプションが[コンテンツ キー ポリシー](https://docs.microsoft.com/rest/api/media/contentkeypolicies)の各プロパティに対してどのように適用されるかを示しています。 

|Name|filter|順序|
|---|---|---|
|id|||
|name|eq、ne、ge、le、gt、lt|昇順および降順|
|properties.created |eq、ne、ge、le、gt、lt|昇順および降順|
|properties.description |eq、ne、ge、le、gt、lt||
|properties.lastModified|eq、ne、ge、le、gt、lt|昇順および降順|
|properties.options |||
|properties.policyId|eq、ne||
|type|||

### <a name="pagination"></a>改ページ位置の自動修正

改ページ位置の自動修正は、4 つの有効な各並べ替え順序でサポートされています。 現時点では、ページ サイズは 10 です。

次の C# の例は、アカウント内のすべての**コンテンツ キー ポリシー**を列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.ContentKeyPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.ContentKeyPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST の例については、[コンテンツ キー ポリシーの一覧](https://docs.microsoft.com/rest/api/media/contentkeypolicies/list)に関する記事を参照してください。

## <a name="jobs"></a>[ジョブ]

### <a name="filteringordering"></a>フィルター処理/順序付け

次の表は、これらのオプションを[ジョブ](https://docs.microsoft.com/rest/api/media/jobs) プロパティに適用する方法をまとめたものです。 

| Name    | filter                        | 順序 |
|---------|-------------------------------|-------|
| name                    | eq            | 昇順および降順|
| properties.state        | eq、ne        |                         |
| properties.created      | gt、ge、lt、le| 昇順および降順|
| properties.lastModified | gt、ge、lt、le | 昇順および降順| 

### <a name="pagination"></a>改ページ位置の自動修正

Media Services v3 では､Jobs の改ページ位置を変更できます｡

次の C# 例は、アカウント内のすべてのジョブを列挙する方法を示しています。

```csharp            
List<string> jobsToDelete = new List<string>();
var pageOfJobs = client.Jobs.List(config.ResourceGroup, config.AccountName, "Encode");

bool exit;
do
{
    foreach (Job j in pageOfJobs)
    {
        jobsToDelete.Add(j.Name);
    }

    if (pageOfJobs.NextPageLink != null)
    {
        pageOfJobs = client.Jobs.ListNext(pageOfJobs.NextPageLink);
        exit = false;
    }
    else
    {
        exit = true;
    }
}
while (!exit);

```

REST の例については､[Jobs - List](https://docs.microsoft.com/rest/api/media/jobs/list) を参照してください｡

## <a name="streaming-locators"></a>ストリーミング ロケーター

### <a name="filteringordering"></a>フィルター処理/順序付け

次の表は、これらのオプションを StreamingLocator プロパティに適用できる方法を示しています。 

|Name|filter|順序|
|---|---|---|
|id |||
|name|eq、ne、ge、le、gt、lt|昇順および降順|
|properties.alternativeMediaId  |||
|properties.assetName   |||
|properties.contentKeys |||
|properties.created |eq、ne、ge、le、gt、lt|昇順および降順|
|properties.defaultContentKeyPolicyName |||
|properties.endTime |eq、ne、ge、le、gt、lt|昇順および降順|
|properties.startTime   |||
|properties.streamingLocatorId  |||
|properties.streamingPolicyName |||
|type   |||

### <a name="pagination"></a>改ページ位置の自動修正

改ページ位置の自動修正は、4 つの有効な各並べ替え順序でサポートされています。 現時点では、ページ サイズは 10 です。

次の C# の例は、アカウント内のすべての StreamingLocator を列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.StreamingLocators.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingLocators.ListNextAsync(currentPage.NextPageLink);
}
```

REST の例については、[ストリーミング ロケーターの一覧](https://docs.microsoft.com/rest/api/media/streaminglocators/list)に関する記事を参照してください

## <a name="streaming-policies"></a>ストリーミング ポリシー

### <a name="filteringordering"></a>フィルター処理/順序付け

次の表は、これらのオプションを StreamingPolicy プロパティに適用できる方法を示しています。 

|Name|filter|順序|
|---|---|---|
|id|||
|name|eq、ne、ge、le、gt、lt|昇順および降順|
|properties.commonEncryptionCbcs|||
|properties.commonEncryptionCenc|||
|properties.created |eq、ne、ge、le、gt、lt|昇順および降順|
|properties.defaultContentKeyPolicyName |||
|properties.envelopeEncryption|||
|properties.noEncryption|||
|type|||

### <a name="pagination"></a>改ページ位置の自動修正

改ページ位置の自動修正は、4 つの有効な各並べ替え順序でサポートされています。 現時点では、ページ サイズは 10 です。

次の C# の例は、アカウント内のすべての StreamingPolicy を列挙する方法を示しています。

```csharp
var firstPage = await MediaServicesArmClient.StreamingPolicies.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.StreamingPolicies.ListNextAsync(currentPage.NextPageLink);
}
```

REST の例については、[ストリーミング ポリシーの一覧](https://docs.microsoft.com/rest/api/media/streamingpolicies/list)に関する記事を参照してください

## <a name="transform"></a>変換

### <a name="filteringordering"></a>フィルター処理/順序付け

次の表は、これらのオプションを[変換](https://docs.microsoft.com/rest/api/media/transforms)プロパティに適用する方法をまとめたものです。 

| Name    | filter                        | 順序 |
|---------|-------------------------------|-------|
| name                    | eq            | 昇順および降順|
| properties.created      | gt、ge、lt、le| 昇順および降順|
| properties.lastModified | gt、ge、lt、le | 昇順および降順|

## <a name="next-steps"></a>次の手順

[ファイルのストリーミング](stream-files-dotnet-quickstart.md)
