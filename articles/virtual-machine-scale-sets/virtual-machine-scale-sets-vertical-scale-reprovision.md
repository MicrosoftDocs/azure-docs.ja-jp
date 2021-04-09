---
title: Azure 仮想マシン スケール セットの垂直方向のスケール
description: Azure Automation によるアラートの監視に応じて仮想マシンを垂直方向にスケーリングする方法
author: ju-shim
ms.author: jushiman
ms.topic: how-to
ms.service: virtual-machine-scale-sets
ms.subservice: autoscale
ms.date: 04/18/2019
ms.reviewer: avverma
ms.custom: avverma
ms.openlocfilehash: b172f1f7137b53e98384d92c9c709694eaf0b7e9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594496"
---
# <a name="vertical-autoscale-with-virtual-machine-scale-sets"></a>仮想マシン スケール セットを使用した垂直方向の自動スケール

この記事では、再プロビジョニングありまたはなしで Azure [仮想マシン スケール セット](https://azure.microsoft.com/services/virtual-machine-scale-sets/) を垂直方向にスケーリングする方法について説明します。 

*スケール アップ* および *スケール ダウン* とも呼ばれる垂直スケーリングとは、ワークロードに応じて仮想マシン (VM) のサイズを増減させることを意味します。 この動作を、*スケール アウト* および *スケール イン* とも呼ばれる、VM の数がワークロードに応じて変更される [水平方向のスケーリング](virtual-machine-scale-sets-autoscale-overview.md)と比較してください。

再プロビジョニングとは、既存の仮想マシンを削除して、新しい仮想マシンで置き換えることを意味します。 仮想マシン スケール セットの VM のサイズを増減する場合、既存の VM のサイズを変更してデータを保持することも、新しいサイズの新しい VM をデプロイする必要があることもあります。 このドキュメントでは、どちらの場合についても説明します。

垂直スケーリングは、次のような場合に便利です。

* 仮想マシン上に構築されたサービスの使用率が低い場合 (週末のみなど)。 仮想マシンのサイズを小さくすると、月額料金を削減できます。
* より大きな要求に応じるために、追加の仮想マシンを作成せずに仮想マシンのサイズを増やす場合。

仮想マシン スケール セットからのメトリック ベース アラートに基づいて、トリガーされる垂直方向のスケーリングをセットアップできます。 アラートがアクティブになると、Webhook によって、スケール セットをスケールアップまたはダウンできる Runbook がトリガーされます。 垂直方向のスケーリングは、以下の手順で構成できます。

1. 実行機能を持つ Azure Automation アカウントを作成します。
2. サブスクリプションに、仮想マシン スケール セット向けの Azure Automation の垂直スケールの Runbook をインポートします。
3. Webhook を Runbook に追加します。
4. Webhook 通知を使用して、仮想マシン スケール セットにアラートを追加します。

> [!NOTE]
> 最初の仮想マシンのサイズによっては、スケーリングできるサイズが制限される場合があります。これは、その仮想マシンがデプロイされているクラスターの空き容量によるものです。 この記事で使用される公開済みの Automation Runbook では、このケースのみを扱い、VM のサイズ ペアを超えない範囲でのみスケーリングします。 つまり、Standard_D1v2 仮想マシンが急に Standard_G5 にスケールアップしたり、Basic_A0 にスケールダウンしたりすることはありません。 また、制約付きの仮想マシンのサイズのスケールアップ/スケールダウンはサポートされていません。 次のようなサイズのペアの間でスケールの設定を選択できます。
> 
> | VM サイズのスケーリング ペアのメンバー | メンバー |
> | --- | --- |
> | Basic_A0 |Basic_A4 |
> | Standard_A0 |Standard_A4 |
> | Standard_A5 |Standard_A7 |
> | Standard_A8 |Standard_A9 |
> | Standard_A10 |Standard_A11 |
> | Standard_A1_v2 |Standard_A8_v2 |
> | Standard_A2m_v2 |Standard_A8m_v2  |
> | Standard_B1s |Standard_B2s |
> | Standard_B1ms |Standard_B8ms |
> | Standard_D1 |Standard_D4 |
> | Standard_D11 |Standard_D14 |
> | Standard_DS1 |Standard_DS4 |
> | Standard_DS11 |Standard_DS14 |
> | Standard_D1_v2 |Standard_D5_v2 |
> | Standard_D11_v2 |Standard_D14_v2 |
> | Standard_DS1_v2 |Standard_DS5_v2 |
> | Standard_DS11_v2 |Standard_DS14_v2 |
> | Standard_D2_v3 |Standard_D64_v3 |
> | Standard_D2s_v3 |Standard_D64s_v3 |
> | Standard_DC2s |Standard_DC4s |
> | Standard_E2_v3 |Standard_E64_v3 |
> | Standard_E2s_v3 |Standard_E64s_v3 |
> | Standard_F1 |Standard_F16 |
> | Standard_F1s |Standard_F16s |
> | Standard_F2sv2 |Standard_F72sv2 |
> | Standard_G1 |Standard_G5 |
> | Standard_GS1 |Standard_GS5 |
> | Standard_H8 |Standard_H16 |
> | Standard_H8m |Standard_H16m |
> | Standard_L4s |Standard_L32s |
> | Standard_L8s_v2 |Standard_L80s_v2 |
> | Standard_M8ms  |Standard_M128ms |
> | Standard_M32ls  |Standard_M64ls |
> | Standard_M64s  |Standard_M128s |
> | Standard_M64  |Standard_M128 |
> | Standard_M64m  |Standard_M128m |
> | Standard_NC6 |Standard_NC24 |
> | Standard_NC6s_v2 |Standard_NC24s_v2 |
> | Standard_NC6s_v3 |Standard_NC24s_v3 |
> | Standard_ND6s |Standard_ND24s |
> | Standard_NV6 |Standard_NV24 |
> | Standard_NV6s_v2 |Standard_NV24s_v2 |
> | Standard_NV12s_v3 |Standard_NV48s_v3 |
> 

## <a name="create-an-azure-automation-account-with-run-as-capability"></a>実行機能を持つ Azure Automation アカウントを作成する
最初に、仮想マシン スケール セットのインスタンスをスケーリングするために使用する runbook をホストする、Azure Automation アカウントを作成する必要があります。 最近、[Azure Automation](https://azure.microsoft.com/services/automation/) では、ユーザーの代わりに Runbook を自動的に実行するためのサービス プリンシパルをセットアップする "アカウントとして実行" 機能が導入されました。 詳細については、次を参照してください。

* [Azure 実行アカウントを使用した Runbook の認証](../automation/manage-runas-account.md)

## <a name="import-azure-automation-vertical-scale-runbooks-into-your-subscription"></a>サブスクリプションに Azure Automation の垂直スケールの Runbook をインポートする

仮想マシン スケール セットの垂直方向へのスケーリングに必要な Runbook は、Azure Automation Runbook ギャラリーに既に公開されています。 Runbook をサブスクリプションにインポートするには、次の記事の手順に従ってください。

* [Azure Automation 用の Runbook ギャラリーとモジュール ギャラリー](../automation/automation-runbook-gallery.md)

Runbook のメニューから、[ギャラリーの参照] オプションを選択します。

![インポートする Runbook][runbooks]

インポートする必要がある Runbook が表示されます。 垂直スケーリングを再プロビジョニングありまたはなしで実行するかどうかに基づいて、Runbook を選択します。

![Runbook ギャラリー][gallery]

## <a name="add-a-webhook-to-your-runbook"></a>Webhook を Runbook に追加する

Runbook をインポートしたら、仮想マシン スケール セットからのアラートによって Webhook がトリガーされるように、Runbook に追加します。 Runbook で Webhook を作成する方法の詳細は、次の記事を参照してください。

* [Azure Automation Webhook](../automation/automation-webhooks.md)

> [!NOTE]
> Webhook のダイアログを閉じる前に、Webhook の URL をコピーしてください。このアドレスは次のセクションで必要になります。
> 
> 

## <a name="add-an-alert-to-your-virtual-machine-scale-set"></a>仮想マシン スケール セットにアラートを追加する

仮想マシン スケール セットにアラートを追加する方法を説明する PowerShell スクリプトを以下に示します。 アラートを発生させるメトリックの名前を取得するには、「[Azure Monitor の自動スケールの一般的なメトリック](../azure-monitor/autoscale/autoscale-common-metrics.md)」をご覧ください。

```powershell
$actionEmail = New-AzAlertRuleEmail -CustomEmail user@contoso.com
$actionWebhook = New-AzAlertRuleWebhook -ServiceUri <uri-of-the-webhook>
$threshold = <value-of-the-threshold>
$rg = <resource-group-name>
$id = <resource-id-to-add-the-alert-to>
$location = <location-of-the-resource>
$alertName = <name-of-the-resource>
$metricName = <metric-to-fire-the-alert-on>
$timeWindow = <time-window-in-hh:mm:ss-format>
$condition = <condition-for-the-threshold> # Other valid values are LessThanOrEqual, GreaterThan, GreaterThanOrEqual
$description = <description-for-the-alert>

Add-AzMetricAlertRule  -Name  $alertName `
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

アラートを作成する方法について詳しくは、次の記事を参照してください。

* [Azure Monitor PowerShell のサンプル](../azure-monitor/powershell-samples.md)
* [Azure Monitor クロスプラットフォーム CLI のサンプル](../azure-monitor/cli-samples.md)

## <a name="summary"></a>まとめ

この記事では、簡単な垂直スケーリングの例を示しました。 これらの構成要素 (Automation アカウント、Runbook、Webhook、アラート) を使用して、さまざまなイベントを、カスタマイズされた一連のアクションで接続できます。

[runbooks]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks.png
[gallery]: ./media/virtual-machine-scale-sets-vertical-scale-reprovision/runbooks-gallery.png
