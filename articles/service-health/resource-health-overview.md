---
title: Azure Resource Health の概要 | Microsoft Docs
description: Azure Resource Health の概要
author: stephbaron
ms.author: stbaron
ms.topic: conceptual
ms.service: service-health
ms.workload: Supportability
ms.date: 11/16/2018
ms.openlocfilehash: d2a77e831290aa1ee0fcb6d4addf8f6e90786d52
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858911"
---
# <a name="azure-resource-health-overview"></a>Azure Resource Health の概要
 
Azure Resource Health は、Azure サービスの問題によってリソースが影響を受けているときに、診断を行ったり、サポートを受けたりするのに役立ちます。 Azure Resource Health では、リソースの現在と過去の正常性に関する情報が通知されます。 また、問題を軽減するのに役立つテクニカル サポートが提供されます。

[Azure の状態](https://status.azure.com)は広範囲の Azure ユーザーに影響するサービスの問題の情報を提供する一方で、Resource Health はお使いのリソースの正常性に関するパーソナライズされたダッシュボードを提供します。 Resource Health は、過去に Azure サービスの問題によって指定のリソースを利用できなかったすべての時間を示します。 そのため、SLA に違反していたかどうかを簡単に把握できます。 

## <a name="resource-definition-and-health-assessment"></a>リソース定義と正常性評価
リソースは Azure サービスの特定のインスタンスです (例: 仮想マシン、Web アプリ、SQL データベース)。

Resource Health は、異なる Azure サービスから生成される信号を基にリソースが正常であるかどうかを評価します。 リソースが正常でない場合、Resource Health は追加の情報を分析して問題の原因を特定します。 また、問題を修正するためにマイクロソフトがとっているアクションや、問題の原因に対処するためにユーザーが実行できるアクションを特定します。 

正常性を評価する方法に関する追加情報については、[Azure Resource Health](resource-health-checks-resource-types.md) に関する記事でリソースの種類と正常性チェックの全一覧を確認してください。

## <a name="health-status"></a>正常性状態
リソースの正常性は、次のいずれかの状態として表示されます。

### <a name="available"></a>使用可能
**使用可能**状態は、サービスにおいて、リソースの正常性に影響するイベントが一切検出されていないことを意味します。 リソースが過去 24 時間以内に予定外のダウンタイムから回復した場合は、**最近解決された**ことを示す通知が表示されます。

!["使用可能" 状態の仮想マシンについて、"最近解決された" 問題が通知されている画面](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>使用不可
**使用不可**状態は、サービスにおいて、リソースの正常性に影響を与える継続中のイベント (プラットフォームまたはプラットフォーム以外のイベント) が検出されたことを意味します。

#### <a name="platform-events"></a>プラットフォームのイベント
プラットフォームのイベントは、Azure インフラストラクチャの複数のコンポーネントによってトリガーされます。 これには、スケジュールされたアクション (計画的なメンテナンスなど) と、予期しないインシデント (計画されていないホストの再起動など) の両方が含まれます。

Resource Health は、イベントと復旧プロセスについてさらに詳細な情報を提供します。 また、ユーザーは、有効な Microsoft サポート契約がない場合でも、サポートに問い合わせることができます。

![プラットフォームのイベントによって "使用不可" 状態となっている仮想マシン](./media/resource-health-overview/Unavailable.png)

#### <a name="non-platform-events"></a>プラットフォーム以外のイベント
プラットフォーム以外のイベントは、ユーザーのアクションによってトリガーされます。 たとえば、仮想マシンを停止した場合や、Azure Cache for Redis への接続数が最大数に到達した場合などです。

![プラットフォーム以外のイベントによって "使用不可" 状態となっている仮想マシン](./media/resource-health-overview/Unavailable_NonPlatform.png)

### <a name="unknown"></a>Unknown
**不明**状態は、Resource Health がこのリソースに関する情報を 10 分以上受け取っていないことを示します。 この状態はリソースの状態を明確に示すものではありませんが、トラブルシューティング プロセスにおいて重要なデータ ポイントです。

リソースが予期した通りに実行されている場合、リソースの状態は数分後に**使用可能**に変更されます。

リソースで問題が発生している場合、**不明**状態は、プラットフォーム内のイベントによってリソースが影響を受けていることを示している可能性があります。

!["不明" 状態の仮想マシン](./media/resource-health-overview/Unknown.png)

### <a name="degraded"></a>低下しています
**低下**状態は、リソースでパフォーマンス低下が検出されたものの、まだ使用可能であることを示します。
さまざまなリソースは、リソース機能低下を指定するときの独自の条件を持っています。

!["低下" 状態の仮想マシン](./media/resource-health-overview/degraded.png)

## <a name="reporting-an-incorrect-status"></a>正しくない状態の報告
現在の正常性状態が正しくないと思われる場合は、**[報告]** を選択して正常ではない状態をマイクロソフトに報告できます。 Azure の問題の影響を受けている場合は、Resource Health からサポートにお問い合わせください。 

![正しくない状態に関する情報を送信するためのボックス](./media/resource-health-overview/incorrect-status.png)

## <a name="historical-information"></a>履歴情報
Resource Health の **[正常性の履歴]** セクションで、過去 14 日間の正常性履歴にアクセスできます。 

![過去 2 週間の Resource Health イベントの一覧](./media/resource-health-overview/history-blade.png)

## <a name="getting-started"></a>使用の開始
1 つのリソースについて Resource Health を開くには
1.  Azure ポータルにサインインします。
2.  リソースを参照します。
3.  左側のウィンドウのリソース メニューで、**[リソース正常性]** を選択します。

![リソース ビューから Resource Health を開く様子](./media/resource-health-overview/from-resource-blade.png)

Resource Health には、**[すべてのサービス]** を選択し、フィルター テキスト ボックスに「**resource health**」と入力してアクセスすることもできます。 **[ヘルプとサポート]** ウィンドウで、[[リソース正常性]](https://ms.portal.azure.com/#blade/Microsoft_Azure_Monitoring/AzureMonitoringBrowseBlade/resourceHealth) を選択します。

![[すべてのサービス] から Resource Health を開く様子](./media/resource-health-overview/FromOtherServices.png)

## <a name="next-steps"></a>次の手順

Resource Health について詳しくは、次のリソースをご覧ください。
-  [Azure Resource Health で利用できるリソースの種類と正常性チェック](resource-health-checks-resource-types.md)
-  [Azure Resource Health に関してよく寄せられる質問](resource-health-faq.md)




