---
title: カスタム プロバイダー用の RESTful エンドポイントを作成する
description: このチュートリアルでは、カスタム プロバイダー用の RESTful エンドポイントを作成する方法について説明します。 サポートされている RESTful HTTP メソッドの要求と応答を処理する方法について詳しく説明します。
author: jjbfour
ms.service: managed-applications
ms.topic: tutorial
ms.date: 06/19/2019
ms.author: jobreen
ms.openlocfilehash: 176e3b02cbda7577e306d86363cfe5b41335fb6e
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/11/2019
ms.locfileid: "67799181"
---
# <a name="authoring-a-restful-endpoint-for-custom-providers"></a>カスタム プロバイダー用の RESTful エンドポイントを作成する

カスタム プロバイダーを使うと、Azure 上でワークフローをカスタマイズできます。 カスタム プロバイダーは、Azure と`endpoint`の間のコントラクトです。 このチュートリアルでは、カスタム プロバイダーの RESTful `endpoint`を作成する手順について説明します。 Azure カスタム プロバイダーについてよくご存じでない場合は、[カスタム リソース プロバイダーの概要](./custom-providers-overview.md)に関するページをご覧ください。

このチュートリアルは、次のステップに分かれています。

- カスタム アクションとカスタム リソースを使用する
- ストレージ内のカスタム リソースをパーティション分割する方法
- カスタム プロバイダーの RESTful メソッドをサポートする
- RESTful の操作を統合する

このチュートリアルは、次のチュートリアルが基になっています。

- [Azure カスタム プロバイダー用に Azure Functions を設定する](./tutorial-custom-providers-function-setup.md)

> [!NOTE]
> このチュートリアルは、前のチュートリアルが基になっています。 チュートリアルの一部のステップは、Azure 関数がカスタム プロバイダーを使うように設定されている場合にのみ機能します。

## <a name="working-with-custom-actions-and-custom-resources"></a>カスタム アクションとカスタム リソースを使用する

このチュートリアルでは、カスタム プロバイダーに対する RESTful エンドポイントとして動作するように関数を更新します。 Azure のリソースとアクションは、RESTful の基本的な仕様に従ってモデル化されています。PUT - 新しいリソースを作成します。GET (インスタンス) - 既存のリソースを取得します。DELETE - 既存のリソースを削除します。POST - アクションをトリガーします。GET (コレクション) - すべての既存リソースの一覧を取得します。 このチュートリアルではストレージとして Azure Table を使いますが、任意のデータベースまたはストレージ サービスを使用できます。

## <a name="how-to-partition-custom-resources-in-storage"></a>ストレージ内のカスタム リソースをパーティション分割する方法

RESTful サービスを作成するので、作成したリソースをストレージに格納する必要があります。 Azure Table Storage では、データのパーティションと行キーを生成する必要があります。 カスタム プロバイダーでは、データをカスタム プロバイダーにパーティション分割する必要があります。 受信要求がカスタム プロバイダーに送信されると、カスタム プロバイダーによって`endpoint`への送信要求に `x-ms-customproviders-requestpath` ヘッダーが追加されます。

カスタム リソースに対する `x-ms-customproviders-requestpath` ヘッダーのサンプル:

```
X-MS-CustomProviders-RequestPath: /subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/{myResourceType}/{myResourceName}
```

上記のサンプルの `x-ms-customproviders-requestpath` ヘッダーに基づいて、ストレージの partitionKey と rowKey を次のように作成できます。

パラメーター | Template | 説明
---|---
partitionKey | '{subscriptionId}:{resourceGroupName}:{resourceProviderName}' | partitionKey は、データをパーティション分割する方法です。 ほとんどの場合は、カスタム プロバイダーのインスタンスによってデータをパーティション分割する必要があります。
rowKey | '{myResourceType}:{myResourceName}' | rowKey は、データの個別の識別子です。 ほとんどの場合、これはリソースの名前です。

さらに、カスタム リソースをモデル化するための新しいクラスを作成する必要もあります。 このチュートリアルでは、`CustomResource` クラスを関数に追加します。これは、入力された任意のデータを受け付けるジェネリック クラスです。

```csharp
// Custom Resource Table Entity
public class CustomResource : TableEntity
{
    public string Data { get; set; }
}
```

