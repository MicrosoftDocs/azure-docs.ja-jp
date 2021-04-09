---
title: Media Services v2 から v3 への移行の概要
description: この記事では、Media Services v2 から v3 への移行の概要について説明します。
services: media-services
author: IngridAtMicrosoft
manager: femila
ms.service: media-services
ms.topic: conceptual
ms.workload: media
ms.date: 1/14/2020
ms.author: inhenkel
ms.openlocfilehash: 3514a7c809e939ea2c45afa5ab60539232b8781f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98953293"
---
# <a name="migrate-from-media-services-v2-to-v3-introduction"></a>Media Services v2 から v3 への移行の概要

![移行ガイドのロゴ](./media/migration-guide/azure-media-services-logo-migration-guide.svg)

Media Services 移行ガイドでは、現在利用可能な新機能を活用した移行に基づいて、Media Services V2 API から V3 API に移行する方法について説明します。 お客様のシナリオに最も適した方法をお客様の判断に基づいてお選びください。

## <a name="how-to-use-this-guide"></a>このガイドの使用方法

### <a name="navigating"></a>ナビゲーション

ガイド全体を通じて、次の図が表示されます。

![移行の手順](./media/migration-guide/steps.svg)<br/>

次のように、実行中の手順が手順番号の色の違いで示されます。

![移行の手順 2](./media/migration-guide/steps-2.svg)<br/>

各ページの最後に、**次の手順** の見出しの下の残りの移行ドキュメントへのリンクが表示されます。

### <a name="guidance"></a>ガイダンス

ここで紹介するガイダンスは *全般* です。 これには、V3 で使用できるようになった機能、および Media Services ワークフローの変更内容を認識しやすくするコンテンツが含まれています。

スクリーンショットや概念図などの詳細なガイダンスについては、各シナリオベースのトピックの概念、チュートリアル、クイックスタート、サンプル、API リファレンスへのリンクがあります。 V2 API を V3 API と比較するのに役立つサンプルも示しています。

## <a name="migration-guidance-overview"></a>移行ガイダンスの概要

移行中は、次の 4 つの一般的な手順を実行します。

## <a name="step-1-benefits"></a>手順 1 利点

<hr color="#5ea0ef" size="10">

Media Services API V3 への移行の[利点について説明します](migrate-v-2-v-3-migration-benefits.md)。

## <a name="step-2-differences"></a>手順 2 相違点

<hr color="#5ea0ef" size="10">

Media Services V2 API と V3 API の違いについて説明します。

- [API アクセス](migrate-v-2-v-3-differences-api-access.md)
- [機能のギャップ](migrate-v-2-v-3-differences-feature-gaps.md)
- [用語とエンティティの変更](migrate-v-2-v-3-differences-terminology.md)

## <a name="step-3-sdk-setup"></a>手順 3 SDK のセットアップ

<hr color="#5ea0ef" size="10">

SDK の相違点について説明し [V3 REST API またはクライアント SDK に移行するように設定します](migrate-v-2-v-3-migration-setup.md)。

## <a name="step-4-scenario-based-guidance"></a>手順 4 シナリオベースのガイダンス

<hr color="#5ea0ef" size="10">

Media Services V2 のアプリケーションが、独自のものである場合もあります。 そのため、過去にメディア サービスを使用したと "*思われる*" 方法に基づくシナリオベースのガイダンス、および下記のようなサービスの各機能についての手順を提供しています。

- [[エンコード]](migrate-v-2-v-3-migration-scenario-based-encoding.md)
- [ライブ ストリーミング](migrate-v-2-v-3-migration-scenario-based-live-streaming.md)
- [パッケージとデリバリー](migrate-v-2-v-3-migration-scenario-based-publishing.md)
- [コンテンツ保護](migrate-v-2-v-3-migration-scenario-based-content-protection.md)
- [メディア占有ユニット (MRU)](migrate-v-2-v-3-migration-scenario-based-media-reserved-units.md)

## <a name="next-steps"></a>次のステップ

[!INCLUDE [migration guide next steps](./includes/migration-guide-next-steps.md)]
