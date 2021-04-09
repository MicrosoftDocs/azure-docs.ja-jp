---
title: Peering Service - よくあるご質問
titleSuffix: Azure
description: Peering Service - よくあるご質問
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: reference
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 507e503b881df123ffc6694c53b0e9cc9b6a8872
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "75774026"
---
# <a name="peering-service---faqs"></a>Peering Service - よくあるご質問

一般的な質問については、以下の情報を参照してください。

**通信事業者は Microsoft との既存の Direct ピアリングを使用して Peering Service をサポートできますか?**

はい。通信事業者は、既存の PNI を利用して Peering Service をサポートできます。 HA をサポートするために、Peering Service PNI には多様性が必要です。 既存の PNI に既に多様性がある場合、新しいインフラストラクチャは必要ありません。 既存の PNI に多様性が必要な場合は、それを拡張できます。

**通信事業者は Peering Service をサポートするために Microsoft との新しい Direct ピアリングを使用できますか?**

はい。これも可能です。 Microsoft は通信事業者と連携し、Peering Service をサポートする新しい Direct ピアリングを作成します。  

**Direct ピアリングが Peering Service をサポートする要件であるのはなぜですか?**

Peering Service の背後にある主な要因の 1 つは、適切に接続された SP を介して Microsoft オンライン サービスへの接続を提供することです。 PNI は、常に Gbps の範囲にあるため、通信事業者と Microsoft 間の高スループット接続の基本的な構成要素です。

**Peering Service をサポートするには、Direct ピアリングにどのような多様性の要件がありますか?**

PNI は、ローカル冗長性と geo冗長性をサポートする必要があります。 ローカル冗長性は、特定のピアリング サイトの 2 つの異なるパスのセットであると定義されます。 geo 冗長性を確保するには、プライマリ サイトで障害が発生した場合に備えて、通信事業者が別の Microsoft Edge サイトで追加の接続を確立する必要があります。 障害が短時間の場合、通信事業者はバックアップ サイトを介してトラフィックをルーティングできます。

**通信事業者は SLA とエンタープライズ グレードのインターネットを既に提供していますが、このオファリングはどのような違いがありますか?**

一部の通信事業者は、ネットワークの一部で SLA とエンタープライズ グレードのインターネットを提供しています。 Microsoft は、Peering Service で、Microsoft のネットワークの一部に SLA オファー トラフィックを提供します。 Peering Service を選択すると、お客様はエンドツーエンドの SLA を得られます。 ご自分のサイトから ISP ネットワーク上の Microsoft Edge までの SLA は、ISP によってカバーされます。 Microsoft Edge からエンド ユーザー アプリケーションへの Microsoft グローバル ネットワークの SLA は、現在、Microsoft によってカバーされています。

**サービス プロバイダーが既に PNI を使用して Microsoft とピアリングしている場合、Peering Service をサポートするにはどのような変更が必要ですか?**

* Peering Service ユーザーとそのトラフィックを識別するためのソフトウェアの変更。 Peering Service 接続を介して最も近い Microsoft Edge でユーザーのトラフィックを交換するには、ルーティング ポリシーの変更が必要になる場合があります。
* 接続にローカル冗長性と geo 冗長性があることを確認してください。
