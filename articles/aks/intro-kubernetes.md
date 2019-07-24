---
title: Azure Kubernetes Service の概要
description: Azure でコンテナー ベースのアプリケーションを簡単にデプロイして管理するための、Azure Kubernetes Service の機能とメリットついて説明します。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: overview
ms.date: 03/05/2019
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: a147d95701af166c650411a91fb24e3448cfec09
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "59995032"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

Azure Kubernetes Service (AKS) を使用すると、マネージド Kubernetes クラスターを Azure 内に簡単にデプロイできます。 AKS では、責任の多くを Azure にオフロードすることで、Kubernetes の管理の複雑さと運用上のオーバーヘッドを軽減します。 ホストされた Kubernetes サービスとして、Azure は正常性監視やメンテナンスなどの重要なタスクを自動的に処理します。 Kubernetes マスターは、Azure によって管理されます。 ユーザーは、エージェント ノードの管理とメンテナンスだけを行います。 マネージド Kubernetes サービスである AKS は無料のため、マスターではなく、クラスター内のエージェント ノードに対してのみ料金を支払います。

Azure portal では、Azure CLI、または Resource Manager テンプレートや Terraform などのテンプレート駆動型のデプロイ オプションを使用して、AKS クラスターを作成できます。 AKS クラスターをデプロイすると、Kubernetes マスターとすべてのノードが自動的にデプロイされ、構成されます。 デプロイ プロセス中に、高度なネットワーク、Azure Active Directory 統合、監視などの追加機能も構成できます。

Kubernetes の基礎について詳しくは、[AKS における Kubernetes の中心概念][concepts-clusters-workloads]に関するページを参照してください。

開始するには、[Azure portal で][aks-portal]、または [Azure CLI を使用して][aks-cli]、AKS のクイック スタートを完了します。

## <a name="access-security-and-monitoring"></a>アクセス、セキュリティ、および監視

セキュリティと管理の強化のため、AKS では、Azure Active Directory と統合して、Kubernetes のロールベースのアクセス制御を使用することができます。 また、クラスターとリソースの正常性を監視することもできます。

### <a name="identity-and-security-management"></a>ID とセキュリティ管理

クラスター リソースへのアクセスを制限するため、AKS は [Kubernetes のロールベースのアクセス制御 (RBAC)][kubernetes-rbac] をサポートしています。 RBAC を使用すると、Kubernetes のリソースと名前空間へのアクセスのほか、それらのリソースに対するアクセス許可を制御することができます。 Azure Active Directory (AD) と統合されるように AKS クラスターを構成することもできます。 Azure AD の統合によって、既存の ID とグループ メンバーシップに基づいて、Kubernetes アクセスを構成できます。 Azure AD の既存のユーザーとグループには、AKS リソースへのアクセスと、統合されたサインオン エクスペリエンスを提供できます。

ID の詳細については、[AKS でのアクセスと ID オプション][concepts-identity]に関するページを参照してください。

AKS クラスターをセキュリティで保護するには、[Azure Active Directory と AKS の統合][aks-aad]に関するページを参照してください。

### <a name="integrated-logging-and-monitoring"></a>ログ記録と監視の統合

AKS クラスターやデプロイされたアプリケーションがどのようなパフォーマンスを発揮しているかを理解するため、コンテナーの正常性に関する Azure Monitor が、コンテナー、ノード、およびコント ローラーからメモリとプロセッサのメトリックを収集します。 コンテナー ログが用意されていて、[Kubernetes マスター ログをよく調べる][aks-master-logs]こともできます。 この監視データは、Azure Log Analytics ワークスペースに格納され、Azure portal、Azure CLI、または REST エンドポイントを介して使用できます。

詳細については、[Azure Kubernetes Service コンテナーの正常性の監視][container-health]に関するページを参照してください。

## <a name="cluster-and-node"></a>クラスターとノード

AKS ノードは Azure 仮想マシンで実行されます。 ストレージをノードやポッドに接続し、クラスター コンポーネントをアップグレードして、GPU を使用することができます。

### <a name="cluster-node-and-pod-scaling"></a>クラスター ノードとポッドのスケーリング

リソースに対する需要は変化するため、サービスを実行するクラスター ノードやポッドの数を、自動的にスケールアップまたはスケールダウンすることができます。 ポッドの水平オートスケーラーとクラスター オートスケーラーの両方を使用できます。 このスケーリング方法では、AKS クラスターは需要に対して自動的に調整され、必要とされるリソースのみを実行します。

詳細については、「[Azure Kubernetes Service (AKS) クラスターのスケーリング][aks-scale]」を参照してください。

### <a name="cluster-node-upgrades"></a>クラスター ノードのアップグレード

Azure Kubernetes Service では、複数の Kubernetes バージョンを提供しています。 AKS で新しいバージョンが使用できるようになると、Azure portal または Azure CLI を使用してクラスターをアップグレードできます。 アップグレード プロセス中、実行中のアプリケーションの中断を最小限に抑えるために、ノードは慎重に切断およびドレインされます。

ライフ サイクルのバージョンの詳細については、[AKS でサポートされている Kubernetes のバージョン][aks-supported versions]に関するページを参照してください。 アップグレードする方法の手順については、「[Azure Kubernetes Service (AKS) クラスターのアップグレード][aks-upgrade]」をご覧ください。

