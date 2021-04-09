---
title: RESTful エンドポイントを作成する
description: このチュートリアルでは、カスタム プロバイダー用の RESTful エンドポイントを作成する方法について説明します。 サポートされている RESTful HTTP メソッドの要求と応答を処理する方法について詳しく説明します。
author: jjbfour
ms.topic: tutorial
ms.date: 01/13/2021
ms.author: jobreen
ms.openlocfilehash: 54d0df287865d5d92403bf68227a2d4c5faa8bb4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98200211"
---
# <a name="author-a-restful-endpoint-for-custom-providers"></a>カスタム プロバイダー用の RESTful エンドポイントを作成する

カスタム プロバイダーは、Azure とエンドポイントの間のコントラクトです。 カスタム プロバイダーを使うと、Azure 上のワークフローをカスタマイズできます。 このチュートリアルでは、カスタム プロバイダーの RESTful エンドポイントを作成する方法について説明します。 Azure カスタム プロバイダーについてなじみがない場合は、[カスタム リソースプロバイダーの概要](overview.md)に関するページを参照してください。

> [!NOTE]
> このチュートリアルは、「[Azure カスタム プロバイダー用に Azure Functions を設定する](./tutorial-custom-providers-function-setup.md)」のチュートリアルに基づいて作成されています。 このチュートリアルの手順の一部は、Azure 関数アプリがカスタム プロバイダーと連携するよう設定されている場合にのみ機能します。

## <a name="work-with-custom-actions-and-custom-resources"></a>カスタム アクションとカスタム リソースを使用する

このチュートリアルでは、カスタム プロバイダーの RESTful エンドポイントとして動作するように関数アプリを更新します。 Azure のリソースとアクションは、次の RESTful の基本的な仕様に従ってモデル化されています。

- **PUT**: 新しいリソースを作成する
- **GET (インスタンス)** : 既存のリソースを取得する
- **DELETE**: 既存のリソースを削除する
- **POST**: アクションをトリガーする
- **GET (コレクション)** : 既存のリソースをすべてリストする

 このチュートリアルでは、Azure Table Storage を使用します。 ただし、任意のデータベースまたはストレージ サービスも利用できます。

## <a name="partition-custom-resources-in-storage"></a>カスタム リソースをパーティション分割してストレージに格納する

RESTful サービスを作成しているため、作成されたリソースを格納する必要があります。 Azure Table Storage では、データのパーティションと行キーを生成する必要があります。 カスタム プロバイダーでは、データをカスタム プロバイダーにパーティション分割する必要があります。 受信要求がカスタム プロバイダーに送信されると、カスタム プロバイダーによってエンドポイントへの送信要求に `x-ms-customproviders-requestpath` ヘッダーが追加されます。

次の例では、カスタム リソースの `x-ms-customproviders-requestpath` ヘッダーを示します。

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

この例の `x-ms-customproviders-requestpath` ヘッダーに基づいて、次の表に示すように、ストレージの *partitionKey* パラメーターと *rowKey* パラメーターを作成できます。

パラメーター | Template | 説明
---|---|---
*partitionKey* | `{subscriptionId}:{resourceGroupName}:{resourceProviderName}` | *partitionKey* パラメーターは、データをどのようにパーティション分割するかを指定します。 通常、データは、カスタム プロバイダーのインスタンスごとにパーティション分割されます。
*rowKey* | `{myResourceType}:{myResourceName}` | *rowKey* パラメーターは、データに個々の識別子を指定します。 通常、識別子はリソースの名前です。

