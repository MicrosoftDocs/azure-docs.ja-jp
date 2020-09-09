---
title: Azure Monitor の Insights の概要 | Microsoft Docs
description: Insights は、Azure Monitor で特定のアプリケーションやサービスを監視するためのカスタマイズされたエクスペリエンスを提供します。 この記事では、現在利用可能な分析情報のそれぞれについて簡単に説明します。
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/22/2019
ms.openlocfilehash: e3eefb97c85b3865abbf116b9912dc45f6db8ce8
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87326870"
---
# <a name="overview-of-insights-in-azure-monitor"></a>Azure Monitor の Insights の概要
Insights は、特定のアプリケーションやサービスを監視するためのカスタマイズされたエクスペリエンスを提供します。 [Azure Monitor データ プラットフォーム](../platform/data-platform.md)にデータを格納し、Azure Monitor のその他の機能を分析やアラートに使用しますが、追加のデータを収集したり、Azure portal で独自のユーザー エクスペリエンスを提供したりできます。 Azure portal で、Azure Monitor メニューの **[Insights]** セクションから分析情報にアクセスします。


以下のセクションでは、Azure Monitor で現在利用可能な分析情報について簡単に説明します。 それぞれの詳細については、詳しいドキュメントを参照してください。

## <a name="application-insights"></a>Application Insights
Application Insights は、複数のプラットフォームで使用できる Web 開発者向けの拡張可能なアプリケーション パフォーマンス管理 (APM) サービスです。 このサービスを使用して、実行中の Web アプリケーションを監視することができます。 オンプレミス、ハイブリッド、または任意のパブリック クラウドでホストされている .NET、Node.js、Java EE などのさまざまなプラットフォーム上のアプリケーションで機能します。 また、DevOps プロセスと統合され、さまざまなツールへの接続ポイントを備えています。

「[Application Insights とは何か?](../app/app-insights-overview.md)」を参照してください。

![Application Insights](media/insights-overview/app-insights.png)

## <a name="azure-monitor-for-containers"></a>Azure Monitor for Containers
コンテナーに対する Azure Monitor は、Azure Kubernetes Service (AKS) でホストされた Azure Container Instances またはマネージド Kubernetes クラスターにデプロイされているコンテナー ワークロードのパフォーマンスを監視します。 コンテナーの監視は、複数のアプリケーションを含む大規模な運用クラスターを実行するときは特に重要です。

「[コンテナーに対する Azure Monitor の概要](./container-insights-overview.md)」を参照してください。

![Azure Monitor for Containers](media/insights-overview/container-insights.png)

## <a name="azure-monitor-for-resource-groups-preview"></a>Azure Monitor for Resource Groups (プレビュー)
リソース グループ向けの Azure Monitor は、個別のリソースで発生した問題をトリアージおよび診断するために役立つ一方で、リソース グループ全体の正常性とパフォーマンスに関してコンテキストを提供します。

「[Azure Monitor でリソース グループを監視する (プレビュー)](./resource-group-insights.md)」を参照してください。

![Azure Monitor for Resource Groups](media/insights-overview/resource-group-insights.png)

## <a name="azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (プレビュー)
Azure Monitor for VMs では、ご利用の Azure 仮想マシン (VM) および仮想マシン スケール セットの大規模な監視が行われます。 これにより、ご利用の Windows VM および Linux VM のプロセスや、その他のリソースおよび外部プロセスとの依存関係を監視することにより、それらの VM のパフォーマンスおよび正常性が分析されます。

[Azure Monitor for VMs ](vminsights-overview.md) に関する記事を参照してください。

![VM に対する Azure Monitor](media/insights-overview/vm-insights.png)

## <a name="azure-monitor-for-networks-preview"></a>Azure Monitor for Networks (プレビュー)
[Azure Monitor for Networks](network-insights-overview.md) には、すべてのネットワーク リソースの正常性とメトリックの包括的ビューが用意されています。 高度な検索機能を使用すると、Web サイト名を検索するだけで、Web サイトをホストしているリソースを特定するなどのシナリオが可能になり、リソースの依存関係を特定できます。

![Azure Monitor for Networks](media/insights-overview/network-insights.png)

## <a name="next-steps"></a>次のステップ
* 分析情報で利用される [Azure Monitor データ プラットフォーム](../platform/data-platform.md)について詳しく理解します。
* 各種の [Azure Monitor によって使用されるデータ ソース](../platform/data-sources.md)と、それぞれの分析情報によって収集されるさまざまな種類のデータについて理解します。

