---
title: パブリックな Basic Load Balancer の操作、イベント、カウンターを監視する
titleSuffix: Azure Load Balancer
description: パブリックな Basic Load Balancer でアラート イベントとプローブの正常性状態のログを有効にする方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: how-to
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/05/2020
ms.author: allensu
ms.openlocfilehash: 42ec5a661bd7b42ba5de5bfa99b3898291cc60fa
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/26/2020
ms.locfileid: "88935604"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>パブリック Basic ロード バランサーの Azure Monitor ログ

Azure の各種ログを使用して、Basic Load Balancer の管理やトラブルシューティングを行うことができます。 一部のログにはポータルからアクセスできます。 ログは、イベント ハブまたは Log Analytics ワークスペースにストリーム配信できます。 どのログも Azure Blob Storage から抽出し、Excel や Power BI などのさまざまなツールで表示できます。  各種ログの詳細については、以下の一覧を参照してください。

* **アクティビティ ログ:** 「[リソースのアクションを監視するアクティビティ ログの表示](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)」を使用して、Azure サブスクリプションに送信されているすべてのアクティビティとそれらの状態を表示できます。 アクティビティ ログは既定で有効になっており、Azure portal で表示できます。
* **アラート イベント ログ:** ロード バランサーによって生成されたアラートは、このログで確認できます。 ロード バランサーの状態は 5 分ごとに収集されます。 このログは、ロード バランサーのアラート イベントが発生した場合にのみ書き込まれます。
* **正常性プローブ ログ:** 正常性プローブによって検出された問題 (バックエンド プールの中で、正常性プローブの障害が原因でロード バランサーから要求を受信していないインスタンスの数など) は、このログで確認できます。 このログは、正常性プローブの状態に変化があったときに書き込まれます。

