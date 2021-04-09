---
title: Azure Media Services を使用した DRM サブシステムのハイブリッド設計 | Microsoft Docs
description: このトピックでは、Azure Media Services を使用した DRM サブシステムのハイブリッド設計について説明します。
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.assetid: 18213fc1-74f5-4074-a32b-02846fe90601
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/10/2021
ms.author: willzhan
ms.reviewer: juliako
ms.openlocfilehash: a48e761c4fb74802c6b1db63884ee192446720a4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103016272"
---
# <a name="hybrid-design-of-drm-subsystems"></a>DRM サブシステムのハイブリッド設計

[!INCLUDE [media services api v2 logo](./includes/v2-hr.md)]

このトピックでは、Azure Media Services を使用した DRM サブシステムのハイブリッド設計について説明します。

## <a name="overview"></a>概要

Azure Media Services は、次の 3 つの DRM システムのサポートを提供します。

* PlayReady
* Widevine (モジュール)
* FairPlay

DRM サポートには、ブラウザー プレーヤー SDK として 3 つすべての DRM をサポートする Azure Media Player による、DRM 暗号化 (動的暗号化) とライセンス配信が含まれます。

DRM/CENC サブシステムの設計と実装の詳細な処理については、「[CENC とマルチ DRM および Access Control](media-services-cenc-with-multidrm-access-control.md)」というタイトルのドキュメントをご覧ください。

Microsoft は 3 つの DRM システムの完全なサポートを提供しますが、ハイブリッド DRM サブシステムを構築するには、Azure Media Services だけでなく、お客様独自のインフラストラクチャ/サブシステムのさまざまな部分を使用する必要が生じることがあります。

以下に、お客様からよく寄せられる質問をいくつか挙げます。

* "自社の DRM ライセンス サーバーを使えますか?" (この場合、お客様は既に埋め込みのビジネス ロジックを含む DRM ライセンス サーバー ファームに投資されています)。
* "AMS でコンテンツをホストせずに、Microsoft の DRM ライセンス配信だけを Azure Media Services で使うことはできますか?"

## <a name="modularity-of-the-ams-drm-platform"></a>AMS DRM プラットフォームのモジュール性

包括的なクラウド ビデオ プラットフォームの一部として、Azure Media Services DRM は、柔軟性とモジュール性を考慮して設計されています。 Azure Media Services は、以下の表に示す次のさまざまな組み合わせのいずれでも使用できます (表で使用している表記の説明を下に示します) 。 

|**コンテンツ ホスティングおよび配信元**|**コンテンツ暗号化**|**DRM ライセンス配信**|
|---|---|---|
|AMS|AMS|AMS|
|AMS|AMS|サード パーティ|
|AMS|サード パーティ|AMS|
|AMS|サード パーティ|サード パーティ|
|サード パーティ|サード パーティ|AMS|

### <a name="content-hosting--origin"></a>コンテンツ ホスティングおよび配信元

* AMS: ビデオ資産が AMS でホストされ、ストリーミングは AMS のストリーミング エンドポイントを経由します (ただし、必ずしもダイナミック パッケージではありません)。
* サード パーティ: ビデオは AMS の外部のサード パーティ製のストリーミング プラットフォームでホストされ配信されます。

### <a name="content-encryption"></a>コンテンツ暗号化

* AMS: コンテンツの暗号化は、AMS の動的暗号化によって、動的に、オンデマンドで実行されます。
* サード パーティ: コンテンツの暗号化は、処理前のワークフローを使用して AMS の外部で実行されます。

### <a name="drm-license-delivery"></a>DRM ライセンス配信

* AMS: DRM ライセンスは AMS ライセンス配信サービスによって配信されます。
* サードパーティ: DRM ライセンスは AMS の外部のサード パーティ製 DRM ライセンス サーバーによって配信されます。

## <a name="configure-based-on-your-hybrid-scenario"></a>ハイブリッド シナリオに応じた構成

### <a name="content-key"></a>コンテンツ キー

コンテンツ キーを構成することで、AMS 動的暗号化と AMS ライセンス配信サービス両方の次の属性を制御できます。

* 動的 DRM 暗号化に使用するコンテンツ キー。
* ライセンス配信サービスによって配信される DRM ライセンス コンテンツ: 権限、コンテンツ キー、および制限。
* **コンテンツ キー承認ポリシー制限** のタイプ: オープン、IP、またはトークン制限。
* **トークン** タイプの **コンテンツ キー承認ポリシー制限を使用する** 場合、**コンテンツ キー承認ポリシー制限** はライセンス発行前に満たす必要があります。

### <a name="asset-delivery-policy"></a>資産の配信ポリシー

資産配信ポリシーを構成することで、AMS ダイナミック パッケージャーおよび AMS ストリーミング エンドポイントの動的暗号化で使用される次の属性を制御できます。

