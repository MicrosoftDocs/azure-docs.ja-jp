---
title: Azure Monitor ログでのネットワーク監視について | Microsoft Docs
description: クラウド、オンプレミス、およびハイブリッド環境でネットワークを管理するための、NPM などのネットワーク監視ソリューションの概要を説明します。
services: monitoring-and-diagnostics
documentationcenter: na
author: agummadi
manager: ''
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2018
ms.author: agummadi
ms.openlocfilehash: a92c6789723dc42ac8f3a9c471e494079dc19328
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98233442"
---
# <a name="network-monitoring-solutions"></a>ネットワーク監視ソリューション 

Azure では、ネットワーク資産を監視するためのソリューションのホストが提供されます。 Azure には、ネットワークの接続性と ExpressRoute 回線の正常性を監視し、クラウド内のネットワーク トラフィックを分析するための、ソリューションとユーティリティがあります。

## <a name="network-performance-monitor-npm"></a>ネットワーク パフォーマンス モニター (NPM)

Network Performance Monitor (NPM) は機能のスイートであり、各機能は、ネットワークの正常性とアプリケーションへのネットワーク接続性を監視し、ネットワークのパフォーマンスに関する洞察を提供します。 NPM はクラウド ベースであり、次のものの間の接続性を監視するハイブリッド ネットワーク監視ソリューションを提供します。
 
* クラウドの展開とオンプレミスの場所
* 複数のデータ センターとブランチ オフィス
* ミッション クリティカルな多層アプリケーション/マイクロサービス
* ユーザーの場所と Web ベースのアプリケーション (HTTP/HTTPS) 

パフォーマンス モニター、ExpressRoute モニター、およびサービス接続モニターは、NPM 内の監視機能です。以下のセクションでこれらについて説明します。

## <a name="performance-monitor"></a>パフォーマンス モニター

Performance Monitor は NPM の一部であり、クラウド、ハイブリッド、オンプレミスの環境のネットワーク監視を行います。 リモート ブランチとフィールド オフィス、店舗の場所、データ センター、クラウドの間のネットワーク接続性を監視できます。 ユーザーから不満があがる前に、ネットワークの問題を検出できます。 主な利点は次のとおりです。

* 各種サブネット間の損失と待ち時間を監視し、アラートを設定する
* ネットワーク上のすべてのパス (冗長パスを含む) を監視する
* 再現が難しい、ネットワークに関する一過性の問題と特定時点の問題をトラブルシューティングする
* パフォーマンス低下の原因となっている特定のセグメントをネットワーク上で見つけ出す
* SNMP を必要とせずに、ネットワークの正常性を監視する

![NPM のトポロジ マップ](./media/network-monitoring-overview/npm-topology-map.png) 

詳しくは、次の記事をご覧ください。

* [Azure Monitor ログで Network Performance Monitor ソリューションを構成する](../azure-monitor/insights/network-performance-monitor.md) 
* [ユース ケース](/archive/blogs/msoms/monitor-on-premises-cloud-iaas-and-hybrid-networks-using-oms-network-performance-monitor)
* 製品の更新:
  * [2017 年 2 月](/archive/blogs/msoms/oms-network-performance-monitor-is-now-generally-available)
  * [2017 年 8 月](/archive/blogs/msoms/improvements-to-oms-network-performance-monitor)

## <a name="expressroute-monitor"></a>ExpressRoute モニター

ExpressRoute 用の NPM は、Azure Private ピアリングおよび Microsoft ピアリング接続の包括的な ExpressRoute 監視を提供します。 ExpressRoute 経由のブランチ オフィスと Azure 間の E2E 接続性とパフォーマンスを監視できます。 主な機能は次のとおりです。

* サブスクリプションに関連付けられている ER 回線の自動検出
* オンプレミスからクラウド アプリケーションへのネットワーク トポロジの検出
* 容量計画、帯域幅の使用率分析
* プライマリ パスとセカンダリ パス両方の監視とアラート
* ExpressRoute を介した Microsoft 365 や Dynamics 365 などの Azure サービスへの接続の監視
* VNet への接続性の低下の検出

![リージョン間のトラフィックを示すマップ](./media/network-monitoring-overview/expressroute-topology-map.png) 

