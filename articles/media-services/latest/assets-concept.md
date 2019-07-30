---
title: Azure Media Services の資産 | Microsoft Docs
description: この記事では、アセットとは何かについて説明し、Azure Media Services によるそれらの使用方法についても説明します。
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 07/02/2019
ms.author: juliako
ms.custom: seodec18
ms.openlocfilehash: d0a81d5d7ce8e7569b77007b6ad9c322cf626f16
ms.sourcegitcommit: 2e4b99023ecaf2ea3d6d3604da068d04682a8c2d
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67670692"
---
# <a name="assets"></a>アセット

Azure Media Services では、[アセット](https://docs.microsoft.com/rest/api/media/assets)には、Azure Storage 内に格納されているデジタル ファイル (ビデオ、オーディオ、画像、サムネイルのコレクション、テキスト トラック、クローズド キャプション ファイルなど) に関する情報が含まれています。 

アセットは [Azure Storage アカウント](storage-account-concept.md)内の BLOB コンテナーにマップされ、アセット内のファイルはブロック BLOB としてそのコンテナーに格納されます。 Media Services は、アカウントが汎用 v2 (GPv2) ストレージを使用している場合に、BLOB 層をサポートします。 GPv2 を使用して、[クール ストレージまたはアーカイブ ストレージ](https://docs.microsoft.com/azure/storage/blobs/storage-blob-storage-tiers)にファイルを移動できます。 **アーカイブ** ストレージは、(エンコード後などに) 不要になったソース ファイルをアーカイブするのに適しています。

**アーカイブ** ストレージ層は、既にエンコードされ、エンコード ジョブの出力が出力 BLOB コンテナーに配置されている非常に大きなソース ファイルの場合のみ推奨されます。 アセットに関連付け、コンテンツのストリーム配信や分析に使用する出力コンテナー内の BLOB は、**ホット**または**クール** ストレージ層に存在する必要があります。

## <a name="upload-digital-files-into-assets"></a>アセットへのデジタル ファイルのアップロード

デジタル ファイルは、ストレージにアップロードされアセットに関連付けられた後、Media Services エンコード、ストリーム配信、コンテンツ分析ワークフローで使用できます。 Media Services の一般的なワークフローの 1 つに、ファイルのアップロード、エンコード、ストリーム配信があります。 このセクションでは、一般的な手順について説明します。

> [!TIP]
> 開発を開始する前に、「[Media Services v3 API を使用した開発](media-services-apis-overview.md)」を確認してください (API や命名規則などへのアクセスに関する情報が含まれています)。

1. Media Services v3 API を使用して、新しい "入力" アセットを作成します。 この操作により、Media Services アカウントに関連付けられているストレージ アカウントにコンテナーが作成されます。 API からコンテナー名が返されます (例: `"container": "asset-b8d8b68a-2d7f-4d8c-81bb-8c7bbbe67ee4"`)。
   
    アセットに関連付ける BLOB コンテナーが既にある場合、アセットを作成するときに、そのコンテナーの名前を指定できます。 Media Services では、現在、ファイル名にパスが含まれていない、コンテナー ルートにある BLOB のみをサポートしています。 そのため、"input.mp4" というファイル名のコンテナーは使用できます。 しかし、"videos/inputs/input.mp4" というファイル名のコンテナーは使用できません。

    Azure CLI を使用して、自分のサブスクリプション内にある、権限を持っている任意のストレージ アカウントおよびコンテナーに直接アップロードできます。 <br/>コンテナー名は一意であり、ストレージの名前付けガイドラインに従っている必要があります。 名前は Media Services アセット コンテナー名 (Asset-GUID) の形式に従う必要はありません。 
    
    ```azurecli
    az storage blob upload -f /path/to/file -c MyContainer -n MyBlob
    ```
2. デジタル ファイルをアセット コンテナーにアップロードするために使用する、読み取り/書き込みアクセス許可のある SAS URL を取得します。 Media Services API を使用して、[アセット コンテナーの URL を一覧表示](https://docs.microsoft.com/rest/api/media/assets/listcontainersas)できます。
3. Azure Storage API または SDK (たとえば、[Storage REST API](../../storage/common/storage-rest-api-auth.md)、[JAVA SDK](../../storage/blobs/storage-quickstart-blobs-java-v10.md)、[.NET SDK](../../storage/blobs/storage-quickstart-blobs-dotnet.md)) を使用して、ファイルをアセット コンテナーにアップロードします。 
4. Media Services v3 API を使用して、"入力" アセットを処理する変換とジョブを作成します。 詳しくは、「[Transform と Job](transform-concept.md)」をご覧ください。
5. "出力" アセットからのコンテンツをストリーム配信します。

アセットの作成、ストレージ内のアセット コンテナーに書き込み可能な SAS URL の取得、SAS URL を使用したストレージ内のコンテナーへのファイル アップロードを行う方法を示す .NET の完全な例については、「[ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)」をご覧ください。

### <a name="create-a-new-asset"></a>新しいアセットの作成

> [!NOTE]
> アセットの Datetime 型のプロパティは、常に UTC 形式です。

#### <a name="rest"></a>REST

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Media/mediaServices/{amsAccountName}/assets/{assetName}?api-version=2018-07-01
```

REST の例については、[REST を使用したアセットの作成](https://docs.microsoft.com/rest/api/media/assets/createorupdate#examples)の例をご覧ください。

この例では、**要求本文**を作成する方法を示しており、説明、コンテナー名、ストレージ アカウントなどの有用な情報を指定できます。

#### <a name="curl"></a>cURL

```cURL
curl -X PUT \
  'https://management.azure.com/subscriptions/00000000-0000-0000-000000000000/resourceGroups/resourceGroupName/providers/Microsoft.Media/mediaServices/amsAccountName/assets/myOutputAsset?api-version=2018-07-01' \
  -H 'Accept: application/json' \
  -H 'Content-Type: application/json' \
  -d '{
  "properties": {
    "description": "",
  }
}'
```

#### <a name="net"></a>.NET

```csharp
 Asset asset = await client.Assets.CreateOrUpdateAsync(resourceGroupName, accountName, assetName, new Asset());
```

完全な例については、「[ローカル ファイルからジョブの入力を作成する](job-input-from-local-file-how-to.md)」をご覧ください。 Media Services v3 では、ジョブの入力を HTTPS URL から作成することもできます (「[HTTPS URL からジョブの入力を作成する](job-input-from-http-how-to.md)」をご覧ください)。

## <a name="map-v3-asset-properties-to-v2"></a>v3 と v2 の資産のプロパティのマッピング

次の表は、v3 の[資産](https://docs.microsoft.com/rest/api/media/assets/createorupdate#asset)のプロパティが v2 の資産のプロパティにどのようにマッピングされるかを示しています。

|v3 のプロパティ|v2 のプロパティ|
|---|---|
|id - (一意) Azure Resource Manager の完全なパス。「[資産](https://docs.microsoft.com/rest/api/media/assets/createorupdate)」の例を参照してください||
|name - (一意)「[名前付け規則](media-services-apis-overview.md#naming-conventions)」を参照してください ||
|alternateId|AlternateId|
|assetId|Id - (一意) 値は `nb:cid:UUID:` プレフィックスで始まります。|
|created|作成されました|
|description|EnableAdfsAuthentication|
|lastModified|LastModified|
|storageAccountName|StorageAccountName|
|storageEncryptionFormat| Options (作成オプション)|
|type||

## <a name="storage-side-encryption"></a>ストレージ側の暗号化

保存時の資産を保護するには、ストレージ側の暗号化で資産を暗号化する必要があります。 次の表では、Media Services でのストレージ側暗号化のしくみを示します。

|暗号化オプション|説明|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services のストレージの暗号化|AES-256 暗号化、Media Services によって管理されるキー|サポートされています<sup>(1)</sup>|サポートされていません<sup>(2)</sup>|
|[Storage Service Encryption for Data at Rest](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Azure Storage によって提供されるサーバー側暗号化、Azure またはお客様が管理するキー|サポートされています|サポートされています|
|[ストレージ クライアント側暗号化](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Azure Storage によって提供されるクライアント側暗号化、お客様が Key Vault で管理するキー|サポートされていません|サポートされていません|

<sup>1</sup> Media Services は、クリアな、どのような形式でも暗号化されていないコンテンツの処理をサポートしますが、そうすることは推奨されません。

<sup>2</sup> Media Services v3 では、ストレージの暗号化 (AES-256 暗号化) は、Media Services v2 で資産を作成した場合の下位互換性のためにのみサポートされています。 つまり、v3 は、既存のストレージの暗号化済み資産では動作しますが、そのような資産を新規作成することはできません。

## <a name="filtering-ordering-paging"></a>フィルター処理、順序付け、ページング

「[Media Services エンティティのフィルター処理、順序付け、ページング](entities-overview.md)」を参照してください。

## <a name="next-steps"></a>次の手順

* [ファイルのストリーミング](stream-files-dotnet-quickstart.md)
* [クラウド DVR の使用](live-event-cloud-dvr.md)
* [Media Services v2 と v3 の違い](migrate-from-v2-to-v3.md)
