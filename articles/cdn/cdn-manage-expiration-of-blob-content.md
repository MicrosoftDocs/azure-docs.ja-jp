---
title: Azure Blob Storage の有効期限を管理する
titleSuffix: Azure Content Delivery Network
description: Azure CDN キャッシュで BLOB の有効期限を制御するオプションについて説明します。
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: how-to
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 206ff6f888229356743bebb816cf03e4f7a7504b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778717"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Azure CDN で Azure Blob Storage の有効期限を管理する
> [!div class="op_single_selector"]
> * [Azure Web コンテンツ](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure BLOB Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

Azure Storage の [Blob Storage サービス](../storage/common/storage-introduction.md#blob-storage)は、Azure ベースに元々あって Azure Content Delivery Network (CDN) と統合されたサービスの 1 つです。 パブリックにアクセス可能な BLOB コンテンツは、その有効期間 (TTL) が経過するまで、Azure CDN でキャッシュできます。 TTL は、配信元サーバーからの HTTP 応答の `Cache-Control` ヘッダーによって決まります。 この記事では、Azure Storage の BLOB で `Cache-Control` ヘッダーを設定する方法のいくつかを示します。

CDN キャッシュ規則を設定して、Azure portal からキャッシュの設定を制御することもです。 キャッシュ規則を作成し、そのキャッシュ動作を **[オーバーライド]** または **[キャッシュのバイパス]** に設定すると、この記事で説明されている最初に示されたキャッシュ設定は無視されます。 全般的なキャッシュの概念については、「[キャッシュのしくみ](cdn-how-caching-works.md)」をご覧ください。

> [!TIP]
> BLOB に TTL を設定しないこともできます。 この場合は、Azure Portal でキャッシュ規則を設定していない限り、Azure CDN によって既定の 7 日間の TTL が自動的に適用されます。 この既定の TTL は、一般的な Web 配信の最適化に対してのみ適用されます。 大きなファイルの最適化に対する既定の TTL は 1 日、メディア ストリーミングの最適化に対する既定の TTL は 1 年です。
> 
> その他のファイルと BLOB へのアクセスを高速化する Azure CDN のしくみについて詳しくは、「[Azure Content Delivery Network (CDN) の概要](cdn-overview.md)」をご覧ください。
> 
> Azure Blob Storage について詳しくは、「[Blob Storage の概要](../storage/blobs/storage-blobs-introduction.md)」をご覧ください。
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>CDN キャッシュ規則を使用した Cache-Control ヘッダーの設定
BLOB の `Cache-Control` ヘッダーを設定するための推奨される方法は、Azure Portal でのキャッシュ規則の使用です。 CDN キャッシュ規則の詳細については、[キャッシュ規則による Azure CDN キャッシュ動作の制御](cdn-caching-rules.md)に関するページを参照してください。

> [!NOTE] 
> キャッシュ規則は、**Azure CDN Standard from Verizon** および **Azure CDN Standard from Akamai** プロファイルでのみ使用できます。 **Azure CDN Premium from Verizon** プロファイルの場合は、同様の機能に対して **管理** ポータルで [Azure CDN ルール エンジン](./cdn-verizon-premium-rules-engine.md)を使用する必要があります。

**[CDN キャッシュ規則] ページに移動するには**:

1. Azure Portal で、CDN プロファイルを選択してから、BLOB のエンドポイントを選択します。

2. 左側のウィンドウの [設定] で、 **[キャッシュ規則]** を選択します。

   ![[CDN キャッシュ規則] ボタン](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   **[キャッシュ規則]** ページが表示されます。

   ![[CDN キャッシュ] ページ](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**グローバル キャッシュ規則を使用して BLOB ストレージ サービスの Cache-Control ヘッダーを設定するには:**

1. **[グローバル キャッシュ規則]** で、 **[クエリ文字列のキャッシュ動作]** を **[クエリ文字列を無視]** に設定し、 **[キャッシュ動作]** を **[オーバーライド]** に設定します。
      
2. **[キャッシュの有効期間]** として、 **[Seconds] (秒)** ボックスに「3600」と入力するか、または **[時間]** ボックスに「1」と入力します。 

   ![CDN グローバル キャッシュ規則の例](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   このグローバル キャッシュ規則は 1 時間のキャッシュ有効期間を設定し、エンドポイントへのすべての要求に影響を与えます。 これは、エンドポイントで指定された配信元サーバーによって送信されるすべての `Cache-Control` または `Expires` HTTP ヘッダーをオーバーライドします。   

3. **[保存]** を選択します。
 
**カスタム キャッシュ規則を使用して BLOB ファイルの Cache-Control ヘッダーを設定するには:**

1. **[Custom caching rules] (カスタム キャッシュ規則)** で、次の 2 つの一致条件を作成します。

     A. 最初の一致条件では、**[一致条件]** を **[パス]** に設定し、**[一致する値]** として `/blobcontainer1/*` を入力します。 **[キャッシュ動作]** を **[オーバーライド]** に設定し、 **[時間]** ボックスに「4」と入力します。

    B. 2 番目の一致条件では、**[一致条件]** を **[パス]** に設定し、**[一致する値]** として `/blobcontainer1/blob1.txt` を入力します。 **[キャッシュ動作]** を **[オーバーライド]** に設定し、 **[時間]** ボックスに「2」と入力します。

    ![CDN カスタム キャッシュ規則の例](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    最初のカスタム キャッシュ規則は、エンドポイントで指定された配信元サーバー上の `/blobcontainer1` フォルダー内のすべての BLOB ファイルに対して 4 時間のキャッシュ有効期間を設定します。 2 番目の規則は `blob1.txt` BLOB ファイルについてのみ最初の規則をオーバーライドし、そのファイルに対して 2 時間のキャッシュ有効期間を設定します。

2. **[保存]** を選択します。


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Azure PowerShell を使った Cache-Control ヘッダーの設定

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/) は、Azure の各種サービスを管理する最も簡単で最も強力な方法の 1 つです。 `Get-AzStorageBlob` コマンドレットを使用して BLOB への参照を取得し、`.ICloudBlob.Properties.CacheControl` プロパティを設定します。 

次に例を示します。

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> PowerShell は、[CDN プロファイルとエンドポイントの管理](cdn-manage-powershell.md)に使用することもできます。
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>.NET を使った Cache-Control ヘッダーの設定
.NET コードを使用して BLOB の `Cache-Control` ヘッダーを指定するには、[.NET 用の Azure Storage クライアント ライブラリ](../storage/blobs/storage-quickstart-blobs-dotnet.md)を使用して [CloudBlob.Properties.CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) プロパティを設定します。

次に例を示します。

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> 「[Azure Blob Storage Samples for .NET ](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)」(.NET 用 Azure Blob Storage のサンプル) には、他にも多くの使用可能な .NET コード サンプルがあります。
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>他の方法を使った Cache-Control ヘッダーの設定

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) を使うと、*CacheControl* のようなプロパティなどの Blob Storage リソースを表示して編集できます。 

Azure Storage Explorer で BLOB の *CacheControl* プロパティを更新するには、以下の操作を行います。
   1. BLOB を選択し、コンテキスト メニューから **[プロパティ]** を選択します。 
   2. 下にスクロールして *CacheControl* プロパティを表示させます。
   3. 値を入力してから、 **[保存]** を選択します。


![Azure Storage Explorer のプロパティ](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Azure コマンド ライン インターフェイス
[Azure コマンド ライン インターフェイス](/cli/azure) (CLI) では、コマンド ラインから Azure BLOB リソースを管理することができます。 Azure CLI で BLOB をアップロードするときにキャッシュ制御ヘッダーを設定するには、`-p` スイッチを使って *cacheControl* プロパティを設定します。 次の例では、TTL を 1 時間 (3,600 秒) に設定する方法を示します。
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Azure ストレージ サービス REST API
[Azure ストレージ サービス REST API](/rest/api/storageservices/) を使うと、要求で次の操作を使って *x-ms-blob-cache-control* プロパティを明示的に設定できます。
  
   - [Put Blob](/rest/api/storageservices/Put-Blob)
   - [Put Block List](/rest/api/storageservices/Put-Block-List)
   - [Set Blob Properties](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Cache-Control ヘッダーのテスト
BLOB の TTL 設定を簡単に確認できます。 ブラウザーの[開発者ツール](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)を使って、BLOB に `Cache-Control` 応答ヘッダーが含まれているかどうかをテストします。 [Wget](https://www.gnu.org/software/wget/)、[Postman](https://www.getpostman.com/)、[Fiddler](https://www.telerik.com/fiddler) などのツールを使って応答ヘッダーを確認することもできます。

## <a name="next-steps"></a>次の手順
* [Azure CDN でクラウド サービスのコンテンツの有効期限を管理する方法を確認します](cdn-manage-expiration-of-cloud-service-content.md)
* [キャッシュの概念を学習する](cdn-how-caching-works.md)