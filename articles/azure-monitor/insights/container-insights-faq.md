---
title: コンテナーの Azure Monitor についてよく寄せられる質問 | Microsoft Docs
description: コンテナーの Azure Monitor は、Azure 内の AKS クラスターおよび Container Instances の正常性を監視するソリューションです。 この記事ではよくある質問の回答を示します。
services: azure-monitor
author: mgoedtel
manager: carmonm
editor: tysonn
ms.service: azure-monitor
ms.topic: article
ms.workload: infrastructure-services
ms.date: 08/14/2019
ms.author: magoedte
ms.openlocfilehash: f8d763f8bb228a0d4d83a3776f818d59939b942d
ms.sourcegitcommit: a6888fba33fc20cc6a850e436f8f1d300d03771f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/16/2019
ms.locfileid: "69559083"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>コンテナーの Azure Monitor についてよく寄せられる質問

この Microsoft FAQ では、コンテナーの Azure Monitor についてよく寄せられる質問を紹介します。 このソリューションについてほかに質問がある場合は、[ディスカッション フォーラム](https://feedback.azure.com/forums/34192--general-feedback)にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="can-i-monitor-my-aks-engine-cluster-with-azure-monitor-for-containers"></a>Azure Monitor for containers で AKS エンジンのクラスターを監視できますか?

Azure Monitor for containers は、Azure をホストとする AKS エンジン (旧称 ACS エンジン) クラスターにデプロイされたコンテナー ワークロードの監視をサポートしています。 このシナリオでの監視を有効にするために必要な手順の概要および詳細については、[AKS エンジンへの Azure Monitor for containers の使用](https://github.com/microsoft/OMS-docker/tree/aks-engine)に関するページを参照してください。

## <a name="why-dont-i-see-data-in-my-log-analytics-workspace"></a>Log Analytics ワークスペースにデータが表示されないのはなぜですか

Log Analytics ワークスペースで、毎日特定の時間にデータが表示されない場合は、既定の 500 MB の制限に達したか、または毎日収集するデータ量を制御するために指定された 1 日の上限に達している可能性があります。 その日の上限に達すると、データ収集が停止し、次の日にしか再開されません。 データ利用状況を確認して、予期される利用パターンに基づいて別の価格レベルに更新するには、[ログ データの使用量とコスト](../platform/manage-cost-storage.md)に関する記事をご覧ください。 

## <a name="what-are-the-container-states-specified-in-the-containerinventory-table"></a>ContainerInventory テーブルではどのようなコンテナーの状態が指定されますか

ContainerInventory テーブルには、停止中と実行中両方のコンテナーに関する情報が含まれています。 テーブルの値は、エージェント内のワークフローによって設定されます。このワークフローでは、Docker に対してすべてのコンテナー (実行中と停止) のクエリが実行され、そのデータが Log Analytics ワークスペースに転送されます。
 
## <a name="how-do-i-resolve-missing-subscription-registration-error"></a>"*サブスクリプション登録がない*" エラーを解決するにはどうすればよいですか

"**Microsoft.OperationsManagement へのサブスクリプション登録がない**" というエラーが表示される場合は、ワークスペースが定義されているサブスクリプションでリソース プロバイダー **Microsoft.OperationsManagement** を登録することで解決できます。 これを行う方法に関するドキュメントは、[こちら](../../azure-resource-manager/resource-manager-register-provider-errors.md)にあります。

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

次のエラーがに表示される場合があります。**要求で指定されている応答 URL が、アプリケーションに関して構成されている応答 URL と一致しません ('<application ID\>'** )。 解決方法については、「[コンテナー用 Azure Monitor でコンテナー ログをリアルタイムで表示する方法](container-insights-live-logs.md#configure-aks-with-azure-active-directory)」をご覧ください。 

## <a name="why-cant-i-upgrade-cluster-after-onboarding"></a>オンボード後にクラスターをアップグレードできないのはなぜですか

AKS クラスターに対して Azure Monitor for containers を有効にした後、クラスターがデータを送信していた Log Analytics ワークスペースを削除する場合、クラスターをアップグレードしようとすると失敗します。 これを回避するには、監視を無効にしてから、サブスクリプション内の別の有効なワークスペースを参照して、監視を再度有効にする必要があります。 クラスターのアップグレードをもう一度実行しようとすると、正常に処理され、完了するはずです。  

## <a name="which-ports-and-domains-do-i-need-to-openwhitelist-for-the-agent"></a>エージェントに対して、どのポートとドメインを開くまたはホワイトリスト登録する必要がありますか
- *.ods.opinsights.azure.com   443
- *.oms.opinsights.azure.com   443
- *.blob.core.windows.net      443
- dc.services.visualstudio.com 443
- *.microsoftonline.com        443
- *.monitoring.azure.com       443
- login.microsoftonline.com    443

## <a name="next-steps"></a>次の手順

AKS クラスターの監視を開始するには、「[How to onboard the Azure Monitor for containers](container-insights-onboard.md)」(コンテナーに対する Azure Monitor をオンボードする方法) を参照し、監視を有効にするための要件と使用できる方法を理解してください。 
