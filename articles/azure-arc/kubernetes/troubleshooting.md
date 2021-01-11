---
title: Azure Arc 対応 Kubernetes の一般的な問題のトラブルシューティング (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Arc 対応 Kubernetes クラスターに関する一般的な問題のトラブルシューティング。
keywords: Kubernetes, Arc, Azure, コンテナー
ms.openlocfilehash: 404516778255409d56dd5c3a7d1fd96711cc981f
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723675"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting-preview"></a>Azure Arc 対応 Kubernetes のトラブルシューティング (プレビュー)

このドキュメントでは、接続、アクセス許可、エージェントに関する一般的なトラブルシューティングのシナリオについて説明します。

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング

### <a name="azure-cli-set-up"></a>Azure CLI のセットアップ
az connectedk8s または az k8sconfiguration CLI コマンドを使用する前に、az が正しい Azure サブスクリプションに対して機能するように設定されていることを確認します。

```console
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>azure-arc エージェント
Azure Arc 対応 Kubernetes のすべてのエージェントは、`azure-arc` 名前空間にポッドとしてデプロイされます。 通常の操作では、すべてのポッドが実行され、正常性チェックに合格する必要があります。

まず、Azure Arc の Helm リリースを確認します。

```console
$ helm --namespace default status azure-arc
NAME: azure-arc
LAST DEPLOYED: Fri Apr  3 11:13:10 2020
NAMESPACE: default
STATUS: deployed
REVISION: 5
TEST SUITE: None
```

Helm リリースが見つからないか欠落している場合は、クラスターをもう一度オンボードしてみます。

Helm リリースが存在し、`STATUS: deployed` の場合は、`kubectl` を使用してエージェントの状態を確認します。

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                        READY   UP-TO-DATE AVAILABLE AGE
deployment.apps/cluster-metadata-operator   1/1     1           1        16h
deployment.apps/clusteridentityoperator     1/1     1           1        16h
deployment.apps/config-agent                1/1     1           1        16h
deployment.apps/controller-manager          1/1     1           1        16h
deployment.apps/flux-logs-agent             1/1     1           1        16h
deployment.apps/metrics-agent               1/1     1           1        16h
deployment.apps/resource-sync-agent         1/1     1           1        16h

NAME                                            READY   STATUS   RESTART AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

すべてのポッドの `STATUS` が `Running` である必要があり、`READY` は `3/3` または `2/2` である必要があります。 ログを取得し、`Error` または `CrashLoopBackOff` を返しているポッドを書き留めます。 これらのポッドのいずれかが `Pending` 状態で止まっている場合、原因として、クラスター ノードにリソースが十分にないことが考えられます。 [クラスターをスケールアップする](https://kubernetes.io/docs/tasks/administer-cluster/cluster-management/#resizing-a-cluster)と、ポッドが `Running` 状態に移行します。

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Azure Arc に Kubernetes クラスターを Azure に接続する

クラスターを Azure に接続するには、Azure サブスクリプションへのアクセスと、ターゲット クラスターへの `cluster-admin` アクセスの両方が必要です。 クラスターに到達できない場合や、クラスターに十分なアクセス許可がない場合、オンボードは失敗します。

### <a name="insufficient-cluster-permissions"></a>クラスターのアクセス許可が不十分

指定された kubeconfig ファイルに、Azure Arc エージェントをインストールするための十分なアクセス許可が含まれていない場合、Azure CLI コマンドは Kubernetes API を呼び出そうとしたときにエラーを返します。

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

クラスター所有者は、クラスター管理者のアクセス許可を持つ Kubernetes ユーザーを使用する必要があります。

### <a name="installation-timeouts"></a>インストールのタイムアウト

Azure Arc エージェントのインストールでは、ターゲット クラスターで一連のコンテナーを実行する必要があります。 クラスターが低速のインターネット接続を介して実行されている場合、コンテナー イメージのプルに、Azure CLI のタイムアウトよりも時間がかかることがあります。

```console
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

## <a name="configuration-management"></a>構成管理

### <a name="general"></a>全般
ソース管理構成に関する問題のトラブルシューティングを行うには、--debug スイッチを指定して az コマンドを実行します。

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8sconfiguration create <parameters> --debug
```

### <a name="create-source-control-configuration"></a>ソース管理構成の作成
Microsoft.KubernetesConfiguration/sourceControlConfiguration リソースを作成する場合、Microsoft.Kubernetes/connectedCluster リソースに対する共同作成者ロールが必要であり、このロールで十分です。

### <a name="configuration-remains-pending"></a>構成が `Pending` のままになる

```console
kubectl -n azure-arc logs -l app.kubernetes.io/component=config-agent -c config-agent
$ k -n pending get gitconfigs.clusterconfig.azure.com  -o yaml
apiVersion: v1
items:
- apiVersion: clusterconfig.azure.com/v1beta1
  kind: GitConfig
  metadata:
    creationTimestamp: "2020-04-13T20:37:25Z"
    generation: 1
    name: pending
    namespace: pending
    resourceVersion: "10088301"
    selfLink: /apis/clusterconfig.azure.com/v1beta1/namespaces/pending/gitconfigs/pending
    uid: d9452407-ff53-4c02-9b5a-51d55e62f704
  spec:
    correlationId: ""
    deleteOperator: false
    enableHelmOperator: false
    giturl: git@github.com:slack/cluster-config.git
    helmOperatorProperties: null
    operatorClientLocation: azurearcfork8s.azurecr.io/arc-preview/fluxctl:0.1.3
    operatorInstanceName: pending
    operatorParams: '"--disable-registry-scanning"'
    operatorScope: cluster
    operatorType: flux
  status:
    configAppliedTime: "2020-04-13T20:38:43.081Z"
    isSyncedWithAzure: true
    lastPolledStatusTime: ""
    message: 'Error: {exit status 1} occurred while doing the operation : {Installing
      the operator} on the config'
    operatorPropertiesHashed: ""
    publicKey: ""
    retryCountPublicKey: 0
    status: Installing the operator
kind: List
metadata:
  resourceVersion: ""
  selfLink: ""
```
## <a name="monitoring"></a>監視

コンテナー用の Azure Monitor では、その DaemonSet を特権モードで実行する必要があります。 監視のために Canonical Charmed Kubernetes クラスターを正常に設定するには、次のコマンドを実行します。

```console
juju config kubernetes-worker allow-privileged=true
```