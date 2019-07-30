---
title: パブリックな Basic Load Balancer の操作、イベント、カウンターを監視する
titlesuffix: Azure Load Balancer
description: パブリックな Basic Load Balancer でアラート イベントとプローブの正常性状態のログを有効にする方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/10/2018
ms.author: allensu
ms.openlocfilehash: 1995ad5e8179fdee11e960c2ad0e7c03602ebd31
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68274800"
---
# <a name="azure-monitor-logs-for-public-basic-load-balancer"></a>パブリック Basic ロード バランサーの Azure Monitor ログ

>[!IMPORTANT] 
>Azure Load Balancer では、2 種類がサポートされています。Basic と Standard です。 この記事では、Basic Load Balancer について説明します。 Standard Load Balancer の詳細については、「[Standard Load Balancer の概要](load-balancer-standard-overview.md)」を参照してください。Azure Monitor での多次元メトリックによるテレメトリが公開されています。

Azure の各種ログを使用して、Basic Load Balancer の管理やトラブルシューティングを行うことができます。 一部のログにはポータルからアクセスできます。 どのログも Azure Blob Storage から抽出し、Excel や PowerBI などのさまざまなツールで表示できます。 各種ログの詳細については、以下の一覧を参照してください。

* **監査ログ:** [Azure 監査ログ](../monitoring-and-diagnostics/insights-debugging-with-events.md) (以前の操作ログ) を使用すると、Azure サブスクリプションに送信されているすべての操作とその状態を表示できます。 監査ログは既定で有効になっており、Azure ポータルで表示できます。
* **アラート イベント ログ:** ロード バランサーによって生成されたアラートは、このログで確認できます。 ロード バランサーの状態は 5 分ごとに収集されます。 このログは、ロード バランサーのアラート イベントが発生した場合にのみ書き込まれます。
* **正常性プローブ ログ:** 正常性プローブによって検出された問題 (バックエンド プールの中で、正常性プローブの障害が原因でロード バランサーから要求を受信していないインスタンスの数など) は、このログで確認できます。 このログは、正常性プローブの状態に変化があったときに書き込まれます。

> [!IMPORTANT]
> Azure Monitor ログは、現在、パブリック Basic ロード バランサーに対してのみ機能します。 ログは、Resource Manager デプロイ モデルでデプロイされたリソースについてのみ使用できます。 クラシック デプロイ モデルのリソースには使用できません。 これらのデプロイ モデルの詳細については、[Resource Manager デプロイとクラシック デプロイ](../azure-resource-manager/resource-manager-deployment-model.md)に関する記事をご覧ください。

## <a name="enable-logging"></a>ログの有効化

監査ログは、リソース マネージャーのすべてのリソースで自動的に有効になります。 イベント ログと正常性プローブ ログでデータの収集を開始するには、ログを有効にする必要があります。 ログ記録を有効にするには、次の手順に従います。

[Azure ポータル](https://portal.azure.com)にサインインします。 ロード バランサーをまだ作成していない場合は、先に進む前に [ロード バランサーを作成](load-balancer-get-started-internet-arm-ps.md) します。

1. ポータルで **[参照]** をクリックします。
2. **[ロード バランサー]** を選択します。

    ![portal - load-balancer](./media/load-balancer-monitor-log/load-balancer-browse.png)

3. 既存のロード バランサーを選択し、 **[すべての設定]** をクリックします。
4. ダイアログの右側のロード バランサー名の下で、 **[監視]** までスクロールし、 **[診断]** をクリックします。

    ![portal - load-balancer-settings](./media/load-balancer-monitor-log/load-balancer-settings.png)

5. **[診断]** ウィンドウの **[状態]** で、 **[オン]** を選択します。
6. **[ストレージ アカウント]** をクリックします。
7. **[ログ]** で既存のストレージ アカウントを選択するか、新しいストレージ アカウントを作成します。 スライダーを使用して、イベント データをイベント ログに格納しておく日数を指定します。 
8. **[Save]** をクリックします。

診断は、指定されたストレージ アカウントで Table Storage に保存されます。 ログが保存されていない場合は、関連ログが生成されていないためです。

![ポータル - 診断ログ](./media/load-balancer-monitor-log/load-balancer-diagnostics.png)

> [!NOTE]
> 監査ログでは別のストレージ アカウントは必要ありません。 イベントと正常性プローブのログ記録にストレージを使用すると、サービス料金が発生します。

## <a name="audit-log"></a>監査ログ

監査ログは既定で生成されます。 ログは、Azure のイベント ログ ストアに 90 日間保存されます。 これらのログの詳細については、「 [イベント ログと監査ログの表示](../monitoring-and-diagnostics/insights-debugging-with-events.md) 」を参照してください。

## <a name="alert-event-log"></a>アラート イベント ログ

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

## <a name="health-probe-log"></a>正常性プローブ ログ

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

JSON 形式の出力でプロパティ フィールドを見れば、プローブの正常性状態の基本的な情報がわかります。 *DipDownCount* プロパティは、プローブの応答の失敗によりネットワーク トラフィックを受信していないバックエンド上のインスタンスの合計数を示します。

## <a name="view-and-analyze-the-audit-log"></a>監査ログの表示と分析

次のいずれかの方法を使用して、監査ログのデータを表示および分析できます。

* **Azure Tools:** Azure PowerShell、Azure コマンド ライン インターフェイス (CLI)、Azure REST API、または Azure プレビュー ポータルを使用して、監査ログから情報を取得します。 それぞれの方法の詳細な手順については、「 [リソース マネージャーの監査操作](../azure-resource-manager/resource-group-audit.md) 」を参照してください。
* **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだお持ちではない場合は、無料で試すことができます。 [Power BI 用 Azure 監査ログ コンテンツ パック](https://powerbi.microsoft.com/documentation/powerbi-content-pack-azure-audit-logs)を使用すると、構成済みのダッシュボードでデータを分析できます。また、要件に合わせてビューをカスタマイズすることもできます。

## <a name="view-and-analyze-the-health-probe-and-event-log"></a>正常性プローブ ログとイベント ログの表示と分析

イベント ログと正常性プローブ ログの場合は、自身のストレージ アカウントに接続して JSON ログ エントリを取得する必要があります。 JSON ファイルをダウンロードした後、そのファイルを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。

## <a name="additional-resources"></a>その他のリソース

* [Power BI を使用した Azure 監査ログの視覚化](https://blogs.msdn.com/b/powerbi/archive/2015/09/30/monitor-azure-audit-logs-with-power-bi.aspx) に関するブログ記事
* [Power BI などにおける Azure 監査ログの表示と分析](https://azure.microsoft.com/blog/analyze-azure-audit-logs-in-powerbi-more/) に関するブログ記事

## <a name="next-steps"></a>次の手順

[Load Balancer プローブを理解する](load-balancer-custom-probe-overview.md)
