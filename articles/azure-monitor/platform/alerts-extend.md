---
title: Log Analytics のアラートを Azure Government Cloud に拡張 (コピー) する
description: アラートを OMS ポータル内の Log Analytics から Azure Alerts にコピーするプロセスの概要について説明し、お客様の一般的な懸念に対応する詳細を示します。
author: msvijayn
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 10/01/2018
ms.author: vinagara
ms.subservice: alerts
ms.openlocfilehash: efd2fc9d164564126b7b641ef35bbb10c4834f49
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55099330"
---
# <a name="extend-log-analytics-alerts-to-azure-alerts"></a>Log Analytics アラートを Azure Alerts に拡張する

> [!NOTE]
> Microsoft は既に、この記事で説明されている Azure のパブリック バージョンに関するプロセスを完了しました。 ただし、それはまだ米国政府バージョンに適用されます。  

最近まで、Azure Log Analytics には、Log Analytics のデータに基づいて状態を事前にユーザーに通知できる独自のアラート機能が含まれていました。 Microsoft Operations Management Suite ポータルでアラート ルールを管理していました。 新しいアラートのエクスペリエンスにより、Microsoft Azure のさまざまなサービスにわたってアラートが統合されました。 これは Azure Portal の Azure Monitor で **[アラート]**  として提供されており、Log Analytics と Azure Application Insights 両方のアクティビティ ログ、メトリック、およびログからのアラートをサポートしています。

OMS ポータルを使用している Azure Government Cloud のお客様は、**2019 年 2 月 1 日**から[ご自身のアラート ルールを自主的に Azure に拡張する](alerts-extend-tool.md)ことができます。 **2019 年 3 月 1 日**以降、Microsoft は、Azure Government - OMS ポータル内のすべての既存のアラート ルールを体系的な方法で Azure に自動的に拡張します。この際、ダウンタイムは発生せず、監視が中断されることもありません。 **2019 年 3 月 1 日**以降、Azure Government Cloud の OMS ポータルで作成された新しいワークスペースは、Azure に自動的に拡張されます。

## <a name="benefits-of-extending-your-alerts"></a>アラートを拡張するメリット
Azure Portal でアラートの作成と管理を行うことには、以下のようにいくつかの利点があります。

- 作成や表示が可能なアラートが 250 件のみである Operations Management Suite ポータルとは異なり、Azure Alerts にこのような制限はありません。
- Azure Alerts からは、すべての種類のアラートを管理、列挙、表示できます。 以前それが可能であったのは、Log Analytics のアラートについてのみでした。
- [Azure Monitor ロール](../../azure-monitor/platform/roles-permissions-security.md)を使用して、ユーザーのアクセスを監視とアラートのみに制限できます。
- Azure Alerts では、[アクション グループ](../../azure-monitor/platform/action-groups.md)を使用できます。 これにより、アラートごとに複数のアクションを指定できます。 SMS の使用、音声通話の送信、Azure Automation Runbook の呼び出し、webhook の呼び出し、および IT Service Management (ITSM) Connector の構成が可能です。 

## <a name="process-of-extending-your-alerts"></a>アラートを拡張するプロセス
Log Analytics のアラートを Azure Alerts に移すプロセスに、アラートの定義、クエリ、構成の変更は含まれません。 必要とされる唯一の変更として、Azure では、アクション グループを使用してすべてのアクションを実行します。 アクション グループが既にアラートに関連付けられている場合、それらは Azure に拡張されるときに含められます。

Log Analytics ワークスペース内のアラートを Azure に拡張するスケジュールを設定しても、それらのアラートは引き続き機能し、構成を損なうことは決してありません。 スケジュールが設定されると、アラートを一時的に変更できなくなる場合がありますが、この期間中、引き続き新しい Azure Alerts を作成できます。 Operations Management Suite ポータルからアラートの編集や作成をしようとする場合は、Log Analytics ワークスペースから作成を続けるオプションが用意されています。 Azure Portal 内の Azure Alerts からそれらを作成することもできます。

 ![Log Analytics または Azure Alerts からアラートを作成するオプションのスクリーンショット](media/alerts-extend/ScheduledDirection.png)

> [!NOTE]
> アラートを Log Analytics から Azure に拡張しても、アカウントに料金は発生しません。 クエリベースの Log Analytics アラートに対して Azure Alerts を使用しても、[Azure Monitor の価格ポリシー](https://azure.microsoft.com/pricing/details/monitor/)に記載されている制限や条件内の使用であれば課金されません。  


### <a name="how-to-extend-your-alerts-voluntarily"></a>アラートを自主的に拡張する方法
アラートを Azure Alerts に拡張するには、Operations Management Suite ポータルに用意されているウィザードを使用するか、API を使用してプログラムによって拡張します。 詳細については、[Operations Management Suite ポータルおよび API を使用してアラートを Azure に拡張する方法](alerts-extend-tool.md)に関するページを参照してください。

## <a name="experience-after-extending-your-alerts"></a>アラート拡張後のエクスペリエンス
アラートは、Azure Alerts に拡張された後、前とまったく変わりなく、管理のために Operations Management Suite ポータルで引き続き使用できます。

![アラートが一覧表示されている Operations Management Suite ポータルのスクリーンショット](media/alerts-extend/PostExtendList.png)

Operations Management Suite ポータルで、既存のアラートの編集や新しいアラートの作成を行おうとすると、自動的に Azure Alerts にリダイレクトされます。  

> [!NOTE]
> アラートを追加または編集する必要がある個人に割り当てられたアクセス許可が、Azure で正しく割り当てられていることを確認してください。 どのようなアクセス許可を付与する必要があるかを理解するには、[Azure Monitor と Azure Alerts を使用するためのアクセス許可](../../azure-monitor/platform/roles-permissions-security.md)についての記事を参照してください。  
> 

アラートは引き続き、[Log Analytics API](../../azure-monitor/platform/api-alerts.md) や [Log Analytics リソース テンプレート](../../azure-monitor/insights/solutions-resources-searches-alerts.md)から作成できます。 これを行うときには、アクション グループを含める必要があります。

## <a name="next-steps"></a>次の手順

* [Log Analytics から Azure へのアラート拡張の開始する](alerts-extend-tool.md)ためのツールについて学習する。
* [Azure Alerts のエクスペリエンス](../../azure-monitor/platform/alerts-overview.md)の詳細について学習する。
* [Azure Alerts でのログ アラート](alerts-unified-log.md)の作成方法について学習します。

