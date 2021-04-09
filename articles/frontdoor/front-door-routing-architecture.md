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
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 948cf3c65dfdc912f2f807dfac34076985f1bc89
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449189"
---
# <a name="routing-architecture-overview"></a>ルーティング アーキテクチャの概要

Azure Front Door がクライアント要求を受け取ると、次の 2 つのいずれかが実行されます。 キャッシュを有効にしている場合は応答します。または、リバース プロキシとして適切なアプリケーション バックエンドに転送します。

## <a name="selecting-the-front-door-environment-for-traffic-routing-anycast"></a><a name = "anycast"></a>トラフィック ルーティング (エニーキャスト) に対する Front Door 環境の選択

Azure Front Door 環境にルーティングされるトラフィックでは、DNS (ドメイン ネーム システム) トラフィックでも HTTP (ハイパーテキスト転送プロトコル) トラフィックでも[エニーキャスト](https://en.wikipedia.org/wiki/Anycast)が使用されるので、ユーザー要求は最も少ないネットワーク ホップで最も近い環境に到達します。 このアーキテクチャでは、スプリット TCP の利点の最大化することによって、エンド ユーザーに対するラウンドトリップ時間が向上します。 Front Door では、環境がプライマリおよびフォールバックの "リング" に編成されます。 外部リングの環境の方がユーザーに近く、待機時間が短くなります。  内部リングは、何らかの問題が発生したときに、外部リング環境に対するフェールオーバーを処理できる環境です。 外部リングはすべてのトラフィックに対して優先されるターゲットであり、内部リングは外部リングからのトラフィック オーバーフローを処理するために使用されます。 Front Door によって提供される各フロントエンド ホストまたはドメインには、プライマリ VIP (仮想インターネット プロトコル アドレス) が割り当てられます。これは、内部および外部リングの両方の環境でアナウンスされます。 フォールバック VIP は、内部リング内の環境によってのみアナウンスされます。 

このアーキテクチャにより、エンド ユーザーからの要求が常に最も近い Front Door 環境に到着します。 優先される Front Door 環境が異常な場合でも、すべてのトラフィックは次に最も近い環境に自動的に転送されます。

## <a name="connecting-to-front-door-environment-split-tcp"></a><a name = "splittcp"></a>Front Door 環境への接続 (分割 TCP)

[分割 TCP](https://en.wikipedia.org/wiki/Performance-enhancing_proxy) は、長いラウンドトリップ時間が発生する接続を小さく分割することによって待機時間と TCP の問題を軽減する手法です。 Front Door 環境がエンド ユーザーに近い場合は、Front Door 環境内で TCP 接続が終了します。 アプリケーション バックエンドに対するラウンドトリップ時間 (RTT) が長い TCP 接続は、2 つの個別の接続に分割されます。 エンド ユーザーと Front Door 環境の間の "短い接続" は、接続が 3 つの長いラウンドトリップではなく 3 つの短いラウンドトリップを介して確立され、待機時間が短縮されることを意味します。 Front Door 環境とバックエンドの間の "長い接続" は、事前に確立して、他のエンド ユーザーの要求間で再利用できるため、接続時間が短縮されます。 SSL/TLS (トランスポート層セキュリティ) 接続を確立すると、接続をセキュリティで保護するラウンドトリップが増えるため、スプリット TCP の効果が相乗されます。

## <a name="processing-request-to-match-a-routing-rule"></a>要求とルーティング規則の一致処理
接続を確立し、TLS ハンドシェイクを完了した後、要求が Front Door 環境に到着すると、最初の手順としてルーティング規則との照合が行われます。 この照合は、要求をどの特定のルーティング規則に照合するかという Front Door の構成によって判断されます。 詳しくは、Front Door が[ルート照合](front-door-route-matching.md)を行う方法についてのページをご覧ください。

## <a name="identifying-available-backends-in-the-backend-pool-for-the-routing-rule"></a>ルーティング規則に対するバックエンド プールで使用可能なバックエンドの識別
Front Door が受信要求のルーティング規則と一致したら、次の手順は、キャッシュがない場合、ルーティング規則に関連付けられているバックエンド プールの正常性プローブの状態を取得することです。 詳しくは、Front Door が[正常性プローブ](front-door-health-probes.md)を使用してバックエンドの正常性を監視する方法に関するページをご覧ください。

## <a name="forwarding-the-request-to-your-application-backend"></a>アプリケーション バックエンドへの要求の転送
最後に、キャッシュが構成されていないものとすると、ユーザー要求は、[ルーティング方法](front-door-routing-methods.md)の構成に基づいて "最善" のバックエンドに転送されます。

## <a name="next-steps"></a>次のステップ

- [フロント ドアの作成](quickstart-create-front-door.md)方法について学習します。