これにより `TableEntity` に基づく基本クラスが作成されて、データの格納に使用されます。 `CustomResource` クラスは、`TableEntity` から 2 つのプロパティ partitionKey と rowKey を継承します。

## <a name="support-custom-provider-restful-methods"></a>カスタム プロバイダーの RESTful メソッドをサポートする

> [!NOTE]
> チュートリアルからコードを直接コピーしていない場合、応答の内容は有効な JSON であり、`Content-Type` ヘッダーは `application/json` に設定されます。

データのパーティション分割を設定したので、基本的な CRUD をスキャフォールディングし、カスタム リソースとカスタム アクションに対するメソッドをトリガーしてみましょう。 カスタム プロバイダーはプロキシとして動作するため、要求と応答を RESTful `endpoint`でモデル化して処理する必要があります。 基本的な RESTful 操作を処理するには、次のスニペットのようにします。

### <a name="trigger-custom-action"></a>カスタム アクションをトリガーする

カスタム プロバイダーの場合、カスタム アクションは `POST` 要求によってトリガーされます。 カスタム アクションでは、必要に応じて、入力パラメーターのセットが含まれる要求本文を受け付けることができます。 その後、アクションでは、アクションの結果を示す応答と、成功したか失敗したかを、返す必要があります。 このチュートリアルでは、`TriggerCustomAction` メソッドを関数に追加します。

```csharp
/// <summary>
/// Triggers a custom action with some side effect.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <returns>The http response result of the custom action.</returns>
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

`TriggerCustomAction` メソッドは、受信した要求を受け取って、応答と成功状態コードを単にエコーバックします。 

### <a name="create-custom-resource"></a>カスタム リソースを作成する

カスタム プロバイダーの場合、カスタム リソースは `PUT` 要求によって作成されます。 カスタム プロバイダーでは、カスタム リソースのプロパティのセットが含まれる JSON 要求の本文が受け付けられます。 Azure では、リソースは RESTful のモデルに従います。 リソースの作成に使われたのと同じ要求 URL で、リソースの取得と削除も行うことができます。 このチュートリアルでは、新しいリソースを作成するために `CreateCustomResource` メソッドを追加します。

```csharp
/// <summary>
/// Creates a custom resource and saves it to table storage.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="azureResourceId">The parsed Azure resource Id.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the created custom resource.</returns>
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

`CreateCustomResource` メソッドでは、Azure 固有のフィールド `id`、`name`、`type` を含むように、受信した要求を更新します。 これらは、Azure 全体のサービスによって使われる最上位レベルのプロパティです。 これらにより、カスタム プロバイダーでは、Azure Policy、Azure Resource Manager テンプレート、Azure アクティビティ ログなどの他のサービスと統合できるようになります。

プロパティ | サンプル | 説明
---|---|---
名前 | '{myCustomResourceName}' | カスタム リソースの名前。
type | 'Microsoft.CustomProviders/resourceProviders/{resourceTypeName}' | リソースの種類の名前空間。
id | '/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/<br>providers/Microsoft.CustomProviders/resourceProviders/{resourceProviderName}/<br>{resourceTypeName}/{myCustomResourceName}' | リソース ID。

プロパティを追加するだけでなく、Azure Table Storage へのドキュメントの保存も行います。 

### <a name="retrieve-custom-resource"></a>カスタム リソースを取得する

カスタム プロバイダーの場合、カスタム リソースは `GET` 要求によって取得されます。 カスタム プロバイダーでは、JSON 要求の本文は受け入れられ "*ません*"。 `GET` 要求の場合、**エンドポイント**では、`x-ms-customproviders-requestpath` ヘッダーを使って作成済みのリソースを返す必要があります。 このチュートリアルでは、既存のリソースを取得するために `RetrieveCustomResource` メソッドを追加します。

