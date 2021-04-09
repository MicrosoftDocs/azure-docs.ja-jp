---
title: Arc 対応 Kubernetes クラスターに対して GitOps を使用して Helm チャートをデプロイする
services: azure-arc
ms.service: azure-arc
ms.date: 03/03/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc 対応クラスター構成に対して GitOps と Helm を使用します
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: 75e2fcb25680817fc3e2bddabbbdd9c52b7dd059
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121407"
---
# <a name="deploy-helm-charts-using-gitops-on-an-arc-enabled-kubernetes-cluster"></a>Arc 対応 Kubernetes クラスターに対して GitOps を使用して Helm チャートをデプロイする

Helm は、Kubernetes アプリケーションのインストールとライフサイクルの管理に役立つオープンソースのパッケージ化ツールです。 APT や Yum などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャート の管理に使用されます。

この記事では、Azure Arc 対応 Kubernetes で Helm を構成して使用する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

Azure Arc 対応 Kubernetes に接続されたクラスターが既に存在することを確認します。 接続されたクラスターが必要な場合は、[Azure Arc 対応 Kubernetes クラスターの接続に関するクイックスタート](./quickstart-connect-cluster.md)を参照してください。

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes での GitOps および Helm の使用方法の概要

 Helm Operator により、Helm Chart の Release を自動化する Flux への拡張機能が提供されます。 Helm Chart の Release は、HelmRelease と呼ばれる Kubernetes のカスタム リソースによって表現されます。 Flux によってこれらのリソースが Git からクラスターに同期され、Helm Operator によって Helm Chart がリソースで指定されたとおりに確実にリリースされるようにします。

 この記事で使用される[リポジトリの例](https://github.com/Azure/arc-helm-demo)は、次のように構成されています。

```console
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
└── releases
    └── app.yaml
```

この Git リポジトリには、2 つのディレクトリがあります。一方には Helm Chart が含まれ、もう一方には Release の構成が含まれています。`releases` ディレクトリ内の `app.yaml` には、次のような HelmRelease の構成が含まれています。

```yaml
apiVersion: helm.fluxcd.io/v1
kind: HelmRelease
metadata:
  name: azure-arc-sample
  namespace: arc-k8s-demo
spec:
  releaseName: arc-k8s-demo
  chart:
    git: https://github.com/Azure/arc-helm-demo
    ref: master
    path: charts/azure-arc-sample
  values:
    serviceName: arc-k8s-demo
```

Helm Release の構成には、次のフィールドが含まれています。

| フィールド | [説明] |
| ------------- | ------------- | 
| `metadata.name` | 必須フィールドです。 Kubernetes の名前付け規則に従っている必要があります。 |
| `metadata.namespace` | 省略可能なフィールド。 Release の作成場所を指定します。 |
| `spec.releaseName` | 省略可能なフィールド。 指定されない場合、Release 名は `$namespace-$name` になります。 |
| `spec.chart.path` | チャートが含まれているディレクトリ (リポジトリのルートからの相対パス)。 |
| `spec.values` | Chart そのものからの既定のパラメーター値に対するユーザーによるカスタム値です。 |

Chart ソースの `values.yaml` で指定されたオプションにより、HelmRelease の `spec.values` に指定されたオプションはオーバーライドされます。

詳細については、公式の [Helm Operator のドキュメント](https://docs.fluxcd.io/projects/helm-operator/en/stable/)を参照してください。

## <a name="create-a-configuration"></a>構成を作成する

`k8s-configuration` の Azure CLI 拡張機能を使用して、接続されたクラスターを Git リポジトリの例にリンクします。 この構成に `azure-arc-sample` という名前を付け、Flux Operator を `arc-k8s-demo` 名前空間にデプロイします。

```console
az k8s-configuration create --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name flux --operator-namespace arc-k8s-demo --operator-params='--git-readonly --git-path=releases' --enable-helm-operator --helm-operator-chart-version='1.2.0' --helm-operator-params='--set helm.versions=v3' --repository-url https://github.com/Azure/arc-helm-demo.git --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>構成パラメータ

構成の作成をカスタマイズするには、[その他のパラメーターを確認してください](./tutorial-use-gitops-connected-cluster.md#additional-parameters)。

## <a name="validate-the-configuration"></a>構成の検証

Azure CLI を使用して、構成の作成が成功したことを検証します。

```console
az k8s-configuration show --name azure-arc-sample --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

構成リソースで、コンプライアンスの状態、メッセージ、およびデバッグの情報が更新されます。

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
    "messageLevel": "3"
  },
  "enableHelmOperator": "True",
  "helmOperatorProperties": {
    "chartValues": "--set helm.versions=v3",
    "chartVersion": "1.2.0"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/azure-arc-sample",
  "name": "azure-arc-sample",
  "operatorInstanceName": "flux",
  "operatorNamespace": "arc-k8s-demo",
  "operatorParams": "--git-readonly --git-path=releases",
  "operatorScope": "namespace",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "",
  "repositoryUrl": "https://github.com/Azure/arc-helm-demo.git",
  "resourceGroup": "AzureArcTest",
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

## <a name="validate-application"></a>アプリケーションを検証する

次のコマンドを実行し、ブラウザーで `localhost:8080` に移動して、アプリケーションが実行されていることを確認します。

```console
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>次のステップ

[Azure Policy](./use-azure-policy.md) を使用してクラスター構成を大規模に適用する。
