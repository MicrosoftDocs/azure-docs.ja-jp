---
title: "内部ロード バランサーの概要 | Microsoft Docs"
description: "内部ロード バランサーとその機能の概要。Azure でのロード バランサーの機能と内部エンドポイントを構成するための考えられるシナリオ"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: tysonn
ms.assetid: 36065bfe-0ef1-46f9-a9e1-80b229105c85
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/24/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 47869775365ea517b94cbd5a2eb83c93f4d2b4df
ms.openlocfilehash: 17b7337ddcfa2671bb3a035de8462e31bfa0c85f
ms.lasthandoff: 02/15/2017

---

# <a name="internal-load-balancer-overview"></a>内部ロード バランサーの概要

インターネットに接続するロード バランサーとは異なり、内部ロード バランサー (ILB) はクラウド サービス内のリソースにのみトラフィックを送信するか、VPN を使用して Azure インフラストラクチャにアクセスします。 インフラストラクチャは、クラウド サービスまたは仮想ネットワークの負荷分散された仮想 IP アドレス (VIP) へのアクセスを制限することで、インターネット エンドポイントに VIP が直接公開されないようにします。 これにより、内部の基幹業務 (LOB) アプリケーションを Azure で実行し、クラウド内やオンプレミスのリソースからアクセスできるようになります。

## <a name="why-you-may-need-an-internal-load-balancer"></a>内部ロード バランサーが必要な理由

Azure 内部負荷分散 (ILB) は、リージョン スコープを持つクラウド サービスまたは仮想ネットワーク内にある仮想マシン間に負荷分散を提供します。 リージョン スコープを持つ仮想ネットワークの使用と構成については、Azure ブログの「 [Regional Virtual Networks (リージョン仮想ネットワーク)](https://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) 」をご覧ください。 アフィニティ グループに構成されている既存の仮想ネットワークは ILB を使用できません。

ILB により、次の種類の負荷分散が可能になります。

* クラウド サービス内では、仮想マシンから、同じクラウド サービス内に存在する仮想マシン セットへ (図 1 を参照)。
* 仮想ネットワーク内では、仮想ネットワーク内の仮想マシンから、仮想ネットワークの同じクラウド サービス内に存在する仮想マシン セットへ (図 2 を参照)。
* クロスプレミス仮想ネットワークでは、オンプレミスのコンピューターから、仮想ネットワークの同じクラウド サービス内に存在する仮想マシン セットへ (図 3 を参照)。
* インターネットに接続された多層アプリケーションでは、バックエンド層はインターネットに接続されていませんが、インターネットに接続された層からのトラフィックを負荷分散する必要があります。
* 負荷分散用の追加のハードウェアやソフトウェアを必要とせずに、Azure でホストされている LOB アプリケーションの負荷を分散する。 オンプレミスのサーバーを含む一連のコンピューターの負荷を分散する。

## <a name="internet-facing-multi-tier-applications"></a>インターネットに接続する多層アプリケーション

Web 層はインターネット クライアント用のインターネットに接続するエンドポイントを持ち、負荷分散セットの一部です。 ロード バランサーは、TCP ポート 443 (HTTPS) の Web クライアントから Web サーバーに着信トラフィックを分散します。

データベース サーバーは、Web サーバーがストレージに使用する ILB エンドポイントの背後にあります。 このデータベース サービスの負荷分散エンドポイントでは、トラフィックが ILB セット内のデータベース サーバー全体に負荷分散されます。

次の図は、同じクラウド サービス内にある、インターネットに接続する多層アプリケーションを示しています。

![内部負荷分散の&1; つのクラウド サービス](./media/load-balancer-internal-overview/IC736321.png)

図 1 - インターネットに接続する多層アプリケーション

多層アプリケーションの考えられるもう&1; つのシナリオは、ILB が ILB のサービスを使用するクラウド サービスとは異なるクラウド サービスにデプロイされている場合です。

同じ仮想ネットワークを使用するクラウド サービスは、ILB エンドポイントにアクセスできます。 次の図は、フロントエンド Web サーバーがデータベース バックエンドとは異なるクラウド サービス内にあり、同じ仮想ネットワーク内の ILB エンドポイントを使用していることを示しています。

![クラウド サービス間の内部負荷分散](./media/load-balancer-internal-overview/IC744147.png)

図 2 - 別のクラウド サービス内のフロントエンド サーバー

## <a name="intranet-line-of-business-applications"></a>イントラネットの基幹業務アプリケーション

オンプレミスのネットワーク上のクライアントからのトラフィックは、Azure ネットワークへの VPN 接続を使用して LOB サーバー セット全体に負荷分散します。

クライアント コンピューターは、ポイント対サイト VPN を使用して、Azure VPN サービスから IP アドレスにアクセスできます。 また、ILB エンドポイントの背後でホストされている LOB アプリケーションを使用できます。

![ポイント対サイト VPN を使用した内部負荷分散](./media/load-balancer-internal-overview/IC744148.png)

図 3 - ILB エンドポイントの背後でホストされている LOB アプリケーション

LOB のもう&1; つのシナリオは、ILB エンドポイントが構成されている仮想ネットワークにサイト間 VPN を設定することです。 これにより、オンプレミス ネットワーク トラフィックを ILB エンドポイントにルーティングできます。

![サイト間 VPN を使用した内部負荷分散](./media/load-balancer-internal-overview/IC744150.png)

図 4 - ILB エンドポイントにルーティングされるオンプレミス ネットワーク トラフィック

## <a name="next-steps"></a>次のステップ

[Azure Resource Manager による Azure Load Balancer のサポート](load-balancer-arm.md)

[インターネットに接続するロード バランサーの構成の開始](load-balancer-get-started-internet-arm-ps.md)

[内部ロード バランサーの構成の開始](load-balancer-get-started-ilb-arm-ps.md)

[ロード バランサー分散モードの構成](load-balancer-distribution-mode.md)

[ロード バランサーのアイドル TCP タイムアウト設定の構成](load-balancer-tcp-idle-timeout.md)

