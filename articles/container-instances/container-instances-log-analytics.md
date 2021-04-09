---
title: リソース ログの収集と分析
description: Azure Container Instances のコンテナー グループからリソース ログとイベント データを Azure Monitor ログに送信する方法について説明します
ms.topic: article
ms.date: 07/13/2020
ms.openlocfilehash: cfdcd1cc8e36a118c4e3c4435eaa002e4d3b1b93
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100579318"
---
# <a name="container-group-and-instance-logging-with-azure-monitor-logs"></a>Azure Monitor ログによるコンテナー グループおよびインスタンスのログ記録

Log Analytics ワークスペースは、Azure リソースからだけでなく、オンプレミスのリソースや他のクラウドのリソースからのログ データも格納して照会できる一元的な場所を提供します。 Azure Container Instances には、ログとイベント データを Azure Monitor ログに送信するための組み込みサポートが含まれています。

コンテナー グループのログとイベント データを Azure Monitor ログに送信するには、コンテナー グループを構成するときに既存の Log Analytics ワークスペース ID とワークスペース キーを指定します。 

以下のセクションでは、ログ記録が有効なコンテナー グループの作成方法と、ログに対するクエリの実行方法について説明します。 また、ワークスペース ID とワークスペースキーで[コンテナー グループを更新](container-instances-update.md)して、ログ記録を有効にすることもできます。

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

> [!NOTE]
> 現在は、Linux コンテナー インスタンスから Log Analytics にイベント データのみを送信できます。

## <a name="prerequisites"></a>前提条件

コンテナー インスタンスでログ記録を有効にするには、次のものが必要です。

* [Log Analytics ワークスペース](../azure-monitor/logs/quick-create-workspace.md)
* [Azure CLI](/cli/azure/install-azure-cli) (または [Cloud Shell](../cloud-shell/overview.md))

## <a name="get-log-analytics-credentials"></a>Log Analytics の資格情報を取得する

Azure Container Instances が Log Analytics ワークスペースにデータを送信するには、アクセス許可が必要です。 このアクセス許可を付与し、ログ記録を有効にするには、コンテナー グループを作成するときに、Log Analytics ワークスペース ID と、そのキーの 1 つ (プライマリまたはセカンダリ) を提供する必要があります。

ログ分析ワークスペースの ID とプライマリ キーを取得するには:

1. Azure portal で Log Analytics ワークスペースに移動します
1. **[設定]** で **[Agents management]\(エージェント管理\)** を選択します
1. 次の値を書き留めておきます。
   * **ワークスペース ID**
   * **主キー**

## <a name="create-container-group"></a>コンテナー グループを作成する

ログ分析のワークスペース ID とプライマリ キーがわかったので、ログ記録が有効なコンテナー グループを作成できます。

次の例は、1 つの [fluentd][fluentd] コンテナーで構成されるコンテナー グループを作成する 2 つの方法を示しています。1 つは Azure CLI を使用する方法で、もう 1 つは Azure CLI と YAML テンプレートを使用する方法です。 fluentd コンテナーは、既定の構成で複数の出力行を生成します。 この出力は Log Analytics ワークスペースに送信されるため、ログの表示とクエリのデモンストレーションに適しています。

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
apiVersion: 2019-12-01
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

次に、以下のコマンドを実行してコンテナー グループをデプロイします。 `myResourceGroup` をサブスクリプション内のリソース グループに置き換えます (または、"myResourceGroup" という名前のリソース グループを最初に作成します)。

```azurecli-interactive
az container create --resource-group myResourceGroup --name mycontainergroup001 --file deploy-aci.yaml
```

コマンドを発行した直後に、Azure から展開の詳細を含む応答を受け取るはずです。

## <a name="view-logs"></a>ログを表示する

コンテナー グループを展開した後、最初のログ エントリが Azure portal に表示されるまでに数分 (最大 10 分) かかることがあります。 

