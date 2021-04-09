---
title: Azure Monitor ログを使用して監視を設定する
description: イベントを視覚化および分析するように Azure Monitor ログを設定して Azure Service Fabric クラスターを監視する方法について説明します。
author: srrengar
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: srrengar
ms.openlocfilehash: ba62ac80b2f8d318d0d13e81e88cc63a8d893a2b
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100570351"
---
# <a name="set-up-azure-monitor-logs-for-a-cluster"></a>クラスターに Azure Monitor ログを設定する

クラスター レベルのイベントを監視する手段として、Microsoft は Azure Monitor ログを推奨しています。 Log Analytics ワークスペースは、Azure Resource Manager、PowerShell、Azure Marketplace のいずれかを使用して設定できます。 将来のために、デプロイの更新された Resource Manager テンプレートを保持している場合は、同じテンプレートを使って Azure Monitor ログ環境を設定します。 診断が有効な状態でデプロイされているクラスターが既にある場合は、Marketplace を使用したほうが簡単にデプロイを行えます。 デプロイ先のアカウントにサブスクリプション レベルのアクセス許可がない場合は、PowerShell または Resource Manager テンプレートを使ってデプロイします。

> [!NOTE]
> クラスターを監視するように Azure Monitor ログを設定するには、クラスターレベルまたはプラットフォーム レベルのイベントを表示するために診断を有効にする必要があります。 詳細については、[Windows クラスターで診断を設定する方法](service-fabric-diagnostics-event-aggregation-wad.md)に関するページおよび [Linux クラスターで診断を設定する方法](service-fabric-diagnostics-oms-syslog.md)に関するページを参照してください。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="deploy-a-log-analytics-workspace-by-using-azure-marketplace"></a>Azure Marketplace を使用して Log Analytics ワークスペースをデプロイする

クラスターをデプロイした後で Log Analytics ワークスペースを追加する場合は、Portal で Azure Marketplace に移動して、**Service Fabric Analytics** を探します。 これは、Service Fabric に固有のデータを含んだ、Service Fabric デプロイのカスタム ソリューションです。 このプロセスでは、ソリューション (分析情報を表示するためのダッシュボード) とワークスペース (基になるクラスター データの集計) の両方を作成します。

1. 左側のナビゲーション メニューで **[新規]** を選択します。 

2. 「**Service Fabric Analytics**」を検索します。 表示されるリソースを選択します。

3. **［作成］** を選択します

    ![Marketplace の Service Fabric Analytics](media/service-fabric-diagnostics-event-analysis-oms/service-fabric-analytics.png)

4. Service Fabric Analytics 作成ウィンドウで **[OMS ワークスペース]** フィールドの **[ワークスペースを選択します]** と **[新しいワークスペースの作成]** を順に選択します。 必須項目を入力します。 ここでの要件は、Service Fabric クラスターとワークスペースのサブスクリプションが同じであることだけです。 入力が検証されると、ワークスペースはデプロイを開始します。 デプロイは数分しかかかりません。

5. 完了したら、Service Fabric Analytics 作成ウィンドウの下部にある **[作成]** をもう一度選択します。 新しいワークスペースが **[OMS ワークスペース]** に表示されていることを確認します。 この操作では、作成したワークスペースにソリューションが追加されます。

Windows を使っている場合は、次の手順に進み、クラスター イベントが格納されているストレージ アカウントに Azure Monitor ログを接続します。 

>[!NOTE]
>Service Fabric Analytics ソリューションは、Windows クラスターでのみサポートされます。 Linux クラスターの場合は、[Linux クラスターに Azure Monitor ログを設定する方法](service-fabric-diagnostics-oms-syslog.md)に関する記事を参照してください。  

### <a name="connect-the-log-analytics-workspace-to-your-cluster"></a>Log Analytics ワークスペースをクラスターに接続する 

1. ワークスペースは、クラスターから取得する診断データとの接続を維持しておく必要があります。 Service Fabric Analytics ソリューションを作成したリソース グループに移動します。 **[ServiceFabric\<nameOfWorkspace\>]** を選択し、概要ページに移動します。 ここで、ソリューションの設定、ワークスペースの設定、Log Analytics ワークスペースへのアクセスを変更できます。

2. 左側のナビゲーション メニューの **[ワークスペースのデータ ソース]** の下にある **[ストレージ アカウント ログ]** を選択します。

3. **[ストレージ アカウント ログ]** ページの上部にある **[追加]** を選択し、クラスターのログをワークスペースに追加します。

4. **[ストレージ アカウント]** を選択し、クラスターで作成された適切なアカウントを追加します。 既定の名前を使用した場合、ストレージ アカウントは **sfdg\<resourceGroupName\>** になります。 これはクラスターのデプロイに使用した Azure Resource Manager テンプレートを使って確認できます。**applicationDiagnosticsStorageAccountName** に使用された値を確認します。 名前が表示されない場合は、下にスクロールして **[さらに読み込む]** を選択します。 ストレージ アカウント名を選択します。

