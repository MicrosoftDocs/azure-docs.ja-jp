---
title: Verizon Standard から Verizon Premium に Azure CDN プロファイルを移行する
description: Verizon Standard から Verizon Premium へのプロファイルの移行について詳しく説明します。
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 06/21/2018
ms.author: allensu
ms.custom: ''
ms.openlocfilehash: e58c1177fb3c4d241a3efe1759b3900abbd04ca1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778386"
---
# <a name="migrate-an-azure-cdn-profile-from-standard-verizon-to-premium-verizon"></a>Standard Verizon から Premium Verizon に Azure CDN プロファイルを移行する

Azure Content Delivery Network (CDN) プロファイルを作成してエンドポイントを管理する場合、Azure CDN では 4 つの異なる製品から選択して使用できます。 各種の製品と使用可能な機能については、「[Azure CDN 製品の機能を比較する](cdn-features.md)」をご覧ください。

**Azure CDN Standard from Verizon** プロファイルを作成し、CDN エンドポイントの管理にそれを使用している場合、**Azure CDN Premium from Verizon** プロファイルにアップグレードすることができます。 アップグレードを行うと、CDN エンドポイントとすべてのデータが保持されます。 

> [!IMPORTANT]
> **Azure CDN Premium from Verizon** プロファイルにアップグレードしたら、後でそれを **Azure CDN Standard from Verizon** プロファイルに戻すことはできません。
> 

**Azure CDN Standard from Verizon** プロファイルをアップグレードするには、[Microsoft サポート](https://azure.microsoft.com/support/options/)に問い合わせます。

## <a name="profile-comparison"></a>プロファイルの比較
**Azure CDN Premium from Verizon** プロファイルは、主に次の点で **Azure CDN Standard from Verizon** プロファイルと異なります。
- [圧縮](cdn-improve-performance.md)、[キャッシュ規則](cdn-caching-rules.md)、[geo フィルタリング](cdn-restrict-access-by-country.md)など、一部の Azure CDN 機能について、Azure CDN インターフェイスを使用できません。**[管理]** ボタン経由で Verizon ポータルを使用する必要があります。
- API: Standard Verizon と異なり、Premium Verizon ポータルからアクセスされるこれらの機能の制御には、API を使用することができません。 ただし、エンドポイントの作成/削除、キャッシュされた資産の消去/読み込み、カスタム ドメインの有効化/無効化など、その他の一般的な機能を制御するために API を使用することはできます。
- 価格: Premium Verizon では、データ転送に関する料金体系が Standard Verizon と異なります。 詳細については、「[Content Delivery Network の価格](https://azure.microsoft.com/pricing/details/cdn/)」をご覧ください。

**Azure CDN Premium from Verizon** プロファイルには、次の追加の機能があります。
- [トークン認証](cdn-token-auth.md): ユーザーが安全なリソースを取り込むためにトークンを取得して使用できるようにします。
- [ルール エンジン](./cdn-verizon-premium-rules-engine.md): HTTP 要求を処理する方法をカスタマイズできるようにします。
- 高度な分析ツール:
   - [詳細な HTTP 分析](cdn-advanced-http-reports.md)
   - [エッジ パフォーマンス分析](cdn-edge-performance.md)
   - [リアルタイム分析](cdn-real-time-alerts.md)


## <a name="next-steps"></a>次のステップ
ルール エンジンの詳細については、「[Azure CDN ルール エンジンのリファレンス](./cdn-verizon-premium-rules-engine-reference.md)」をご覧ください。