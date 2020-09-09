---
title: Azure Media Services v2 から v3 への移行
description: この記事では、Azure Media Services v3 で導入された変更点と、2 つのバージョンの違いについて説明します。
services: media-services
documentationcenter: na
author: IngridAtMicrosoft
manager: femila
editor: ''
tags: ''
keywords: Azure Media Services, ストリーム, ブロードキャスト, ライブ, オフライン
ms.service: media-services
ms.devlang: multiple
ms.topic: conceptual
ms.tgt_pltfrm: multiple
ms.workload: media
ms.date: 08/31/2020
ms.author: inhenkel
ms.openlocfilehash: 061ae48de9a73270ed499282c9fc9a4f8f1dba90
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89298948"
---
# <a name="media-services-v2-vs-v3"></a>Media Services v2 対 v3

[!INCLUDE [media services api v3 logo](./includes/v3-hr.md)]

この記事では、Azure Media Services v3 で導入された変更点と、2 つのバージョンの違いについて説明します。

## <a name="general-changes-from-v2"></a>v2 からの一般的な変更点

* v3 で作成されたアセットの場合、Media Services は [Azure Storage サーバー側のストレージ暗号化](../../storage/common/storage-service-encryption.md)のみをサポートします。
    * V3 API は、[ストレージ暗号化](../previous/media-services-rest-storage-encryption.md) (AES 256) が Media Services によって提供された v2 API で作成されたアセットと一緒に使用できます。
    * v3 API を使用して従来の AES 256 [ストレージ暗号化](../previous/media-services-rest-storage-encryption.md)で新しいアセットを作成することはできません。
