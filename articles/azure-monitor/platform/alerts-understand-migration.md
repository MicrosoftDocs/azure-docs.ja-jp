---
title: Azure Monitor の自主的アラート移行ツールの動作の理解
description: アラート移行ツールの動作と、問題が発生した時のトラブルシューティングの方法について説明します。
author: snehithm
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 03/19/2018
ms.author: snmuvva
ms.subservice: alerts
ms.openlocfilehash: a45a0cff606bc854924d5da0841b26e1cb9031bb
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58631709"
---
# <a name="understand-how-the-migration-tool-works"></a>移行ツールの動作の理解

[以前発表された](monitoring-classic-retirement.md)ように、Azure Monitor のクラシック アラートは 2019 年 7 月に廃止される予定です。 Azure portal では、移行を自主的にトリガーするための移行ツールが提供されており、クラシック アラート ルールを使用しているお客様に展開されつつあります。

この記事では、自主的移行ツールの動作方法について説明します。 また、いくつかの一般的な問題の解決方法について説明します。

## <a name="which-classic-alert-rules-can-be-migrated"></a>移行可能なクラシック アラート ルール

ツールを使用してほぼすべてのクラシック アラート ルールを移行できますが、いくつかの例外があります。 次のアラート ルールはツールを使用して (または 2019 年 7 月 の自動移行時に) 移行されません。

