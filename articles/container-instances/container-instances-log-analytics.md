---
title: Azure Monitor ログによるコンテナー インスタンスのログ記録
description: コンテナーの出力 (STDOUT と STDERR) を Azure Monitor ログに送信する方法を説明します。
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: overview
ms.date: 07/17/2018
ms.author: danlep
ms.openlocfilehash: 13f1fa92365c284ed10bd7c0a1b2fdefef50b29e
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/26/2019
ms.locfileid: "56879715"
---
# <a name="container-instance-logging-with-azure-monitor-logs"></a>Azure Monitor ログによるコンテナー インスタンスのログ記録

Log Analytics ワークスペースは、Azure リソースだけでなくオンプレミスのリソースや他のクラウドのリソースからのログ データも格納して照会できる一元的な場所を提供します。 Azure Container Instances には、Azure Monitor ログにデータを送信するための組み込みサポートが含まれています。

Azure Monitor ログにコンテナー インスタンスのデータを送信するには、Azure CLI (または Cloud Shell) と YAML ファイルを使用して、コンテナー グループを作成する必要があります。 以下のセクションでは、ログ記録が有効なコンテナー グループの作成と、ログに対するクエリの実行について説明します。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>前提条件

コンテナー インスタンスでログ記録を有効にするには、次のものが必要です。

