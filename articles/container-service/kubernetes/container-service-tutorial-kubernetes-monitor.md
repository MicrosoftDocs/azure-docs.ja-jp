---
title: "Azure Container Service チュートリアル - Kubernetes の監視 | Microsoft Docs"
description: "Azure Container Service チュートリアル - Microsoft Operations Management Suite (OMS) で Kubernetes を監視する"
services: container-service
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: acs, azure-container-service
keywords: "Docker, コンテナー, マイクロサービス, Kubernetes, Azure"
ms.assetid: 
ms.service: container-service
ms.devlang: aurecli
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: ebf35877dcd6f980af75f46b437070a6a29b7c54
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="monitor-a-kubernetes-cluster-with-operations-management-suite"></a>Operations Management Suite で Kubernetes クラスターを監視する

Kubernetes クラスターとコンテナーの監視は重要なことであり、複数のアプリを含む大規模な運用クラスターを管理するときは特に重要です。 

Microsoft または他のプロバイダーから提供されている複数の Kubernetes 監視ソリューションを利用できます。 このチュートリアルでは、Microsoft のクラウドベースの IT 管理ソリューションである [Operations Management Suite](../../operations-management-suite/operations-management-suite-overview.md) のコンテナー ソリューションを使って、Kubernetes クラスターを監視します  (OMS コンテナー ソリューションはプレビューです)。

この 7 部構成の 7 番目のチュートリアルでは、次のタスクについて説明します。

> [!div class="checklist"]
> * OMS ワークスペースの設定を取得する
> * Kubernetes ノード上に OMS エージェントを設定する
> * OMS ポータルまたは Azure Portal で監視情報にアクセスする

## <a name="before-you-begin"></a>開始する前に

前のチュートリアルでは、アプリケーションをコンテナー イメージにパッケージ化し、イメージを Azure Container Registry にアップロードして、Kubernetes クラスターを作成しました。 

これらの手順を実行していない場合で、行いたい場合は、「[チュートリアル 1 – コンテナー イメージを作成する](./container-service-tutorial-kubernetes-prepare-app.md)」に戻ってください。 

## <a name="get-workspace-settings"></a>ワークスペースの設定を取得する

[OMS ポータル](https://mms.microsoft.com)にアクセスできる場合は、**[設定]** > **[接続されたソース]** > **[Linux サーバー]** に移動します。 そこでは、"*ワークスペース ID*" とプライマリまたはセカンダリの "*ワークスペース キー*" がわかります。 クラスターに OMS エージェントを設定するときに必要になるので、これらの値を書き留めます。

## <a name="set-up-oms-agents"></a>OMS エージェントを設定する

ここで示すのは、Linux クラスター ノードに OMS エージェントを設定するための YAML ファイルです。 このファイルでは、各クラスター ノードで単一の同じポッドを実行する Kubernetes [DaemonSet](https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/) が作成されます。 DaemonSet リソースは、監視エージェントのデプロイに最適です。 

次のテキストを `oms-daemonset.yaml` という名前のファイルに保存し、*myWorkspaceID* と *myWorkspaceKey* のプレースホルダー値を実際の OMS ワークスペースの ID とキーに置き換えます  (運用環境では、これらの値をシークレットとしてエンコードできます)。

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
    agentVersion: v1.3.4-127
    dockerProviderVersion: 10.0.0-25
  spec:
   containers:
     - name: omsagent 
       image: "microsoft/oms"
       imagePullPolicy: Always
       env:
       - name: WSID
         value: myWorkspaceID
       - name: KEY 
         value: myWorkspaceKey
       - name: DOMAIN
         value: opinsights.azure.com
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
       livenessProbe:
        exec:
         command:
         - /bin/bash
         - -c
         - ps -ef | grep omsagent | grep -v "grep"
        initialDelaySeconds: 60
        periodSeconds: 60
   volumes:
    - name: docker-sock 
      hostPath:
       path: /var/run/docker.sock
    - name: host-log
      hostPath:
       path: /var/log
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

エージェントが実行した後、OMS がデータを取り込んで処理するまでには数分かかります。

## <a name="access-monitoring-data"></a>監視データにアクセスする

OMS ポータルまたは Azure Portal で[コンテナー ソリューション](../../log-analytics/log-analytics-containers.md)を使って OMS コンテナー監視データを表示および分析します。 

[OMS ポータル](https://mms.microsoft.com)を使ってコンテナー ソリューションをインストールするには、**[ソリューション ギャラリー]** に移動します。 そこで、**コンテナー ソリューション**を追加します。 または、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft.containersoms?tab=Overview) からコンテナー ソリューションを追加します。

OMS ポータルの OMS ダッシュボードで、**[コンテナー]** 概要タイルを探します。 タイルをクリックすると、コンテナー イベント、エラー、状態、イメージ インベントリ、CPU とメモリの使用量などの詳細が表示されます。 さらに詳しい情報を見るには、いずれかのタイルで行をクリックするか、[ログ検索](../../log-analytics/log-analytics-log-searches.md)を実行します。

![OMS ポータルのコンテナー ダッシュボード](./media/container-service-tutorial-kubernetes-monitor/oms-containers-dashboard.png)

同様に、Azure Portal では、**[Log Analytics]** に移動してワークスペースを選択します。 **[コンテナー]** 概要タイルを表示するには、**[ソリューション]** > **[コンテナー]** の順にクリックします。 詳細を表示するには、タイルをクリックします。

監視データの照会と分析の詳しいガイダンスについては、[Azure Log Analytics のドキュメント](../../log-analytics/index.yml)をご覧ください。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、OMS で クラスターを監視しました。 次のタスクを行いました。

> [!div class="checklist"]
> * OMS ワークスペースの設定を取得する
> * Kubernetes ノード上に OMS エージェントを設定する
> * OMS ポータルまたは Azure Portal で監視情報にアクセスする


Container Service のビルド済みスクリプト サンプルを見るには、次のリンクをクリックしてください。

> [!div class="nextstepaction"]
> [Azure Container Service のサンプル スクリプト](cli-samples.md)
