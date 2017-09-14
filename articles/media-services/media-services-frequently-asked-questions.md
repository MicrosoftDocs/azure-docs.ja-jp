---
title: "Azure Media Services のよく寄せられる質問 | Microsoft Docs"
description: "よく寄せられる質問 (FAQ)"
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.assetid: 5374f7f4-c189-43ef-8b7f-f2f4141e2748
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: juliako
ms.translationtype: HT
ms.sourcegitcommit: fff84ee45818e4699df380e1536f71b2a4003c71
ms.openlocfilehash: 1f0c5cf93b44433aab2163548de916626a7fa480
ms.contentlocale: ja-jp
ms.lasthandoff: 08/01/2017

---
# <a name="frequently-asked-questions"></a>よく寄せられる質問

この記事では、Azure Media Services (AMS) のユーザー コミュニティからよく寄せられる質問に対して回答します。

## <a name="general-ams-faqs"></a>AMS に関してよく寄せられる一般的な質問
Q: インデックス作成の規模はどのように設定できますか?

A: 予約ユニットは、エンコード タスクでもインデックス作成タスクでも同じです。 [エンコード予約ユニットの規模の設定方法](media-services-scale-media-processing-overview.md)に関するページの手順に従ってください。 **注:** Indexer のパフォーマンスは、予約ユニットの種類には左右されません。

Q: ビデオをアップロード、エンコード、公開しました。 ビデオをストリームしようとしても再生できない場合、どんな原因が考えられますか。

可能性が高い原因は、再生しようとしているストリーミング エンドポイントが**実行中**状態になっていないことです。  

Q: ライブ ストリームで合成はできますか。

A: 現時点では、Azure Media Services でのライブ ストリームの合成はできないため、コンピューター上で事前に作成する必要があります。

Q: ライブ ストリーミングを Azure CDN で使用できますか。

A: Media Services では Azure CDN との統合をサポートしています (詳細については、「 [Media Services アカウントでストリーミング エンドポイントを管理する方法](media-services-portal-manage-streaming-endpoints.md)」をご覧ください)。  CDN でライブ ストリーミングを使用できます。 Azure Media Services は、スムーズ ストリーミング、HLS、MPEG-DASH の出力を提供します。 これらのすべての形式では、データの転送に HTTP を使用し、HTTP キャッシュを活用します。 ライブ ストリーミングでは実際のビデオ/オーディオ データがフラグメントに分割され、この個別のフラグメントが CDN にキャッシュされます。 更新する必要があるデータは、マニフェスト データのみです。 CDN はマニフェスト データを定期的に更新します。

Q: Azure Media services では画像の保存はサポートされますか。

A: JPEG や PNG 画像を保存するだけの場合は、Azure Blob Storage を使用してください。 これらをビデオやオーディオ アセットに関連付けることがない限り、 Media Services アカウントに保存するメリットはありません。 または、ビデオ エンコーダーで画像をオーバーレイとして使用する必要がある場合、メディア エンコーダー スタンダードではビデオの上に画像をオーバーレイさせることができ、JPEG や PNG が入力形式としてサポートされています。 詳細については、「 [オーバーレイの作成](media-services-advanced-encoding-with-mes.md#overlay)」を参照してください。

Q: 1 つの Media Services アカウントから他のアカウントにアセットをコピーする方法を教えてください。

A: .NET を使用してある Media Services アカウントから別のアカウントにアセットをコピーするには、[Azure Media Services .NET SDK Extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions/) リポジトリで入手できる [IAsset.Copy](https://github.com/Azure/azure-sdk-for-media-services-extensions/blob/dev/MediaServices.Client.Extensions/IAssetExtensions.cs#L354) 拡張メソッドを使用します。 詳細については、 [こちらの](https://social.msdn.microsoft.com/Forums/azure/28912d5d-6733-41c1-b27d-5d5dff2695ca/migrate-media-services-across-subscription?forum=MediaServices) フォーラムのスレッドをご覧ください。

Q: AMS を使用する場合、ファイルの名前にはどのような文字を使用できますか。

A: Media Services は、ストリーミング コンテンツ (たとえば、http://{AMSAccount}.origin.mediaservices.windows.net/{GUID}/{IAssetFile.Name}/streamingParameters) の URL を構築する際に、IAssetFile.Name プロパティの値を使用します。このため、パーセントエンコーディングは利用できません。 **Name** プロパティの値には、[パーセント エンコーディング予約文字](http://en.wikipedia.org/wiki/Percent-encoding#Percent-encoding_reserved_characters) (!*'();:@&=+$,/?%#[]") は使用できません。 "." は 1 つのみです。 また、ファイル名拡張子で使用できる

Q: REST を使用して接続する方法を教えてください。

A: AMS API に接続する方法については、「[Azure AD 認証を使用した Azure Media Services API へのアクセス](media-services-use-aad-auth-to-access-ams-api.md)」を参照してください。 に正常に接続されると、 https://media.windows.net 別の Media Services の URI を指定する 301 リダイレクトを受け取ります。 その新しい URI に再度コールする必要があります。 

Q: エンコーディング プロセスでビデオをローテーションする方法を教えてください。

A: [メディア エンコーダー スタンダード](media-services-dotnet-encode-with-media-encoder-standard.md) は、90/180/270 の角度によるローテーションをサポートしています。 既定の動作 "自動" により、受信 MP4/MOV ファイルのローテーション メタデータの検出、およびこの補正が試行されます。 **ここ** で定義されたいずれかの JSON プリセットに対する次の [ソース](media-services-mes-presets-overview.md)要素を含めます。

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


## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

