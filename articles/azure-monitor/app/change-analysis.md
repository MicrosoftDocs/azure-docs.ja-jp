---
title: Azure Monitor でアプリケーション変更分析を使用して Web アプリの問題を見つける | Microsoft Docs
description: Azure App Service のライブ サイトに関するアプリケーションの問題をトラブルシューティングするには、Azure Monitor でアプリケーション変更分析を使用します。
services: application-insights
author: cawams
manager: carmonm
ms.assetid: ea2a28ed-4cd9-4006-bd5a-d4c76f4ec20b
ms.service: application-insights
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: cawa
ms.openlocfilehash: 84e423ac055c074028df217060a548b932823496
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71033381"
---
# <a name="use-application-change-analysis-preview-in-azure-monitor"></a>Azure Monitor でアプリケーション変更分析 (プレビュー) を使用する

ライブ サイトの問題や機能停止が発生した場合は、根本的な原因を迅速に特定することが重要です。 標準的な監視ソリューションでは、問題が警告される場合があります。 障害が発生しているコンポーネントが示される場合もあります。 ただし、この警告が障害の原因を直接的に説明しているとは限りません。 サイトは 5 分前には機能していたことがわかっています。そして今は機能していません。 この 5 分間でどのような変更があったのでしょうか。 これは、Azure Monitor でアプリケーション変更分析から回答が得られるように設計されている問題です。

変更分析は、[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) を基にして構築されており、Azure アプリケーションの変更に関する分析情報を提供し、可観測性を高めて MTTR (平均修理時間) を短縮します。

> [!IMPORTANT]
> 変更分析は現在プレビュー段階です。 このプレビュー バージョンは、サービス レベル アグリーメントなしに提供されます。 このバージョンは運用環境のワークロードにはお勧めできません。 一部の機能はサポート対象ではなく、機能が制限されることがあります。 詳細については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

## <a name="overview"></a>概要

変更分析では、インフラストラクチャ レイヤーからアプリケーション デプロイにわたって、さまざまな種類の変更を検出します。 サブスクリプション内のリソースの変更を確認するサブスクリプションレベルの Azure リソース プロバイダーです。 変更分析は、問題を引き起こした可能性がある変更をユーザーが理解するために役立つ、さまざまな診断ツール向けのデータを提供します。

以下の図に、変更分析のアーキテクチャを示します。

![どのように変更分析で変更データが取得され、クライアント ツールに提供されるかを示すアーキテクチャ図](./media/change-analysis/overview.png)

現在、変更分析は App Service Web アプリの**問題の診断と解決**エクスペリエンスに統合されています。 Web アプリで変更の検出と変更の表示を有効にするには、この記事で後述する「*Web アプリ機能の変更分析*」セクションを参照してください。

### <a name="azure-resource-manager-deployment-changes"></a>Azure Resource Manager デプロイの変更

変更分析では、[Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) が使用され、アプリケーションをホストしている Azure リソースが時間と共にどのように変更されたかを示す履歴の記録が提供されます。 変更分析では、たとえば、IP 構成ルール、マネージド ID、SSL 設定の変更を検出できます。 そのため、Web アプリにタグが追加された場合、変更分析にはその変更が反映されます。 この情報は、`Microsoft.ChangeAnalysis` リソース プロバイダーが Azure サブスクリプションで有効な限り、利用できます。

### <a name="changes-in-web-app-deployment-and-configuration"></a>Web アプリのデプロイと構成の変更

変更分析では、4 時間ごとにアプリケーションのデプロイと構成の状態がキャプチャされます。 たとえば、アプリケーション環境変数の変更を検出できます。 このツールで差分が計算され、変更点が提示されます。 Resource Manager の変更とは異なり、ツールでコード デプロイの変更情報をすぐに利用できない場合があります。 変更分析で最新の変更を表示するには、 **[Scan changes now]\(今すぐ変更をスキャン\)** を選択します。

![[Scan changes now]\(今すぐ変更をスキャン\) ボタンのスクリーンショット](./media/change-analysis/scan-changes.png)

### <a name="dependency-changes"></a>依存関係の変更

