---
title: BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する
description: BLOB インデックス タグを使用して、BLOB オブジェクトの分類、管理、および検出のためのクエリを実行する方法の例をご紹介します。
author: mhopkins-msft
ms.author: mhopkins
ms.date: 04/24/2020
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.reviewer: hux
ms.custom: devx-track-csharp
ms.openlocfilehash: adc510ef89a912e6d76949794aacbf130a8f066d
ms.sourcegitcommit: 419cf179f9597936378ed5098ef77437dbf16295
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/27/2020
ms.locfileid: "89018877"
---
# <a name="utilize-blob-index-tags-preview-to-manage-and-find-data-on-azure-blob-storage"></a>BLOB インデックス タグ (プレビュー) を使用して Azure Blob Storage でデータを管理および検索する

BLOB インデックス タグは、キーと値のタグ属性を使用して、ストレージ アカウント内のデータを分類します。 これらのタグには自動的にインデックスが付けられ、クエリ可能な多次元インデックスとして公開されるため、データを簡単に見つけることができます。 この記事では、BLOB インデックス タグを使用してデータを設定、取得、および検索する方法について説明します。

BLOB インデックスの詳細については、「[BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する (プレビュー)](storage-manage-find-blobs.md)」を参照してください。

> [!NOTE]
> BLOB インデックスはパブリック プレビュー中であり、**カナダ中部**、**カナダ東部**、**フランス中部**、および**フランス南部**リージョンで利用できます。 この機能と既知の問題および制限の詳細については、「[BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する (プレビュー)](storage-manage-find-blobs.md)」を参照してください。