* ストリーミング プロトコルと DRM 暗号化の組み合わせ。CENC と DASH (PlayReady および Widevine)、PlayReady とスムーズ ストリーミング、Widevine または PlayReady と HLS など。
* 関係する各 DRM の既定/埋め込みのライセンス配信 URL。
* DASH MPD または HLS 再生リストのライセンスの取得 URL (LA_URL) に Widevine および FairPlay それぞれのキー ID (KID) のクエリ文字列を含めるかどうか。

## <a name="scenarios-and-samples"></a>シナリオとサンプル

前のセクションの説明に基づき、次の 5 つのハイブリッド シナリオでは、それぞれの **コンテンツ キー**-**資産配信ポリシー** 構成の組み合わせを使用します (最後の列に示したサンプルが表の後に続きます)。

|**コンテンツ ホスティングおよび配信元**|**DRM 暗号化**|**DRM ライセンス配信**|**コンテンツ キーの構成**|**資産配信ポリシーを構成する**|**サンプル**|
|---|---|---|---|---|---|
|AMS|AMS|AMS|はい|はい|サンプル 1|
|AMS|AMS|サード パーティ|はい|はい|サンプル 2|
|AMS|サード パーティ|AMS|はい|いいえ|サンプル 3|
|AMS|サード パーティ|外部|いいえ|いいえ|サンプル 4|
|サード パーティ|サード パーティ|AMS|はい|いいえ|    

サンプルでは、PlayReady 保護は、DASH とスムーズ ストリーミングの両方に対して機能します。 下記のビデオ URL は、スムーズ ストリーミング URL です。 対応する DASH URL を取得するには、"(format=mpd-time-csf)" を追加するだけです。 [Azure のメディア テスト プレーヤー](https://aka.ms/amtest)を使って、ブラウザーでテストすることもできます。 どのテクノロジでも、使用するストリーミング プロトコルを構成できます。 Windows 10 の IE11 および Microsoft Edge が EME を通じて PlayReady をサポートします。 詳細については、[テスト ツールの詳細](./offline-playready-streaming-windows-10.md)に関するページをご覧ください。

### <a name="sample-1"></a>サンプル 1

* ソース (ベース) URL: `https://willzhanmswest.streaming.mediaservices.windows.net/1efbd6bb-1e66-4e53-88c3-f7e5657a9bbd/RussianWaltz.ism/manifest` 
* PlayReady LA_URL (DASH & smooth): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 
* Widevine LA_URL (DASH): `https://willzhanmswest.keydelivery.mediaservices.windows.net/Widevine/?kid=78de73ae-6d0f-470a-8f13-5c91f7c4` 
* FairPlay LA_URL (HLS): `https://willzhanmswest.keydelivery.mediaservices.windows.net/FairPlay/?kid=ba7e8fb0-ee22-4291-9654-6222ac611bd8` 

### <a name="sample-2"></a>サンプル 2

* ソース (ベース) URL: https://willzhanmswest.streaming.mediaservices.windows.net/1a670626-4515-49ee-9e7f-cd50853e41d8/Microsoft_HoloLens_TransformYourWorld_816p23.ism/Manifest 
* PlayReady LA_URL (DASH & smooth): `http://willzhan12.cloudapp.net/PlayReady/RightsManager.asmx` 

### <a name="sample-3"></a>サンプル 3

* ソース URL: https://willzhanmswest.streaming.mediaservices.windows.net/8d078cf8-d621-406c-84ca-88e6b9454acc/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & smooth): `https://willzhanmswest.keydelivery.mediaservices.windows.net/PlayReady/` 

### <a name="sample-4"></a>サンプル 4

* ソース URL: https://willzhanmswest.streaming.mediaservices.windows.net/7c085a59-ae9a-411e-842c-ef10f96c3f89/20150807-bridges-2500.ism/manifest 
* PlayReady LA_URL (DASH & smooth): `https://willzhan12.cloudapp.net/playready/rightsmanager.asmx` 

## <a name="additional-notes"></a>その他のメモ

* Widevine は Google Inc. によって提供されるサービスであり、Google Inc. の利用規約とプライバシー ポリシーが適用されます。

## <a name="summary"></a>まとめ

要約すると、Azure Media Services DRM コンポーネントは柔軟性が高いため、このトピックで説明したとおりにコンテンツ キーと資産配信ポリシーを正しく構成すれば、ハイブリッド シナリオで使用できます。

## <a name="next-steps"></a>次のステップ
Media Services のラーニング パスを確認します。

[!INCLUDE [media-services-learning-paths-include](../../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>フィードバックの提供
[!INCLUDE [media-services-user-voice-include](../../../includes/media-services-user-voice-include.md)]