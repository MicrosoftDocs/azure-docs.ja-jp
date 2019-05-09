---
title: Azure Monitor での使用量と推定コストの監視
description: Azure Monitor の [使用量と推定コスト] ページの利用プロセスの概要
author: dalekoetke
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 08/11/2018
ms.author: mbullwin
ms.reviewer: Dale.Koetke
ms.subservice: ''
ms.openlocfilehash: 2e59699b667215d4b09e4d87c1776431631348e8
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/01/2019
ms.locfileid: "58754257"
---
# <a name="monitoring-usage-and-estimated-costs-in-azure-monitor"></a>Azure Monitor での使用量と推定コストの監視

> [!NOTE]
> この記事では、Azure の異なる価格モデルの複数の監視機能全体の使用量と推定コストを表示する方法について説明します。  関連する情報については、次の記事を参照してください。
> - 「[Log Analytics でデータ ボリュームと保有期間を制御してコストを管理する](manage-cost-storage.md)」では、データ保有期間を変更することでコストを制御する方法について説明します。
> - 「[Log Analytics でのデータ使用状況の分析](../../azure-monitor/platform/data-usage.md)」では、データ使用状況を分析し、アラートを作成する方法について説明します。
> - 「[Application Insights での価格とデータ ボリュームの管理](../../azure-monitor/app/pricing.md)」では、Application Insights でデータ使用状況を分析する方法について説明します。

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Portal の [監視] ハブの **[使用量と推定コスト]** ページには、[アラート、メトリック、通知](https://azure.microsoft.com/pricing/details/monitor/)[Azure Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)、[Azure Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) などの中心的な監視機能の使用量が説明されています。 2018 年 4 月より前の価格プランを利用しているお客様の場合は、Insights と Analytics 経由で購入した Log Analytics の使用量も含まれています。

ユーザーは、サブスクリプションごとに集計された過去 31 日間のリソース使用量をこのページで確認できます。 ドリルインには、その 31 日間の使用傾向が表示されます。 この推定を行うためには大量のデータを集計する必要があるため、ページが完全に読み込まれるまでしばらくお待ちください。

監視された使用量とその推定コストの表示例を次に示します。

![使用量と推定コストを表示しているポータルのスクリーンショット](./media/usage-estimated-costs/001.png)

[月間使用量] 列にあるリンクをクリックすると、過去 31 日間にわたる使用量の傾向を示すグラフが表示されます。

![[Included per node]\(ノードごとに含まれる\) の棒グラフのスクリーンショット](./media/usage-estimated-costs/002.png)

次に示すのは同じような使用量とコストの概要です。 この例は 2018 年 4 月から適用される新しい使用量ベースの価格モデルを使用しているサブスクリプションを示しています。 ノード ベースの課金がなくなっていることに注意してください。 Log Analytics と Application Insights のデータの取り込みと保持が新しい共通メーターで報告されるようになりました。

![使用量と推定コストを表示しているポータルのスクリーンショット - 2018 年 4 月の価格](./media/usage-estimated-costs/003.png)

## <a name="new-pricing-model"></a>新しい価格モデル

2018 年 4 月に[新しい監視の価格モデルがリリースされました](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/)。  これは、クラウドに適した使用量に基づく料金であることを特徴としています。 ノード ベースに支払うのではなく、使用した分だけ支払います。 新しい価格モデルは、[アラート、メトリック、通知](https://azure.microsoft.com/pricing/details/monitor/)、[Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/)、および [Application Insights](https://azure.microsoft.com/pricing/details/application-insights/) で利用できます。 

2018 年 4 月 2 日より後に Log Analytics または Application Insights の利用を開始するお客様は、この新しい料金プランのみを選択できます。 これらのサービスを既に利用しているお客様は、新しい価格モデルに移行するかどうかを選択できます。

## <a name="assessing-the-impact-of-the-new-pricing-model"></a>新しい価格モデルの影響の評価
新しい価格モデルがお客様に与える影響は、お客様の監視の使用パターンによって異なります。 2018 年 4 月 2 日より前に既に Log Analytics または Application Insights を利用しているお客様は、Azure Monitor の **[使用量と推定コスト]** ページで、新しい価格モデルに移行した場合のコストの変動の推定を確認できます。 サブスクリプションを新しいモデルに移行する方法が示されます。 新しい価格モデルにより、ほとんどのお客様は、メリットが得られることになるでしょう。 これは、データの使用量が非常に多いお客様やコストが高いリージョンのお客様には当てはまらない可能性があります。

**[使用量と推定コスト]** ページで選択したサブスクリプションのコストの推定値を確認するには、ページの上部近くにある青いバナーを選択します。 新しい価格モデルを採用できるレベルはサブスクリプションであるため、この操作は 1 度に 1 つのサブスクリプションに対して実行することをお勧めします。

![Monitor の [使用量と推定コスト] ページの新しい価格モデル選択のスクリーンショット](./media/usage-estimated-costs/004.png)

新しいページは以前と似ていますが、緑色のバナーが表示されています。

![Monitor の [使用量と推定コスト] ページの現在の価格モデル選択のスクリーンショット](./media/usage-estimated-costs/005.png)

また、メーターも異なります。このページには、新しい価格モデルに対応する一連のメーターが表示されます。 次に示したのはその例です。

- Insight and Analytics\ノードごとの超過分
- Insight and Analytics\ノードごとに含まれる
- Application Insights\Basic 超過データ
- Application Insights\含まれるデータ

新しい価格モデルにはノード ベースで含まれるデータの割り当てがありません。 そのため、これらのデータ インジェスト メーターは、**共有サービス\データ インジェスト**という名前の新しい共通データ インジェスト メーターに結合されます。 

コストが高いリージョンで Log Analytics または Application Insights に取り込まれたデータには、別の変更があります。 こうしたコストが高いリージョンのデータは、新しいリージョン メーターを使用して表示されます。 たとえば、"**データ インジェスト (米国中西部)**" のように表示されます。

> [!NOTE]
> サブスクリプションあたりの推定コストは、Operations Management Suite (OMS) サブスクリプションのアカウントレベルでのノード単位の権利には考慮されません。 この場合は、アカウント担当者と新しい価格モデルについて、詳しく検討してください。

## <a name="new-pricing-model-and-operations-management-suite-subscription-entitlements"></a>新しい価格モデルと Operations Management Suite のサブスクリプションの権利

Microsoft Operations Management Suite E1 および E2 を購入されたお客様は、[Log Analytics](https://www.microsoft.com/cloud-platform/operations-management-suite) と [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-pricing) でのノード単位のデータ インジェストが可能です。 特定のサブスクリプションで Log Analytics ワークスペースまたは Application Insights リソースの権利を得るには: 

- そのサブスクリプションの価格モデルが、2018 年 4 月以前の価格モデルである必要があります。
- Log Analytics ワークスペースは、"ノードごとの (OMS)" 価格レベルを使用してください。
- Application Insights リソースは、"Enterprise" 価格プランを使用してください。

お客様の組織が購入されたスイートのノード数によっては、一部のサブスクリプションを新しい価格モデルへ移行した方が有益な場合がありますが、移行の前に慎重に検討するようにしてください。 一般に、前述のように 2018 年 4 月より前のモデルを維持することをお勧めします。

> [!WARNING]
> お客様の組織が、Microsoft Operations Management Suite E1 および E2 を購入した場合、2018 年 4 月より前の価格モデルのサブスクリプションを維持することをお勧めします。 
>

## <a name="changes-when-youre-moving-to-the-new-pricing-model"></a>新しい価格モデルに移行するときの変更

新しい料金モデルは、Log Analytics と Application Insights の価格オプションを 1 つのレベル (またはプラン) のみに簡素化します。 サブスクリプションを新しい価格モデルに移動した場合:

- 各 Log Analytics の価格レベルを新しい 1 GB あたりのレベル (Azure Resource Manager では "pergb2018" と呼ばれます) に変更します。
- Application Insights リソースの Enterprise プランが Basic プランに変更されます。

コスト見積もりには、こうした変更の影響が示されます。

> [!WARNING]
> 重要な注意事項として、Azure Resource Manager または PowerShell を使用して [Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-template-workspace-configuration) または [Application Insights](https://docs.microsoft.com/azure/application-insights/app-insights-powershell) をサブスクリプションに展開している場合、新しい価格モデルに移動しています。 Log Analytics で “pergb2018” 以外または Application Insights で “Basic” 以外の価格レベル/プランを指定した場合、無効な価格レベル/プランを指定したために展開が失敗するのではなく、成功しますが**有効な価格レベル/プランのみを使用します** (Log Analytics Free レベルはこれに該当せず、価格レベルが無効であるというメッセージが表示されます)。
>

## <a name="moving-to-the-new-pricing-model"></a>新しい価格モデルへの移行

サブスクリプションに対して新しい価格モデルを採用する場合は、**[使用量と推定コスト]** ページの上部にある **[価格モデルの選択]** オプションを選択します。

![Monitor の [使用量と推定コスト] ページの新しい価格モデル選択のスクリーンショット](./media/usage-estimated-costs/006.png)

**[価格モデルの選択]** ページが表示されます。 前のページで表示した各サブスクリプションが一覧表示されます。

![価格モデル選択のスクリーン ショット](./media/usage-estimated-costs/007.png)

サブスクリプションを新しい価格モデルに移行するには、チェックボックスをオンにし、**[保存]** を選択します。 同じ方法で、古い価格モデルに戻すことができます。 価格モデルを変更するには、サブスクリプションの所有者または共同作成者のアクセス許可が必要であることに注意してください。

## <a name="automate-moving-to-the-new-pricing-model"></a>新しい価格モデルへの移行を自動化する

以下のスクリプトを実行するには、Azure PowerShell モジュールが必要です。 最新バージョンがあるかどうかを確認するには、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページをご覧ください。

最新バージョンの Azure PowerShell がある場合は、まず ``Connect-AzAccount`` を実行する必要があります。

``` PowerShell
# To check if your subscription is eligible to adjust pricing models.
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

isGrandFatherableSubscription の結果が True の場合、このサブスクリプションの価格モデルを価格モデル間で移動できることを示します。 optedInDate の下に値がない場合、現在このサブスクリプションが古い価格モデルに設定されていることを意味します。

```
isGrandFatherableSubscription optedInDate
----------------------------- -----------
                         True            
```

このサブスクリプションを新しい価格モデルに移行するには、次を実行します。

```powershell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
```

変更が成功したことを確認するには、次を再実行します。

```powershell
$ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
```

移行が成功した場合、結果は次のようになります。

```
isGrandFatherableSubscription optedInDate                      
----------------------------- -----------                      
                         True 2018-05-31T13:52:43.3592081+00:00
```

optInDate に、このサブスクリプションが新しい価格モデルにオプトインしたときのタイムスタンプが含まれるようになります。

古い価格モデルに戻す必要がある場合は、次を実行します。

```powershell
 $ResourceID ="/subscriptions/<Subscription-ID-Here>/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action rollbacktolegacypricingmodel `
 -Force
```

その後、``-Action listmigrationdate`` が含まれる以前のスクリプトを再実行すると、optedInDate の値が空になり、サブスクリプションが従来の価格モデルに戻ったことを示します。

同じテナントにホストされた複数のサブスクリプションを移行する場合は、次のスクリプトを使用して独自のバリアントを作成できます。

```powershell
#Query tenant and create an array comprised of all of your tenants subscription IDs
$TenantId = <Your-tenant-id>
$Tenant =Get-AzSubscription -TenantId $TenantId
$Subscriptions = $Tenant.Id
```

テナント内のすべてのサブスクリプションで新しい価格モデルを使用できるかどうかを確認するには、次を実行します。

```powershell
Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force
}
```

スクリプトは、3 つの配列を生成するスクリプトを作成することで改善できます。 最初の配列は、```isGrandFatherableSubscription``` が True に設定されており、現在 optedInDate に値がないすべてのサブスクリプション ID で構成されます。 2 番目の配列は、現在新しい価格モデルになっているすべてのサブスクリプションです。 3 番目の配列には、テナント内にある新しい価格モデルを使用できないサブスクリプション ID のみが入力されます。

```powershell
[System.Collections.ArrayList]$Eligible= @{}
[System.Collections.ArrayList]$NewPricingEnabled = @{}
[System.Collections.ArrayList]$NotEligible = @{}

Foreach ($id in $Subscriptions)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
$Result= Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action listmigrationdate `
 -Force

     if ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $False)
     {
     $Eligible.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $True -and [bool]$Result.optedInDate -eq $True)
     {
     $NewPricingEnabled.Add($id)
     }

     elseif ($Result.isGrandFatherableSubscription -eq $False)
     {
     $NotEligible.add($id)
     }
}
```

> [!NOTE]
> サブスクリプションの数によっては、上記のスクリプトの実行に時間がかかる場合があります。 .add() メソッドの使用により、アイテムが各配列に追加されるたびに、PowerShell ウィンドウが値の増分をエコーします。

サブスクリプションが 3 つの配列に分割されたところで、結果を慎重に確認する必要があります。 将来変更を元に戻す必要性が出てきた場合に対応できるように、配列のコンテンツのバックアップ コピーを作成できます。 現在古い価格モデルを使用しており、かつ新しい価格モデルを使用できるすべてのサブスクリプションを変換する場合、このタスクは次のように実行できます。

```powershell
Foreach ($id in $Eligible)
{
$ResourceID ="/subscriptions/$id/providers/microsoft.insights"
Invoke-AzResourceAction `
 -ResourceId $ResourceID `
 -ApiVersion "2017-10-01" `
 -Action migratetonewpricingmodel `
 -Force
}

```
