---
title: Arc 対応 Kubernetes クラスターに対して GitOps を使用して Helm チャートをデプロイする (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc 対応クラスター構成に対して GitOps と Helm を使用する (プレビュー)
keywords: GitOps, Kubernetes, K8s, Azure, Helm, Arc, AKS, Azure Kubernetes Service, コンテナー
ms.openlocfilehash: cca48910b679ff8f72ee06f4ed990bd480fb2200
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723641"
---
# <a name="deploy-helm-charts-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Arc 対応 Kubernetes クラスターに対して GitOps を使用して Helm チャートをデプロイする (プレビュー)

Helm は、Kubernetes アプリケーションのインストールとライフサイクルの管理に役立つオープンソースのパッケージ化ツールです。 APT や Yum などの Linux パッケージ マネージャーと同様に、Helm は、構成済みの Kubernetes リソースのパッケージである Kubernetes チャートの管理に使用されます。

この記事では、Azure Arc 対応 Kubernetes で Helm を構成して使用する方法を説明します。

## <a name="before-you-begin"></a>開始する前に

この記事では、Azure Arc 対応 Kubernetes に接続されたクラスターが既に存在することを前提としています。 接続されたクラスターが必要な場合は、[クラスターの接続についてのクイックスタート](./connect-cluster.md)のページを参照してください。

最初に、このチュートリアル全体で使用する環境変数を設定しましょう。 接続されたクラスターのリソース グループ名とクラスター名が必要です。

```bash
export RESOURCE_GROUP=<Resource_Group_Name>
export CLUSTER_NAME=<ClusterName>
```

## <a name="verify-your-cluster-is-enabled-with-arc"></a>クラスターで Arc が有効になっていることを確認する

```bash
az connectedk8s list -g $RESOURCE_GROUP -o table
```

出力:
```bash
Name           Location    ResourceGroup
-------------  ----------  ---------------
arc-helm-demo  eastus      k8s-clusters
```

## <a name="overview-of-using-gitops-and-helm-with-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes での GitOps および Helm の使用方法の概要

 Helm Operator により、Helm Chart の Release を自動化する Flux への拡張機能が提供されます。 Chart の Release は、HelmRelease と呼ばれる Kubernetes のカスタム リソースによって表現されます。 Flux によってこれらのリソースが Git からクラスターに同期され、Helm Operator によって Helm Chart がリソースで指定されたとおりに確実にリリースされるようにします。

 このチュートリアルで使用する Git リポジトリの構造の例を次に示します。

```bash
├── charts
│   └── azure-arc-sample
│       ├── Chart.yaml
│       ├── templates
│       │   ├── NOTES.txt
│       │   ├── deployment.yaml
│       │   └── service.yaml
│       └── values.yaml
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

- `metadata.name` は必須で、Kubernetes の名前付け規則に従っている必要があります
- `metadata.namespace` はオプションで、Release の作成場所を指定します
- `spec.releaseName` はオプションで、指定されない場合、Release 名は $namespace-$name になります
- `spec.chart.path` は Chart が含まれているディレクトリであり、リポジトリのルートからの相対パスとして指定されます
- `spec.values` は、Chart そのものからの既定のパラメーター値に対するユーザーによるカスタム値です

Chart ソースの values.yaml で指定されたオプションにより、HelmRelease の spec.values に指定されたオプションはオーバーライドされます。

詳細については、公式の [Helm オペレーターのドキュメンテーション](https://docs.fluxcd.io/projects/helm-operator/en/stable/)を参照してください

## <a name="create-a-configuration"></a>構成を作成する

`k8sconfiguration` の Azure CLI 拡張機能を使用して、接続されたクラスターを Git リポジトリの例にリンクしましょう。 この構成に `azure-arc-sample` という名前を付け、Flux Operator を `arc-k8s-demo` 名前空間にデプロイします。

```bash
az k8sconfiguration create --name azure-arc-sample \
  --resource-group $RESOURCE_GROUP --cluster-name $CLUSTER_NAME \
  --operator-instance-name flux --operator-namespace arc-k8s-demo \
  --operator-params='--git-readonly --git-path=releases' \
  --enable-helm-operator --helm-operator-version='0.6.0' \
  --helm-operator-params='--set helm.versions=v3' \
  --repository-url https://github.com/Azure/arc-helm-demo.git  \
  --scope namespace --cluster-type connectedClusters
```

### <a name="configuration-parameters"></a>構成パラメーター

構成の作成をカスタマイズするには、[使用可能な追加のパラメーター](./use-gitops-connected-cluster.md#additional-parameters)に関するページを参照してください。

## <a name="validate-the-configuration"></a>構成を検証する

Azure CLI を使用して、`sourceControlConfiguration` の作成が成功したことを確認します。

```console
az k8sconfiguration show --resource-group $RESOURCE_GROUP --name azure-arc-sample --cluster-name $CLUSTER_NAME --cluster-type connectedClusters
```

`sourceControlConfiguration` リソースで、コンプライアンスの状態、メッセージ、およびデバッグの情報が更新されます。

**出力:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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
    "chartVersion": "0.6.0"
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

```bash
kubectl port-forward -n arc-k8s-demo svc/arc-k8s-demo 8080:8080
```

## <a name="next-steps"></a>次のステップ

- [Azure Policy を使用してクラスター構成を管理する](./use-azure-policy.md)
