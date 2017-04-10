---
title: "Azure 仮想マシン スケール セットの垂直方向のスケール | Microsoft Docs"
description: "Azure Automation によるアラートの監視に応じて仮想マシンを垂直方向にスケーリングする方法"
services: virtual-machine-scale-sets
documentationcenter: 
author: gbowerman
manager: madhana
editor: 
tags: azure-resource-manager
ms.assetid: 16b17421-6b8f-483e-8a84-26327c44e9d3
ms.service: virtual-machine-scale-sets
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 08/03/2016
ms.author: guybo
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: 4533b446f661568d0e70a23bb64880022038bb9a


---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>仮想マシン スケール セットの使用を開始する
この記事では、再プロビジョニングありまたはなしで Azure [仮想マシン スケール セット](https://azure.microsoft.com/services/virtual-machine-scale-sets/) を垂直方向にスケーリングする方法について説明します。 スケール セットにない仮想マシンの垂直方向のスケーリングについては、「 [Azure Automation で Azure 仮想マシンを垂直方向にスケーリングする](../virtual-machines/virtual-machines-windows-vertical-scaling-automation.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」を参照してください。

*スケール アップ*および*スケール ダウン*とも呼ばれる垂直スケーリングとは、ワークロードに応じて仮想マシン (VM) のサイズを増減させることを意味します。 これを、*スケール アウト*および*スケール イン*とも呼ばれる、仮想マシンの数がワークロードに応じて変更される[水平方向のスケーリング](virtual-machine-scale-sets-autoscale-overview.md)と比較してください。

再プロビジョニングとは、既存の仮想マシンを削除して、新しい仮想マシンで置き換えることを意味します。 仮想マシン スケール セットの仮想マシンのサイズを増減する場合、既存の仮想マシンのサイズを変更してデータを保持することも、新しいサイズの新しい仮想マシンをデプロイする必要があることもあります。 このドキュメントでは、どちらの場合についても説明します。

垂直スケーリングは、次のような場合に便利です。

* 仮想マシン上に構築されたサービスの使用率が低い場合 (週末のみなど)。 仮想マシンのサイズを小さくすると、月額料金を削減できます。
* より大きな要求に応じるために、追加の仮想マシンを作成せずに仮想マシンのサイズを増やす場合。

仮想マシン スケール セットからのメトリック ベース アラートに基づいて、トリガーされる垂直方向のスケーリングをセットアップできます。 アラートがアクティブになると、Webhook によって、スケール セットをスケールアップまたはダウンできる Runbook がトリガーされます。 垂直方向のスケーリングは、以下の手順で構成できます。

1. 実行機能を持つ Azure Automation アカウントを作成します。
2. サブスクリプションに、仮想マシン スケール セット向けの Azure Automation の垂直スケールの Runbook をインポートします。
3. Webhook を Runbook に追加します。
4. Webhook 通知を使用して、VM スケール セットにアラートを追加します。

> [!NOTE]
> 垂直方向の自動スケールは、特定の VM のサイズ範囲内でのみ実行できます。 スケールを決める前に各サイズの仕様を比較してください (大きな数値が必ずしも大きな VM サイズを示すとは限りません)。 次のようなサイズのペアの間でスケールの設定を選択できます。
> 
> | VM サイズのスケーリングのペア |  |
> | --- | --- |
> | Standard_A0 |Standard_A11 |
> | Standard_D1 |Standard_D14 |
> | Standard_DS1 |Standard_DS14 |
> | Standard_D1v2 |Standard_D15v2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> 
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>実行機能を持つ Azure Automation アカウントを作成する
最初に、VM スケール セットのインスタンスをスケーリングするために使用する runbook をホストする、Azure Automation アカウントを作成する必要があります。 最近、 [Azure Automation](https://azure.microsoft.com/services/automation/) では、ユーザーの代わりに非常に簡単に Runbook を自動的に実行するためのサービス プリンシパルをセットアップする "アカウントとして実行" 機能が導入されました。 詳しくは、次の記事を参照してください。

* [Azure 実行アカウントを使用した Runbook の認証](../automation/automation-sec-configure-azure-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>サブスクリプションに Azure Automation の垂直スケールの Runbook をインポートする
VM スケール セットの垂直方向へのスケーリングに必要な Runbook は、Azure Automation Runbook ギャラリーに既に公開されています。 Runbook をサブスクリプションにインポートするには、次の記事の手順に従ってください。

* [Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](../automation/automation-runbook-gallery.md)

Runbook のメニューから、[ギャラリーの参照] オプションを選択します。

![インポートする Runbook][runbooks]

インポートする必要がある Runbook が表示されます。 垂直スケーリングを再プロビジョニングありまたはなしで実行するかどうかに基づいて、Runbook を選択します。

![Runbook ギャラリー][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Webhook を Runbook に追加する
Runbook をインポートしたら、VM スケール セットからのアラートによって Webhook がトリガーされるように、Runbook に追加する必要があります。 Runbook で Webhook を作成する方法の詳細は、次の記事を参照してください。

* [Azure Automation Webhook](../automation/automation-webhooks.md)

> [!NOTE]
> Webhook のダイアログを閉じる前に、Webhook の URL をコピーしてください。これは次のセクションで必要になります。
> 
> 

## <a name="add-an-alert-to-your-vm-scale-set"></a>VM スケール セットにアラートを追加する
VM スケール セットにアラートを追加する方法を説明する PowerShell スクリプトを以下に示します。 アラートを発生させるメトリックの名前を取得するには、「[Azure Insights の自動スケールの一般的なメトリック](../monitoring-and-diagnostics/insights-autoscale-common-metrics.md)」をご覧ください。

```
$actionEmail = New-AzureRmAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzureRmAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzureRmMetricAlertRule  -Name  $alertName `
                            -Location  $location `
                            -ResourceGroup $rg `
                            -TargetResourceId $id `
                            -MetricName $metricName `
                            -Operator  $condition `
                            -Threshold $threshold `
                            -WindowSize  $timeWindow `
                            -TimeAggregationOperator Average `
                            -Actions $actionEmail, $actionWebhook `
                            -Description $description
```

> [!NOTE]
> 垂直スケーリングおよび関連付けられているサービスの中断が頻繁に発生しないようにするために、アラートの期間を適切に制限することをお勧めします。 少なくとも 20 - 30 分以上の間隔を検討してください。 中断を避けるには、水平スケーリングを検討してください。
> 
> 

警告を作成する方法について詳しくは、次の記事を参照してください。

* [Azure Insights の PowerShell クイック スタート サンプル](../monitoring-and-diagnostics/insights-powershell-samples.md)
* [Azure Insights クロスプラットフォーム CLI のクイック スタート サンプル](../monitoring-and-diagnostics/insights-cli-samples.md)

## <a name="summary"></a>概要
この記事では、簡単な垂直スケーリングの例を示しました。 これらの構成要素 (Automation アカウント、Runbook、Webhook、アラート) を使用して、さまざまなイベントを、カスタマイズされた一連のアクションで接続できます。

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png



<!--HONumber=Nov16_HO3-->