* [Log Analytics ワークスペース](../azure-monitor/learn/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (または [Cloud Shell](/azure/cloud-shell/overview))

## <a name="get-log-analytics-credentials"></a>Log Analytics の資格情報を取得する

Azure Container Instances が Log Analytics ワークスペースにデータを送信するには、アクセス許可が必要です。 このアクセス許可を付与し、ログ記録を有効にするには、コンテナー グループを作成するときに、Log Analytics ワークスペース ID と、そのキーの 1 つ (プライマリまたはセカンダリ) を提供する必要があります。

ログ分析ワークスペースの ID とプライマリ キーを取得するには:

1. Azure portal で Log Analytics ワークスペースに移動します
1. **[設定]** で **[詳細設定]** を選択します
1. **[接続されたソース]** > **[Windows サーバー]** (または **[Linux サーバー]** -- ID とキーはどちらの場合も同じです) を選択します
1. 次の値を書き留めておきます。
   * **ワークスペース ID**
   * **プライマリ キー**

## <a name="create-container-group"></a>コンテナー グループを作成する

ログ分析のワークスペース ID とプライマリ キーがわかったので、ログ記録が有効なコンテナー グループを作成できます。

次の例は、1 つの [fluentd][fluentd] コンテナーが含まれたコンテナー グループを作成する 2 つの方法を示します。1 つは Azure CLI を使用する方法で、もう 1 つは Azure CLI と YAML テンプレートを使用する方法です。 Fluentd コンテナーは、既定の構成で複数の出力行を生成します。 この出力は Log Analytics ワークスペースに送信されるため、ログの表示とクエリのデモンストレーションに適しています。

### <a name="deploy-with-azure-cli"></a>Azure CLI でのデプロイ

Azure CLI でデプロイするには、[az container create][az-container-create] コマンドで `--log-analytics-workspace` パラメーターと `--log-analytics-workspace-key` パラメーターを指定します。 次のコマンドを実行する前に、2 つのワークスペースの値を前の手順で取得した値に置き換えます (また、リソース グループ名を更新します)。

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainergroup001 \
    --image fluent/fluentd \
    --log-analytics-workspace <WORKSPACE_ID> \
    --log-analytics-workspace-key <WORKSPACE_KEY>
```

### <a name="deploy-with-yaml"></a>YAML でのデプロイ

YAML でコンテナー グループをデプロイしたい場合にはこの方法を使用します。 次の YAML は、1 つのコンテナーが含まれたコンテナー グループを定義します。 YAML を新しいファイルにコピーしてから、`LOG_ANALYTICS_WORKSPACE_ID` と `LOG_ANALYTICS_WORKSPACE_KEY` を前の手順で取得した値に置き換えます。 ファイルを **deploy-aci.yaml** として保存します。

```yaml
apiVersion: 2018-06-01
location: eastus
name: mycontainergroup001
properties:
  containers:
  - name: mycontainer001
    properties:
      environmentVariables: []
      image: fluent/fluentd
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
  diagnostics:
    logAnalytics:
      workspaceId: LOG_ANALYTICS_WORKSPACE_ID
      workspaceKey: LOG_ANALYTICS_WORKSPACE_KEY
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

次に、以下のコマンドを実行してコンテナー グループを展開します。`myResourceGroup` は、サブスクリプション内のリソース グループに置き換えます (または、"myResourceGroup" という名前のリソース グループを最初に作成します)。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

コマンドを発行した直後に、Azure から展開の詳細を含む応答を受け取るはずです。

## <a name="view-logs-in-azure-monitor-logs"></a>Azure Monitor ログのログを表示する

コンテナー グループを展開した後、最初のログ エントリが Azure portal に表示されるまでに数分 (最大 10 分) かかることがあります。 コンテナー グループのログを表示するには、Log Analytics ワークスペースを開いた後、次のようにします。

1. **[OMS ワークスペース]** の概要で、**[ログ検索]** を選択します。 OMS ワークスペースは、Log Analytics ワークスペースと呼ばれるようになりました。  
1. **[お試しいただけるクエリがほかにもいくつかあります]** で、**[収集されたすべてのデータ]** リンクを選択します

`search *` クエリによっていくつかの結果が表示されます。 最初に結果が何も表示されない場合は、数分待ってから、**[実行]** ボタンを選択してクエリをもう一度実行します。 既定では、ログ エントリは "一覧" ビューに表示されます。**[テーブル]** を選択すると、より圧縮された形式でログ エントリが表示されます。 その後、行を展開して個々のログ エントリの内容を表示できます。

![Azure portal のログ検索の結果][log-search-01]

## <a name="query-container-logs"></a>コンテナー ログのクエリを実行する

Azure Monitor ログには、何千行にもなる可能性があるログ出力から情報を抽出ための広範な[クエリ言語][query_lang]が含まれます。

Azure Container Instances のログ エージェントは、Log Analytics ワークスペースの `ContainerInstanceLog_CL` テーブルにエントリを送信します。 クエリの基本構造では、ソース テーブル (`ContainerInstanceLog_CL`) の後に、一連の演算子をパイプ文字 (`|`) で区切って記述します。 複数の演算子を連結して結果を絞り込み、高度な機能を実行できます。

クエリ結果の例を見るには、次のクエリをクエリ テキスト ボックス ([レガシ言語コンバーターを表示します] の下) に貼り付けてから、**[実行]** ボタンを選択してクエリを実行します。 このクエリは、"Message" フィールドに "warn" という単語が含まれるすべてのログ エントリを表示します。

```query
ContainerInstanceLog_CL
| where Message contains("warn")
```

さらに複雑なクエリもサポートされています。 たとえば、次のクエリでは、過去 1 時間以内に生成された "mycontainergroup001" コンテナー グループのログ エントリのみが表示されます。

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>次の手順

### <a name="azure-monitor-logs"></a>Azure Monitor ログ

Azure Monitor ログでのログのクエリとアラートの構成について詳しくは、以下をご覧ください。

* [Azure Monitor ログでのログ検索について](../log-analytics/log-analytics-log-search.md)
* [Azure Monitor での統合アラート](../azure-monitor/platform/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>コンテナーの CPU とメモリを監視する

コンテナー インスタンスの CPU とメモリ リソースの監視については、以下をご覧ください。

* [Azure Container Instances のコンテナー リソースを監視する](container-instances-monitor.md)

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: https://aka.ms/LogAnalyticsLanguage

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create