* v3 の[アセット](assets-concept.md)のプロパティは v2 と異なります。[プロパティのマッピング](#map-v3-asset-properties-to-v2)に関するページを参照してください。
* v3 SDK が Storage SDK から分離されたため、使用する Storage SDK のバージョンをより詳細に制御し、バージョン管理の問題を回避できるようになりました。 
* v3 API では、エンコード ビット レートはすべてビット/秒単位です。 これは v2 Media Encoder Standard のプリセットとは異なります。 たとえば、v2 のビットレートは 128 (kbps) と指定されていますが、v3 では 128,000 (ビット/秒) です。 
* v3 にはエンティティ AssetFiles、AccessPolicies、IngestManifests が存在しません。
* v3 には IAsset.ParentAssets プロパティが存在しません。
* ContentKeys はエンティティではなくなり、ストリーミング ロケーターのプロパティになりました。
* Event Grid のサポートによって NotificationEndpoints を置き換えられました。
* 次のエンティティ名が変更されました
    * ジョブ出力は Task を置き換え、ジョブの一部になりました。
    * ストリーミング ロケーターによって Locator が置き換えられました。
    * ライブ イベントによって Channel が置き換えられました。<br/>ライブ イベントの課金はライブ チャンネルの測定に基づいています。 詳細については、[価格](live-event-states-billing.md)と[課金](https://azure.microsoft.com/pricing/details/media-services/)に関するセクションを参照してください。
    * ライブ出力によって Program が置き換えられました。
* ライブ出力は作成すると開始され、削除されると停止します。 v2 API ではプログラムの動作方法が異なり、作成後に起動される必要がありました。
* ジョブに関する情報を取得するには、ジョブが作成された Transform の名前を知っている必要があります。 
* v2 では、XML の[入力](../previous/media-services-input-metadata-schema.md)と[出力](../previous/media-services-output-metadata-schema.md)のメタデータ ファイルは、エンコード ジョブの結果として生成されます。 v3 では、メタデータの形式が XML から JSON に変更されました。 
* Media Services v2 では、初期化ベクター (IV) を指定できます。 Media Services v3 では、FairPlay IV を指定することはできません。 パッケージ化とライセンス デリバリーの両方で Media Services を使用する顧客には影響しませんが、サードパーティの DRM システムを使用して FairPlay ライセンス (ハイブリッド モード) を提供すると、問題になる可能性があります。 その場合は、FairPlay IV が cbcs キー ID から派生し、次の式を使用して取得できることを理解しておくことが重要です。

    ```
    string cbcsIV =  Convert.ToBase64String(HexStringToByteArray(cbcsGuid.ToString().Replace("-", string.Empty)));
    ```

    with

    ``` 
    public static byte[] HexStringToByteArray(string hex)
    {
        return Enumerable.Range(0, hex.Length)
            .Where(x => x % 2 == 0)
            .Select(x => Convert.ToByte(hex.Substring(x, 2), 16))
            .ToArray();
    }
    ```

    詳細については、 [ライブおよび VOD 操作の両方のためのハイブリッド モードの Media Services v3 の Azure Functions C# コード](https://github.com/Azure-Samples/media-services-v3-dotnet-core-functions-integration/tree/master/LiveAndVodDRMOperationsV3)に関する記事を参照してください。
 
> [!NOTE]
> [Media Services v3 リソース](media-services-apis-overview.md#naming-conventions)に適用される名前付け規則をご確認ください。 [BLOB の名前付け](assets-concept.md#naming)についてもご確認ください。

## <a name="feature-gaps-with-respect-to-v2-apis"></a>v2 API に関する機能のギャップ

v3 API には v2 API に関して次の機能ギャップがあります。 ギャップを埋めることは進行中の作業です。

* [Premium Encoder](../previous/media-services-premium-workflow-encoder-formats.md) と従来の [Media Analytics プロセッサ](../previous/media-services-analytics-overview.md)(Azure Media Services Indexer 2 プレビュー、Face Redactor など) は v3 を使用してアクセスできません。<br/>Media Indexer 1 または2 のプレビューからの移行を希望するお客様は、v3 API でプリセットされている AudioAnalyzer をすぐに使用できます。  この新しいプリセットには、古い Media Indexer 1 または 2 より多くの機能が含まれています。 
* 次に示すような、API の [v2 における Media Encoder Standard の高度な機能](../previous/media-services-advanced-encoding-with-mes.md) の多くは現在、v3 では利用できません。
  
    * アセットの結合
    * オーバーレイ
    * トリミング
    * サムネイル スプライト
    * 音声が入力されない場合に、無音オーディオ トラックを挿入する
    * 入力に映像が含まれていない場合に、ビデオ トラックを挿入する
* 現在、コード変換を使用するライブ イベントでは、ストリーム中のスレート挿入および API 呼び出しによる Ad マーカー挿入はサポートされていません。 
* .NETCore SDK の V2 REST API の使用に関するベスト プラクティスとパターンについては、`https://github.com/Azure-Samples/media-services-v2-dotnet-core-restsharp-sample.git` サンプル コードを参照してください。

## <a name="asset-specific-changes"></a>資産固有の変更

### <a name="map-v3-asset-properties-to-v2"></a>v3 と v2 の資産のプロパティのマッピング

次の表は、v3 の[資産](/rest/api/media/assets/createorupdate#asset)のプロパティが v2 の資産のプロパティにどのようにマッピングされるかを示しています。

|v3 のプロパティ|v2 のプロパティ|
|---|---|
|`id` - (一意) Azure Resource Manager の完全なパス。[アセット](/rest/api/media/assets/createorupdate)に関するページで例を参照してください||
|`name` - (一意)「[名前付け規則](media-services-apis-overview.md#naming-conventions)」を参照してください ||
|`alternateId`|`AlternateId`|
|`assetId`|`Id` - (一意) 値は `nb:cid:UUID:` プレフィックスで始まります。|
|`created`|`Created`|
|`description`|`Name`|
|`lastModified`|`LastModified`|
|`storageAccountName`|`StorageAccountName`|
|`storageEncryptionFormat`| `Options` (作成オプション)|
|`type`||

### <a name="storage-side-encryption"></a>ストレージ側の暗号化

保存時の資産を保護するには、ストレージ側の暗号化で資産を暗号化する必要があります。 次の表では、Media Services でのストレージ側暗号化のしくみを示します。

|暗号化オプション|説明|Media Services v2|Media Services v3|
|---|---|---|---|
|Media Services のストレージの暗号化|AES-256 暗号化、Media Services によって管理されるキー。|サポートされています<sup>(1)</sup>|サポートされていません<sup>(2)</sup>|
|[Storage Service Encryption for Data at Rest](../../storage/common/storage-service-encryption.md)|Azure Storage によって提供されるサーバー側暗号化、Azure またはお客様が管理するキー。|サポートされています|サポートされています|
|[ストレージ クライアント側暗号化](../../storage/common/storage-client-side-encryption.md)|Azure Storage によって提供されるクライアント側暗号化、お客様が Key Vault で管理するキー。|サポートされていません|サポートされていません|

<sup>1</sup> Media Services は、クリアな、どのような形式でも暗号化されていないコンテンツの処理をサポートしますが、そうすることは推奨されません。

<sup>2</sup> Media Services v3 では、ストレージの暗号化 (AES-256 暗号化) は、Media Services v2 で資産を作成した場合の下位互換性のためにのみサポートされています。 つまり、v3 は、既存のストレージの暗号化済みアセットでは動作しますが、そのようなアセットを新規作成することはできません。

## <a name="code-differences"></a>コードの違い

次の表では、一般的なシナリオでの v2 と v3 のコードの違いを示します。

|シナリオ|V2 API|V3 API|
|---|---|---|
|アセットの作成とファイルのアップロード |[v2 .NET の例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L113)|[v3 .NET の例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L169)|
|ジョブの送信|[v2 .NET の例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L146)|[v3 .NET の例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L298)<br/><br/>まず変換を作成し、次にジョブを送信する方法を示しています。|
|AES 暗号化を使用してアセットを公開する |1.ContentKeyAuthorizationPolicyOption を作成します<br/>2.ContentKeyAuthorizationPolicy を作成します<br/>3.AssetDeliveryPolicy を作成します<br/>4.アセットを作成し、コンテンツをアップロードするか、ジョブを送信して出力資産を使用します<br/>5.AssetDeliveryPolicy を資産に関連付けます<br/>6.ContentKey を作成します<br/>7.ContentKey をアセットにアタッチします<br/>8.AccessPolicy を作成します<br/>9.ロケーターを作成します<br/><br/>[v2 .NET の例](https://github.com/Azure-Samples/media-services-dotnet-dynamic-encryption-with-aes/blob/master/DynamicEncryptionWithAES/DynamicEncryptionWithAES/Program.cs#L64)|1.コンテンツ キー ポリシーを作成します<br/>2.アセットを作成します<br/>3.コンテンツをアップロードするか、アセットを JobOutput として使用します<br/>4.ストリーミング ロケーターを作成します<br/><br/>[v3 .NET の例](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/EncryptWithAES/Program.cs#L105)|
|ジョブの詳細を取得し、ジョブを管理する |[v2 を使用したジョブの管理](../previous/media-services-dotnet-manage-entities.md#get-a-job-reference) |[v3 を使用したジョブの管理](https://github.com/Azure-Samples/media-services-v3-dotnet-tutorials/blob/master/AMSV3Tutorials/UploadEncodeAndStreamFiles/Program.cs#L546)|

> [!NOTE]
> この記事にブックマークを設定し、更新がないか随時確認してください。

## <a name="ask-questions-give-feedback-get-updates"></a>質問、フィードバックの送信、最新情報の入手

「[Azure Media Services community (Azure Media Services コミュニティ)](media-services-community.md)」を参照して、さまざまな質問の方法、フィードバックする方法、Media Services に関する最新情報の入手方法を確認してください。

## <a name="next-steps"></a>次のステップ

[Media Services v2 から v3 への移行のガイダンス](migrate-from-v2-to-v3.md)
