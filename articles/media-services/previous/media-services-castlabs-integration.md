---
title: castLabs を使用して Azure Media Services に Widevine ライセンスを配信する | Microsoft Docs
description: この記事では、PlayReady と Widevine DRM の両方を使用して AMS で動的に暗号化されたストリームを、Azure Media Services (AMS) を使用して配信する方法について説明します。
services: media-services
documentationcenter: ''
author: Mingfeiy
manager: femila
editor: ''
ms.assetid: 2a9a408a-a995-49e1-8d8f-ac5b51e17d40
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: Juliako
ms.reviewer: willzhan
ms.openlocfilehash: 29a344c739d8d99da2e5c81d41a11c601e48022e
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/10/2019
ms.locfileid: "74969142"
---
# <a name="using-castlabs-to-deliver-widevine-licenses-to-azure-media-services"></a>castLabs を使用して Azure Media Services に Widevine ライセンスを配信する 
> [!div class="op_single_selector"]
> * [Axinom](media-services-axinom-integration.md)
> * [castLabs](media-services-castlabs-integration.md)
> 
> 

## <a name="overview"></a>概要

この記事では、PlayReady と Widevine DRM の両方を使用して AMS で動的に暗号化されたストリームを、Azure Media Services (AMS) を使用して配信する方法について説明します。 PlayReady ライセンスは Media Services PlayReady サーバーから取得し、Widevine ライセンスは **castLabs** ライセンス サーバーから取得します。

CENC (PlayReady または Widevine、あるいはその両方) によって保護されたストリーミング コンテンツを再生するには、[Azure Media Player](https://aka.ms/azuremediaplayer) を使用できます。 詳細については、 [AMP のドキュメント](https://amp.azure.net/libs/amp/latest/docs/) を参照してください。

次の図は、Azure Media Services と castLabs の統合アーキテクチャを大まかに示しています。

![統合](./media/media-services-castlabs-integration/media-services-castlabs-integration.png)

## <a name="typical-system-set-up"></a>一般的なシステムの設定

* メディア コンテンツは AMS に格納されます。
* コンテンツ キーのキー ID は castLabs と AMS の両方に格納されます。
* castLabs と AMS はどちらもトークン認証が組み込まれています。 次のセクションでは、認証トークンについて説明します。 
* クライアントからビデオのストリーミング要求があると、そのコンテンツが **Common Encryption** (CENC) によって動的に暗号化されたうえで、AMS によって Smooth Streaming や DASH として動的にパッケージ化されます。 また、HLS ストリーミング プロトコル用に PlayReady M2TS エレメンタリ ストリームの暗号化も行います。
* PlayReady ライセンスは AMS ライセンス サーバーから取得し、Widevine ライセンスは castLabs ライセンス サーバーから取得します。 
* Media Player ではクライアントのプラットフォームの機能に基づいて、フェッチするライセンスを自動的に決定します。 

## <a name="authentication-token-generation-for-getting-a-license"></a>ライセンスを取得するための認証トークンの生成

CastLabs と AMS の両方でライセンスの承認に使用する JWT (JSON Web Token) トークン形式をサポートしています。 

### <a name="jwt-token-in-ams"></a>AMS での JWT トークン

次の表では AMS での JWT トークンについて説明しています。 

| 発行者 | 選択したからセキュリティ トークン サービス (STS) から取得した発行者の文字列 |
| --- | --- |
| 対象ユーザー |使用される STS から取得した対象文字列 |
| Claims |一連の要求 |
| NotBefore |トークンの有効性開始 |
| Expires |トークンの有効性終了 |
| SigningCredentials |PlayReady ライセンス サーバー、castLabs ライセンス サーバー、STS の間でキーが共有されている場合、対称キーまたは非対称キーのどちらかになります。 |

### <a name="jwt-token-in-castlabs"></a>CastLabs での JWT トークン

次の表では castLabs での JWT トークンについて説明してます。 

| Name | 説明 |
| --- | --- |
| optData |ユーザーに関する情報が含まれる JSON 文字列 |
| crt |アセット、ライセンス情報、再生権に関する情報が含まれる JSON 文字列 |
| iat |エポックの現在の日時 |
| jti |このトークンの一意の識別子 (どのトークンも castLabs システムで 1 度しか使用できません) |

## <a name="sample-solution-setup"></a>サンプル ソリューションの設定

[サンプル ソリューション](https://github.com/AzureMediaServicesSamples/CastlabsIntegration) は次の 2 つのプロジェクトで構成されます。

* PlayReady と Widevine の両方について、既に取り込まれたアセットへの DRM 制限の設定に使用できるコンソール アプリケーション
* トークンを渡す Web アプリケーション (非常に簡略化された STS として表示される)

コンソール アプリケーションを使用するには、次の手順に従います。

1. app.config を変更して、AMS 資格情報、castLabs 資格情報、STS 構成、共有キーをセットアップします。
2. AMS にアセットをアップロードします。
3. アップロードされたアセットから UUID を取得し、Program.cs ファイルで行 32 を変更します。
   
      var objIAsset = _context.Assets.Where(x => x.Id == "nb:cid:UUID:dac53a5d-1500-80bd-b864-f1e4b62594cf").FirstOrDefault();
4. AssetId を使用して castLabs システムでアセットに名前を付けます (Program.cs ファイルでは行 44)。
   
   **castLabs** に AssetId を設定する必要があります。文字列には一意の英数字を使用してください。
5. プログラムを実行します。

Web アプリケーション (STS) を使用するには、次の手順に従います。

1. web.config を変更して、castlabs の商業 ID、STS 構成、共有キーを設定します。
2. Azure Websites にデプロイします。
3. Web サイトに移動します。

## <a name="playing-back-a-video"></a>ビデオを再生する

共通暗号化 (PlayReady または Widevine、あるいはその両方) で暗号化されたビデオを再生するには、[Azure Media Player](https://aka.ms/azuremediaplayer) を使用できます。 コンソール アプリケーションを実行すると、コンテンツ キー ID とマニフェスト URL がエコーされます。

1. 新しいタブを開き、STS を起動します。 http://[yourStsName].azurewebsites.net/api/token/assetid/[yourCastLabsAssetId]/contentkeyid/[thecontentkeyid]
2. [Azure Media Player](https://aka.ms/azuremediaplayer)に移動します。
3. ストリーミング URL に貼り付けます。
4. **[詳細オプション]** チェックボックスをクリックします。
5. **[保護]** ドロップダウンで、PlayReady や Widevine を選択します。
6. STS から取得したトークンを [トークン] テキストボックスに貼り付けます。 
   
   castLab ライセンス サーバーの場合は、トークンの前に “Bearer=” プレフィックスを置く必要はありません。 トークンを送信する前にプレフィックスを削除してください。
7. プレーヤーを更新します。
8. ビデオが再生されるはずです。

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. のサービス使用条件とプライバシー ポリシーが適用されます。

## <a name="media-services-learning-paths"></a>Media Services のラーニング パス
[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]

