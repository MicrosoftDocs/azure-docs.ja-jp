---
title: パブリック ロード バランサーの操作、イベント、およびカウンターを監視する
titleSuffix: Azure Load Balancer
description: Azure Load Balancer のログを有効にする方法について学習します。
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
ms.openlocfilehash: 36937ace82d2bd8d4317f90a375042de10fe719f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709820"
---
# <a name="azure-monitor-logs-for-azure-standard-load-balancer"></a>Azure Standard Load Balancer 用の Azure Monitor のログ

Azure Monitor の各種ログを使用して、Azure Standard Load Balancer の管理やトラブルシューティングを行うことができます。 ログは、イベント ハブまたは Log Analytics ワークスペースにストリーム配信できます。 Azure Blob Storage からすべてのログを抽出し、Excel や Power BI などのツールで表示することができます。 

ログの種類は次のとおりです。

* **アクティビティ ログ:** Azure サブスクリプションに送信されているすべてのアクティビティをその状態と共に表示することができます。 詳細については、「[リソースのアクションを監査するアクティビティ ログの表示](../azure-resource-manager/management/view-activity-logs.md)」を参照してください。 アクティビティ ログは既定で有効になっており、Azure portal で表示できます。 これらのログは、Azure Basic Load Balancer および Standard Load Balancer の両方で使用できます。
* **Standard Load Balancer のメトリック:** このログを使用すると、Standard Load Balancer のログとしてエクスポートされたメトリックに対してクエリを実行できます。 これらのログは Standard Load Balancer でのみ使用できます。

