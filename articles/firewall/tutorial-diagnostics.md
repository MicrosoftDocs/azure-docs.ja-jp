---
title: 'チュートリアル: Azure Firewall のログとメトリックを監視する'
description: このチュートリアルでは、Azure Firewall のログとメトリックを有効にして管理する方法を学習します。
services: firewall
author: vhorne
ms.service: firewall
ms.topic: tutorial
ms.date: 10/24/2018
ms.author: victorh
ms.openlocfilehash: 393441e7ff620f3795e42c2cb376f99f8763f25b
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/18/2019
ms.locfileid: "59044863"
---
# <a name="tutorial-monitor-azure-firewall-logs-and-metrics"></a>チュートリアル:Azure Firewall のログとメトリックを監視する

Azure Firewall を監視するには、ファイアウォール ログを使用できます。 また、アクティビティ ログを使用して、Azure Firewall リソースに対する操作を監査することもできます。 メトリックを使用して、ポータルにパフォーマンス カウンターを表示できます。 

一部のログにはポータルからアクセスできます。 ログを [Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)、Storage、および Event Hubs に送信し、Azure Monitor ログや他のツール (Excel、Power BI など) で分析することができます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Portal を使用したログの有効化
> * PowerShell を使用したログの有効化
> * アクティビティ ログの表示と分析
> * ネットワークおよびアプリケーション ルール ログの表示と分析
> * メトリックを表示する


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを開始する前に、[Azure Firewall のログとメトリック](logs-and-metrics.md)に関する記事で、Azure Firewall で 入手できる診断ログとメトリックの概要を確認してください。


## <a name="enable-diagnostic-logging-through-the-azure-portal"></a>Azure portal を使用した診断ログの有効化

この手順を完了して診断ログを有効にしてからデータがログに記録されるまでに数分かかることがあります。 最初に何も表示されない場合は、数分後にもう一度確認してください。

1. Azure portal で、ファイアウォール リソース グループを開き、ファイアウォールをクリックします。
2. **[監視]** の **[診断設定]** をクリックします。

   Azure Firewall の場合、サービスに固有の 2 種類のログを使用できます。

   * AzureFirewallApplicationRule
   * AzureFirewallNetworkRule

3. データの収集を開始するには、**[診断を有効にする]** をクリックします。
4. **[診断設定]** ページに、診断ログの設定が表示されます。 
5. この例では、Azure Monitor ログにログを保存するため、名前として「**Firewall log analytics**」と入力します。
6. **[Send to Log Analytics]\(Log Analytics に送信\)** をクリックして、ワークスペースを構成します。 イベント ハブとストレージ アカウントを使用して診断ログを保存することもできます。
7. **[Log Analytics]** で、**[構成]** をクリックします。
8. [Log Analytics ワークスペース] ページで、**[新しいワークスペースの作成]** をクリックします。
9. **[Log Analytics ワークスペース]** ページで、新しい **Log Analytics ワークスペース**の名前として「**firewall-oms**」と入力します。
10. サブスクリプションを選択し、既存のファイアウォール リソース グループ (**Test-FW-RG**) を使用します。場所として **[米国東部]** を選択し、**[Free]** 価格レベルを選択します。
11. Click **OK**.
   ![構成プロセスの開始][1] OMS ワークスペースは、Log Analytics ワークスペースと呼ばれるようになりました。  
12. アプリケーション ルールとネットワーク ルールのログを収集するために、**[ログ]** で **[AzureFirewallApplicationRule]** と **[AzureFirewallNetworkRule]** をクリックします。
   ![診断設定を保存する][2]
13. **[Save]** をクリックします。

## <a name="enable-logging-with-powershell"></a>PowerShell を使用したログの有効化

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 診断ログで取得できるデータの収集を開始するには、診断ログを有効にする必要があります。

診断ログを有効にするには、次の手順を使用します。

1. ログ データを保存するストレージ アカウントのリソース ID をメモしておきます。 この値の形式は、*/subscriptions/\<サブスクリプション ID\>/resourceGroups/\<リソース グループ名\>/providers/Microsoft.Storage/storageAccounts/\<ストレージ アカウント名\>* です。

   サブスクリプション内の任意のストレージ アカウントを使用できます。 この情報は、Azure Portal で確認できます。 情報はリソースの**プロパティ** ページにあります。

2. ログが有効になっているファイアウォールのリソース ID に注意してください。 この値の形式は、*/subscriptions/\<サブスクリプション ID\>/resourceGroups/\<リソース グループ名\>/providers/Microsoft.Network/azureFirewalls/\<ファイアウォール名\>* です。

   この情報は、ポータルで確認できます。

3. 次の PowerShell コマンドレットを使用して、診断ログを有効にします。

    ```powershell
    Set-AzDiagnosticSetting  -ResourceId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Network/azureFirewalls/<Firewall name> `
   -StorageAccountId /subscriptions/<subscriptionId>/resourceGroups/<resource group name>/providers/Microsoft.Storage/storageAccounts/<storage account name> `
   -Enabled $true     
    ```
    
> [!TIP] 
>診断ログでは別のストレージ アカウントは必要ありません。 アクセス ログとパフォーマンス ログにストレージを使用すると、サービス料金が発生します。

## <a name="view-and-analyze-the-activity-log"></a>アクティビティ ログの表示と分析

次のいずれかの方法を使用して、アクティビティ ログのデータを表示および分析できます。

* **Azure Tools**:Azure PowerShell、Azure CLI、Azure REST API、または Azure portal を使用して、アクティビティ ログから情報を取得します。 それぞれの方法の詳細な手順については、「[リソース マネージャーの監査操作](../azure-resource-manager/resource-group-audit.md)」を参照してください。
* **Power BI**:[Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。 [Power BI 用 Azure アクティビティ ログ コンテンツ パック](https://powerbi.microsoft.com/en-us/documentation/powerbi-content-pack-azure-audit-logs/)を使用すると、事前に構成されたダッシュボードでデータを分析できます。ダッシュボードは、そのまま使用することも、カスタマイズすることもできます。

## <a name="view-and-analyze-the-network-and-application-rule-logs"></a>ネットワークおよびアプリケーション ルール ログの表示と分析

[Azure Monitor ログ](../azure-monitor/insights/azure-networking-analytics.md)は、カウンターおよびイベント ログ ファイルを収集します。 このツールには、ログを分析するための視覚化と強力な検索機能が含まれています。

Azure Firewall のログ分析のサンプル クエリについては、「[Azure Firewall Log Analytics のサンプル](log-analytics-samples.md)」を参照してください。

自身のストレージ アカウントに接続して、アクセス ログとパフォーマンス ログの JSON ログ エントリを取得することもできます。 JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。

## <a name="view-metrics"></a>メトリックを表示する
Azure Firewall に移動し、**[監視]** の **[メトリック]** をクリックします。 利用できる値を表示するには、**[メトリック]** ドロップダウン リストを選択します。

## <a name="next-steps"></a>次の手順

ログを収集するようにファイアウォールを構成した後、Azure Monitor ログを使用してデータを表示できます。

> [!div class="nextstepaction"]
> [Azure Monitor ログのネットワーク監視ソリューション](../azure-monitor/insights/azure-networking-analytics.md)

[1]: ./media/tutorial-diagnostics/figure1.png
[2]: ./media/tutorial-diagnostics/figure2.png