リソースの依存関係の変更も、Web アプリで問題を引き起こす場合があります。 たとえば、Web アプリから Redis キャッシュが呼び出される場合、Redis キャッシュ SKU が Web アプリのパフォーマンスに影響を与える可能性があります。 依存関係の変更を検出するために、変更分析では Web アプリの DNS レコードが確認されます。 このようにして、問題を引き起こす可能性のあるすべてのアプリ コンポーネントの変更が特定されます。
現在、次の依存関係がサポートされています。
- Web Apps
- Azure Storage
- Azure SQL


## <a name="change-analysis-for-the-web-apps-feature"></a>Web アプリ機能の変更分析

Azure Monitor では、現在、変更分析はセルフサービスの**問題の診断と解決**エクスペリエンスに組み込まれています。 このエクスペリエンスには、App Service アプリケーションの **[概要]** ページからアクセスします。

![[概要] ボタンと [問題の診断と解決] ボタンのスクリーンショット](./media/change-analysis/change-analysis.png)

### <a name="enable-change-analysis-in-the-diagnose-and-solve-problems-tool"></a>問題の診断と解決ツールで変更分析を有効にする

1. **[Availability and Performance]\(可用性とパフォーマンス\)** を選択します。

    ![[Availability and Performance]\(可用性とパフォーマンス\) のトラブルシューティング オプションのスクリーンショット](./media/change-analysis/availability-and-performance.png)

1. **[Application Changes]\(アプリケーションの変更\)** を選択します。 この機能は、 **[アプリケーションのクラッシュ]** でも使用できることに注意してください。

   ![[アプリケーションのクラッシュ] ボタンのスクリーンショット](./media/change-analysis/application-changes.png)

1. 変更分析を有効にするには、 **[今すぐ有効にする]** を選択します。

   ![[アプリケーションのクラッシュ] オプションのスクリーンショット](./media/change-analysis/enable-changeanalysis.png)

1. **変更分析**を有効にして **[保存]** を選択します。

    !["変更分析を有効にする" ユーザー インターフェイスのスクリーンショット](./media/change-analysis/change-analysis-on.png)


1. 変更分析にアクセスするには、 **[問題の診断と解決]**  >  **[Availability and Performance]\(可用性とパフォーマンス\)**  >  **[アプリケーションのクラッシュ]** の順に選択します。 変更の種類を時系列でまとめたグラフと、その変更の詳細が表示されます。

     ![変更の差分ビューのスクリーンショット](./media/change-analysis/change-view.png)


### <a name="enable-change-analysis-at-scale"></a>大規模な変更分析を有効にする

サブスクリプションに多数の Web アプリが含まれている場合、Web アプリのレベルでサービスを有効にすることは非効率的です。 サブスクリプション内のすべての Web アプリを有効にするには、次のスクリプトを実行します。

前提条件:
* PowerShell Az モジュール。 「[Azure PowerShell モジュールのインストール](https://docs.microsoft.com/en-us/powershell/azure/install-az-ps?view=azps-2.6.0)」の手順に従います

次のスクリプトを実行します。

```PowerShell
# Log in to your Azure subscription
Connect-AzAccount

# Get subscription Id
$SubscriptionId = Read-Host -Prompt 'Input your subscription Id'

# Make Feature Flag visible to the subscription
Set-AzContext -SubscriptionId $SubscriptionId

# Register resource provider
Register-AzResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis"


# Enable each web app
$webapp_list = Get-AzWebApp | Where-Object {$_.kind -eq 'app'}
foreach ($webapp in $webapp_list)
{
    $tags = $webapp.Tags
    $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
    Set-AzResource -ResourceId $webapp.Id -Tag $tags -Force
}

```



## <a name="next-steps"></a>次の手順

- [Azure App Service アプリ](azure-web-apps.md)の Application Insights を有効にします。
- [Azure VM と Azure 仮想マシン スケール セットの IIS でホストされたアプリ](azure-vm-vmss-apps.md)の Application Insights を有効にします。
- [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) の詳細を参照してください。変更分析の強化に役立ちます。
