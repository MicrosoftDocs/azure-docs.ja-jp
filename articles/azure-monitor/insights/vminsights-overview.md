---
title: Azure Monitor for VMs (プレビュー) とは? | Microsoft Docs
description: Azure Monitor for VMs は Azure Monitor の機能です。この機能では、Azure VM オペレーティング システムの正常性とパフォーマンスの監視が組み合わされているほか、アプリケーション コンポーネントと他のリソースとの依存関係が自動的に検出され、それらの間の通信がマップされます。 この記事では概要を説明します。
services: azure-monitor
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: tysonn
ms.assetid: ''
ms.service: azure-monitor
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/13/2019
ms.author: magoedte
ms.openlocfilehash: f7a0300619d82f760c0e307601efbd3987eb6067
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58004588"
---
# <a name="what-is-azure-monitor-for-vms-preview"></a>Azure Monitor for VMs (プレビュー) とは?

Azure Monitor for VMs では、ご利用の Azure 仮想マシン (VM) および仮想マシン スケール セットの大規模な監視が行われます。 これにより、ご利用の Windows VM および Linux VM のプロセスや、その他のリソースおよび外部プロセスとの依存関係を監視することにより、それらの VM のパフォーマンスおよび正常性が分析されます。 

ソリューションとして、Azure Monitor for VMs では、オンプレミスまたは別のクラウド プロバイダーでホストされている VM を対象にしたパフォーマンスおよびアプリケーションの依存関係の監視がサポートされています。 次の 3 つの主要な機能により、詳細な分析情報が提供されます。

* **Windows および Linux が稼働している Azure VM の論理コンポーネント**: 事前に構成された正常性基準と照らし合わせて測定され、評価条件が満たされると通知が届きます。  

* **事前に定義された、傾向を示すパフォーマンス グラフ**:ゲスト VM オペレーティング システムから主要なパフォーマンス メトリックが表示されます。

* **依存関係マップ**: さまざまなリソース グループおよびサブスクリプションから、該当する VM と相互接続されているコンポーネントが表示されます。  

これらの機能は、次の 3 つの観点に分けて構成されています。

* Health
* パフォーマンス
* マップ

>[!NOTE]
>現在、正常性機能は、Azure 仮想マシンと仮想マシン スケール セットに対してのみ提供されています。 パフォーマンス機能とマップ機能では、ご利用の環境または他のクラウド プロバイダーにおいてホストされている Azure VM および仮想マシンの両方がサポートされています。

Azure Monitor ログとの統合により、強力な集計とフィルター処理が実現されています。さらに、時間の経過に伴うデータの傾向を分析することができます。 そうした包括的なワークロード監視は、Azure Monitor または Service Map 単独では実現できません。  

単一の VM 内のこのデータをその仮想マシンから直接表示することも、Azure Monitor を使用して、ご利用の複数の VM の集計ビューを提供することもできます。 このビューは、各機能の次の観点に基づいています。

* **正常性**: VM はリソース グループに関連付けられています。
* **マップ**と**パフォーマンス**: VM は、特定の Log Analytics ワークスペースに報告するように構成されます。

![Azure portal での仮想マシン分析情報のパースペクティブ](./media/vminsights-overview/vminsights-azmon-directvm-01.png)

Azure Monitor for VMs を使用すると、重要なアプリケーションのパフォーマンスおよび可用性を予測することができます。 重要なオペレーティング システムのイベント、パフォーマンスのボトルネック、およびネットワークの問題が識別されます。 Azure Monitor for VMs はまた、問題が他の依存関係に関連しているかどうかの把握にも役立ちます。  

## <a name="data-usage"></a>データ利用状況 

Azure Monitor for VM をデプロイすると、ご利用の VM によって収集されたデータが取り込まれ、Azure Monitor に格納されます。 正常性基準メトリックは、時系列データベースで Azure Monitor に格納され、収集されたパフォーマンスと依存関係データは Log Analytics ワークスペースに格納されます。 「[Azure Monitor の価格](https://azure.microsoft.com/pricing/details/monitor/)」ページに公開されている価格に基づいて、Azure Monitor for VMs は以下に対して課金されます。

* 取り込まれ格納されるデータ。
* 監視される正常性基準メトリック時系列の数。
* 作成されるアラート ルール。
* 送信される通知。 

ログのサイズは、パフォーマンス カウンターの文字列の長さによって異なり、仮想マシンに割り当てられた論理ディスクおよびネットワーク アダプターの数に応じて大きくなる可能性があります。 ワークスペースを既にお持ちで、これらのカウンターを収集している場合、重複する課金は適用されません。 Service Map を既に使用している場合、確認できる変更点は、Azure Monitor に送信される追加の接続データだけです。

## <a name="next-steps"></a>次の手順
ご利用の仮想マシンを監視する上で役立つ要件と方法については、[Azure Monitor for VMs のデプロイ](vminsights-onboard.md)に関するページをご覧ください。
