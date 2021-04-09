---
title: コンテンツ保護の移行ガイダンス
description: この記事では、Azure Media Services v2 から v3 への最小移行に役立つコンテンツ保護のシナリオベースのガイダンスを提供します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 9c0040c0ad34b019eaa90bbd1571377ad3539578
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98940121"
---
# <a name="content-protection-scenario-based-migration-guidance"></a>コンテンツ保護のシナリオベースの移行ガイダンス

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

<hr color="#5ea0ef" size="10">

![移行の手順 2](./media/migration-guide/steps-4.svg)

この記事では、Azure Media Services v2 から v3 への最小移行に役立つコンテンツ保護のシナリオベースのガイダンスを提供します。

## <a name="protect-content-in-v3-api"></a>v3 API でのコンテンツの保護

新しい v3 API での[マルチキー](design-multi-drm-system-with-access-control.md)機能のサポートを使用します。

具体的な手順については、下の「コンテンツ保護の概念、チュートリアル、およびハウツー ガイド」を参照してください。

## <a name="content-protection-concepts-tutorials-and-how-to-guides"></a>コンテンツ保護の概念、チュートリアル、およびハウツー ガイド

### <a name="concepts"></a>概念

- [Media Services 動的暗号化を使用してコンテンツを保護する](content-protection-overview.md)
- [アクセス制御を使用したマルチ DRM コンテンツ保護システムの設計](design-multi-drm-system-with-access-control.md)
- [Media Services v3 と PlayReady ライセンス テンプレート](playready-license-template-overview.md)
- [Widevine ライセンス テンプレートを使用した Media Services v3 の概要](widevine-license-template-overview.md)
- [Apple FairPlay ライセンスの要件と構成](fairplay-license-overview.md)
- [ストリーミング ポリシー](streaming-policy-concept.md)
- [コンテンツ キー ポリシー](content-key-policy-concept.md)

### <a name="tutorials"></a>チュートリアル

[クイック スタート:ポータルを使用してコンテンツを暗号化する](encrypt-content-quickstart.md)

### <a name="how-to-guides"></a>ハウツー ガイド

- [既存のポリシーから署名キーを取得する](get-content-key-policy-dotnet-howto.md)
- [Media Services v3 を使用した iOS 用のオフラインの FairPlay ストリーミング](offline-fairplay-for-ios.md)
- [Media Services v3 を使用した Android 用のオフラインの Widevine ストリーミング](offline-widevine-for-android.md)
- [Media Services v3 を使用した Windows 10 用のオフラインの PlayReady ストリーミング](offline-plaready-streaming-for-windows-10.md)

## <a name="samples"></a>サンプル

[V2 と V3 のコードをコード サンプルで比較する](migrate-v-2-v-3-migration-samples.md)こともできます。

## <a name="next-steps"></a>次のステップ

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]