詳細については、次の記事を参照してください。

* [ExpressRoute に使用する Network Performance Monitor の構成](../expressroute/how-to-npm.md)
* [ブログの投稿](https://aka.ms/NPMExRmonitorGA)

## <a name="service-connectivity-monitor"></a>サービス接続モニター

サービス接続の監視では、アプリケーションの到達可能性をテストし、オンプレミス、通信事業者ネットワーク、クラウド/プライベート データ センターの間のパフォーマンス ボトルネックを検出できます。

* アプリケーションへのエンド ツー エンドのネットワーク接続性を監視する
* アプリケーションの配信とネットワークのパフォーマンスを関連付け、ユーザーとアプリケーションの間のパスに沿った低下の正確な場所を検出する
* 世界中の複数のユーザーの場所からアプリケーションへの到達可能性をテストする
* 基幹業務アプリケーションと SaaS アプリケーションのネットワーク待機時間とパケット損失を特定する
* アプリケーション パフォーマンス低下の原因になっている可能性があるネットワーク上のホット スポットを特定する
* Microsoft 365、Dynamics 365、Skype for Business、その他の Microsoft サービス用の組み込みテストを使って、Microsoft 365 アプリケーションへの到達可能性を監視する

詳細については、次の記事を参照してください。

* [サービス エンドポイントを監視するための Network Performance Monitor の構成](../azure-monitor/insights/network-performance-monitor-service-connectivity.md#configuration)
* [ブログの投稿](https://aka.ms/svcendptmonitor)

## <a name="traffic-analytics"></a>Traffic Analytics
Traffic Analytics は、クラウド ネットワーク上のユーザとアプリケーションのアクティビティを可視化するクラウドベースのソリューションです。 NSG フロー ログが分析され、以下に関する分析情報が提供されます。

* Azure とインターネット、パブリック クラウド リージョン、VNET、およびサブネット間のネットワークのトラフィック フロー。
* ネットワーク上のアプリケーションとプロトコル。スニッファや専用のフロー コレクター アプライアンスは不要です。
* クラウドのトップ トーカー、チャット数が多いアプリケーション、VM の会話、ホット スポット。
* VNET 間のトラフィックのソースと宛先、重要なビジネス サービスとアプリケーション間の内部関係。
* セキュリティ –。悪意のあるトラフィック、インターネットに対して開かれているポート、インターネットにアクセスしようとしているアプリケーションや VM など。
* 容量使用率。VPN ゲートウェイとその他のサービスの使用率の傾向を監視することによって、過剰なプロビジョニングまたは過小使用の問題を回避できます。

Traffic Analytics は、組織のネットワーク アクティビティを監査し、アプリケーションとデータをセキュリティで保護し、ワークロードのパフォーマンスを最適化し、コンプライアンスを維持するために役立つ実用的な情報を提供します。

![リージョン間のトラフィックを示すマップ 2](../network-watcher/media/traffic-analytics/geo-map-view-showcasing-traffic-distribution-to-countries-and-continents.png) 

関連リンク:
* [ブログ記事](https://aka.ms/trafficanalytics)、[ドキュメント](../network-watcher/traffic-analytics.md)、[FAQ](../network-watcher/traffic-analytics-faq.md)

## <a name="dns-analytics"></a>DNS Analytics
DNS 管理者用に開発されたこのソリューションは、DNS ログの収集、分析、および相互の関連付けを行って、セキュリティ、運用、およびパフォーマンスに関する分析情報を提供します。  いくつかの機能を次に示します。

* 悪意のあるドメインに解決しようとしているクライアントの識別
* 古いリソース レコードの識別
* 頻繁にクエリが実行されるドメインの名前と話し好きな DNS クライアントの可視化
* DNS サーバーでの要求の負荷の可視化
* 動的 DNS 登録エラーの監視

![DNS Analytics ダッシュボード](./media/network-monitoring-overview/dns-analytics-overview.png) 

関連リンク:
* [ブログ記事](/archive/blogs/msoms/introducing-oms-dns-analytics)、[ドキュメント](../azure-monitor/insights/dns-analytics.md)

## <a name="miscellaneous"></a>その他

* [新しい価格](../azure-monitor/insights/network-performance-monitor-pricing-faq.md)