> [!IMPORTANT]
> **正常性プローブのイベント ログは現在機能しておらず、[Azure Load Balancer に関する既知の問題](whats-new.md#known-issues)に記載されています。** ログは、Resource Manager デプロイ モデルでデプロイされたリソースについてのみ使用できます。 クラシック デプロイ モデルのリソースには使用できません。 これらのデプロイ モデルの詳細については、[Resource Manager デプロイとクラシック デプロイ](../azure-resource-manager/management/deployment-models.md)に関する記事をご覧ください。

## <a name="enable-logging"></a>ログの有効化

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 イベント ログと正常性プローブ ログでデータの収集を開始するには、これらのログを有効にします。 ログ記録を有効にするには、次の手順に従います。

[Azure portal](https://portal.azure.com) にサインインします。 ロード バランサーをまだ作成していない場合は、先に進む前に [ロード バランサーを作成](https://docs.microsoft.com/azure/load-balancer/quickstart-create-basic-load-balancer-portal) します。

1. ポータルで、 **[リソース グループ]** をクリックします。
2. ロード バランサーがある **\<resource-group-name>** を選択します。
3. ロード バランサーを選択します。
4. **[アクティビティ ログ]**  >  **[診断設定]** を選択します。
5. **[診断設定]** ウィンドウの **[診断設定]** で、 **[+ Add diagnostic setting]\(+ 診断設定の追加\)** を選択します。
6. **[診断設定]** 作成ウィンドウで、 **[名前]** フィールドに「**myLBDiagnostics**」と入力します。
7. **[診断設定]** には、3 つのオプションがあります。  1 つ、2 つ、または 3 つすべてを選択し、それぞれをご自身の要件に合わせて構成することができます。
   * **ストレージ アカウントへのアーカイブ**
   * **イベント ハブへのストリーム**
   * **Log Analytics への送信**

    ### <a name="archive-to-a-storage-account"></a>ストレージ アカウントへのアーカイブ
    このプロセス用に既に作成されているストレージ アカウントが必要になります。  ストレージ アカウントを作成する場合は、「[ストレージ アカウントの作成](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account?tabs=azure-portal)」を参照してください

    1. **[ストレージ アカウントへのアーカイブ]** の横のチェックボックスをオンにします。
    2. **[構成]** を選択して、 **[ストレージ アカウントの選択]** ウィンドウを開きます。
    3. プルダウン ボックスで、ストレージ アカウントが作成された**サブスクリプション**を選択します。
    4. プルダウン ボックスの **[ストレージ アカウント]** で、ストレージ アカウントの名前を選択します。
    5. [OK] を選択します。

    ### <a name="stream-to-an-event-hub"></a>イベント ハブへのストリーミング
    このプロセス用に既に作成されているイベント ハブが必要になります。  イベント ハブを作成するには、「[クイックスタート:Azure portal を使用したイベント ハブの作成](https://docs.microsoft.com/azure/event-hubs/event-hubs-create)」を参照してください

    1. **[イベント ハブへのストリーム]** の横にあるチェックボックスをオンにします
    2. **[構成]** を選択して、 **[イベント ハブの選択]** ウィンドウを開きます。
    3. プルダウン ボックスで、イベント ハブが作成された**サブスクリプション**を選択します。
    4. プルダウン ボックスで **[イベント ハブの名前空間の選択]** を選択します。
    5. プルダウン ボックスで **[イベント ハブ ポリシー名の選択]** を選択します。
    6. [OK] を選択します。

    ### <a name="send-to-log-analytics"></a>Log Analytics への送信
    このプロセスでは、Log Analytics ワークスペースが既に作成され、構成されている必要があります。  Log Analytics ワークスペースを作成するには、「[Azure portal で Log Analytics ワークスペースを作成する](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)」を参照してください

    1. **[Log Analytics への送信]** の横のチェックボックスをオンにします。
    2. プルダウン ボックスで、Log Analytics ワークスペースがある**サブスクリプション**を選択します。
    3. プルダウン ボックスで、 **[Log Analytics ワークスペース]** を選択します。


8. **[診断設定]** ウィンドウの **[ログ]** セクションの下で、両方の横にあるチェック ボックスをオンにします。
   * **LoadBalancerAlertEvent**
   * **LoadBalancerProbeHealthStatus**

9.  **[診断設定]** ウィンドウの **[メトリック]** セクションの下で、以下の横にあるチェック ボックスをオンにします。
   * **AllMetrics**

11. すべてが正しく表示されていることを確認し、 **[診断設定]** の作成ウィンドウの上部にある **[保存]** をクリックします。

## <a name="activity-log"></a>アクティビティ ログ

アクティビティ ログは既定で生成されます。 ログは、Azure のイベント ログ ストアに 90 日間保存されます。 これらのログについて詳しくは、「[リソースのアクションを監視するアクティビティ ログの表示](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit)」を参照してください。

## <a name="archive-to-storage-account-logs"></a>ストレージ アカウント ログへのアーカイブ

### <a name="alert-event-log"></a>アラート イベント ログ

このログは、ロード バランサーごとにログを有効にした場合にのみ生成されます。 イベントは JSON 形式で記録され、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 イベントの例を次に示します。

```json
{
    "time": "2016-01-26T10:37:46.6024215Z",
    "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
    "category": "LoadBalancerAlertEvent",
    "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
    "operationName": "LoadBalancerProbeHealthStatus",
    "properties": {
        "eventName": "Resource Limits Hit",
        "eventDescription": "Ports exhausted",
        "eventProperties": {
            "public ip address": "40.117.227.32"
        }
    }
}
```

JSON 形式の出力で *eventname* プロパティを見ると、ロード バランサーでアラートが生成された理由がわかります。 この例では、アラートが生成された理由は、ソース IP NAT (SNAT) の制限により TCP ポートが枯渇したことです。

### <a name="health-probe-log"></a>正常性プローブ ログ

このログは、既に詳しく説明したように、ロード バランサーごとにログを有効にした場合にのみ生成されます。 データは、ログ記録を有効にしたときに指定したストレージ アカウントに格納されます。 'insights-logs-loadbalancerprobehealthstatus' という名前のコンテナーが作成され、次のデータがログに記録されます。

```json
{
    "records":[
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 1,
            "healthPercentage": 50.000000
        }
    },
    {
        "time": "2016-01-26T10:37:46.6024215Z",
        "systemId": "32077926-b9c4-42fb-94c1-762e528b5b27",
        "category": "LoadBalancerProbeHealthStatus",
        "resourceId": "/SUBSCRIPTIONS/XXXXXXXXXXXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXX/RESOURCEGROUPS/RG7/PROVIDERS/MICROSOFT.NETWORK/LOADBALANCERS/WWEBLB",
        "operationName": "LoadBalancerProbeHealthStatus",
        "properties": {
            "publicIpAddress": "40.83.190.158",
            "port": "81",
            "totalDipCount": 2,
            "dipDownCount": 0,
            "healthPercentage": 100.000000
        }
    }]
}
```

JSON 形式の出力でプロパティ フィールドを見れば、プローブの正常性状態の基本的な情報がわかります。 *dipDownCount* プロパティは、プローブの応答の失敗によりネットワーク トラフィックを受信していないバックエンド上のインスタンスの合計数を示します。

### <a name="view-and-analyze-the-activity-log"></a>アクティビティ ログの表示と分析

次のいずれかの方法を使用して、アクティビティ ログのデータを表示および分析できます。

* **Azure Tools:** Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、Azure REST API、または Azure portal を使用して、アクティビティ ログから情報を取得します。 それぞれの方法の詳細な手順については、「 [リソース マネージャーの監査操作](../azure-resource-manager/management/view-activity-logs.md) 」を参照してください。
* **Power BI:** [Power BI](https:// .microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。 [Power BI 用 Azure 監査ログ コンテンツ パック](https:// .microsoft.com/documentation/ -content-pack-azure-audit-logs)を使用すると、構成済みのダッシュボードでデータを分析できます。また、要件に合わせてビューをカスタマイズすることもできます。

### <a name="view-and-analyze-the-health-probe-and-event-log"></a>正常性プローブ ログとイベント ログの表示と分析

イベント ログと正常性プローブ ログの場合は、自身のストレージ アカウントに接続して JSON ログ エントリを取得します。 JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。

## <a name="stream-to-an-event-hub"></a>イベント ハブへのストリーミング
診断情報がイベント ハブにストリーム配信されたら、Azure Monitor 統合を使用して、サードパーティ製の SIEM ツールで集中ログ分析に使用できます。 詳しくは、「[イベント ハブへの Azure 監視データのストリーム配信](../azure-monitor/platform/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)」を参照してください

## <a name="send-to-log-analytics"></a>Log Analytics への送信
Azure 内のリソースは、Log Analytics ワークスペースに直接送信される診断情報を持つことができます。このワークスペースでは、トラブルシューティングと分析のために情報に対して複雑なクエリを実行できます。  詳しくは、「[Azure Monitor の Log Analytics ワークスペースで Azure リソース ログを収集する](https://docs.microsoft.com/azure/azure-monitor/platform/resource-logs-collect-workspace)」を参照してください

## <a name="next-steps"></a>次のステップ

[Load Balancer プローブを理解する](load-balancer-custom-probe-overview.md)