## <a name="prerequisites"></a>前提条件
# <a name="portal"></a>[ポータル](#tab/azure-portal)
- BLOB インデックス プレビューへのアクセスが登録および承認されたサブスクリプション
- [Azure portal](https://portal.azure.com/) へのアクセス

# <a name="net"></a>[.NET](#tab/net)
BLOB インデックスがパブリック プレビューであるため、.NET ストレージ パッケージはプレビュー NuGet フィードでリリースされます。 このライブラリは、今から一般公開されるまでに変更される可能性があります。 

1. Visual Studio プロジェクトをセットアップして、.NET 用 Azure Blob Storage クライアント ライブラリ v12 の使用を開始します。 詳細については、[.NET クイックスタート](storage-quickstart-blobs-dotnet.md)を参照してください

2. NuGet パッケージ マネージャーで、**Azure.Storage.Blobs** パッケージを見つけて、バージョン **12.5.0-preview.6** 以降をプロジェクトにインストールします。 コマンド ```Install-Package Azure.Storage.Blobs -Version 12.5.0-preview.6``` を実行することもできます

   詳細については、「[パッケージを検索してインストールする](https://docs.microsoft.com/nuget/consume-packages/install-use-packages-visual-studio#find-and-install-a-package)」を参照してください。

3. 次の using ステートメントを、コード ファイルの先頭に追加します。
```csharp
using Azure;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using Azure.Storage.Blobs.Specialized;
using System;
using System.Collections.Generic;
using System.Threading.Tasks;
```
---

## <a name="upload-a-new-blob-with-index-tags"></a>インデックス タグを使用して新しい BLOB をアップロードする
# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) で、自分のストレージ アカウントを選択します 

2. **[Blob service]** の下にある **[コンテナー]** オプションに移動し、コンテナーを選択します

3. **[アップロード]** ボタンを選択してアップロード ブレードを開き、ローカル ファイル システムを参照して、ブロック BLOB としてアップロードするファイルを見つけます。

4. **[詳細]** ドロップダウンを展開し、 **[Blob Index Tags]\(BLOB インデックス タグ\)** セクションに移動します

5. データに適用する、キーと値の BLOB インデックス タグを入力します

6. **[アップロード]** ボタンを選択して、BLOB をアップロードします

![BLOB インデックス タグを使用してデータをアップロードする](media/storage-blob-index-concepts/blob-index-upload-data-with-tags.png)

# <a name="net"></a>[.NET](#tab/net)

次の例は、作成時にタグが設定される追加 BLOB を作成する方法を示しています。

```csharp
static async Task BlobIndexTagsOnCreate()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create an append blob
          AppendBlobClient appendBlobWithTags = container.GetAppendBlobClient("myAppendBlob0.logs");

          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Sealed", "false" },
              { "Content", "logs" },
              { "Date", "2020-04-20" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags.CreateAsync(appendOptions);
      }
      finally
      {
      }
   }
```

---

## <a name="get-set-and-update-blob-index-tags"></a>BLOB インデックス タグを取得、設定、および更新する
# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) で、自分のストレージ アカウントを選択します 

2. **[Blob service]** の下にある **[コンテナー]** オプションに移動し、コンテナーを選択します

3. 選択したコンテナー内の BLOB の一覧から目的の BLOB を選択します

4. BLOB の概要タブに、すべての **BLOB インデックス タグ**を含む、BLOB のプロパティが表示されます

5. BLOB の任意のキーと値のインデックス タグを取得、設定、変更、または削除できます

6. **[保存]** を選択して、BLOB の更新を確定します

![オブジェクトの BLOB インデックス タグを取得、設定、更新、および削除する](media/storage-blob-index-concepts/blob-index-get-set-tags.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task BlobIndexTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container = serviceClient.GetBlobContainerClient("mycontainer");

      try
      {
          // Create a container
          await container.CreateIfNotExistsAsync();

          // Create a new append blob
          AppendBlobClient appendBlob = container.GetAppendBlobClient("myAppendBlob1.logs");
          await appendBlob.CreateAsync();

          // Set or Update Blob Index tags on existing blob
          Dictionary<string, string> tags = new Dictionary<string, string>
          {
              { "Project", "Contoso" },
              { "Status", "Unprocessed" },
              { "Sealed", "true" }
          };
          await appendBlob.SetTagsAsync(tags);

          // Get Blob Index tags
          Response<IDictionary<string, string>> tagsResponse = await appendBlob.GetTagsAsync();
          Console.WriteLine(appendBlob.Name);
          foreach (KeyValuePair<string, string> tag in tagsResponse.Value)
          {
              Console.WriteLine($"{tag.Key}={tag.Value}");
          }

          // List Blobs with all options returned including Blob Index tags
          await foreach (BlobItem blobItem in container.GetBlobsAsync(BlobTraits.All))
          {
              Console.WriteLine(Environment.NewLine + blobItem.Name);
              foreach (KeyValuePair<string, string> tag in blobItem.Tags)
              {
                  Console.WriteLine($"{tag.Key}={tag.Value}");
              }
          }

          // Delete existing Blob Index tags by replacing all tags
          Dictionary<string, string> noTags = new Dictionary<string, string>();
          await appendBlob.SetTagsAsync(noTags);

      }
      finally
      {
      }
   }
```

---

## <a name="filter-and-find-data-with-blob-index-tags"></a>BLOB インデックス タグを使用してデータをフィルター処理および検索する

# <a name="portal"></a>[ポータル](#tab/azure-portal)

Azure portal 内では、BLOB インデックス タグ フィルターによって `@container` パラメーターが自動的に適用され、選択したコンテナーがスコープになります。 ストレージ アカウント全体で、タグ付けされたデータをフィルター処理して検索する場合は、REST API、SDK、またはツールを使用してください。

1. [Azure portal](https://portal.azure.com/) で、ストレージ アカウントを選択します。 

2. **[Blob service]** の下にある **[コンテナー]** オプションに移動し、コンテナーを選択します

3. **[Blob Index tags filter]\(BLOB インデックス タグのフィルター\)** を選択し、選択したコンテナー内でフィルター処理を行います

4. BLOB のインデックス タグ キーとタグ値を入力してください

5. 追加のタグ フィルター (最大 10 個) を追加するには、 **[Blob Index tags filter]\(BLOB インデックス タグのフィルター\)** を選択します

![BLOB インデックス タグを使用してタグ付きオブジェクトをフィルター処理および検索する](media/storage-blob-index-concepts/blob-index-tag-filter-within-container.png)

# <a name="net"></a>[.NET](#tab/net)
```csharp
static async Task FindBlobsByTagsExample()
   {
      BlobServiceClient serviceClient = new BlobServiceClient(ConnectionString);
      BlobContainerClient container1 = serviceClient.GetBlobContainerClient("mycontainer");
      BlobContainerClient container2 = serviceClient.GetBlobContainerClient("mycontainer2");

      // Blob Index queries and selection
      String singleEqualityQuery = @"""Archive"" = 'false'";
      String andQuery = @"""Archive"" = 'false' AND ""Priority"" = '01'";
      String rangeQuery = @"""Date"" >= '2020-04-20' AND ""Date"" <= '2020-04-30'";
      String containerScopedQuery = @"@container = 'mycontainer' AND ""Archive"" = 'false'";

      String queryToUse = containerScopedQuery;

      try
      {
          // Create a container
          await container1.CreateIfNotExistsAsync();
          await container2.CreateIfNotExistsAsync();

          // Create append blobs
          AppendBlobClient appendBlobWithTags0 = container1.GetAppendBlobClient("myAppendBlob00.logs");
          AppendBlobClient appendBlobWithTags1 = container1.GetAppendBlobClient("myAppendBlob01.logs");
          AppendBlobClient appendBlobWithTags2 = container1.GetAppendBlobClient("myAppendBlob02.logs");
          AppendBlobClient appendBlobWithTags3 = container2.GetAppendBlobClient("myAppendBlob03.logs");
          AppendBlobClient appendBlobWithTags4 = container2.GetAppendBlobClient("myAppendBlob04.logs");
          AppendBlobClient appendBlobWithTags5 = container2.GetAppendBlobClient("myAppendBlob05.logs");
           
          // Blob Index tags to upload
          CreateAppendBlobOptions appendOptions = new CreateAppendBlobOptions();
          appendOptions.Tags = new Dictionary<string, string>
          {
              { "Archive", "false" },
              { "Priority", "01" },
              { "Date", "2020-04-20" }
          };
          
          CreateAppendBlobOptions appendOptions2 = new CreateAppendBlobOptions();
          appendOptions2.Tags = new Dictionary<string, string>
          {
              { "Archive", "true" },
              { "Priority", "02" },
              { "Date", "2020-04-24" }
          };

          // Upload data with tags set on creation
          await appendBlobWithTags0.CreateAsync(appendOptions);
          await appendBlobWithTags1.CreateAsync(appendOptions);
          await appendBlobWithTags2.CreateAsync(appendOptions2);
          await appendBlobWithTags3.CreateAsync(appendOptions);
          await appendBlobWithTags4.CreateAsync(appendOptions2);
          await appendBlobWithTags5.CreateAsync(appendOptions2);

          // Find Blobs given a tags query
          Console.WriteLine("Find Blob by Tags query: " + queryToUse + Environment.NewLine);

          List<FilterBlobItem> blobs = new List<FilterBlobItem>();
          await foreach (FilterBlobItem filterBlobItem in serviceClient.FindBlobsByTagsAsync(queryToUse))
          {
              blobs.Add(filterBlobItem);
          }

          foreach (var filteredBlob in blobs)
          {
              Console.WriteLine($"BlobIndex result: ContainerName= {filteredBlob.ContainerName}, " +
                  $"BlobName= {filteredBlob.Name}");
          }

      }
      finally
      {
      }
   }
```

---

## <a name="lifecycle-management-with-blob-index-tag-filters"></a>BLOB インデックス タグのフィルターを使用したライフサイクル管理

# <a name="portal"></a>[ポータル](#tab/azure-portal)

1. [Azure portal](https://portal.azure.com/) で、ストレージ アカウントを選択します。 

2. **[Blob service]** の下の **[ライフサイクル管理]** オプションに移動します

3. *[ルールの追加]* を選択し、[アクション セット] フォームのフィールドに入力します

4. [フィルター セット] を選択して、プレフィックスの一致と BLOB インデックスの一致に関するオプションのフィルターを追加します![ライフサイクル管理用の BLOB インデックス タグのフィルターを追加する](media/storage-blob-index-concepts/blob-index-match-lifecycle-filter-set.png)

5. **[確認 + 追加]** を選択し、ルール設定を確認します ![ライフサイクル管理ルールと BLOB インデックス タグのフィルターの例](media/storage-blob-index-concepts/blob-index-lifecycle-management-example.png)

6. ライフサイクル管理ポリシーに新しいルールを適用するには、 **[追加]** を選択します

# <a name="net"></a>[.NET](#tab/net)
[ライフサイクル管理](storage-lifecycle-management-concepts.md)ポリシーは、各ストレージ アカウントにコントロール プレーン レベルで適用されます。 .NET の場合、ライフサイクル管理ルール内で BLOB インデックスの一致フィルターを活用するには、[Microsoft Azure Management Storage Library バージョン 16.0.0](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/) 以降をインストールします。

---

## <a name="next-steps"></a>次のステップ

Blob インデックスの詳細を学習します。 「[BLOB インデックスを使用して Azure Blob Storage でデータを管理および検索する (プレビュー)](storage-manage-find-blobs.md )」を参照してください

ライフサイクル管理の詳細を学習します。 「[Azure Blob Storage のライフサイクルを管理する](storage-lifecycle-management-concepts.md)」を参照してください
