---
title: チュートリアル - Prometheus と Grafana を使用して Apache Spark アプリケーション レベルのメトリックを監視する
description: チュートリアル - Apache Spark アプリケーション メトリック ソリューションを Azure Kubernetes Service (AKS) クラスターにデプロイする方法と、Grafana ダッシュボードを統合する方法について説明します。
services: synapse-analytics
author: hrasheed-msft
ms.author: jejiang
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.topic: tutorial
ms.subservice: spark
ms.date: 01/22/2021
ms.openlocfilehash: 6a0b63dc7fda25e3911ae713a0bea7ae7a0969f9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104602300"
---
# <a name="tutorial-monitor-apache-spark-application-level-metrics-with-prometheus-and-grafana"></a>チュートリアル: Prometheus と Grafana を使用して Apache Spark アプリケーション レベルのメトリックを監視する

## <a name="overview"></a>概要

このチュートリアルでは、Apache Spark アプリケーション メトリック ソリューションを Azure Kubernetes Service (AKS) クラスターにデプロイする方法と、Grafana ダッシュボードを統合する方法について説明します。

このソリューションを使用して、Apache Spark メトリック データの収集とクエリをほぼリアルタイムで実行できます。 統合された Grafana ダッシュボードを使用して、Apache Spark アプリケーションを診断および監視できます。 ソース コードと構成は GitHub でオープンソース化されています。

## <a name="prerequisites"></a>前提条件

