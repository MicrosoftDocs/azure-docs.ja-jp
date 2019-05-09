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
ms.date: 02/26/2019
ms.author: magoedte
ms.openlocfilehash: 250bf6ede3a54b26b6e9c68850f8e7260020002e
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/27/2019
ms.locfileid: "56960513"
---
# <a name="azure-monitor-for-containers-frequently-asked-questions"></a>コンテナーの Azure Monitor についてよく寄せられる質問
この Microsoft FAQ では、コンテナーの Azure Monitor についてよく寄せられる質問を紹介します。 このソリューションについてほかに質問がある場合は、[ディスカッション フォーラム](https://feedback.azure.com/forums/34192--general-feedback)にアクセスして質問を投稿してください。 よく寄せられる質問については、すばやく簡単に見つけることができるように、この記事に追加していきます。

## <a name="i-am-unable-to-see-any-data-in-the-log-analytics-workspace-at-a-certain-time-everyday-how-do-i-resolve-this"></a>毎日、特定の時間に Log Analytics ワークスペース内のデータがまったく閲覧できなくなります。 解決するにはどうすればよいですか? 

既定の 500 MB の制限に達したか、または毎日収集されるデータの量を制御する日次上限が指定されている可能性があります。 その日の上限に達すると、データ収集が停止し、次の日にしか再開されません。 データ利用状況を確認して、予期される利用パターンに基づいて別の価格レベルに更新するには、[ログ データの使用量とコスト](../platform/manage-cost-storage.md)に関する記事をご覧ください。 

## <a name="what-are-the-states-of-containers-specified-in-the-containerinventory-table"></a>ContainerInventory テーブルではどのようなコンテナーの状態が指定されていますか
ContainerInventory テーブルには、停止中と実行中両方のコンテナーに関する情報が含まれています。 テーブルの値は、エージェント内のワークフローによって設定されます。このワークフローでは、Docker に対してすべてのコンテナー (実行中と停止) のクエリが実行され、そのデータが Log Analytics ワークスペースに転送されます。
 
## <a name="how-do-i-resolve-errors-related-to-missing-subscription-registration-for-microsoftoperationsmanagement"></a>**Microsoft.OperationsManagement のサブスクリプション登録がない**ことに関するエラーを解決するにはどうすればよいですか
エラーを解決するには、ワークスペースが定義されているサブスクリプションでリソース プロバイダー **Microsoft.OperationsManagement** を登録します。 これを行う方法に関するドキュメントは、[こちら](../../azure-resource-manager/resource-manager-register-provider-errors.md)にあります。

## <a name="does-azure-monitor-for-containers-include-support-for-rbac-enabled-aks-clusters"></a>コンテナーの Azure Monitor には、RBAC が有効な AKS クラスターのサポートが含まれますか
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

## <a name="how-do-i-resolve-azure-active-directory-errors-when-i-enable-live-logs"></a>ライブ ログを有効にしたときの Azure Active Directory のエラーを解決するにはどうすればよいですか 
次のエラーがに表示される場合があります。**要求で指定されている応答 URL が、アプリケーションに関して構成されている応答 URL と一致しません ('<application ID\>'**)。 解決方法については、「[コンテナー用 Azure Monitor でコンテナー ログをリアルタイムで表示する方法](container-insights-live-logs.md#configure-aks-with-azure-active-directory)」をご覧ください。 

## <a name="next-steps"></a>次の手順
AKS クラスターの監視を開始するには、「[How to onboard the Azure Monitor for containers](container-insights-onboard.md)」(コンテナーに対する Azure Monitor をオンボードする方法) を参照し、監視を有効にするための要件と使用できる方法を理解してください。 