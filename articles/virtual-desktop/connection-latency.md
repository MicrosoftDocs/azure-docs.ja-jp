---
title: Windows Virtual Desktop ユーザー接続の待機時間 - Azure
description: Windows Virtual Desktop ユーザー用の接続の待機時間
author: Heidilohr
ms.topic: conceptual
ms.date: 10/30/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: bb0ee52d37fe901a610723d5864240b8778d8b17
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103574596"
---
# <a name="determine-user-connection-latency-in-windows-virtual-desktop"></a>Windows Virtual Desktop でのユーザー接続の待機時間の決定

Windows Virtual Desktop はグローバルに使用できます。 管理者は、必要な任意の Azure リージョンに仮想マシン (VM) を作成できます。 接続の待機時間は、ユーザーと仮想マシンの場所によって異なります。 Windows Virtual Desktop サービスは、待機時間を短縮するために、新しい地域に継続的にロールアウトされます。

[Windows Virtual Desktop エクスペリエンス見積もりツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)を使用すると、VM の待機時間を最適化するための最適な場所を判断できます。 Windows Virtual Desktop が新しい領域にロールアウトされるときに、最適な場所が変更されていないことを確認するために、2 - 3 か月おきにこのツールを使用することをお勧めします。

## <a name="interpreting-results-from-the-windows-virtual-desktop-experience-estimator-tool"></a>Windows Virtual Desktop エクスペリエンス予測ツールの結果を解釈する

Windows Virtual Desktop では、150 ms までの待機時間は、レンダリングもビデオも含まないユーザー エクスペリエンスに影響を与えないはずです。 テキスト処理では、150 ms から 200 ms の間の待機時間は問題ありません。 200 ms を超える待機時間は、ユーザー エクスペリエンスに影響を与える場合があります。 

さらに、Windows Virtual Desktop 接続は、ユーザーがサービスを使用しているマシンのインターネット接続に依存します。 次のいずれかの状況で、ユーザーは接続ができなくなるか、入力遅延が発生するおそれがあります。

 - ユーザーが安定したローカル インターネット接続を確立しておらず、待機時間が 200 ms を超えている。
 - ネットワークが飽和状態であるか、レート制限されている。

可能な限りご自分のユーザーの近くにある VM の場所を選択することをお勧めします。 たとえば、ユーザーがインドにいるのに VM が米国にある場合、ユーザー エクスペリエンス全体に影響を与える待機時間が発生します。 

## <a name="azure-front-door"></a>Azure Front Door

Windows Virtual Desktop は、[Azure Front Door](https://azure.microsoft.com/services/frontdoor/) を使用して、ソース IP アドレスに基づいて最も近い Windows Virtual Desktop ゲートウェイにユーザー接続をリダイレクトします。 Windows Virtual Desktop では、クライアントが選択した Windows Virtual Desktop ゲートウェイが常に使用されます。

## <a name="next-steps"></a>次のステップ

- 最適な待機時間のための最適な場所を確認するには、「[Windows Virtual Desktop エクスペリエンス見積もりツール](https://azure.microsoft.com/services/virtual-desktop/assessment/)」を参照してください。
- 価格プランの詳細については、「[Windows Virtual Desktop の価格](https://azure.microsoft.com/pricing/details/virtual-desktop/)」を参照してください。
- Windows Virtual Desktop のデプロイを開始するには、[こちらのチュートリアル](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-azure-marketplace)を確認してください。