1.  [Azure CLI](/cli/azure/install-azure-cli)
2.  [Helm クライアント 3.30 以上](https://github.com/helm/helm/releases)
3.  [kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)
4.  [Azure Kubernetes Service (AKS)](https://azure.microsoft.com/en-us/services/kubernetes-service/)

または、Azure CLI、Helm クライアント、kubectl があらかじめ含まれている [Azure Cloud Shell](https://shell.azure.com/) を使用します。

## <a name="log-in-to-azure"></a>Azure にログインする

```bash
az login
az account set --subscription "<subscription_id>"
```

## <a name="create-an-azure-kubernetes-service-instance-aks"></a>Azure Kubernetes サービス インスタンス (AKS) を作成する

Azure CLI コマンドを使用して、サブスクリプションに Kubernetes クラスターを作成します。

```
az aks create --name <kubernetes_name> --resource-group <kubernetes_resource_group> --location <location> --node-vm-size Standard_D2s_v3
az aks get-credentials --name <kubernetes_name> --resource-group <kubernetes_resource_group>
```

注: 既に AKS クラスターがある場合は、この手順をスキップできます。

## <a name="create-a-service-principal-and-grant-permission-to-synapse-workspace"></a>サービス プリンシパルを作成して Synapse ワークスペースへのアクセス許可を付与する

```bash
az ad sp create-for-rbac --name <service_principal_name>
```

結果は次のようになります。

```json
{
  "appId": "abcdef...",
  "displayName": "<service_principal_name>",
  "name": "http://<service_principal_name>",
  "password": "abc....",
  "tenant": "<tenant_id>"
}
```

アプリ ID、パスワード、テナント ID をメモしておきます。

[![SRBAC アクセス許可付与のスクリーンショット](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-grant-permission-srbac-new.png#lightbox)

1. Synapse Administrator として [Azure Synapse Analytics ワークスペース](https://web.azuresynapse.net/) にログインします

2. Synapse Studio の左側のペインで、 **[管理] > [アクセス制御]** を選択します

3. 左上にある [追加] ボタンをクリックして、**ロールの割り当てを追加** します

4. [スコープ] で、 **[ワークスペース]** を選択します

5. [ロール] で、 **[Synapse Compute Operator]** を選択します

6. [ユーザーの選択] で、 **<service_principal_name>** を入力してサービス プリンシパルをクリックします

7. **[適用]** をクリックします (アクセス許可が有効になるまで 3 分間待機します。)

## <a name="install-connector-prometheus-server-grafana-dashboard"></a>コネクタ、Prometheus サーバー、Grafana ダッシュボードをインストールする

1. synapse-charts リポジトリを Helm クライアントに追加します。

```bash
helm repo add synapse-charts https://github.com/microsoft/azure-synapse-spark-metrics/releases/download/helm-chart
```

2.  Helm クライアントからコンポーネントをインストールします。

```bash
helm install spo synapse-charts/synapse-prometheus-operator --create-namespace --namespace spo \
    --set synapse.workspaces[0].workspace_name="<workspace_name>" \
    --set synapse.workspaces[0].tenant_id="<tenant_id>" \
    --set synapse.workspaces[0].service_principal_name="<service_principal_app_id>" \
    --set synapse.workspaces[0].service_principal_password="<service_principal_password>" \
    --set synapse.workspaces[0].subscription_id="<subscription_id>" \
    --set synapse.workspaces[0].resource_group="<workspace_resource_group_name>"
```

 - workspace_name: Synapse ワークスペースの名前。
 - subscription_id: Synapse ワークスペースのサブスクリプション ID。
 - workspace_resource_group_name: Synapse ワークスペースのリソース グループ名。
 - tenant_id: Synapse ワークスペースのテナント ID。
 - service_principal_app_id: サービス プリンシパルの "appId"
 - service_principal_password: 作成したサービス プリンシパルのパスワード。

## <a name="log-in-to-grafana"></a>Grafana にログインする

Grafana の既定のパスワードとアドレスを取得します。 Grafana の設定でパスワードを変更できます。

```bash
kubectl get secret --namespace spo spo-grafana -o jsonpath="{.data.admin-password}" | base64 --decode ; echo
kubectl -n spo get svc spo-grafana
```

サービスの IP を取得し、外部 IP をコピーしてブラウザーに貼り付けて、ユーザー名 "admin" とパスワードを指定してログインします。

## <a name="use-grafana-dashboards"></a>Grafana ダッシュボードを使用する

Grafana ページの左上隅にある Synapse ダッシュボードを探し (ホーム -> Synapse Workspace / Synapse Application)、Synapse Studio でサンプル コードを実行して、メトリックが取得されるまで数秒待ってみます。

また、"Synapse Workspace / Workspace" および "Synapse Workspace / Spark pools" ダッシュボードを使用して、ワークスペースと Apache Spark プールの概要を取得することもできます。

## <a name="uninstall"></a>アンインストール

次のように、Helm コマンドでコンポーネントを削除します。

```bash
helm delete <release_name> -n <namespace>
```

AKS クラスターを削除します。

```bash
az aks delete --name <kubernetes_cluster_name> --resource-group <kubernetes_cluster_rg>
```

## <a name="components-introduction"></a>コンポーネントの概要

Azure Synapse Analytics では、Prometheus Operator および Synapse Prometheus コネクタに基づいた [Helm チャート](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm)が提供されます。 Helm チャートには、Apache Spark アプリケーション レベルのメトリックのための Prometheus サーバー、Grafana サーバー、Grafana ダッシュボードが含まれます。 一般的なオープンソースの監視システムである [Prometheus](https://prometheus.io/) を使用して、これらのメトリックをほぼリアルタイムで収集し、[Grafana](https://github.com/grafana/grafana) を使用して視覚化できます。

### <a name="synapse-prometheus-connector"></a>Synapse Prometheus コネクタ

Synapse Prometheus コネクタは、Azure Synapse Apache Spark プールと Prometheus サーバーを接続するのに役立ちます。 以下を実装します:

1.  認証: AAD ベースの認証であり、アプリケーションの検出、メトリックのインジェスト、その他の機能のために、サービス プリンシパルの AAD トークンを自動的に更新できます。
2.  Apache Spark アプリケーション検出: ターゲット ワークスペースにアプリケーションを送信すると、Synapse Prometheus コネクタがこれらのアプリケーションを自動的に検出できます。
3.  Apache Spark アプリケーション メタデータ: 基本的なアプリケーション情報を収集し、データを Prometheus にエクスポートします。

Synapse Prometheus コネクタは、[Microsoft Container Registry](https://github.com/microsoft/containerregistry) でホストされる Docker イメージとしてリリースされます。 オープンソースであり、[Azure Synapse Spark アプリケーション メトリック](https://github.com/microsoft/azure-synapse-spark-metrics)にあります。

### <a name="prometheus-server"></a>Prometheus サーバー

Prometheus は、オープンソースの監視およびアラート ツールキットです。 Prometheus は Cloud Native Computing Foundation (CNCF) を卒業し、クラウドネイティブ監視の事実上の標準になりました。 Prometheus は、大量の時系列データの収集、クエリ、保存に役立ち、Grafana と簡単に統合できます。 このソリューションでは、Helm チャートに基づいて Prometheus コンポーネントをデプロイします。

### <a name="grafana-and-dashboards"></a>Grafana とダッシュボード

Grafana はオープンソースの視覚化および分析ソフトウェアです。 メトリックのクエリ、視覚化、操作、またメトリックに基づいたアラートの作成を行うために使用できます。 Azure Synapse Analytics には、Apache Spark アプリケーション レベルのメトリックを視覚化するための、既定の Grafana ダッシュボードのセットが用意されています。

"Synapse Workspace / Workspace" ダッシュボードは、すべての Apache Spark プール、アプリケーション数、CPU コアなどのワークスペース レベルのビューを提供します。

[![ワークスペース ダッシュボードのスクリーンショット](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-workspace.png#lightbox)

"Synapse Workspace / Spark pools" ダッシュボードには、選択した Apache Spark プールで期間中に実行される Apache Spark アプリケーションのメトリックが含まれます。

[![Spark プール ダッシュボードのスクリーンショット](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-sparkpool.png#lightbox)

"Synapse Workspace / Spark Application" ダッシュボードには、選択した Apache Spark アプリケーションが含まれます。

[![アプリケーション ダッシュボードのスクリーンショット](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png)](./media/monitor-azure-synapse-spark-application-level-metrics/screenshot-dashboard-application.png#lightbox)

上記のダッシュボード テンプレートは、[Azure Synapse Spark アプリケーション メトリック](https://github.com/microsoft/azure-synapse-spark-metrics/tree/main/helm/synapse-prometheus-operator/grafana_dashboards)でオープンソース化されています。