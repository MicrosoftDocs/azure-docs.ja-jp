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
ms.openlocfilehash: 45df8f9e57223ea60a11c6af2187d362184cae2b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/28/2019
ms.locfileid: "67443377"
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

サブスクリプションに多数の Web アプリが含まれている場合、Web アプリのレベルでサービスを有効にすることは非効率的です。 この場合は、次の代替手順に従ってください。

### <a name="register-the-change-analysis-resource-provider-for-your-subscription"></a>サブスクリプション用の変更分析リソース プロバイダーを登録する

1. 変更分析機能フラグ (プレビュー) を登録します。 機能フラグはプレビュー段階なので、登録してサブスクリプションに表示されるようにする必要があります。

   1. [Azure Cloud Shell](https://azure.microsoft.com/features/cloud-shell/) を開きます。

      ![変更の Cloud Shell のスクリーンショット](./media/change-analysis/cloud-shell.png)

   1. シェルの種類を **PowerShell** に変更します。

      ![変更の Cloud Shell のスクリーンショット](./media/change-analysis/choose-powershell.png)

   1. 次の PowerShell コマンドを実行します。

        ``` PowerShell
        Set-AzContext -Subscription <your_subscription_id> #set script execution context to the subscription you are trying to enable
        Get-AzureRmProviderFeature -ProviderNamespace "Microsoft.ChangeAnalysis" -ListAvailable #Check for feature flag availability
        Register-AzureRmProviderFeature -FeatureName PreviewAccess -ProviderNamespace Microsoft.ChangeAnalysis #Register feature flag
        ```

1. サブスクリプション用の変更分析リソース プロバイダーを登録します。

   - **[サブスクリプション]** に移動し、変更サービスで有効にするサブスクリプションを選択します。 次にリソース プロバイダーを選択します。

        ![変更分析リソース プロバイダーを登録する方法を示すスクリーンショット](./media/change-analysis/register-rp.png)

       - **[Microsoft.ChangeAnalysis]** を選択します。 次に、ページ上部にある **[登録]** を選択します。

       - リソース プロバイダーを有効にすると、Web アプリに非表示のタグを設定して、デプロイ レベルで変更を検出できます。 非表示のタグを設定するには、「**Unable to fetch Change Analysis information (変更分析の情報をフェッチできない)** 」の手順を実行してください。

   - または、PowerShell スクリプトを使用してリソース プロバイダーを登録することもできます。

        ```PowerShell
        Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState #Check if RP is ready for registration

        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.ChangeAnalysis" #Register the Change Analysis RP
        ```

        PowerShell を使用して Web アプリに非表示のタグを設定するには、次のコマンドを実行します。

        ```powershell
        $webapp=Get-AzWebApp -Name <name_of_your_webapp>
        $tags = $webapp.Tags
        $tags[“hidden-related:diagnostics/changeAnalysisScanEnabled”]=$true
        Set-AzResource -ResourceId <your_webapp_resourceid> -Tag $tag
        ```

     > [!NOTE]
     > 非表示のタグを追加しても、変更が表示されるまでには最大 4 時間かかることがあります。 結果が遅延するのは、変更分析では 4 時間ごとに Web アプリがスキャンされるためです。 4 時間のスケジュールによって、スキャンのパフォーマンスへの影響が抑えられます。

## <a name="next-steps"></a>次の手順

- Azure Monitor で [Application Insights 機能を有効にする](azure-web-apps.md)ことで、App Service をより効果的に監視します。
- [Azure Resource Graph](https://docs.microsoft.com/azure/governance/resource-graph/overview) の詳細を参照してください。変更分析の強化に役立ちます。
