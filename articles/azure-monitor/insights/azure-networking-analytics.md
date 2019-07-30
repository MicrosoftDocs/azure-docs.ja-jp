---
title: Azure Monitor の Azure Networking Analytics ソリューション | Microsoft Docs
description: Azure Monitor の Azure Networking Analytics ソリューションを使用すると、Azure ネットワーク セキュリティ グループのログと Azure Application Gateway のログをレビューできます。
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.assetid: 66a3b8a1-6c55-4533-9538-cad60c18f28b
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: bwren
ms.openlocfilehash: 13908706f8dcec0eb2d1773bcef2ee622b4ebcc1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67048639"
---
# <a name="azure-networking-monitoring-solutions-in-azure-monitor"></a>Azure Monitor の Azure ネットワーク監視ソリューション

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Azure Monitor には、ネットワークを監視することを目的とした次のソリューションが用意されています。
* ネットワーク パフォーマンス モニター (NPM)
    * ネットワークの正常性の監視
* Azure Application Gateway 分析によるレビュー
    * Azure Application Gateway のログ
    * Azure Application Gateway のメトリック
* クラウド ネットワークのネットワーク アクティビティを監視および監査するためのソリューション
    * [Traffic Analytics](https://docs.microsoft.com/azure/networking/network-monitoring-overview#traffic-analytics) 
    * Azure ネットワーク セキュリティ グループ分析

## <a name="network-performance-monitor-npm"></a>ネットワーク パフォーマンス モニター (NPM)

[ネットワーク パフォーマンス モニター](https://docs.microsoft.com/azure/networking/network-monitoring-overview)管理ソリューションは、ネットワークの正常性、可用性、到達の可能性を監視するネットワーク監視ソリューションです。  次の 2 点間の接続を監視する目的で使用します。

* パブリック クラウドとオンプレミス
* データ センターとユーザー拠点 (支社)
* 多層アプリケーションの各種階層をホストするサブネット間。

詳細については、[ネットワーク パフォーマンス モニター](https://docs.microsoft.com/azure/networking/network-monitoring-overview)に関するページを参照してください。

## <a name="azure-application-gateway-and-network-security-group-analytics"></a>Azure Application Gateway とネットワーク セキュリティ グループの分析
ソリューションを使用するには次の手順に従います。
1. 管理ソリューションを Azure Monitor に追加します。
2. 診断を有効にして、Azure Monitor の Log Analytics ワークスペースに診断結果をリダイレクトします。 Azure Blob Storage にログを書き込む必要はありません。

診断を有効にし、Application Gateway とネットワーク セキュリティ グループのいずれかまたは両方に対応するソリューションを有効にすることができます。

ソリューションをインストールしたものの特定のリソース タイプの診断ログを有効にしなかった場合、そのリソースに対応するダッシュボードのブレードは空白になり、エラー メッセージが表示されます。

> [!NOTE]
> 2017 年 1 月、Application Gateway とネットワーク セキュリティ グループから Log Analytics ワークスペースへのログ送信をサポートする方法が変更になりました。 **Azure Networking Analytics (非推奨)** ソリューションが表示される場合は、「[旧バージョンの Networking Analytics ソリューションからの移行](#migrating-from-the-old-networking-analytics-solution)」を参照して手順に従ってください。
>
>

## <a name="review-azure-networking-data-collection-details"></a>Azure Networking データ収集の詳細の確認
Azure Application Gateway とネットワーク セキュリティ グループの診断ログは、Azure Application Gateway 分析とネットワーク セキュリティ グループ分析の管理ソリューションによって直接収集されます。 Azure Blob Storage にログを記述する必要はありません。データ収集のエージェントも不要です。

次の表は、Azure Application Gateway 分析とネットワーク セキュリティ グループ分析におけるデータの収集に関して、その手段と各種情報をまとめたものです。

| プラットフォーム | 直接エージェント | Systems Center Operations Manager エージェント | Azure | Operations Manager が必要か | 管理グループによって送信される Operations Manager エージェントのデータ | 収集の頻度 |
| --- | --- | --- | --- | --- | --- | --- |
| Azure |  |  |&#8226; |  |  |ログの際 |


## <a name="azure-application-gateway-analytics-solution-in-azure-monitor"></a>Azure Monitor の Azure Application Gateway 分析ソリューション

![Azure Application Gateway 分析のシンボル](media/azure-networking-analytics/azure-analytics-symbol.png)

Application Gateway に関しては、次のログがサポートされます。

* ApplicationGatewayAccessLog
* ApplicationGatewayPerformanceLog
* ApplicationGatewayFirewallLog

Application Gateway に関しては、次のメトリックがサポートされます。


* 5 分間のスループット

### <a name="install-and-configure-the-solution"></a>ソリューションのインストールと構成
Azure Application Gateway 分析ソリューションのインストールと構成は、次の手順で行います。

1. Azure Application Gateway 分析ソリューションを有効にします。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureAppGatewayAnalyticsOMS?tab=Overview) から有効にするか、[ソリューション ギャラリーからの Azure Monitor ソリューションの追加](../../azure-monitor/insights/solutions.md)に関するページで説明されている手順に従って有効にします。
2. 監視する [Application Gateway](../../application-gateway/application-gateway-diagnostics.md) の診断ログを有効にします。

#### <a name="enable-azure-application-gateway-diagnostics-in-the-portal"></a>Azure Application Gateway の診断を Azure Portal で有効にする

1. Azure portal で、監視する Application Gateway リソースに移動します。
2. *[診断ログ]* を選択して、次のページを開きます。

   ![Azure Application Gateway リソースの画像](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics01.png)
3. *[診断を有効にする]* をクリックして、次のページを開きます。

   ![Azure Application Gateway リソースの画像](media/azure-networking-analytics/log-analytics-appgateway-enable-diagnostics02.png)
4. 診断を有効にするには、 *[状態]* の下の *[オン]* をクリックします。
5. *[Log Analytics への送信]* チェックボックスをオンにします。
6. 既存の Log Analytics ワークスペースを選択するか、ワークスペースを作成します。
7. 収集するログの種類ごとに **[ログ]** の下のチェックボックスをオンにます。
8. *[保存]* をクリックして、Azure Monitor への診断のログ記録を有効にします。

#### <a name="enable-azure-network-diagnostics-using-powershell"></a>PowerShell を使用した Azure ネットワーク診断を有効にする

次の PowerShell スクリプトは、Application Gateway の診断ログを有効にする方法の例を示しています。

```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$gateway = Get-AzApplicationGateway -Name 'ContosoGateway'

Set-AzDiagnosticSetting -ResourceId $gateway.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-application-gateway-analytics"></a>Azure Application Gateway 分析の使用
![Azure Application Gateway 分析タイルの画像](media/azure-networking-analytics/log-analytics-appgateway-tile.png)

[概要] で **[Azure Application Gateway 分析]** タイルをクリックした後、収集されたログの概要を表示し、次のカテゴリについて詳細表示できます。

* Application Gateway のアクセス ログ
  * Application Gateway のアクセス ログにおけるクライアント エラーとサーバー エラー
  * 1 時間あたりの要求数 (Application Gateway ごと)
  * 1 時間あたりの失敗した要求数 (Application Gateway ごと)
  * Application Gateway のユーザー エージェントごとのエラー数
* Application Gateway のパフォーマンス
  * Application Gateway のホストの正常性
  * Application Gateway の失敗した要求数の最大と 95 パーセンタイル

![Azure Application Gateway 分析ダッシュボードの画像](media/azure-networking-analytics/log-analytics-appgateway01.png)

![Azure Application Gateway 分析ダッシュボードの画像](media/azure-networking-analytics/log-analytics-appgateway02.png)

**[Azure Application Gateway 分析]** ダッシュボードにあるいずれかのブレードで概要情報を確認し、ログの検索ページで、詳細情報の表示対象をクリックします。

どのログの検索ページでも、時間、詳細結果、ログ検索履歴を表示することができます。 結果を絞り込むファセットを使用してフィルター処理することもできます。


## <a name="azure-network-security-group-analytics-solution-in-azure-monitor"></a>Azure Monitor の Azure ネットワーク セキュリティ グループ分析ソリューション

![Azure ネットワーク セキュリティ グループ分析のシンボル](media/azure-networking-analytics/azure-analytics-symbol.png)

> [!NOTE]
> [Traffic Analytics](../../network-watcher/traffic-analytics.md)によって機能が置き換えられたので、ネットワーク セキュリティ グループ分析ソリューションは、コミュニティ サポートに移動します。
> - ソリューションは [Azure クイック スタート テンプレート](https://azure.microsoft.com/resources/templates/oms-azurensg-solution/)で利用可能になっており、まもなく Azure Marketplace では利用できなくなります。
> - ソリューションを自身のワークスペースに既に追加している既存のお客様については、変更せずに機能し続けます。
> - Microsoft は、診断設定を使用して、ワークスペースへの NSG 診断ログの送信をサポートし続けます。

ネットワーク セキュリティ グループに関しては、次のログがサポートされます。

* NetworkSecurityGroupEvent
* NetworkSecurityGroupRuleCounter

### <a name="install-and-configure-the-solution"></a>ソリューションのインストールと構成
Azure Networking Analytics ソリューションのインストールと構成は、次の手順で行います。

1. Azure ネットワーク セキュリティ グループ分析ソリューションを有効にします。[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureNSGAnalyticsOMS?tab=Overview) から有効にするか、[ソリューション ギャラリーからの Azure Monitor ソリューションの追加](../../azure-monitor/insights/solutions.md)に関するページで説明されている手順に従って有効にします。
2. 監視する[ネットワーク セキュリティ グループ](../../virtual-network/virtual-network-nsg-manage-log.md)のリソースの診断ログを有効にします。

### <a name="enable-azure-network-security-group-diagnostics-in-the-portal"></a>Azure Portal で Azure ネットワーク セキュリティ グループの診断を有効にする

1. Azure Portal で、監視するネットワーク セキュリティ グループのリソースに移動します。
2. *[診断ログ]* を選択して、次のページを開きます。

   ![Azure ネットワーク セキュリティ グループのリソースの画像](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics01.png)
3. *[診断を有効にする]* をクリックして、次のページを開きます。

   ![Azure ネットワーク セキュリティ グループのリソースの画像](media/azure-networking-analytics/log-analytics-nsg-enable-diagnostics02.png)
4. 診断を有効にするには、 *[状態]* の下の *[オン]* をクリックします。
5. *[Send to Log Analytics]* (Log Analytics に送信) のチェックボックスをクリックします。
6. 既存の Log Analytics ワークスペースを選択するか、ワークスペースを作成します。
7. 収集するログの種類ごとに **[ログ]** の下のチェックボックスをクリックします。
8. *[保存]* をクリックして Log Analytics の診断ログを有効にします。

### <a name="enable-azure-network-diagnostics-using-powershell"></a>PowerShell を使用した Azure ネットワーク診断を有効にする

次の PowerShell スクリプトは、ネットワーク セキュリティ グループの診断ログを有効にする方法の例を示しています。
```powershell
$workspaceId = "/subscriptions/d2e37fee-1234-40b2-5678-0b2199de3b50/resourcegroups/oi-default-east-us/providers/microsoft.operationalinsights/workspaces/rollingbaskets"

$nsg = Get-AzNetworkSecurityGroup -Name 'ContosoNSG'

Set-AzDiagnosticSetting -ResourceId $nsg.ResourceId  -WorkspaceId $workspaceId -Enabled $true
```

### <a name="use-azure-network-security-group-analytics"></a>Azure ネットワーク セキュリティ グループ分析を使用する
[概要] で **[Azure ネットワーク セキュリティ グループ分析]** タイルをクリックした後、収集されたログの概要を表示し、次のカテゴリについて詳細表示することができます。

* ネットワーク セキュリティ グループのブロック済みフロー数
  * ネットワーク セキュリティ グループの規則とブロック済みフロー数
  * MAC アドレスとブロック済みフロー数
* ネットワーク セキュリティ グループの許可済みフロー数
  * ネットワーク セキュリティ グループの規則と許可済みフロー数
  * MAC アドレスと許可済みフロー数

![Azure ネットワーク セキュリティ グループ分析ダッシュボードの画像](media/azure-networking-analytics/log-analytics-nsg01.png)

![Azure ネットワーク セキュリティ グループ分析ダッシュボードの画像](media/azure-networking-analytics/log-analytics-nsg02.png)

**[Azure ネットワーク セキュリティ グループ分析]** ダッシュボードにあるいずれかのブレードで概要情報を確認し、ログの検索ページで、詳細情報の表示対象をクリックします。

どのログの検索ページでも、時間、詳細結果、ログ検索履歴を表示することができます。 結果を絞り込むファセットを使用してフィルター処理することもできます。

## <a name="migrating-from-the-old-networking-analytics-solution"></a>旧バージョンの Networking Analytics ソリューションからの移行
2017 年 1 月、Azure Application Gateway と Azure ネットワーク セキュリティ グループから Log Analytics ワークスペースへのログ送信をサポートする方法が変更になりました。 これらの変更には次の利点があります。
+ ストレージ アカウントを使用する必要がなく、ログは Azure Monitor に直接書き込まれます。
+ ログが生成されてから Azure Monitor で使用できるまでの待機時間が短縮されます。
+ 構成手順が簡素化されます。
+ すべての種類の Azure Diagnostics の共通形式です。

最新のソリューションを使用するには、次の操作を行います。

1. [Azure Application Gateway から Azure Monitor に診断が直接送信されるように構成します。](#enable-azure-application-gateway-diagnostics-in-the-portal)
2. [Azure ネットワーク セキュリティ グループから Azure Monitor に診断が直接送信されるように構成します。](#enable-azure-network-security-group-diagnostics-in-the-portal)
2. [ソリューション ギャラリーからの Azure Monitor ソリューションの追加](solutions.md)に関する記事で説明されている手順に従って、*Azure Application Gateway Analytics* ソリューションと *Azure Network Security Group Analytics* ソリューションを有効にします。
3. 新しいデータ型を使用するように、保存されたクエリ、ダッシュボード、またはアラートを更新します。
   + 型を AzureDiagnostics にします。 ResourceType を使用して、Azure ネットワーク ログをフィルター処理できます。

     | 代替のデータ型は次のとおりです。 | 次のコマンドを使用します。 |
     | --- | --- |
     | NetworkApplicationgateways &#124; where OperationName=="ApplicationGatewayAccess" | AzureDiagnostics &#124; where ResourceType=="APPLICATIONGATEWAYS" and OperationName=="ApplicationGatewayAccess" |
     | NetworkApplicationgateways &#124; where OperationName=="ApplicationGatewayPerformance" | AzureDiagnostics &#124; where ResourceType=="APPLICATIONGATEWAYS" and OperationName=="ApplicationGatewayPerformance" |
     | NetworkSecuritygroups | AzureDiagnostics &#124; where ResourceType=="NETWORKSECURITYGROUPS" |

   + 名前に \_s、\_d、または \_g のサフィックスがあるフィールドについては、最初の文字を小文字に変更します。
   + 名前に \_o のサフィックスがあるフィールドについては、入れ子になったフィールド名に基づき、データは個別のフィールドに分割されます。
4. *Azure Networking Analytics (非推奨)* ソリューションを削除します。
   + PowerShell を使用している場合は、次のコードを使用します。`Set-AzureOperationalInsightsIntelligencePack -ResourceGroupName <resource group that the workspace is in> -WorkspaceName <name of the log analytics workspace> -IntelligencePackName "AzureNetwork" -Enabled $false`

変更前に収集されたデータは、新しいソリューションには表示されません。 元の型とフィールド名を使用して、このデータのクエリを続行できます。

## <a name="troubleshooting"></a>トラブルシューティング
[!INCLUDE [log-analytics-troubleshoot-azure-diagnostics](../../../includes/log-analytics-troubleshoot-azure-diagnostics.md)]

## <a name="next-steps"></a>次の手順
* [Azure Monitor でログ クエリ](../log-query/log-query-overview.md)を使用して、詳細な Azure 診断データを表示します。