`ContainerInstanceLog_CL` テーブル内のコンテナー グループのログを表示するには、次の手順を実行します。

1. Azure portal で Log Analytics ワークスペースに移動します
1. **[全般]** で **[ログ]** を選択します  
1. クエリ `ContainerInstanceLog_CL | limit 50` を入力します
1. **[実行]** を選択します

クエリによっていくつかの結果が表示されます。 最初に結果が何も表示されない場合は、数分待ってから、 **[実行]** ボタンを選択してクエリをもう一度実行します。 既定では、ログ エントリは **テーブル** 形式で表示されます。 その後、行を展開して個々のログ エントリの内容を表示できます。

![Azure portal のログ検索の結果][log-search-01]

## <a name="view-events"></a>イベントの表示

コンテナー インスタンスのイベントは、Azure portal で確認することもできます。 イベントには、インスタンスが作成された時刻やインスタンスが開始された時刻が含まれています。 `ContainerEvent_CL` テーブル内のイベント データを表示するには、次の手順に従います。

1. Azure portal で Log Analytics ワークスペースに移動します
1. **[全般]** で **[ログ]** を選択します  
1. クエリ `ContainerEvent_CL | limit 50` を入力します
1. **[実行]** を選択します

クエリによっていくつかの結果が表示されます。 最初に結果が何も表示されない場合は、数分待ってから、 **[実行]** ボタンを選択してクエリをもう一度実行します。 既定では、エントリが **テーブル** 形式で表示されます。 その後、行を展開して個々のエントリの内容を表示できます。

![Azure portal でのイベント検索の結果][log-search-02]

## <a name="query-container-logs"></a>コンテナー ログのクエリを実行する

Azure Monitor ログには、何千行にもなる可能性があるログ出力から情報を抽出ための広範な[クエリ言語][query_lang]が含まれます。

クエリの基本構造では、ソース テーブル (この記事では `ContainerInstanceLog_CL` または `ContainerEvent_CL`) の後に、一連の演算子をパイプ文字 (`|`) で区切って記述します。 複数の演算子を連結して結果を絞り込み、高度な機能を実行できます。

クエリ結果の例を見るには、クエリ テキスト ボックスに次のクエリを貼り付けてから、 **[実行]** ボタンを選択してクエリを実行します。 このクエリは、"Message" フィールドに "warn" という単語が含まれるすべてのログ エントリを表示します。

```query
ContainerInstanceLog_CL
| where Message contains "warn"
```

さらに複雑なクエリもサポートされています。 たとえば、次のクエリでは、過去 1 時間以内に生成された "mycontainergroup001" コンテナー グループのログ エントリのみが表示されます。

```query
ContainerInstanceLog_CL
| where (ContainerGroup_s == "mycontainergroup001")
| where (TimeGenerated > ago(1h))
```

## <a name="next-steps"></a>次のステップ

### <a name="azure-monitor-logs"></a>Azure Monitor ログ

Azure Monitor ログでのログのクエリとアラートの構成について詳しくは、以下をご覧ください。

* [Azure Monitor ログでのログ検索について](../azure-monitor/logs/log-query-overview.md)
* [Azure Monitor での統合アラート](../azure-monitor/alerts/alerts-overview.md)


### <a name="monitor-container-cpu-and-memory"></a>コンテナーの CPU とメモリを監視する

コンテナー インスタンスの CPU とメモリ リソースの監視については、以下をご覧ください。

* [Azure Container Instances のコンテナー リソースを監視する](container-instances-monitor.md)

<!-- IMAGES -->
[log-search-01]: ./media/container-instances-log-analytics/portal-query-01.png
[log-search-02]: ./media/container-instances-log-analytics/portal-query-02.png

<!-- LINKS - External -->
[fluentd]: https://hub.docker.com/r/fluent/fluentd/
[query_lang]: /azure/data-explorer/

<!-- LINKS - Internal -->
[az-container-create]: /cli/azure/container#az-container-create