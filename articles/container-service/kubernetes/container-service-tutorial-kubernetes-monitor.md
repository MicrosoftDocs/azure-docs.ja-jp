---
title: (非推奨) Azure Container Service チュートリアル - Kubernetes の監視
description: Azure Container Service チュートリアル - Log Analytics を使用した Kubernetes の監視
services: container-service
author: iainfoulds
manager: jeconnoc
ms.service: container-service
ms.topic: tutorial
ms.date: 04/05/2018
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: 6f95aa701228730682c0122dc1fd46d8a2537ce1
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/06/2018
ms.locfileid: "53001608"
---
# <a name="deprecated-monitor-a-kubernetes-cluster-with-log-analytics"></a>(非推奨) Log Analytics を使用した Kubernetes クラスターの監視

> [!TIP]
> Azure Kubernetes Service を使用したこのチュートリアルの更新版については、「[コンテナーに対する Azure Monitor (プレビュー) の概要](../../azure-monitor/insights/container-insights-overview.md)」を参照してください。

[!INCLUDE [ACS deprecation](../../../includes/container-service-kubernetes-deprecation.md)]

Kubernetes クラスターとコンテナーの監視は重要なことであり、複数のアプリを含む大規模な運用クラスターを管理するときは特に重要です。

Microsoft または他のプロバイダーから提供されている複数の Kubernetes 監視ソリューションを利用できます。 このチュートリアルでは、Microsoft のクラウドベースの IT 管理ソリューションである [Log Analytics](../../operations-management-suite/operations-management-suite-overview.md) のコンテナー ソリューションを使って、Kubernetes クラスターを監視します  (このコンテナー ソリューションはプレビューです)。

この 7 部構成の 7 番目のチュートリアルでは、次のタスクについて説明します。

> [!div class="checklist"]
> * Log Analytics ワークスペースの設定を取得する
> * Kubernetes ノード上に Log Analytics エージェントを設定する
> * Log Analytics ポータルまたは Azure Portal で監視情報にアクセスする

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、イメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。

これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./container-service-tutorial-kubernetes-prepare-app.md)」に戻ってください。

## <a name="get-workspace-settings"></a>ワークスペースの設定を取得する

[Log Analytics ポータル](https://mms.microsoft.com)にアクセスできる場合は、**[設定]** > **[接続されたソース]** > **[Linux サーバー]** に移動します。 そこでは、"*ワークスペース ID*" とプライマリまたはセカンダリの "*ワークスペース キー*" がわかります。 クラスターに Log Analytics エージェントを設定するときに必要になるので、これらの値を書き留めます。

## <a name="create-kubernetes-secret"></a>Kubernetes シークレットを作成する

[kubectl create secret][kubectl-create-secret] コマンドを使用して、Log Analytics ワークスペースの設定を `omsagent-secret` という名前の Kubernetes シークレットに格納します。 `WORKSPACE_ID` を Log Analytics ワークスペース ID で更新し、`WORKSPACE_KEY` をワークスペース キーで更新します。

```console
kubectl create secret generic omsagent-secret --from-literal=WSID=WORKSPACE_ID --from-literal=KEY=WORKSPACE_KEY
```

## <a name="set-up-log-analytics-agents"></a>Log Analytics エージェントを設定する

次の Kubernetes マニフェスト ファイルを使用して、Kubernetes クラスター上にコンテナー監視エージェントを構成できます。 このファイルでは、各クラスター ノードで単一の同じポッドを実行する Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) が作成されます。

次のテキストを `oms-daemonset.yaml` という名前のファイルに保存します。

```YAML
apiVersion: extensions/v1beta1
kind: DaemonSet
metadata:
 name: omsagent
spec:
 template:
  metadata:
   labels:
    app: omsagent
    agentVersion: 1.4.3-174
    dockerProviderVersion: 1.0.0-30
  spec:
   containers:
     - name: omsagent
       image: "microsoft/oms"
       imagePullPolicy: Always
       securityContext:
         privileged: true
       ports:
       - containerPort: 25225
         protocol: TCP
       - containerPort: 25224
         protocol: UDP
       volumeMounts:
        - mountPath: /var/run/docker.sock
          name: docker-sock
        - mountPath: /var/log
          name: host-log
        - mountPath: /etc/omsagent-secret
          name: omsagent-secret
          readOnly: true
        - mountPath: /var/lib/docker/containers
          name: containerlog-path
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   nodeSelector:
    beta.kubernetes.io/os: linux
   # Tolerate a NoSchedule taint on master that ACS Engine sets.
   tolerations:
    - key: "node-role.kubernetes.io/master"
      operator: "Equal"
      value: "true"
      effect: "NoSchedule"
   volumes:
    - name: docker-sock
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
    - name: omsagent-secret
      secret:
       secretName: omsagent-secret
    - name: containerlog-path
      hostPath:
       path: /var/lib/docker/containers
```

次のコマンドを使って DaemonSet を作成します。

```azurecli-interactive
kubectl create -f oms-daemonset.yaml
```

DaemonSet が作成されたことを確認するには、次のコマンドを実行します。

```azurecli-interactive
kubectl get daemonset
```

次のような出力になります。

```azurecli-interactive
NAME       DESIRED   CURRENT   READY     UP-TO-DATE   AVAILABLE   NODE-SELECTOR   AGE
omsagent   3         3         3         0            3           <none>          5m
```

エージェントが実行状態になった後、Log Analytics がデータを取り込んで処理するまでには数分かかります。

## <a name="access-monitoring-data"></a>監視データにアクセスする

Log Analytics ポータルまたは Azure Portal で[コンテナー ソリューション](../../azure-monitor/insights/containers.md)を使ってコンテナー監視データを表示および分析します。

[Log Analytics ポータル](https://mms.microsoft.com)を使ってコンテナー ソリューションをインストールするには、**[ソリューション ギャラリー]** に移動します。 そこで、**コンテナー ソリューション**を追加します。 または、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview) からコンテナー ソリューションを追加します。

Log Analytics ポータルのダッシュボードで、**[コンテナー]** 概要タイルを探します。 タイルをクリックすると、コンテナー イベント、エラー、状態、イメージ インベントリ、CPU とメモリの使用量などの詳細が表示されます。 さらに詳しい情報を見るには、いずれかのタイルで行をクリックするか、[ログ検索](../../log-analytics/log-analytics-log-searches.md)を実行します。

![Azure portal のコンテナー ダッシュボード](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

同様に、Azure Portal では、**[Log Analytics]** に移動してワークスペースを選択します。 **[コンテナー]** 概要タイルを表示するには、**[ソリューション]** > **[コンテナー]** の順にクリックします。 詳細を表示するには、タイルをクリックします。

監視データの照会と分析の詳しいガイダンスについては、[Azure Log Analytics のドキュメント](../../azure-monitor/log-query/log-query-overview.md)をご覧ください。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Log Analytics を使用して Kubernetes クラスターを監視しました。 次のタスクを行いました。

> [!div class="checklist"]
> * Log Analytics ワークスペースの設定を取得する
> * Kubernetes ノード上に Log Analytics エージェントを設定する
> * Log Analytics ポータルまたは Azure Portal で監視情報にアクセスする


Container Service のビルド済みスクリプト サンプルを見るには、次のリンクをクリックしてください。

> [!div class="nextstepaction"]
> [Azure Container Service のサンプル スクリプト](cli-samples.md)
