---
title: コンテナーの Azure Monitor についてよく寄せられる質問 | Microsoft Docs
description: コンテナーの Azure Monitor は、Azure 内の AKS クラスターおよび Container Instances の正常性を監視するソリューションです。 この記事ではよくある質問の回答を示します。
ms.topic: conceptual
ms.date: 10/15/2019
ms.openlocfilehash: b0d2be8b573dbbf047f4a27ae9ac9f611b76dc51
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/15/2020
ms.locfileid: "75977760"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>コンテナーの Azure Monitor についてよく寄せられる質問

この Microsoft FAQ では、コンテナーの Azure Monitor についてよく寄せられる質問を紹介します。 このソリューションについてほかに質問がある場合は、[ディスカッション フォーラム](https://feedback.azure.com/forums/34192--general-feedback)にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="i-dont-see-image-and-name-property-values-populated-when-i-query-the-containerlog-table"></a>ContainerLog テーブルのクエリを実行したとき、Image プロパティと Name プロパティの値が出力されません。

エージェント バージョン ciprod12042019 以降では、ログ データの収集にかかるコストを最小限に抑えるため、既定では、これら 2 つのプロパティはすべてのログ行に出力されません。 これらのプロパティとその値を含めるようにテーブルをクエリする 2 つの方法があります。

### <a name="option-1"></a>方法 1

他のテーブルを結合して、これらのプロパティ値を結果に含めます。

ContainerID プロパティに結合することによって ```ContainerInventory``` テーブルの Image プロパティと ImageTag プロパティを含めるようにクエリを変更します。 ContainerID プロパティに結合することによって、KubepodInventory テーブルの ContaineName フィールドから (以前に ```ContainerLog``` テーブルにあったときのように) Name プロパティを含めることができます。こちらの方法をお勧めします。

次の例は、結合を使用してこれらのフィールド値を取得する方法を説明するサンプルの詳細なクエリです。

```
// 過去 1 時間分のログをクエリしているとします
let startTime = ago(1h);
let endTime = now();
// タイム ウィンドウ内におけるすべての ContainerID の最新の Image と ImageTag を取得します
let ContainerInv = ContainerInventory | where TimeGenerated >= startTime and TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID, Image, ImageTag | project-away TimeGenerated | project ContainerID1=ContainerID, Image1=Image ,ImageTag1=ImageTag;
// タイム ウィンドウ内におけるすべての ContainerID の最新の Name を取得します
let KubePodInv  = KubePodInventory | where ContainerID != "" | where TimeGenerated >= startTime | where TimeGenerated < endTime | summarize arg_max(TimeGenerated, *)  by ContainerID2 = ContainerID, Name1=ContainerName | project ContainerID2 , Name1;
// 上の 2 つを結合し、 Name, Image, ImageTag を持つ '結合後のテーブル' を取得します。 KubePod のレコードが存在しない場合でもログを失うことがないため、左外部結合はより安全です
let ContainerData = ContainerInv | join kind=leftouter (KubePodInv) on $left.ContainerID1 == $right.ContainerID2;
// ContainerLog テーブルを上記の '結合後のテーブル' と結合し、 project-away による冗長なフィールド/カラムの除外と書き換えられたカラムをリネームします
// (レイテンシやデータ タイプ間での時間のずれなどにより) ログラインのコンテナ メタデータが見つからない場合でもログを失うことがないため、左外部結合はより安全です
ContainerLog
| where TimeGenerated >= startTime and TimeGenerated < endTime 
| join kind= leftouter (
   ContainerData
) on $left.ContainerID == $right.ContainerID2 | project-away ContainerID1, ContainerID2, Name, Image, ImageTag | project-rename Name = Name1, Image=Image1, ImageTag=ImageTag1 

```

### <a name="option-2"></a>方法 2

すべてのコンテナー ログ行について、これらのプロパティの収集を再び有効にします。

クエリの変更を伴うため最初の方法が難しい場合、[データ収集の構成設定](./container-insights-agent-config.md)に関するページの説明に従い、エージェントの ConfigMap で `log_collection_settings.enrich_container_logs`設定を有効にすることによって、これらのフィールドの収集を再び有効にすることができます。

> [!NOTE]
> 2 番目の方法は、50 を超えるノードを持つ大規模なクラスターでは推奨されません。このエンリッチメントを実行するために、クラスター内のすべてのノードから API サーバー呼び出しが生成されるからです。 この方法を使用すると、収集されるすべてのログ行のデータ サイズも増加します。

## <a name="can-i-view-metrics-collected-in-grafana"></a>Grafana で収集されたメトリックを表示できますか?

Azure Monitor for Containers では、Grafana ダッシュボードの Log Analytics ワークスペースに格納されているメトリックの表示をサポートしています。 Grafana の[ダッシュボード リポジトリ](https://grafana.com/grafana/dashboards?dataSource=grafana-azure-monitor-datasource&category=docker)からダウンロードできるテンプレートが用意されています。これを使って作業を開始し、監視対象クラスターから追加データのクエリを実行して、カスタム Grafana ダッシュボードで視覚化する方法を学習できます。 

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Azure Monitor for containers で AKS エンジンのクラスターを監視できますか?

Azure Monitor for containers は、Azure をホストとする AKS エンジン (旧称 ACS エンジン) クラスターにデプロイされたコンテナー ワークロードの監視をサポートしています。 このシナリオでの監視を有効にするために必要な手順の概要および詳細については、[AKS エンジンへの Azure Monitor for containers の使用](https://github.com/microsoft/OMS-docker/tree/aks-engine)に関するページを参照してください。

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Log Analytics ワークスペースにデータが表示されないのはなぜですか

Log Analytics ワークスペースで、毎日特定の時間にデータが表示されない場合は、既定の 500 MB の制限に達したか、または毎日収集するデータ量を制御するために指定された 1 日の上限に達している可能性があります。 その日の上限に達すると、データ収集が停止し、次の日にしか再開されません。 データ利用状況を確認して、予期される利用パターンに基づいて別の価格レベルに更新するには、[ログ データの使用量とコスト](../platform/manage-cost-storage.md)に関する記事をご覧ください。 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory テーブルではどのようなコンテナーの状態が指定されますか

ContainerInventory テーブルには、停止中と実行中両方のコンテナーに関する情報が含まれています。 テーブルの値は、エージェント内のワークフローによって設定されます。このワークフローでは、Docker に対してすべてのコンテナー (実行中と停止) のクエリが実行され、そのデータが Log Analytics ワークスペースに転送されます。
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>"*サブスクリプション登録がない*" エラーを解決するにはどうすればよいですか

"**Microsoft.OperationsManagement へのサブスクリプション登録がない**" というエラーが表示される場合は、ワークスペースが定義されているサブスクリプションでリソース プロバイダー **Microsoft.OperationsManagement** を登録することで解決できます。 これを行う方法に関するドキュメントは、[こちら](../../azure-resource-manager/templates/error-register-resource-provider.md)にあります。

## <a name="is-there-support-for-rbac-enabled-aks-clusters"></a>RBAC 対応の AKS クラスターはサポートされていますか

コンテナー監視ソリューションでは RBAC がサポートされていませんが、Azure Monitor for Containers ではサポートされています。 これらのクラスターのデータが示されるブレードでは、ソリューションの詳細ページに正しい情報が表示されない場合があります。

## <a name="how-do-i-enable-log-collection-for-containers-in-the-kube-system-namespace-through-helm"></a>Helm を使用して kube システム名前空間内のコンテナーのログ収集を有効にするにはどうすればよいですか

Kube システム名前空間内のコンテナーからのログ収集は、既定では無効になっています。 omsagent で環境変数を設定することにより、ログ収集を有効にすることができます。 詳しくは、[コンテナーの Azure Monitor](https://github.com/helm/charts/tree/master/incubator/azuremonitor-containers)に関する GitHub のページをご覧ください。 

## <a name="how-do-i-update-the-omsagent-to-the-latest-released-version"></a>omsagent を最新リリースのバージョンに更新するにはどうすればよいですか

エージェントをアップグレードする方法については、[エージェントの管理](container-insights-manage-agent.md)に関する記事をご覧ください。

## <a name="how-do-i-enable-multi-line-logging"></a>複数行のログ記録を有効にするにはどうすればよいですか

現在、コンテナーの Azure Monitor では複数行のログ記録はサポートされていませんが、利用可能な回避策があります。 JSON 形式で書き込むようにすべてのサービスを構成することができ、Docker/Moby ではそれが単一行として書き込まれます。

たとえば、サンプルの node.js アプリケーションに対する次の例で示すように、JSON オブジェクトとしてログをラップすることができます。

```
console.log(json.stringify({ 
      "Hello": "This example has multiple lines:",
      "Docker/Moby": "will not break this into multiple lines",
      "and you will receive":"all of them in log analytics",
      "as one": "log entry"
      }));
```

このデータは、クエリを実行すると、Azure Monitor のログに次の例のように表示されます。

```
LogEntry : ({“Hello": "This example has multiple lines:","Docker/Moby": "will not break this into multiple lines", "and you will receive":"all of them in log analytics", "as one": "log entry"}

```

問題の詳細については、こちらの [GitHub リンク](https://github.com/moby/moby/issues/22920)をご覧ください。

## <a name="how-do-i-resolve-azure-ad-errors-when-i-enable-live-logs"></a>ライブ ログを有効にしたときの Azure AD のエラーを解決するにはどうすればよいですか 

次のエラーがに表示される場合があります。**要求で指定されている応答 URL が、アプリケーションに関して構成されている応答 URL と一致しません ('<application ID\>'** )。 それを解決するためのソリューションについては、[Azure Monitor for containers を使用して、コンテナー データをリアルタイムで表示する方法](container-insights-livedata-setup.md#configure-ad-integrated-authentication)に関する記事をご覧ください。 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>オンボード後にクラスターをアップグレードできないのはなぜですか

AKS クラスターに対して Azure Monitor for containers を有効にした後、クラスターがデータを送信していた Log Analytics ワークスペースを削除する場合、クラスターをアップグレードしようとすると失敗します。 これを回避するには、監視を無効にしてから、サブスクリプション内の別の有効なワークスペースを参照して、監視を再度有効にする必要があります。 クラスターのアップグレードをもう一度実行しようとすると、正常に処理され、完了するはずです。  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>エージェントに対して、どのポートとドメインを開くまたはホワイトリスト登録する必要がありますか

Azure、Azure US Government、および Azure China クラウドでコンテナー化されたエージェントに必要なプロキシとファイアウォールの構成情報については、「[Network firewall requirements](container-insights-onboard.md#network-firewall-requirements)」(ネットワーク ファイアウォールの要件) を参照してください。

## <a name="next-steps"></a>次のステップ

AKS クラスターの監視を開始するには、「[How to onboard the Azure Monitor for containers](container-insights-onboard.md)」(コンテナーに対する Azure Monitor をオンボードする方法) を参照し、監視を有効にするための要件と使用できる方法を理解してください。 