- 仮想マシンのゲスト メトリックに対するクラシック アラート ルール (Windows と Linux の両方)。 [新しいメトリック アラートでこのようなアラート ルールを再作成する方法についてのガイダンスを参照してください](#guest-metrics-on-virtual-machines)
- クラシック ストレージ メトリックに対するクラシック アラート ルール。 [クラシック ストレージ アカウントの監視に関するガイダンスを参照してください](https://azure.microsoft.com/blog/modernize-alerting-using-arm-storage-accounts/)
- 一部のストレージ アカウント メトリックに対するクラシック アラート ルール。 [詳細は以下を参照してください。](#storage-account-metrics)

サブスクリプションにこれらのクラシック ルールが含まれている場合は、その他のルールは移行されますが、これらのルールは手動で移行する必要があります。 自動移行を提供できないため、新しいアラートへの移行に時間的な余裕を持たせるために、これらの既存のクラシック メトリック アラートは引き続き 2020 年 6 月まで提供されます。 ただし、2019 年 6 月を過ぎると、新しいクラシック アラートは作成できません。

### <a name="guest-metrics-on-virtual-machines"></a>仮想マシンのゲスト メトリック

ゲスト メトリックに新しいメトリック アラートを作成できるようにするために、ゲスト メトリックを Azure Monitor カスタム メトリック ストアに送信する必要があります。 次の手順に従って、診断設定で Azure Monitor シンクを有効にします。

- [Windows VM のゲスト メトリックの有効化](collect-custom-metrics-guestos-resource-manager-vm.md)
- [Linux VM のゲスト メトリックの有効化](https://docs.microsoft.com/azure/azure-monitor/platform/collect-custom-metrics-linux-telegraf)

上記の手順を終了すると、ゲスト メトリックに対する新しいメトリック アラートを作成できます。 新しいメトリック アラートを再作成したら、クラシック アラートを削除できます。

### <a name="storage-account-metrics"></a>ストレージ アカウントのメトリック

次のメトリックに対するアラートを除いて、ストレージ アカウントに対するすべてのクラシック アラートを移行できます。

- PercentAuthorizationError
- PercentClientOtherError
- PercentNetworkError
- PercentServerOtherError
- PercentSuccess
- PercentThrottlingError
- PercentTimeoutError
- AnonymousThrottlingError
- SASThrottlingError

パーセント メトリックに対するクラシック アラート ルールは、[従来のストレージ メトリックと新しいストレージ メトリック間のマッピング](https://docs.microsoft.com/azure/storage/common/storage-metrics-migration#metrics-mapping-between-old-metrics-and-new-metrics)に基づいて移行する必要があります。 使用可能な新しいメトリックは絶対値であるため、しきい値を適切に変更する必要があります。

クラシック アラート ルール AnonymousThrottlingError および SASThrottlingError は、同様の機能を提供する結合されたメトリックがないため、2 つの新しいアラートに分割する必要があります。 しきい値を適切に調整する必要があります。

## <a name="roll-out-phases"></a>展開の段階

移行ツールは、クラシック アラート ルールを使用しているすべてのお客様に対して段階的に展開中です。 ツールを使用したサブスクリプションの移行準備が整うと、**サブスクリプションの所有者**に電子メールが届きます。

> [!NOTE]
> ツールは段階的に展開されているため、初期段階では、ほとんどのサブスクリプションがまだ移行できる状態ではない可能性があります。

現在、次のリソース タイプに対するクラシック アラート ルール**のみ**を持つ**一部の**サブスクリプションが移行できる状態になっています。 他のリソースの種類に対するサポートは、今後の段階で追加されます。

- Microsoft.apimanagement/service
- Microsoft.batch/batchaccounts
- Microsoft.cache/redis
- Microsoft.datafactory/datafactories
- Microsoft.dbformysql/servers
- Microsoft.dbforpostgresql/servers
- Microsoft.eventhub/namespaces
- Microsoft.logic/workflows
- Microsoft.network/applicationgateways
- Microsoft.network/dnszones
- Microsoft.network/expressroutecircuits
- Microsoft.network/loadbalancers
- Microsoft.network/networkwatchers/connectionmonitors
- Microsoft.network/publicipaddresses
- Microsoft.network/trafficmanagerprofiles
- Microsoft.network/virtualnetworkgateways
- Microsoft.search/searchservices
- Microsoft.servicebus/namespaces
- Microsoft.streamanalytics/streamingjobs
- Microsoft.timeseriesinsights/environments
- Microsoft.web/hostingenvironments/workerpools
- Microsoft.web/serverfarms
- Microsoft.web/sites

## <a name="who-can-trigger-the-migration"></a>移行をトリガーできるユーザー

サブスクリプション レベルで、組み込みロールの**監視共同作業者**を割り当てられたすべてのユーザーが移行をトリガーできます。 また、次の権限を持つカスタム ロールを割り当てられたユーザーも移行をトリガーできます。

- */read
- Microsoft.Insights/actiongroups/*
- Microsoft.Insights/AlertRules/*
- Microsoft.Insights/metricAlerts/*

## <a name="common-issues-and-remediations"></a>よくある問題と解決方法

ユーザーが[移行をトリガー](alerts-using-migration-tool.md)すると、移行の完了またはユーザーのアクションが必要な場合に指定された電子メール アドレスを使用して通知が送信されます。 次のセクションでは、いくつかの一般的な問題と解決方法について説明します。

### <a name="validation-failed"></a>検証に失敗しました

サブスクリプション内のクラシック アラートに対する最近のいくつかの変更が原因で、サブスクリプションを移行できません。 これは一時的な問題です。 移行の状態が **[Ready for migration]\(移行準備完了\)** に戻ったら、移行を再開できます。

### <a name="policyscope-lock-preventing-us-from-migrating-your-rules"></a>ポリシー/スコープ ロックによりルールを移行できません

移行の一環として、新しいメトリック アラートと新しいアクション グループが作成され、(新しいルールが作成されると) クラシック アラート ルールは削除されます。 ただし、ポリシーまたはスコープ ロックにより、リソースを作成できません。 ポリシーまたはスコープ ロックによって、一部またはすべてのルールを移行できませんでした。 スコープ ロックまたはポリシーを一時的に削除して移行を再度トリガーすることで、この問題を解決できます。

## <a name="next-steps"></a>次の手順

- [移行ツールの使用方法](alerts-using-migration-tool.md)
- [移行を準備する](alerts-prepare-migration.md)