さらに、カスタム リソースをモデル化するための新しいクラスを作成する必要もあります。 このチュートリアルでは、関数アプリに次の **CustomResource** クラスを追加します。

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```
**CustomResource** は、入力データを受け取るシンプルなジェネリック クラスです。 そのベースは **TableEntity** で、データの格納に使用されます。 **CustomResource** クラスは、**TableEntity** から 2 つのプロパティ **partitionKey** と **rowKey** を継承します。

## <a name="support-custom-provider-restful-methods"></a>カスタム プロバイダーの RESTful メソッドをサポートする

> [!NOTE]
> このチュートリアルからコードを直接コピーしていない場合、応答の内容は、`Content-Type` ヘッダーを `application/json` に設定する有効な JSON であることが必要です。

データのパーティション分割を設定したので、基本的な CRUD を作成し、カスタム リソースとカスタム アクションに対するメソッドをトリガーします。 カスタム プロバイダーはプロキシとして機能するため、RESTful エンドポイントは、要求と応答をモデル化して処理する必要があります。 以下のコード スニペットでは、基本的な RESTful 操作の処理方法を示しています。

### <a name="trigger-a-custom-action"></a>カスタム アクションのトリガー

カスタム プロバイダーでは、カスタム アクションが POST 要求を通じてトリガーされます。 カスタム アクションでは、必要に応じて、入力パラメーターのセットが含まれる要求本文を受け付けることができます。 その後、このアクションは、アクションの結果を伝える応答と共に、成功したか失敗したかを返します。

次の **TriggerCustomAction** メソッドを関数アプリに追加します。

```csharp
/// <summary>
/// Triggers a custom action with some side effects.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <returns>The HTTP response result of the custom action.</returns>
public static async Task<HttpResponseMessage> TriggerCustomAction(HttpRequestMessage requestMessage)
{
    var myCustomActionRequest = await requestMessage.Content.ReadAsStringAsync();

    var actionResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    actionResponse.Content = myCustomActionRequest != string.Empty ? 
        new StringContent(JObject.Parse(myCustomActionRequest).ToString(), System.Text.Encoding.UTF8, "application/json") :
        null;
    return actionResponse;
}
```

**TriggerCustomAction** メソッドは、受信した要求を受け取って、状態コードと共に応答をエコーバックするだけです。

### <a name="create-a-custom-resource"></a>カスタム リソースの作成

カスタム プロバイダーでは、カスタム リソースが PUT 要求を通じて作成されます。 カスタム プロバイダーは、カスタム リソースの一連のプロパティを含んだ JSON 要求本文を受け取ります。 Azure のリソースは RESTful のモデルに従います。 リソースの作成、取得、削除には、同じ要求 URL を使用できます。

新しいリソースを作成するには、次の **CreateCustomResource** メソッドを追加します。

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="azureResourceId">The parsed Azure resource ID.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the created custom resource.</returns>
public static async Task<HttpResponseMessage> CreateCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, ResourceId azureResourceId, string partitionKey, string rowKey)
{
    // Adds the Azure top-level properties.
    var myCustomResource = JObject.Parse(await requestMessage.Content.ReadAsStringAsync());
    myCustomResource["name"] = azureResourceId.Name;
    myCustomResource["type"] = azureResourceId.FullResourceType;
    myCustomResource["id"] = azureResourceId.Id;

    // Save the resource into storage.
    var insertOperation = TableOperation.InsertOrReplace(
        new CustomResource
        {
            PartitionKey = partitionKey,
            RowKey = rowKey,
            Data = myCustomResource.ToString(),
        });
    await tableStorage.ExecuteAsync(insertOperation);

    var createResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    createResponse.Content = new StringContent(myCustomResource.ToString(), System.Text.Encoding.UTF8, "application/json");
    return createResponse;
}
```

**CreateCustomResource** メソッドは、受信要求を更新して、Azure 固有のフィールドである **id**、**name**、**type** を追加します。 これらのフィールドは、Azure 全体のサービスによって使われる最上位レベルのプロパティです。 カスタム プロバイダーは、これらを通じて、Azure Policy、Azure Resource Manager テンプレート、Azure アクティビティ ログなど、他のサービスと相互運用できるようになります。

プロパティ | 例 | 説明
---|---|---
**name** | {myCustomResourceName} | カスタム リソースの名前
**type** | Microsoft.CustomProviders/resourceProviders/{resourceTypeName} | リソースの種類の名前空間
**ID** | /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName} | リソース ID

プロパティを追加するだけでなく、Azure Table Storage へのドキュメントの保存も行いました。

### <a name="retrieve-a-custom-resource"></a>カスタム リソースの取得

カスタム プロバイダーでは、カスタム リソースが GET 要求を通じて取得されます。 カスタム プロバイダーは、JSON 要求本文を受け取り "*ません*"。 GET 要求に対し、エンドポイントが `x-ms-customproviders-requestpath` ヘッダーを使って、作成済みのリソースを返します。

