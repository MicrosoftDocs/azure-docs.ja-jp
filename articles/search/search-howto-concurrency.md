---
title: "Azure Search でリソースへの書き込みを同時実行を管理する方法"
description: "更新または削除するには Azure Search のインデックス、インデクサー、データ ソースに空中衝突を回避するのにには、オプティミスティック同時実行制御を使用します。"
services: search
documentationcenter: 
author: HeidiSteen
manager: jhubbard
editor: 
tags: azure-portal
ms.assetid: 
ms.service: search
ms.devlang: 
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/21/2017
ms.author: heidist
ms.openlocfilehash: aee1b7376d4829e3e2f5a232525e3c3cb4df9d8e
ms.sourcegitcommit: 422efcbac5b6b68295064bd545132fcc98349d01
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/29/2017
---
# <a name="how-to-manage-concurrency-in-azure-search"></a>Azure Search で、同時実行を管理する方法

インデックスおよびデータ ソースなどの Azure Search のリソースを管理するには、ときにすることが重要リソースを安全に、更新リソースは、アプリケーションのさまざまなコンポーネントで同時にアクセスする場合に特にです。 2 つのクライアントは、同時に調整なしのリソースを更新するときに競合状態が可能です。 これを回避するには、Azure Search が用意されています、*オプティミスティック同時実行制御モデル*です。 リソースのロックはありません。 代わりに、ETag が回避偶発的な要求を作成できるように、リソースのバージョンを識別するすべてのリソースが上書きされます。

> [!Tip]
> 内の概念的なコード、[サンプル c# ソリューション](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)Azure Search での同時実行制御のしくみについて説明します。 コードでは、同時実行制御を呼び出すための条件を作成します。 読み取り、[次のコード片](#samplecode)を実行する場合は、ほとんどの開発者サービス名と管理 api キーを追加するされる appsettings.json の編集のための十分な可能性があります。 サービスの URL を指定された`http://myservice.search.windows.net`、サービス名は`myservice`します。

## <a name="how-it-works"></a>動作のしくみ

オプティミスティック同時実行制御を実装を介してアクセス条件を確認インデックス、インデクサー、データ ソース、および synonymMap リソースへの書き込み API 呼び出しで。 

すべてのリソースが、 [*エンティティ タグ (ETag)* ](https://en.wikipedia.org/wiki/HTTP_ETag)オブジェクトのバージョン情報を提供します。 ETag を最初に確認するには、一般的なワークフローで同時に更新を回避できます (get、変更をローカルで、更新)、リソースの ETag と一致するローカル コピーを確保します。 

+ REST API を使用して、 [ETag](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)要求ヘッダーにします。
+ .NET SDK は、設定、accessCondition オブジェクトを通じて ETag を設定、 [If-match |場合: 一致-None ヘッダー](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)リソースにします。 継承する任意のオブジェクト[IResourceWithETag (.NET SDK)](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.iresourcewithetag) accessCondition オブジェクトが存在します。

リソースを更新するたびに、ETag が自動的に変更します。 同時実行の管理を実装するときに、クライアント上で変更したリソースのコピーとして同じ ETag にリモート リソースを必要とする更新の要求での事前条件を配置することはしています。 同時実行プロセスは、リモート リソースを既に変更した場合は、ETag、前提条件と一致しないと HTTP 412 で、要求は失敗します。 明らかに、.NET SDK を使用している場合、`CloudException`場所、`IsAccessConditionFailed()`拡張メソッドが true を返します。

> [!Note]
> 同時実行の 1 つだけのメカニズムがあります。 リソースの更新の API の使用に関係なく常に使用されます。 

<a name="samplecode"></a>
## <a name="use-cases-and-sample-code"></a>ユース ケースとサンプル コード

次のコードでは、accessCondition がキーの更新操作のチェックを示します。

+ リソースが存在しない場合、更新プログラムが失敗します。
+ リソースのバージョンが変更された場合、更新プログラムが失敗します。

### <a name="sample-code-from-dotnetetagsexplainer-programhttpsgithubcomazure-samplessearch-dotnet-getting-startedtreemasterdotnetetagsexplainer"></a>サンプル コード[DotNetETagsExplainer プログラム](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetETagsExplainer)

```
    class Program
    {
        // This sample shows how ETags work by performing conditional updates and deletes
        // on an Azure Search index.
        static void Main(string[] args)
        {
            IConfigurationBuilder builder = new ConfigurationBuilder().AddJsonFile("appsettings.json");
            IConfigurationRoot configuration = builder.Build();

            SearchServiceClient serviceClient = CreateSearchServiceClient(configuration);

            Console.WriteLine("Deleting index...\n");
            DeleteTestIndexIfExists(serviceClient);

            // Every top-level resource in Azure Search has an associated ETag that keeps track of which version
            // of the resource you're working on. When you first create a resource such as an index, its ETag is
            // empty.
            Index index = DefineTestIndex();
            Console.WriteLine(
                $"Test index hasn't been created yet, so its ETag should be blank. ETag: '{index.ETag}'");

            // Once the resource exists in Azure Search, its ETag will be populated. Make sure to use the object
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
            // Azure Search instead of potentially two. It also avoids an extra Delete request in cases where
            // the resource is deleted concurrently, but this doesn't matter much since resource deletion in
            // Azure Search is idempotent.

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

## <a name="design-pattern"></a>デザイン パターン

デザイン パターンを実装するオプティミスティック同時実行したときに、アクセス条件を再試行ループを含める必要がありますでは、アクセス条件のテストを確認し、必要に応じて変更を再度適用する前に更新済みのリソースを取得します。 

このコード スニペットは、既に存在するインデックスに synonymMap の追加を示しています。 このコードは、 [Azure Search のシノニム (プレビュー) c# チュートリアル](https://docs.microsoft.com/azure/search/search-synonyms-tutorial-sdk)です。 

スニペットは、「ホテル」インデックスを取得、更新操作のオブジェクトのバージョンを確認、条件が失敗した場合、例外をスローし操作を再試行 (3 回まで)、最新バージョンを取得するサーバーからのインデックスの取得を開始しますします。

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


## <a name="next-steps"></a>次のステップ

確認、[シノニム c# サンプル](https://github.com/Azure-Samples/search-dotnet-getting-started/tree/master/DotNetHowToSynonyms)安全に既存のインデックスを更新する方法の詳細なコンテキストです。

Etag または AccessCondition オブジェクトを含めるには、次のサンプルのいずれかを変更してください。

+ [Github に REST API のサンプル](https://github.com/Azure-Samples/search-rest-api-getting-started) 
+ [Github の .NET SDK サンプル](https://github.com/Azure-Samples/search-dotnet-getting-started)です。 このソリューションには、この記事で紹介したコードを含む"DotNetEtagsExplainer"プロジェクトが含まれています。

## <a name="see-also"></a>関連項目

  [共通の HTTP 要求と応答ヘッダー](https://docs.microsoft.com/rest/api/searchservice/common-http-request-and-response-headers-used-in-azure-search)    
  [HTTP ステータス コード](https://docs.microsoft.com/rest/api/searchservice/http-status-codes)[インデックス操作 (REST API)](https://docs.microsoft.com/\rest/api/searchservice/index-operations)