---
title: Azure Front Door - ルーティング アーキテクチャ | Microsoft Docs
description: この記事では、Front Door のアーキテクチャの全体像について説明します。
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/10/2018
ms.author: duau
ms.openlocfilehash: b36852e27f6aa3a909dd645c19a12c55e082b761
ms.sourcegitcommit: 5a3b9f35d47355d026ee39d398c614ca4dae51c6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/02/2020
ms.locfileid: "89399329"
---
# <a name="routing-architecture-overview"></a>ルーティング アーキテクチャの概要

Azure Front Door は、クライアントの要求を受け取ると、応答するか (キャッシュが有効になっている場合)、または適切なアプリケーション バックエンドに転送します (リバース プロキシとして)。

</br>Azure Front Door にルーティングするとき、およびバックエンドにルーティングするときに、トラフィックを最適化する機会があります。

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>トラフィック ルーティング (エニーキャスト) に対する Front Door 環境の選択

Azure Front Door 環境へのルーティングでは、DNS (ドメイン ネーム システム) トラフィックでも HTTP (ハイパーテキスト転送プロトコル) トラフィックでも[エニーキャスト](https://en.wikipedia.org/wiki/Anycast)が使用されるので、ユーザー トラフィックはネットワーク トポロジの観点から最も近い(最小ホップ数の) 環境に送られます。 通常、このアーキテクチャでは、エンド ユーザーに対するラウンドトリップ時間が向上します (スプリット TCP の利点の最大化)。 Front Door では、環境がプライマリおよびフォールバックの "リング" に編成されます。  外部リングの環境の方がユーザーに近く、待機時間が短くなります。  内部リングは、問題が発生したときに、外部リング環境に対するフェールオーバーを処理できる環境です。 外部リングはすべてのトラフィックに対する優先ターゲットですが、内部リングは外部リングからのトラフィック オーバーフローを処理するために必要です。 VIP (仮想インターネット プロトコル アドレス) に関しては、各フロントエンド ホストつまり Front Door によって提供されるドメインには、プライマリ VIP (内部リングと外部リングの両方の環境でアナウンスされます) とフォールバック VIP (内部リングの環境によってのみアナウンスされます) が割り当てられます。 

</br>この全体的な戦略により、エンド ユーザーからの要求は常に最も近い Front Door に到達すること、および優先 Front Door 環境が正常でない場合であってもトラフィックは次に近い環境に自動的に移動することが保証されます。

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Front Door 環境への接続 (分割 TCP)

[分割 TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) は、長いラウンドトリップ時間が発生する接続を小さく分割することによって待機時間と TCP の問題を軽減する手法です。  Front Door 環境をエンド ユーザーの近くに配置し、Front Door 環境内で TCP 接続を終了することにより、アプリケーション バックエンドに対するラウンドトリップ時間 (RTT) が長い 1 つの TCP 接続が、2 つの TCP 接続に分割されます。 エンド ユーザーと Front Door 環境の間の接続が短いということは、接続が 3 つの長いラウンドトリップではなく 3 つの短いラウンドトリップに対して確立され、待機時間が短縮されることを意味します。  Front Door 環境とバックエンドの間の長い接続は、事前に確立して、複数のエンド ユーザー呼び出しで再利用できるので、やはり TCP 接続時間が短縮されます。  SSL/TLS (トランスポート層セキュリティ) 接続を確立するとラウンドトリップが増えて接続がセキュリティ保護されるので、効果が相乗されます。

## <a name="processing-request-to-match-a-routing-rule"></a>要求とルーティング規則の一致処理
接続を確立し、TLS ハンドシェイクを実行した後、要求が Front Door 環境に到着すると、最初の手順としてルーティング規則の照合が行われます。 この一致は基本的に Front Door のすべての構成から決定され、要求が一致する特定のルーティング規則です。 詳しくは、Front Door が[ルート照合](front-door-route-matching.md)を行う方法についてのページをご覧ください。

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>ルーティング規則に対するバックエンド プールで使用可能なバックエンドの識別
Front Door に受信した要求に基づくルーティング規則との一致が存在し、キャッシュがない場合、次のステップは、一致したルートに関連付けられているバックエンド プールの正常性プローブの状態を取得することです。 詳しくは、Front Door が[正常性プローブ](front-door-health-probes.md)を使用してバックエンドの正常性を監視する方法に関するページをご覧ください。

## <a name="forwarding-the-request-to-your-application-backend"></a>アプリケーション バックエンドへの要求の転送
最後に、キャッシュが構成されていないものとすると、ユーザー要求は、[Front Door のルーティング方法](front-door-routing-methods.md)の構成に基づいて "最善" のバックエンドに転送されます。

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