既存のリソースを取得するには、次の **RetrieveCustomResource** メソッドを追加します。

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the existing custom resource.</returns>
public static async Task<HttpResponseMessage> RetrieveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    var retrieveResponse = requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NotFound);

    retrieveResponse.Content = existingCustomResource != null ?
            new StringContent(existingCustomResource.Data, System.Text.Encoding.UTF8, "application/json"):
            null;
    return retrieveResponse;
}
```

Azure では、リソースは RESTful のモデルに従います。 GET 要求を実行する場合、リソースを作成する要求 URL でもリソースが返されます。

### <a name="remove-a-custom-resource"></a>カスタム リソースの削除

カスタム プロバイダーでは、カスタム リソースが DELETE 要求を通じて削除されます。 カスタム プロバイダーは、JSON 要求本文を受け取り "*ません*"。 DELETE 要求に対し、エンドポイントが `x-ms-customproviders-requestpath` ヘッダーを使って、作成済みのリソースを削除します。

既存のリソースを削除するには、次の **RemoveCustomResource** メソッドを追加します。

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure storage account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The HTTP response containing the result of the deletion.</returns>
public static async Task<HttpResponseMessage> RemoveCustomResource(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string rowKey)
{
    // Attempt to retrieve the Existing Stored Value
    var tableQuery = TableOperation.Retrieve<CustomResource>(partitionKey, rowKey);
    var existingCustomResource = (CustomResource)(await tableStorage.ExecuteAsync(tableQuery)).Result;

    if (existingCustomResource != null) {
        var deleteOperation = TableOperation.Delete(existingCustomResource);
        await tableStorage.ExecuteAsync(deleteOperation);
    }

    return requestMessage.CreateResponse(
        existingCustomResource != null ? HttpStatusCode.OK : HttpStatusCode.NoContent);
}
```

Azure では、リソースは RESTful のモデルに従います。 DELETE 要求を実行する場合、リソースを作成する要求 URL でもリソースが削除されます。

### <a name="list-all-custom-resources"></a>すべてのカスタム リソースの一覧を取得する

カスタム プロバイダーでは、既存のカスタム リソースの一覧をコレクションの GET 要求を使用して列挙できます。 カスタム プロバイダーは、JSON 要求本文を受け取り "*ません*"。 GET 要求のコレクションでは、エンドポイントが `x-ms-customproviders-requestpath` ヘッダーを使って、作成済みのリソースを列挙する必要があります。

