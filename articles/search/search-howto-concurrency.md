---
title: リソースへの同時書き込みを管理する方法
titleSuffix: Azure Cognitive Search
description: オプティミスティック コンカレンシーを使用して、Azure Cognitive Search インデックス、インデクサー、データ ソースの更新または削除の競合を回避します。
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/26/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 52260facc1a458a3ef18b067e6d52e189f5d329c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98881756"
---
# <a name="how-to-manage-concurrency-in-azure-cognitive-search"></a>Azure Cognitive Search でコンカレンシーを管理する方法

インデックスやデータ ソースなどの Azure Cognitive Search リソースを管理するときは、リソースを安全に更新することが重要であり、アプリケーションの異なるコンポーネントによってリソースが同時にアクセスされる場合は特にそうです。 2 つのクライアントが調整なしでリソースを同時に更新すると、競合状態になる可能性があります。 これを回避するために、Azure Cognitive Search では *オプティミスティック同時実行制御モデル* を提供します。 リソースのロックはありません。 代わりに、偶発的な上書きを避ける要求を作成できるよう、リソースのバージョンを識別する ETag がすべてのリソースに存在します。

> [!Tip]
> [サンプル C# ソリューション](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)の概念コードは、Azure Cognitive Search でコンカレンシー制御がどのように機能するかを説明します。 コードでは、コンカレンシー制御を呼び出す条件を作成します。 [次のコード フラグメント](#samplecode)に目を通すだけでもほとんどの開発者にとっては十分と思われますが、コードを実行したい場合は、appsettings.json を編集してサービス名と管理者 API キーを追加します。 サービス URL を `http://myservice.search.windows.net` とした場合、サービス名は `myservice` です。

## <a name="how-it-works"></a>しくみ

オプティミスティック コンカレンシーは、インデックス、インデクサー、データ ソース、スキルセット、および synonymMap リソースに書き込む API 呼び出しでのアクセス条件チェックによって実装されます。

すべてのリソースには、オブジェクトのバージョン情報を提供する [*エンティティ タグ (ETag)*](https://en.wikipedia.org/wiki/HTTP_ETag) があります。 最初に ETag をチェックして、リソースの ETag がローカル コピーと一致することを確認することにより、典型的なワークフロー (取得、ローカル変更、更新) における同時更新を回避できます。

+ REST API では、要求ヘッダーで [ETag](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search) を使用します。

+ .NET SDK では、accessCondition オブジェクトを通じて ETag を設定し、リソースの [If-Match | If-Match-None ヘッダー](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)を設定します。 ETag を使用するオブジェクト ([SynonymMap.ETag](/dotnet/api/azure.search.documents.indexes.models.synonymmap.etag) や [SearchIndex.ETag](/dotnet/api/azure.search.documents.indexes.models.searchindex.etag) など) には、accessCondition オブジェクトがあります。

リソースを更新するたびに、その ETag が自動的に変化します。 コンカレンシー管理を実装するときに行うのは、リモート リソースの ETag が、クライアントで変更したリソースのコピーの ETag と同じであることを要求する前提条件を更新要求に課すことだけです。 同時実行プロセスがリモート リソースを既に変更している場合、ETag は前提条件に一致せず、要求は HTTP 412 で失敗します。 .NET SDK を使用している場合、これは (`IsAccessConditionFailed()` 拡張メソッドが true を返す) `CloudException` として明示されます。

> [!Note]
> コンカレンシーのメカニズムは 1 つしかありません。 どの API がリソース更新に使用されるかによらず、常にそのメカニズムが使用されます。

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>ユース ケースとサンプル コード

次のコードでは、キーの更新操作に対する accessCondition チェックの例を示します。

+ リソースがもう存在しない場合、更新は失敗します
+ リソースのバージョンが変わると、更新は失敗します

### <a name="sample-code-from-dotnetetagsexplainer-program"></a>[DotNetETagsExplainer プログラム](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)からのサンプル コード

```csharp
class Program
{
    // This sample shows how ETags work by performing conditional updates and deletes
    // on an Azure Cognitive Search index.
    static void Main(string[] args)
    {
        IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
        IConfigurationRoot configuration = builder.Build();

        SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

        Console.WriteLine("Deleting index...\n");
        DeleteTestIndexIfExists(serviceClient);

        // Every top-level resource in Azure Cognitive Search has an associated ETag that keeps track of which version
        // of the resource you're working on. When you first create a resource such as an index, its ETag is
        // empty.
        Index index = DefineTestIndex();
        Console.WriteLine(
            $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

        // Once the resource exists in Azure Cognitive Search, its ETag will be populated. Make sure to use the object
        // returned by the SearchServiceClient! Otherwise, you will still have the old object with the
        // blank ETag.
        Console.WriteLine("Creating index...\n");
        index = serviceClient.Indexes.Create(index);
        
        Console.WriteLine($"Test index created; Its ETag should be populated. ETag: '{index.ETag}'");

        // ETags let you do some useful things you couldn't do otherwise. For example, by using an If-Match
        // condition, we can update an index using CreateOrUpdate and be guaranteed that the update will only
        // succeed if the index already exists.
        index.Fields.Add(new Field("name", AnalyzerName.EnMicrosoft));
        index =
            serviceClient.Indexes.CreateOrUpdate(
                index,
                accessCondition: AccessCondition.GenerateIfExistsCondition());

        Console.WriteLine(
            $"Test index updated; Its ETag should have changed since it was created. ETag: '{index.ETag}'");

        // More importantly, ETags protect you from concurrent updates to the same resource. If another
        // client tries to update the resource, it will fail as long as all clients are using the right
        // access conditions.
        Index indexForClient1 = index;
        Index indexForClient2 = serviceClient.Indexes.Get("test");

        Console.WriteLine("Simulating concurrent update. To start, both clients see the same ETag.");
        Console.WriteLine($"Client 1 ETag: '{indexForClient1.ETag}' Client 2 ETag: '{indexForClient2.ETag}'");

        // Client 1 successfully updates the index.
        indexForClient1.Fields.Add(new Field("a", DataType.Int32));
        indexForClient1 =
            serviceClient.Indexes.CreateOrUpdate(
                indexForClient1,
                accessCondition: AccessCondition.IfNotChanged(indexForClient1));

        Console.WriteLine($"Test index updated by client 1; ETag: '{indexForClient1.ETag}'");

        // Client 2 tries to update the index, but fails, thanks to the ETag check.
        try
        {
            indexForClient2.Fields.Add(new Field("b", DataType.Boolean));
            serviceClient.Indexes.CreateOrUpdate(
                indexForClient2,
                accessCondition: AccessCondition.IfNotChanged(indexForClient2));

            Console.WriteLine("Whoops; This shouldn't happen");
            Environment.Exit(1);
        }
        catch (CloudException e) when (e.IsAccessConditionFailed())
        {
            Console.WriteLine("Client 2 failed to update the index, as expected.");
        }

        // You can also use access conditions with Delete operations. For example, you can implement an
        // atomic version of the DeleteTestIndexIfExists method from this sample like this:
        Console.WriteLine("Deleting index...\n");
        serviceClient.Indexes.Delete("test", accessCondition: AccessCondition.GenerateIfExistsCondition());

        // This is slightly better than using the Exists method since it makes only one round trip to
        // Azure Cognitive Search instead of potentially two. It also avoids an extra Delete request in cases where
        // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
        // Azure Cognitive Search is idempotent.

        // And we're done! Bye!
        Console.WriteLine("Complete.  Press any key to end application...\n");
        Console.ReadKey();
    }

    private static SearchServiceClient CreateSearchServiceClient(IConfigurationRoot configuration)
    {
        string searchServiceName = configuration["SearchServiceName"];
        string adminApiKey = configuration["SearchServiceAdminApiKey"];

        SearchServiceClient serviceClient =
            new SearchServiceClient(searchServiceName, new SearchCredentials(adminApiKey));
        return serviceClient;
    }

    private static void DeleteTestIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("test"))
        {
            serviceClient.Indexes.Delete("test");
        }
    }

    private static Index DefineTestIndex() =>
        new Index()
        {
            Name = "test",
            Fields = new[] { new Field("id", DataType.String) { IsKey = true } }
        };
    }
}
```

## <a name="design-pattern"></a>設計パターン

オプティミスティック コンカレンシーを実装するための設計パターンには、アクセス条件チェック、アクセス条件のテストを再試行し、変更の再適用を試みる前に更新済みリソースを必要に応じて取得するループを含めるようにします。

このコード スニペットでは、既に存在するインデックスに synonymMap を追加する例を示します。 このコードは [Azure Cognitive Search のシノニム C# の例](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/v10/DotNetHowToSynonyms)からの引用です。

スニペットでは "hotels" インデックスを取得し、更新操作時にオブジェクトのバージョンをチェックし、条件に適合しない場合は例外をスローした後、操作を (最大 3 回) 再試行します。この際、最新バージョンを取得するために、まずサーバーからインデックスを取得します。

```csharp
private static void EnableSynonymsInHotelsIndexSafely(SearchServiceClient serviceClient)
{
    int MaxNumTries = 3;

    for (int i = 0; i < MaxNumTries; ++i)
    {
        try
        {
            Index index = serviceClient.Indexes.Get("hotels");
            index = AddSynonymMapsToFields(index);

            // The IfNotChanged condition ensures that the index is updated only if the ETags match.
            serviceClient.Indexes.CreateOrUpdate(index, accessCondition: AccessCondition.IfNotChanged(index));

            Console.WriteLine("Updated the index successfully.\n");
            break;
        }
        catch (CloudException e) when (e.IsAccessConditionFailed())
        {
            Console.WriteLine($"Index update failed : {e.Message}. Attempt({i}/{MaxNumTries}).\n");
        }
    }
}

private static Index AddSynonymMapsToFields(Index index)
{
    index.Fields.First(f => f.Name == "category").SynonymMaps = new[] { "desc-synonymmap" };
    index.Fields.First(f => f.Name == "tags").SynonymMaps = new[] { "desc-synonymmap" };
    return index;
}
```

## <a name="next-steps"></a>次のステップ

他のサンプルを変更して、ETag または AccessCondition オブジェクトを試してみてください。

+ [GitHub 上の search-dotnet-getting-started](https://github.com/Azure-Samples/search-dotnet-getting-started)。 このリポジトリには、"DotNetEtagsExplainer" プロジェクトが含まれています。

+ [GitHub 上の azure-search-dotnet-samples](https://github.com/Azure-Samples/azure-search-dotnet-samples) には、追加の C# のサンプルが含まれています。

## <a name="see-also"></a>関連項目

+ [一般的な HTTP 要求ヘッダーと応答ヘッダー](/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)
+ [HTTP 状態コード](/rest/api/searchservice/http-status-codes)
+ [インデックス操作 (REST API)](/rest/api/searchservice/index-operations)