---
title: Azure Arc 対応 Kubernetes の一般的な問題のトラブルシューティング
services: azure-arc
ms.service: azure-arc
ms.date: 03/02/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Arc 対応 Kubernetes クラスターに関する一般的な問題のトラブルシューティング。
keywords: Kubernetes, Arc, Azure, コンテナー
ms.openlocfilehash: e1f4e84f16c6b584f1ffbd918a86c251f47efcca
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101654002"
---
# <a name="azure-arc-enabled-kubernetes-troubleshooting"></a>Azure Arc 対応 Kubernetes のトラブルシューティング

このドキュメントでは、接続、アクセス許可、エージェントに関する問題のトラブルシューティング ガイドを提供します。

## <a name="general-troubleshooting"></a>一般的なトラブルシューティング

### <a name="azure-cli"></a>Azure CLI

`az connectedk8s` または `az k8s-configuration` CLI コマンドを使用する前に、Azure CLI が適切な Azure サブスクリプションに対して機能するように設定されていることを確認します。

```azurecli
az account set --subscription 'subscriptionId'
az account show
```

### <a name="azure-arc-agents"></a>Azure Arc エージェント

Azure Arc 対応 Kubernetes のすべてのエージェントは、`azure-arc` 名前空間にポッドとしてデプロイされます。 すべてのポッドが実行され、正常性チェックに合格する必要があります。

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

Helm リリースが見つからないか欠落している場合は、もう一度[クラスターを Azure Arc に接続](./connect-cluster.md)してみてください。

Helm リリースが存在し、`STATUS: deployed` の場合は、`kubectl` を使用してエージェントの状態を確認します。

```console
$ kubectl -n azure-arc get deployments,pods
NAME                                       READY  UP-TO-DATE  AVAILABLE  AGE
deployment.apps/clusteridentityoperator     1/1       1          1       16h
deployment.apps/config-agent                1/1       1          1       16h
deployment.apps/cluster-metadata-operator   1/1       1          1       16h
deployment.apps/controller-manager          1/1       1          1       16h
deployment.apps/flux-logs-agent             1/1       1          1       16h
deployment.apps/metrics-agent               1/1       1          1       16h
deployment.apps/resource-sync-agent         1/1       1          1       16h

NAME                                            READY   STATUS  RESTART  AGE
pod/cluster-metadata-operator-7fb54d9986-g785b  2/2     Running  0       16h
pod/clusteridentityoperator-6d6678ffd4-tx8hr    3/3     Running  0       16h
pod/config-agent-544c4669f9-4th92               3/3     Running  0       16h
pod/controller-manager-fddf5c766-ftd96          3/3     Running  0       16h
pod/flux-logs-agent-7c489f57f4-mwqqv            2/2     Running  0       16h
pod/metrics-agent-58b765c8db-n5l7k              2/2     Running  0       16h
pod/resource-sync-agent-5cf85976c7-522p5        3/3     Running  0       16h
```

すべてのポッドの `STATUS` が `Running` と示され、`READY` 列の下に `3/3` または `2/2` のいずれかが表示されます。 ログをフェッチし、`Error` または `CrashLoopBackOff` を返しているポッドを書き留めます。 これらのポッドのいずれかが `Pending` 状態で止まっている場合、クラスター ノードにリソースが十分にないことが考えられます。 [クラスターをスケールアップする](https://kubernetes.io/docs/tasks/administer-cluster/)と、これらのポッドを `Running` 状態に移行させることができます。

## <a name="connecting-kubernetes-clusters-to-azure-arc"></a>Azure Arc に Kubernetes クラスターを Azure に接続する

クラスターを Azure に接続するには、Azure サブスクリプションへのアクセスと、ターゲット クラスターへの `cluster-admin` アクセスの両方が必要です。 クラスターに接続できない場合、またはアクセス許可が不十分な場合は、クラスターの Azure Arc への接続は失敗します。

### <a name="insufficient-cluster-permissions"></a>クラスターのアクセス許可が不十分

指定された kubeconfig ファイルに、Azure Arc エージェントをインストールするための十分なアクセス許可が含まれていない場合、Azure CLI コマンドはエラーを返します。

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...

Error: list: failed to list: secrets is forbidden: User "myuser" cannot list resource "secrets" in API group "" at the cluster scope
```

クラスターを Azure Arc に接続するユーザーには、そのクラスターで `cluster-admin` ロールが割り当てられている必要があります。

### <a name="installation-timeouts"></a>インストールのタイムアウト

Kubernetes クラスターを Azure Arc 対応 Kubernetes に接続するには、クラスターに Azure Arc エージェントをインストールする必要があります。 クラスターが低速のインターネット接続を介して実行されている場合、エージェントのコンテナー イメージのプルに、Azure CLI のタイムアウトよりも時間がかかることがあります。

```azurecli
$ az connectedk8s connect --resource-group AzureArc --name AzureArcCluster
Ensure that you have the latest helm version installed before proceeding to avoid unexpected errors.
This operation might take a while...
```

### <a name="helm-issue"></a>Helm の問題

Helm バージョン `v3.3.0-rc.1` には、helm install または helm upgrade を実行すると (`connectedk8s` CLI 拡張機能で使用) すべてのフックが実行され、以下のエラーが発生するという[問題](https://github.com/helm/helm/pull/8527)があります。

```console
$ az connectedk8s connect -n shasbakstest -g shasbakstest
Ensure that you have the latest helm version installed before proceeding.
This operation might take a while...

Please check if the azure-arc namespace was deployed and run 'kubectl get pods -n azure-arc' to check if all the pods are in running state. A possible cause for pods stuck in pending state could be insufficientresources on the kubernetes cluster to onboard to arc.
ValidationError: Unable to install helm release: Error: customresourcedefinitions.apiextensions.k8s.io "connectedclusters.arc.azure.com" not found
```

この問題を解決するには、次の手順に従います。

1. Azure portal で、Azure Arc 対応 Kubernetes リソースを削除します。
2. お使いのマシンで次のコマンドを実行します。
    
    ```console
    kubectl delete ns azure-arc
    kubectl delete clusterrolebinding azure-arc-operator
    kubectl delete secret sh.helm.release.v1.azure-arc.v1
    ```

3. リリース候補バージョンではなく、[安定したバージョン](https://helm.sh/docs/intro/install/) (Helm 3) をマシンにインストールします。
4. 適切な値を指定して `az connectedk8s connect` コマンドを実行し、クラスターを Azure Arc に接続します。

## <a name="configuration-management"></a>構成管理

### <a name="general"></a>全般
リソースの構成に関する問題のトラブルシューティングに役立てるため、`--debug` パラメーターを指定して az コマンドを実行します。

```console
az provider show -n Microsoft.KubernetesConfiguration --debug
az k8s-configuration create <parameters> --debug
```

### <a name="create-configurations"></a>構成の作成

クラスターで構成を作成するには、Azure Arc 対応 Kubernetes リソース (`Microsoft.Kubernetes/connectedClusters/Write`) に対する書き込みアクセス許可があれば十分です。

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