既存のリソースを列挙するには、次の **EnumerateAllCustomResources** メソッドを追加します。

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider ID.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The HTTP response containing a list of resources stored under 'value'.</returns>
public static async Task<HttpResponseMessage> EnumerateAllCustomResources(HttpRequestMessage requestMessage, CloudTable tableStorage, string partitionKey, string resourceType)
{
    // Generate upper bound of the query.
    var rowKeyUpperBound = new StringBuilder(resourceType);
    rowKeyUpperBound[rowKeyUpperBound.Length - 1]++;

    // Create the enumeration query.
    var enumerationQuery = new TableQuery<CustomResource>().Where(
        TableQuery.CombineFilters(
            TableQuery.GenerateFilterCondition("PartitionKey", QueryComparisons.Equal, partitionKey),
            TableOperators.And,
            TableQuery.CombineFilters(
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.GreaterThan, resourceType),
                TableOperators.And,
                TableQuery.GenerateFilterCondition("RowKey", QueryComparisons.LessThan, rowKeyUpperBound.ToString()))));
    
    var customResources = (await tableStorage.ExecuteQuerySegmentedAsync(enumerationQuery, null))
        .ToList().Select(customResource => JToken.Parse(customResource.Data));

    var enumerationResponse = requestMessage.CreateResponse(HttpStatusCode.OK);
    enumerationResponse.Content = new StringContent(new JObject(new JProperty("value", customResources)).ToString(), System.Text.Encoding.UTF8, "application/json");
    return enumerationResponse;
}
```

> [!NOTE]
> RowKey QueryComparisons.GreaterThan と QueryComparisons.LessThan は、文字列の "startswith" クエリを実行するための Azure Table Storage の構文です。

既存のリソースすべてを一覧表示するには、カスタム プロバイダーのパーティションにリソースが存在することを保証する Azure Table Storage クエリを生成します。 その後、このクエリは、行キーが同じ `{myResourceType}` 値で始まっていることをチェックします。

## <a name="integrate-restful-operations"></a>RESTful の操作を統合する

すべての RESTful メソッドを関数アプリに追加した後は、関数を呼び出して各種の REST 要求を処理するメインの **Run** メソッドを更新します。

```csharp
/// <summary>
/// Entry point for the Azure function app webhook that acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The HTTP request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Table storage account.</param>
/// <returns>The HTTP response for the custom Azure API.</returns>
public static async Task<HttpResponseMessage> Run(HttpRequestMessage req, ILogger log, CloudTable tableStorage)
{
    // Get the unique Azure request path from request headers.
    var requestPath = req.Headers.GetValues("x-ms-customproviders-requestpath").FirstOrDefault();

    if (requestPath == null)
    {
        var missingHeaderResponse = req.CreateResponse(HttpStatusCode.BadRequest);
        missingHeaderResponse.Content = new StringContent(
            new JObject(new JProperty("error", "missing 'x-ms-customproviders-requestpath' header")).ToString(),
            System.Text.Encoding.UTF8, 
            "application/json");
    }

    log.LogInformation($"The Custom Provider Function received a request '{req.Method}' for resource '{requestPath}'.");

    // Determines if it is a collection level call or action.
    var isResourceRequest = requestPath.Split('/').Length % 2 == 1;
    var azureResourceId = isResourceRequest ? 
        ResourceId.FromString(requestPath) :
        ResourceId.FromString($"{requestPath}/");

    // Create the Partition Key and Row Key
    var partitionKey = $"{azureResourceId.SubscriptionId}:{azureResourceId.ResourceGroupName}:{azureResourceId.Parent.Name}";
    var rowKey = $"{azureResourceId.FullResourceType.Replace('/', ':')}:{azureResourceId.Name}";

    switch (req.Method)
    {
        // Action request for a custom action.
        case HttpMethod m when m == HttpMethod.Post && !isResourceRequest:
            return await TriggerCustomAction(
                requestMessage: req);

        // Enumerate request for all custom resources.
        case HttpMethod m when m == HttpMethod.Get && !isResourceRequest:
            return await EnumerateAllCustomResources(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                resourceType: rowKey);

        // Retrieve request for a custom resource.
        case HttpMethod m when m == HttpMethod.Get && isResourceRequest:
            return await RetrieveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Create request for a custom resource.
        case HttpMethod m when m == HttpMethod.Put && isResourceRequest:
            return await CreateCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                azureResourceId: azureResourceId,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Remove request for a custom resource.
        case HttpMethod m when m == HttpMethod.Delete && isResourceRequest:
            return await RemoveCustomResource(
                requestMessage: req,
                tableStorage: tableStorage,
                partitionKey: partitionKey,
                rowKey: rowKey);

        // Invalid request received.
        default:
            return req.CreateResponse(HttpStatusCode.BadRequest);
    }
}
```

更新された **Run** メソッドには、Azure Table Storage のために追加した *tableStorage* 入力バインドが含まれます。 メソッドの最初の部分では、`x-ms-customproviders-requestpath` ヘッダーを読み取り、`Microsoft.Azure.Management.ResourceManager.Fluent` ライブラリを使って値をリソース ID として解析します。 `x-ms-customproviders-requestpath` ヘッダーはカスタム プロバイダーによって送信され、受信した要求のパスを指定します。

解析されたリソース ID を使って、カスタム リソースを参照または格納するための、データに対する **partitionKey** 値と **rowKey** 値を生成できます。

メソッドとクラスを追加したら、関数アプリの **using** メソッドを更新する必要があります。 C# ファイルの先頭に次のコードを追加します。

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent"

using System;
using System.Net;
using System.Net.Http;
using System.Net.Http.Headers;
using System.Configuration;
using System.Text;
using System.Threading;
using System.Globalization;
using System.Collections.Generic;
using Microsoft.Azure.WebJobs;
using Microsoft.Azure.WebJobs.Host;
using Microsoft.WindowsAzure.Storage.Table;
using Microsoft.Azure.Management.ResourceManager.Fluent.Core;
using Newtonsoft.Json;
using Newtonsoft.Json.Linq;
```

このチュートリアルでの作業が途中でわからなくなった場合は、完成したコード サンプルを「[カスタム プロバイダーの C# RESTful エンドポイント リファレンス](./reference-custom-providers-csharp-endpoint.md)」で確認できます。 関数アプリが完成したら、その URL を保存してください。 後続のチュートリアルでこの関数アプリをトリガーする際に使用できます。

## <a name="next-steps"></a>次のステップ

この記事では、Azure のカスタム プロバイダーのエンドポイントで動作する RESTful エンドポイントを作成しました。 カスタム プロバイダーの作成方法については、[カスタム プロバイダーの作成のチュートリアル](./tutorial-custom-providers-create.md)に関する記事をご覧ください。