```csharp
/// <summary>
/// Retrieves a custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the existing custom resource.</returns>
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

Azure では、リソースは RESTful のモデルに従う必要があります。 `GET` 要求を実行する場合、リソースを作成した要求 URL で、リソースの取得も行う必要があります。

### <a name="remove-custom-resource"></a>カスタム リソースを削除する

カスタム プロバイダーの場合、カスタム リソースは `DELETE` 要求によって削除されます。 カスタム プロバイダーでは、JSON 要求の本文は受け入れられ "*ません*"。 `DELETE` 要求の場合、**エンドポイント**では、`x-ms-customproviders-requestpath` ヘッダーを使って作成済みのリソースを削除する必要があります。 このチュートリアルでは、既存のリソースを削除するために `RemoveCustomResource` メソッドを追加します。

```csharp
/// <summary>
/// Removes an existing custom resource.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="rowKey">The row key for storage. This is '{resourceType}:{customResourceName}'.</param>
/// <returns>The http response containing the result of the delete.</returns>
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

Azure では、リソースは RESTful のモデルに従う必要があります。 `DELETE` 要求を実行する場合、リソースを作成した要求 URL で、リソースの削除も行う必要があります。

### <a name="list-all-custom-resources"></a>すべてのカスタム リソースの一覧を取得する

カスタム プロバイダーの場合、既存のカスタム リソースの一覧はコレクション `GET` 要求で列挙できます。 カスタム プロバイダーでは、JSON 要求の本文は受け入れられ "*ません*"。 コレクション `GET` 要求の場合、`endpoint`では、`x-ms-customproviders-requestpath` ヘッダーを使って作成済みのリソースを列挙する必要があります。 このチュートリアルでは、既存のリソースを列挙するために `EnumerateAllCustomResources` メソッドを追加します。

```csharp
/// <summary>
/// Enumerates all the stored custom resources for a given type.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <param name="partitionKey">The partition key for storage. This is the custom provider id.</param>
/// <param name="resourceType">The resource type of the enumeration.</param>
/// <returns>The http response containing a list of resources stored under 'value'.</returns>
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
> 行キーの GreaterThan および LessThan は、文字列に対するクエリ "startswith" を実行するための Azure Table の構文です。 

既存のすべてのリソースの一覧を取得するため、カスタム プロバイダーのパーティションに確実にリソースが存在する Azure Table クエリを生成します。 クエリでは、行キーが同じ `{myResourceType}` で始まっていることをチェックします。

## <a name="integrate-restful-operations"></a>RESTful の操作を統合する

すべての RESTful メソッドを関数に追加した後は、関数を呼び出して異なる REST 要求を処理するように、メインの `Run` メソッドを更新できます。

```csharp
/// <summary>
/// Entry point for the Azure Function webhook and acts as the service behind a custom provider.
/// </summary>
/// <param name="requestMessage">The http request message.</param>
/// <param name="log">The logger.</param>
/// <param name="tableStorage">The Azure Storage Account table.</param>
/// <returns>The http response for the custom Azure API.</returns>
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
        // Action request for an custom action.
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

更新された `Run` メソッドには、Azure Table Storage に対して追加された `tableStorage` 入力バインドが含まれるようになります。 メソッドの最初の部分では、`x-ms-customproviders-requestpath` ヘッダーを読み取り、`Microsoft.Azure.Management.ResourceManager.Fluent` ライブラリを使って値をリソース ID として解析します。 `x-ms-customproviders-requestpath` ヘッダーはカスタム プロバイダーによって送信され、ヘッダーでは受信した要求のパスが指定されています。 解析されたリソース ID を使って、カスタム リソースを参照または格納するための、データに対する partitionKey と rowKey を生成できます。

メソッドとクラスの追加に加えて、関数の using メソッドを更新する必要があります。 ファイルの先頭に以下を追加します。

```csharp
#r "Newtonsoft.Json"
#r "Microsoft.WindowsAzure.Storage"
#r "../bin/Microsoft.Azure.Management.ResourceManager.Fluent.dll"

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

このチュートリアルでの作業が途中でわからなくなった場合は、完成したコード サンプルを「[Custom provider C# RESTful endpoint reference (カスタム プロバイダーの C# RESTful エンドポイント リファレンス)](./reference-custom-providers-csharp-endpoint.md)」で確認できます。 関数が完成したら、後のチュートリアルで使うので、関数をトリガーするために使用できる関数の URL を保存します。

## <a name="next-steps"></a>次の手順

この記事では、Azure のカスタム プロバイダーの`endpoint`を使用するための RESTful エンドポイントを作成しました。 次の記事に進んで、カスタム プロバイダーの作成方法を学習してください。

- [チュートリアル:カスタム プロバイダーを作成する](./tutorial-custom-providers-create.md)