5. データ型を指定します。 **[Service Fabric イベント]** に設定します。

6. [ソース] に自動的に "**WADServiceFabric\*EventTable**" が設定されることを確認します。

7. **[OK]** を選択し、ワークスペースをクラスターのログに接続します。

    ![Azure Monitor ログにストレージ アカウント ログを追加する](media/service-fabric-diagnostics-event-analysis-oms/add-storage-account.png)

現在、アカウントがワークスペースのデータ ソースのストレージ アカウント ログに表示されているはずです。

これで、クラスターのプラットフォームとアプリケーション ログ テーブルに正しく接続されている Log Analytics ワークスペースに Service Fabric Analytics ソリューションが追加されていることになります。 同じ方法でソースをワークスペースに追加できます。


## <a name="deploy-azure-monitor-logs-with-azure-resource-manager"></a>Azure Resource Manager を使用して Azure Monitor ログをデプロイする

Resource Manager テンプレートを使用してクラスターをデプロイすると、テンプレートは新しい Log Analytics ワークスペースを作成し、Service Fabric ソリューションをそのワークスペースに追加し、適切なストレージ テーブルからデータを読み取るように構成します。

[このサンプル テンプレート](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-OMS-UnSecure)を使用し、要件に合うように変更できます。 このテンプレートは、次の処理を実行します

* 新しい 5 ノードの Service Fabric クラスターを作成する
* Log Analytics ワークスペースと Service Fabric ソリューションを作成する
* 2 つサンプル パフォーマンス カウンターを収集して、ワークスペースに送信するように Log Analytics エージェントを構成する
* Service Fabric を収集するように WAD を構成し、Azure sストレージ テーブル (WADServiceFabric*EventTable) に送信する
* これらのテーブルからイベントを読み取るように Log Analytics ワークスペースを構成する


テンプレートを Resource Manager アップグレードとしてクラスターにデプロイするには、Azure PowerShell モジュールで `New-AzResourceGroupDeployment` API を使用します。 以下はコマンド例です。

```powershell
New-AzResourceGroupDeployment -ResourceGroupName "<resourceGroupName>" -TemplateFile "<templatefile>.json" 
``` 

Azure Resource Manager は、このコマンドが既存のリソースに対する更新であると検出します。 既存のデプロイで使用されているテンプレートと、指定された新しいテンプレートの間の変更点だけを処理します。

## <a name="deploy-azure-monitor-logs-with-azure-powershell"></a>Azure PowerShell を使用して Azure Monitor ログをテプロイする

`New-AzOperationalInsightsWorkspace` コマンドを使って、PowerShell から Log Analytics リソースをデプロイすることもできます。 この方法を使用するには、[Azure PowerShell](/powershell/azure/install-az-ps) がインストールされていることを確認します。 このスクリプトを使って、新しい Log Analytics ワークスペースを作成し、Service Fabric ソリューションを追加します。 

```powershell

$SubID = "<subscription ID>"
$ResourceGroup = "<Resource group name>"
$Location = "<Resource group location>"
$WorkspaceName = "<Log Analytics workspace name>"
$solution = "ServiceFabric"

# Sign in to Azure and access the correct subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionId $SubID 

# Create the resource group if needed
try {
    Get-AzResourceGroup -Name $ResourceGroup -ErrorAction Stop
} catch {
    New-AzResourceGroup -Name $ResourceGroup -Location $Location
}

New-AzOperationalInsightsWorkspace -Location $Location -Name $WorkspaceName -Sku Standard -ResourceGroupName $ResourceGroup
Set-AzOperationalInsightsIntelligencePack -ResourceGroupName $ResourceGroup -WorkspaceName $WorkspaceName -IntelligencePackName $solution -Enabled $true

```

終了したら、前のセクションの手順に従い、Azure Monitor ログを適切なストレージ アカウントに接続します。

PowerShell を使って、他のソリューションを追加したり、Log Analytics ワークスペースに他の変更を行うこともできます。 詳しくは、「[PowerShell を使用した Azure Monitor ログの管理](../azure-monitor/logs/powershell-workspace-configuration.md)」をご覧ください。

## <a name="next-steps"></a>次のステップ
* お使いのノードに [Log Analytics エージェントをデプロイ](service-fabric-diagnostics-oms-agent.md)してパフォーマンス カウンターを収集し、Docker の統計とコンテナーのログを収集する
* Azure Monitor ログの一部として提供されている[ログ検索とクエリ](../azure-monitor/logs/log-query-overview.md)機能の詳細を確認します
* [Azure Monitor ログのビュー デザイナーを使用してカスタム ビューを作成する](../azure-monitor/visualize/view-designer.md)
