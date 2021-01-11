---
title: Azure Arc 対応の Kubernetes クラスターを接続する (プレビュー)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Azure Arc 対応の Kubernetes クラスターを Azure Arc と接続する
keywords: Kubernetes, Arc, Azure, K8s, コンテナー
ms.custom: references_regions
ms.openlocfilehash: eb3921d3ab2090b6bac54c9b68e9def3949ed4b5
ms.sourcegitcommit: 5b6acff3d1d0603904929cc529ecbcfcde90d88b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/21/2020
ms.locfileid: "88723743"
---
# <a name="connect-an-azure-arc-enabled-kubernetes-cluster-preview"></a>Azure Arc 対応の Kubernetes クラスターを接続する (プレビュー)

このドキュメントでは、Cloud Native Computing Foundation (CNCF) で認定された Kubernetes クラスター (Azure 上の AKS エンジン、Azure Stack Hub 上の AKS エンジン、GKE、EKS、VMware vSphere クラスターなど) を Azure Arc に接続するプロセスについて説明します。

## <a name="before-you-begin"></a>開始する前に

次の要件の準備ができていることを確認します。

* 稼働している Kubernetes クラスター。 既存の Kubernetes クラスターがない場合は、次のいずれかのガイドを使用してテスト クラスターを作成できます。
  * [Docker 内の Kubernetes (kind)](https://kind.sigs.k8s.io/) を使用して Kubernetes クラスターを作成する
  * [Mac](https://docs.docker.com/docker-for-mac/#kubernetes) または [Windows](https://docs.docker.com/docker-for-windows/#kubernetes) 用の Docker を使用して Kubernetes クラスターを作成する
* Arc 対応の Kubernetes エージェントをデプロイするには、クラスターとクラスター上のクラスター管理者ロールにアクセスするための kubeconfig ファイルが必要です。
* `az login` および `az connectedk8s connect` コマンドで使用されるユーザーまたはサービス プリンシパルには、"Microsoft.Kubernetes/connectedclusters" リソースの種類に対する "読み取り" と "書き込み" のアクセス許可が必要です。 ユーザーまたはサービス プリンシパルに対するロールの割り当てには、これらのアクセス許可を持つ "Kubernetes クラスター - Azure Arc のオンボード" ロールを使用できます。
* connectedk8s 拡張機能を使用してクラスターをオンボードするには、Helm 3 が必要です。 この要件を満たすには、[最新リリースの Helm 3 をインストール](https://helm.sh/docs/intro/install)してください。
* Azure Arc 対応 Kubernetes CLI 拡張機能をインストールするには、Azure CLI バージョン2.3 以降が必要です。 [Azure CLI をインストール](/cli/azure/install-azure-cli?view=azure-cli-latest)するか、最新バージョンに更新して、Azure CLI バージョン 2.3 以降があるようにしてください。
* Arc 対応 Kubernetes CLI 拡張機能をインストールします。
  
  Kubernetes クラスターを Azure に接続するために必要な `connectedk8s` 拡張機能をインストールします。
  
  ```console
  az extension add --name connectedk8s
  ```
  
  `k8sconfiguration` 拡張機能をインストールします。
  
  ```console
  az extension add --name k8sconfiguration
  ```
  
  これらの拡張機能を後で更新する場合は、次のコマンドを実行します。
  
  ```console
  az extension update --name connectedk8s
  az extension update --name k8sconfiguration
  ```

## <a name="supported-regions"></a>サポートされているリージョン

* 米国東部
* 西ヨーロッパ

## <a name="network-requirements"></a>ネットワークの要件

Azure Arc エージェントが機能するには、次のプロトコル、ポート、送信 URL が必要です。

* ポート 443 での TCP --> `https://:443`
* ポート 9418 での TCP --> `git://:9418`

| エンドポイント (DNS)                                                                                               | 説明                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | エージェントが Azure に接続してクラスターを登録するために必要です                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | エージェントが状態をプッシュして構成情報をフェッチするためのデータ プレーン エンドポイント                                      |
| `https://docker.io`                                                                                            | コンテナー イメージをプルするために必要です                                                                                         |
| `https://github.com`、git://github.com                                                                         | GitOps リポジトリの例は、GitHub でホストされています。 構成エージェントには、指定する git エンドポイントへの接続が必要です。 |
| `https://login.microsoftonline.com`                                                                            | Azure Resource Manager トークンをフェッチして更新するために必要です                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Azure Arc エージェント用のコンテナー イメージをプルするために必要です                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  システムによって割り当てられたマネージド ID 証明書をプルするために必須                                                                  |

## <a name="register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes 用の 2 つのプロバイダーを登録する:

```console
az provider register --namespace Microsoft.Kubernetes

az provider register --namespace Microsoft.KubernetesConfiguration
```

登録は非同期プロセスです。 登録には、10 分ほどかかる場合があります。 次のコマンドを使用して、登録プロセスを監視できます。

```console
az provider show -n Microsoft.Kubernetes -o table
```

```console
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="create-a-resource-group"></a>リソース グループを作成する

クラスターのメタデータを格納するには、リソース グループを使用します。

最初に、接続されたクラスター リソースを保持するためのリソース グループを作成します。

```console
az group create --name AzureArcTest -l EastUS -o table
```

**出力:**

```console
Location    Name
----------  ------------
eastus      AzureArcTest
```

## <a name="connect-a-cluster"></a>クラスターを接続する

次に、Kubernetes クラスターを Azure に接続します。 `az connectedk8s connect` のワークフローは次のとおりです。

1. `KUBECONFIG`、`~/.kube/config`、または `--kube-config` で、Kubernetes クラスターへの接続を確認します
1. Helm 3 を使用して、Kubernetes 用の Azure Arc エージェントを `azure-arc` 名前空間にデプロイします

```console
az connectedk8s connect --name AzureArcTest1 --resource-group AzureArcTest
```

**出力:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Helm release deployment succeeded

{
  "aadProfile": {
    "clientAppId": "",
    "serverAppId": "",
    "tenantId": ""
  },
  "agentPublicKeyCertificate": "...",
  "agentVersion": "0.1.0",
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1",
  "identity": {
    "principalId": null,
    "tenantId": null,
    "type": "None"
  },
  "kubernetesVersion": "v1.15.0",
  "location": "eastus",
  "name": "AzureArcTest1",
  "resourceGroup": "AzureArcTest",
  "tags": {},
  "totalNodeCount": 1,
  "type": "Microsoft.Kubernetes/connectedClusters"
}
```

## <a name="verify-connected-cluster"></a>接続されたクラスターを検証する

接続されているクラスターの一覧を表示します。

```console
az connectedk8s list -g AzureArcTest -o table
```

**出力:**

```console
Command group 'connectedk8s' is in preview. It may be changed/removed in a future release.
Name           Location    ResourceGroup
-------------  ----------  ---------------
AzureArcTest1  eastus      AzureArcTest
```

このリソースは、[Azure portal](https://portal.azure.com/) で表示することもできます。 ブラウザーでポータルを開いたら、先ほど `az connectedk8s connect` コマンドで使用したリソース名とリソース グループ名の入力に基づいて、リソース グループと Azure Arc 対応の Kubernetes リソースに移動します

> [!NOTE]
> クラスターをオンボードした後、Azure portal の Azure Arc 対応の Kubernetes リソースの概要ページで、クラスターのメタデータ (クラスターのバージョン、エージェントのバージョン、ノードの数) が画面に表示されるまでに約 5～10 分かかります。

## <a name="connect-using-an-outbound-proxy-server"></a>送信プロキシ サーバーを使用して接続する

クラスターが送信プロキシ サーバーの内側にある場合、Azure CLI および Arc 対応 Kubernetes エージェントは、要求を送信プロキシ サーバー経由でルーティングする必要があります。 次の構成で可能になること:

1. 次のコマンドを実行して、お使いのコンピューターにインストールされている `connectedk8s` 拡張機能のバージョンを確認します。

    ```bash
    az -v
    ```

    送信プロキシを使用してエージェントを設定するには、`connectedk8s` 拡張機能のバージョン 0.2.3 以降が必要です。 お使いのコンピューターに 0.2.3 より前のバージョンがある場合は、[更新手順](#before-you-begin)に従って、そのコンピューター上に最新バージョンの拡張機能を取得してください。

2. Azure CLI に必要な環境変数を設定します。

    ```bash
    export HTTP_PROXY=<proxy-server-ip-address>:<port>
    export HTTPS_PROXY=<proxy-server-ip-address>:<port>
    export NO_PROXY=<cluster-apiserver-ip-address>:<port>
    ```

3. プロキシ パラメーターを指定して connect コマンドを実行します。

    ```bash
    az connectedk8s connect -n <cluster-name> -g <resource-group> \
    --proxy-https https://<proxy-server-ip-address>:<port> \
    --proxy-http http://<proxy-server-ip-address>:<port> \
    --proxy-skip-range <excludedIP>,<excludedCIDR>
    ```

> [!NOTE]
> 1. --proxy-skip-range で excludedCIDR を指定することは、エージェントのクラスター内通信が切断されないようにするために重要です。
> 2. 上記のプロキシ仕様は、現在、sourceControlConfiguration で使用される Flux ポッドではなく、Arc エージェントに対してのみ適用されます。 Arc 対応の Kubernetes チームはこの機能に積極的に取り組んでいるため、間もなく利用できるようになります。

## <a name="azure-arc-agents-for-kubernetes"></a>Kubernetes 用 Azure Arc エージェント

Azure Arc 対応の Kubernetes では、`azure-arc` 名前空間にいくつかのオペレーターがデプロイされます。 これらのデプロイとポッドを次のようにして表示できます。

```console
kubectl -n azure-arc get deployments,pods
```

**出力:**

```console
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

Azure Arc 対応の Kubernetes は、`azure-arc` 名前空間にデプロイされたクラスター内で実行されるいくつかのエージェント (オペレーター) で構成されています。

* `deployment.apps/config-agent`: クラスターに適用されるソース管理構成リソースの接続されたクラスターを監視し、コンプライアンス状態を更新します
* `deployment.apps/controller-manager`: オペレーターのオペレーターであり、Azure Arc コンポーネント間の相互作用を統制します
* `deployment.apps/metrics-agent`: 他の Arc エージェントのメトリックを収集し、これらのエージェントのパフォーマンスが最適であることを確認します
* `deployment.apps/cluster-metadata-operator`: クラスターのメタデータを収集します (クラスターのバージョン、ノード数、Azure Arc エージェントのバージョン)
* `deployment.apps/resource-sync-agent`: 前述のクラスター メタデータを Azure に同期します
* `deployment.apps/clusteridentityoperator`:Azure Arc 対応 Kubernetes では、現在、システムによって割り当てられた ID がサポートされています。 clusteridentityoperator では、他のエージェントが Azure との通信に使用する管理サービス ID (MSI) 証明書が保持されます。
* `deployment.apps/flux-logs-agent`: ソース管理構成の一部としてデプロイされる Flux オペレーターからログを収集します

## <a name="delete-a-connected-cluster"></a>接続されているクラスターを削除する

Azure CLI または Azure portal を使用して `Microsoft.Kubernetes/connectedcluster` リソースを削除できます。


* **Azure CLI を使用した削除**:次の Azure CLI コマンドを使用して、Azure Arc 対応の Kubernetes リソースの削除を開始できます。
  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```
  このコマンドにより、Azure 内の `Microsoft.Kubernetes/connectedCluster` リソースおよび関連するすべての `sourcecontrolconfiguration` リソースが削除されます。 Azure CLI では、Helm のアンインストールを使用して、クラスターで実行されているエージェントが削除されます。

* **Azure portal での削除**:Azure portal で Azure Arc 対応の Kubernetes リソースを削除すると、Azure 内の `Microsoft.Kubernetes/connectedcluster` リソースおよび関連するすべての `sourcecontrolconfiguration` リソースが削除されますが、クラスターで実行されているエージェントは削除されません。 クラスターで実行されているエージェントを削除するには、次のコマンドを実行します。

  ```console
  az connectedk8s delete --name AzureArcTest1 --resource-group AzureArcTest
  ```

## <a name="next-steps"></a>次のステップ

* [接続されたクラスターで GitOps を使用する](./use-gitops-connected-cluster.md)
* [Azure Policy を使用してクラスター構成を管理する](./use-azure-policy.md)