### <a name="gpu-enabled-nodes"></a>GPU 対応ノード

AKS は GPU 対応ノード プールの作成をサポートしています。 Azure は現在、単一または複数の GPU に対応する VM を提供しています。 GPU 対応 VM は、コンピューティング処理やグラフィック処理の負荷が高い視覚化ワークロードを想定して設計されています。

詳細については、「[AKS での GPU の使用][aks-gpu]」を参照してください。

### <a name="storage-volume-support"></a>ストレージ ボリュームのサポート

アプリケーション ワークロードをサポートするため、永続的なデータ用にストレージ ボリュームをマウントできます。 静的ボリュームと動的ボリュームの両方を使用できます。 ストレージを共有するために接続されているポッドの数に応じて、単一ポッドへのアクセスのためには Azure Disks、複数のポッドへの同時アクセスのためには Azure Files のいずれかに基づくストレージを使用できます。

詳細については、[AKS でのアプリケーションのストレージ オプション][concepts-storage]に関するページを参照してください。

[Azure Disks][azure-disk] または [Azure Files][azure-files] を使用して、動的な永続ボリュームの利用を開始してください。

## <a name="virtual-networks-and-ingress"></a>仮想ネットワークとイングレス

AKS クラスターは、既存の仮想ネットワークにデプロイできます。 この構成では、クラスター内のすべてのポッドに仮想ネットワーク内の IP アドレスが割り当てられて、クラスター内の他のポッドや仮想ネットワーク内の他のノードと、直接通信することができます。 ポッドは、ExpressRoute やサイト間 (S2S) VPN 接続を経由してピアリングされた仮想ネットワーク内の他のサービスや、オンプレミス ネットワークとも接続できます。

詳細については、[AKS のアプリケーションにおけるネットワークの概念][aks-networking]に関するページを参照してください。

イングレス トラフィックの使用を開始するには、「[HTTP アプリケーションのルーティング][aks-http-routing]」を参照してください。

### <a name="ingress-with-http-application-routing"></a>HTTP アプリケーション ルーティングを使用するイングレス

HTTP アプリケーション ルーティング アドオンを利用すると、AKS クラスターにデプロイされたアプリケーションへのアクセスが容易になります。 有効にすると、HTTP アプリケーション ルーティング ソリューションによって AKS クラスター内のイングレス コントローラーが構成されます。 アプリケーションがデプロイされると、パブリック アクセス可能な DNS 名が自動的に構成されます。 HTTP アプリケーション ルーティングは、DNS ゾーンを構成し、それを AKS クラスターと統合します。 その後は、Kubernetes イングレス リソースを通常どおりデプロイできます。

イングレス トラフィックの使用を開始するには、「[HTTP アプリケーションのルーティング][aks-http-routing]」を参照してください。

## <a name="development-tooling-integration"></a>開発ツールの統合

Kubernetes には、Helm、Draft、Visual Studio Code 用の Kubernetes 拡張機能など、開発および管理ツールの豊富なエコシステムがあります。 これらのツールは、AKS でシームレスに動作します。

その上、Azure Dev Spaces では、高速で反復的な Kubernetes 開発エクスペリエンスをチームに提供しています。 最小限の構成で、AKS 内で直接、コンテナーの実行とデバッグを行えます。 始める際には、[Azure Dev Spaces][azure-dev-spaces] についてのページを参照してください。

Azure DevOps プロジェクトには、既存のコードや Git リポジトリを Azure に取り込むための簡単なソリューションが用意されています。 この DevOps プロジェクトによって自動的に、Azure DevOps Services 内に AKS などの Azure リソースや CI 用のビルド パイプラインを含むリリース パイプラインを作成し、CD 用のリリース パイプラインを設定して、その後、監視用の Azure Application Insights リソースを作成することができます。

詳細については、[Azure DevOps プロジェクト][azure-devops]に関するページを参照してください。

## <a name="docker-image-support-and-private-container-registry"></a>Docker イメージのサポートとプライベート コンテナー レジストリ

AKS は Docker イメージ形式をサポートしています。 Docker イメージのプライベート ストレージのために、AKS を Azure Container Registry (ACR) と統合できます。

プライベート イメージ ストアを作成するには、「[Azure Container Registry][acr-docs]」を参照してください。

## <a name="kubernetes-certification"></a>Kubernetes 認定

Azure Kubernetes Service (AKS) は、Kubernetes 準拠として CNCF 認定されています。

## <a name="regulatory-compliance"></a>規制に対するコンプライアンス

Azure Kubernetes Service (AKS) は、SOC、ISO、PCI DSS、HIPAA に準拠しています。 詳細については、「[Overview of Microsoft Azure compliance (Microsoft Azure コンプライアンスの概要)][compliance-doc]」を参照してください。

## <a name="next-steps"></a>次の手順

AKS のデプロイと管理の詳細については、Azure CLI のクイックスタートで説明しています。

> [!div class="nextstepaction"]
> [AKS クイックスタート][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: https://docs.microsoft.com/azure/dev-spaces/azure-dev-spaces
[azure-devops]: https://docs.microsoft.com/azure/devops-project/overview
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../monitoring/monitoring-container-health.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#role-based-access-controls-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
