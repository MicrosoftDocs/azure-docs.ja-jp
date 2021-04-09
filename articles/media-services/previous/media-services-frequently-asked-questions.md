---
title: Azure Media Services のよく寄せられる質問
description: この記事では、Azure Media Services についてよく寄せられる質問に対する回答を提供します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: inhenkel
ms.openlocfilehash: 220aae64bd9ec493af8c8ee61901e27027dc9798
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103013382"
---
# <a name="media-services-v2-frequently-asked-questions"></a>Media Services v2 のよく寄せられる質問

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

この記事では、Azure Media Services (AMS) のユーザー コミュニティからよく寄せられる質問に対して回答します。

## <a name="general-ams-faqs"></a>AMS に関してよく寄せられる一般的な質問

Q:Apple iOS デバイスにはどのようにストリーミングしますか。

A: URL の "/Manifest" 部分に "(format=m3u8-aapl)" パスを追加して、Apple iOS ネイティブ デバイスでの使用のために HLS コンテンツを返すようストリーミング配信元サーバーに指示します (詳細については、[コンテンツの配信](media-services-deliver-content-overview.md)に関する記事をご覧ください)。

Q:インデックス作成の規模はどのように設定しますか。

A:予約ユニットは、エンコーディング タスクとインデックス作成タスクで同じです。 [エンコード予約ユニットの規模の設定方法](media-services-scale-media-processing-overview.md)に関するページの手順に従ってください。 **注:** Indexer のパフォーマンスは、予約ユニットの種類には左右されません。

Q:ビデオをアップロード、エンコード、および公開しました。 ビデオをストリームしようとしても再生できない場合、どんな原因が考えられますか。

A:最も一般的な原因の 1 つは、再生しようとしているストリーミング エンドポイントが **実行中** の状態になっていないことです。  

Q:ライブ ストリーム上で合成は可能ですか。

A:Azure Media Services では現在、ライブ ストリーム上での合成が提供されていないため、コンピューター上で事前に作成する必要があります。

Q:ライブ ストリーミングで Azure CDN を使用できますか。

A:Media Services は Azure CDN との統合をサポートしています (詳細については、[Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-portal-manage-streaming-endpoints.md)に関するページを参照)。  CDN でライブ ストリーミングを使用できます。 Azure Media Services は、スムーズ ストリーミング、HLS、MPEG-DASH の出力を提供します。 これらのすべての形式では、データの転送に HTTP を使用し、HTTP キャッシュを活用します。 ライブ ストリーミングでは実際のビデオ/オーディオ データがフラグメントに分割され、この個別のフラグメントが CDN にキャッシュされます。 更新する必要があるデータは、マニフェスト データのみです。 CDN はマニフェスト データを定期的に更新します。

Q:Azure Media Services は画像の格納をサポートしていますか。

A:JPEG または PNG 画像を格納するだけである場合は、それらを Azure Blob Storage に保持してください。 これらをビデオやオーディオ アセットに関連付けることがない限り、 Media Services アカウントに保存するメリットはありません。 または、ビデオ エンコーダーで画像をオーバーレイとして使用する必要がある場合、メディア エンコーダー スタンダードではビデオの上に画像をオーバーレイさせることができ、JPEG や PNG が入力形式としてサポートされています。 詳細については、「 [オーバーレイの作成](media-services-advanced-encoding-with-mes.md#overlay)」を参照してください。

Q:ある Media Services アカウントから別のアカウントにアセットをコピーするにはどうすればよいですか。

A:.NET を使用してある Media Services アカウントから別のアカウントにアセットをコピーするには、[Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/) リポジトリで入手できる [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) 拡張メソッドを使用します。 詳細については、 [こちらの](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) フォーラムのスレッドをご覧ください。

Q:AMS の操作時、ファイルに名前を付けるためにサポートされている文字は何ですか。

A:Media Services は、ストリーミング コンテンツ (たとえば、 http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters) の URL を構築する際に、IAssetFile.Name プロパティの値を使用します。このため、パーセントエンコーディングは利用できません。 **Name** プロパティの値には、[パーセント エンコーディング予約文字](https://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) (!*'();:@&=+$,/?%#[]") は使用できません。 また、ファイル名拡張子で使用できる "." は 1 つのみです。

Q:REST を使用してどのように接続すればよいですか。

A:AMS API に接続する方法については、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。 

Q:エンコーディング プロセス中にビデオをローテーションするにはどうすればよいですか。

A:[Media Encoder Standard](media-services-dotnet-encode-with-media-encoder-standard.md) は、90/180/270 の角度によるローテーションをサポートしています。 既定の動作 "自動" により、受信 MP4/MOV ファイルのローテーション メタデータの検出、およびこの補正が試行されます。 **ここ** で定義されたいずれかの JSON プリセットに対する次の [ソース](media-services-mes-presets-overview.md)要素を含めます。

```json
"Version": 1.0,
"Sources": [
{
  "Streams": [],
  "Filters": {
    "Rotation": "90"
  }
}
],
"Codecs": [

...
```


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]
