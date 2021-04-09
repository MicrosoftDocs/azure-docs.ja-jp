---
title: Azure Red Hat OpenShift 3.11 から Azure Red Hat OpenShift 4 への移行
description: Azure Red Hat OpenShift 3.11 から Azure Red Hat OpenShift 4 への移行
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/13/2020
keywords: 移行、aro、OpenShift、Red Hat
ms.openlocfilehash: 371672de83a6d745d7b367f8327a64e11059923e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653280"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Azure Red Hat OpenShift 3.11 から Azure Red Hat OpenShift 4 への移行

Azure Red Hat OpenShift 4 により、Red Hat Core OS、プライベート クラスター、独自の仮想ネットワークのサポート、完全なクラスター管理者のロールに Kubernetes 1.16 が導入されます。 さらに、オペレーター フレームワーク、Operator Hub、OpenShift Service Mesh のサポートなど、多くの新機能を利用できるようになりました。

Azure Red Hat OpenShift 3.11 から Azure Red Hat OpenShift 4 に問題なく移行するには、[記憶域、ネットワーク、ログ、セキュリティ、監視の違い](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html)を確認してください。

この記事では、Azure Red Hat OpenShift 3.11 クラスターから Azure Red Hat 4 クラスターに移行する方法について説明します。

> [!NOTE]
> Control Plane Migration Assistance Tool や Cluster Application Migration Tool (CAM) などの Red Hat OpenShift 移行ツールは、Azure Red Hat OpenShift 3.11 クラスターでは使用できません。

## <a name="before-you-begin"></a>始める前に

この記事では、Azure Red Hat OpenShift 3.11 クラスターが既にあることを前提としています。

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>ターゲット Azure Red Hat OpenShift 4 クラスターを作成する

まず、ターゲット クラスターとして使用する [Azure Red Hat OpenShift 4 クラスターを作成](tutorial-create-cluster.md)します。 ここでは、基本構成を使用します。 別の設定に関心がある場合は、[Azure Red Hat OpenShift 4 クラスターの作成に関するチュートリアル](tutorial-create-cluster.md)を参照してください。

マスター ノードとワーカー ノード用に 2 つの空のサブネットを持つ仮想ネットワークを作成します。

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

次のコマンドを入力して、クラスターを作成します。

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>ターゲット OpenShift 4 クラスターを構成する

### <a name="authentication"></a>認証

Azure Red Hat OpenShift を操作するユーザーは、まずクラスターに対して認証を行う必要があります。 認証レイヤーにより、Azure Red Hat OpenShift API への要求に関連付けられているユーザーが識別されます。 その後、認証レイヤーでは、要求元のユーザーに関する情報を使用して、要求が許可されているかどうかが判断されます。

Azure Red Hat OpenShift 4 クラスターが作成されると、一時的な管理ユーザーが作成されます。 [クラスターに接続](tutorial-connect-cluster.md)し、ユーザーとグループを追加して、両方に[適切なアクセス許可を構成](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html)します。

### <a name="networking"></a>ネットワーク

Azure Red Hat OpenShift 4 では、クラスター内にネットワークを設定するために、いくつかの異なる演算子が使用されています。[Cluster Network Operator](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator)、[DNS Operator](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)、[Ingress Operator](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html)。 Azure Red Hat OpenShift 4 クラスターでネットワークを設定する方法の詳細については、[ネットワーク図](concepts-networking.md)と「[Understanding Networking](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html)」 (ネットワークについて) を参照してください。

### <a name="storage"></a>ストレージ
Azure Red Hat OpenShift 4 では、次の PersistentVolume プラグインがサポートされています。

- AWS Elastic Block Store (EBS)
- Azure ディスク
- Azure ファイル
- GCE Persistent Disk
- HostPath
- iSCSI
- ローカル ボリューム
- NFS
- Red Hat OpenShift Container Storage

これらのストレージの種類の構成の詳細については、[永続ストレージの構成](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage)に関するページを参照してください。

### <a name="registry"></a>レジストリ

Azure Red Hat OpenShift 4 では、ソース コードからイメージをビルドし、デプロイし、ライフサイクルを管理できます。 これを可能にするために、Azure Red Hat OpenShift 4 では、お使いの Azure Red Hat OpenShift 環境にデプロイしてイメージをローカルで管理できる、[内部の統合されたコンテナー イメージ レジストリ](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html)が提供されています。

[Azure Container Registry](../container-registry/index.yml)、[Red Hat Quay レジストリ](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options)、[認証が有効になっている Red Hat レジストリ](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options)などの外部レジストリを使用している場合は、手順に従って、クラスターに資格情報を提供してクラスターがリポジトリにアクセスできるようにします。

### <a name="monitoring"></a>監視

Azure Red Hat OpenShift には、Prometheus オープン ソース プロジェクトとその広範なエコシステムに基づく、事前に構成され、事前にインストールされた、自己更新型の監視スタックが含まれています。 これによりクラスター コンポーネントの監視機能が提供されます。またこれには、発生している問題についてクラスター管理者に直ちに通知する一連のアラートと、一連の Grafana ダッシュボードが含まれています。 クラスター監視スタックは、Azure Red Hat OpenShift クラスターの監視でのみサポートされています。 詳細については、[Azure Red Hat OpenShift のクラスターの監視](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html)に関するページを参照してください。

[Azure Red Hat OpenShift 3.11 に対して Azure Monitor for Containers](../azure-monitor/containers/container-insights-azure-redhat-setup.md) を使用している場合は、[Azure Red Hat OpenShift 4 クラスター](../azure-monitor/containers/container-insights-azure-redhat4-setup.md)に対しても Azure Monitor for Containers を有効にして、同じ Log Analytics ワークスペースを使用し続けることができます。

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>DNS またはロードバランサーの構成を新しいクラスターに移動する

Azure Traffic Manager を使用している場合は、ターゲット クラスターを参照するエンドポイントを追加し、これらのエンドポイントに優先順位を付けます。

## <a name="deploy-application-to-your-target-cluster"></a>ターゲット クラスターにアプリケーションをデプロイする

ワークロードに合わせてターゲット クラスターを適切に構成したら、[クラスターに接続](tutorial-connect-cluster.md)し、プロジェクトに必要なアプリケーション、コンポーネント、またはサービスを作成します。 Azure Red Hat OpenShift を使用すると、Git、コンテナー イメージ、Red Hat Developer Catalog、Dockerfile、YAML/JSON 定義、またはカタログからデータベース サービスを選択することによって、これらを作成できます。

## <a name="delete-your-source-cluster"></a>ソース クラスターを削除する
Azure Red Hat OpenShift 4 クラスターが適切に設定されていることを確認したら、Azure Red Hat OpenShift 3.11 クラスターを削除します。

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>次のステップ
Red Hat OpenShift のドキュメントを[ここ](https://docs.openshift.com/container-platform/4.6/welcome/index.html)から覧ください。