> [!IMPORTANT]
> 正常性プローブと Load Balancer アラートの各イベント ログは現在機能しておらず、[Azure Load Balancer に関する既知の問題](whats-new.md#known-issues)に一覧表示されています。 

> [!IMPORTANT]
> ログは、Azure Resource Manager デプロイ モデルでデプロイされたリソースについてのみ使用できます。 クラシック デプロイ モデルのリソースでログを使用することはできません。 これらのデプロイ モデルの詳細については、[Resource Manager デプロイとクラシック デプロイ](../azure-resource-manager/management/deployment-models.md)に関する記事をご覧ください。

## <a name="enable-logging"></a>ログの有効化

アクティビティ ログは、Resource Manager のすべてのリソースで自動的に有効になります。 イベント ログと正常性プローブ ログでデータの収集を開始するには、これらのログを有効にします。 次の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。 ロード バランサーをまだ作成していない場合は、先に進む前に [ロード バランサーを作成](./quickstart-load-balancer-standard-public-portal.md) します。
1. ポータルで、 **[リソース グループ]** を選択します。
2. ロード バランサーがある **\<resource-group-name>** を選択します。
3. ロード バランサーを選択します。
4. **[アクティビティ ログ]**  >  **[診断設定]** を選択します。
5. **[診断設定]** ウィンドウの **[診断設定]** で、 **[+ Add diagnostic setting]\(+ 診断設定の追加\)** を選択します。
6. **[診断設定]** 作成ペインで、 **[名前]** ボックスに「**myLBDiagnostics**」と入力します。
7. **[診断設定]** には、3 つのオプションがあります。 1 つ、2 つ、または 3 つすべてを選択し、それぞれをご自身の要件に合わせて構成することができます。

   * **[ストレージ アカウントへのアーカイブ]** 。 このプロセス用に既に作成されているストレージ アカウントが必要になります。 ストレージ アカウントを作成するには、[ストレージ アカウントの作成](../storage/common/storage-account-create.md?tabs=azure-portal)に関するページを参照してください。
     1. **[ストレージ アカウントへのアーカイブ]** チェック ボックスをオンにします。
     2. **[構成]** を選択して、 **[ストレージ アカウントの選択]** ウィンドウを開きます。
     3. **[サブスクリプション]** ドロップダウン リストで、ストレージ アカウントが作成されたサブスクリプションを選択します。
     4. **[ストレージ アカウント]** ドロップダウン リストで、ストレージ アカウントの名前を選択します。
     5. **[OK]** を選択します。

   * **イベント ハブにストリーミングします**。 このプロセス用に既に作成されているイベント ハブが必要になります。 イベント ハブを作成するには、「[クイックスタート:Azure portal を使用したイベント ハブの作成](../event-hubs/event-hubs-create.md)」の手順に従います。
     1. **[イベント ハブへのストリーム]** チェック ボックスをオンにします。
     2. **[構成]** を選択して、 **[イベント ハブの選択]** ウィンドウを開きます。
     3. **[サブスクリプション]** ドロップダウン リストで、イベント ハブが作成されたサブスクリプションを選択します。
     4. **[イベント ハブの名前空間の選択]** ドロップダウン リストで、名前空間を選択します。
     5. **[イベント ハブ ポリシー名の選択]** ドロップダウン リストで、名前を選択します。
     6. **[OK]** を選択します。

   * **[Log Analytics への送信]** 。 このプロセスでは、Log Analytics ワークスペースが既に作成され、構成されている必要があります。 Log Analytics ワークスペースを作成する場合は、「[Azure portal で Log Analytics ワークスペースを作成する](../azure-monitor/logs/quick-create-workspace.md)」を参照してください
     1. **[Log Analytics への送信]** チェック ボックスをオンにします。
     2. **[サブスクリプション]** ドロップダウン リストで、Log Analytics ワークスペースがあるサブスクリプションを選択します。
     3. **[Log Analytics ワークスペース]** ドロップダウン リストで、ワークスペースを選択します。

8. **[診断設定]** ペインの **[メトリック]** セクションで、 **[AllMetrics]** チェック ボックスをオンにします。

9. すべてが正しく表示されていることを確認してから、 **[診断設定]** 作成ペインの上部にある **[保存]** を選択します。

## <a name="view-and-analyze-the-activity-log"></a>アクティビティ ログの表示と分析

アクティビティ ログは既定で生成されます。 [こちらの記事の手順に従って](../azure-monitor/essentials/activity-log.md)、サブスクリプション レベルでエクスポートするように構成できます。 これらのログについて詳しくは、「[リソースのアクションを監視するアクティビティ ログの表示](../azure-resource-manager/management/view-activity-logs.md)」を参照してください。

次のいずれかの方法を使用して、アクティビティ ログのデータを表示および分析できます。

* **Azure Tools:** Azure PowerShell、Azure CLI、Azure REST API、または Azure portal を使用して、アクティビティ ログから情報を取得します。 [Resource Manager の監査操作](../azure-resource-manager/management/view-activity-logs.md)に関する記事に、各方法の詳細な手順が示されています。
* **Power BI:** [Power BI](https://powerbi.microsoft.com/pricing) アカウントをまだ所有していない場合は、無料で試すことができます。 [Power BI 用 Azure 監査ログ統合](https://powerbi.microsoft.com/integrations/azure-audit-logs/)を使用すると、構成済みのダッシュボードでデータを分析できます。 または、要件に合わせてビューをカスタマイズすることもできます。

## <a name="view-and-analyze-metrics-as-logs"></a>メトリックをログとして表示および分析する
Azure Monitor のエクスポート機能を使用すると、Load Balancer のメトリックをエクスポートできます。 これらのメトリックで、1 分のサンプリング間隔ごとにログ エントリが生成されます。

メトリックからログへのエクスポートは、リソース レベルごとに有効になります。 これらのログを有効にするには、次のようにします。

1. **[診断設定]** ペインに移動します。
1. リソース グループでフィルター処理してから、メトリックのエクスポートを有効にする Load Balancer インスタンスを選択します。 
1. Load Balancer の診断設定ページが起動したら、 **[AllMetrics]** を選択して、資格のあるメトリックをログとしてエクスポートします。

メトリックのエクスポートの制限事項については、こちらの記事の「[制限事項](#limitations)」セクションを参照してください。

Standard Load Balancer の診断設定で **AllMetrics** を有効にした後に、イベント ハブまたは Log Analytics ワークスペースを使用する場合、これらのログは **AzureMonitor** テーブルに設定されます。 

ストレージにエクスポートする場合は、ストレージ アカウントに接続し、イベントおよび正常性プローブ ログの JSON ログ エントリを取得します。 JSON ファイルをダウンロードした後、それらを CSV に変換し、Excel、Power BI などのデータ視覚化ツールで表示できます。 

> [!TIP]
> Visual Studio を使い慣れていて、C# の定数と変数の値を変更する基本的な概念を理解している場合は、GitHub から入手できる[ログ変換ツール](https://github.com/Azure-Samples/networking-dotnet-log-converter)を使用できます。

## <a name="stream-to-an-event-hub"></a>イベント ハブへのストリーミング
診断情報がイベント ハブにストリーム配信されたら、Azure Monitor 統合を利用し、パートナーの SIEM ツールで集中ログ分析に使用できます。 詳細については、[イベント ハブへの Azure 監視データのストリーム配信](../azure-monitor/essentials/stream-monitoring-data-event-hubs.md#partner-tools-with-azure-monitor-integration)に関するページを参照してください。

## <a name="send-to-log-analytics"></a>Log Analytics への送信
Azure のリソースの診断情報は、Log Analytics ワークスペースに直接送信できます。 このワークスペースでは、トラブルシューティングや分析のために情報に対して複雑なクエリを実行できます。 詳細については、[Azure Monitor の Log Analytics ワークスペースでの Azure リソース ログの収集](../azure-monitor/essentials/resource-logs.md#send-to-log-analytics-workspace)に関するページを参照してください。

## <a name="limitations"></a>制限事項
Azure Load Balancer 用のメトリックからログへのエクスポート機能には、次の制限があります。
* メトリックは、現在、ログとしてエクスポートされたときに内部名を使用して表示されます。 マッピングは以下の表で確認できます。
* メトリックの次元は保持されません。 たとえば、**DipAvailability** (正常性プローブの状態) などのメトリックの場合、バックエンド IP アドレスごとに分割または表示することはできません。
* 使用された SNAT ポートと割り当てられた SNAT ポートの各メトリックは、現在、ログとしてエクスポートできません。

## <a name="next-steps"></a>次のステップ
* [ロード バランサーで使用可能なメトリックを確認する](./load-balancer-standard-diagnostics.md)
* [Azure Monitor の指示に従ってクエリを作成してテストする](../azure-monitor/logs/log-query-overview.md)