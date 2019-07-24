---
title: 概念 - Azure Kubernetes Service (AKS) におけるセキュリティ
description: Azure Kubernetes Service (AKS) におけるセキュリティ (マスターとノードの通信、ネットワーク ポリシー、Kubernetes シークレットなど) について説明します。
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: iainfou
ms.openlocfilehash: 8fd5b726c01b056d38e7e187cec8270ee4e127a9
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009006"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS) でのアプリケーションとクラスターに対するセキュリティの概念

Azure Kubernetes Service (AKS) でアプリケーション ワークロードを実行する場合に顧客データを保護するためには、クラスターのセキュリティが重要な考慮事項です。 Kubernetes には、*ネットワーク ポリシー*や*シークレット*などのセキュリティ コンポーネントが含まれています。 ネットワーク セキュリティ グループや調整されたクラスターのアップグレードなどのコンポーネントは、Azure で後で追加されます。 これらのセキュリティ コンポーネントを組み合わせることで、AKS クラスターでは常に最新の OS セキュリティ更新プログラムと Kubernetes リリースが実行され、ポッドのトラフィックと機密の資格情報へのアクセスがセキュリティで保護されます。

この記事では、AKS 内でアプリケーションをセキュリティで保護する主要な概念について説明します。

- [マスター コンポーネントのセキュリティ](#master-security)
- [ノードのセキュリティ](#node-security)
- [クラスターのアップグレード](#cluster-upgrades)
- [ネットワークのセキュリティ](#network-security)
- [Kubernetes シークレット](#kubernetes-secrets)

## <a name="master-security"></a>マスターのセキュリティ

AKS では、Kubernetes マスター コンポーネントは、Microsoft で提供されているマネージド サービスの一部です。 各 AKS クラスターには、API サーバーやスケジューラなどを提供する独自シングル テナントの専用 Kubernetes マスターがあります。このマスターの管理と保守は、Microsoft によって行われます。

既定では、Kubernetes API サーバーは、パブリック IP アドレスを完全修飾ドメイン名 (FQDN) と一緒に使用します。 API サーバーへのアクセスは、Kubernetes のロールベースのアクセス制御と Azure Active Directory を使って制御できます。 詳細については、[Azure AD と AKS の統合][aks-aad]に関するページを参照してください。

## <a name="node-security"></a>ノードのセキュリティ

AKS ノードは、ユーザーが管理および保守する Azure 仮想マシンです。 ノードは、Moby コンテナー ランタイムを使用して、最適化された Ubuntu Linux ディストリビューションを実行します。 AKS クラスターを作成またはスケールアップすると、ノードは自動的に、最新の OS セキュリティ更新プログラムと構成でデプロイされます。

Azure プラットフォームでは、OS セキュリティ更新プログラムが夜間スケジュールでノードに自動的に適用されます。 OS セキュリティ更新プログラムがホストの再起動を必要とする場合、再起動は自動的には実行されません。 ノードは手動で再起動できますが、一般的な方法は [Kured][kured] (Kubernetes 用のオープン ソースの再起動デーモン) を使用する方法です。 Kured は [DaemonSet][aks-daemonsets] として実行され、再起動が必要であることを示すファイルの存在を各ノードで監視します。 再起動は、クラスター アップグレードと同じ[切断およびドレイン プロセス](#cordon-and-drain)を使用するクラスターで管理されます。

ノードは、パブリック IP アドレスが割り当てられていないプライベート仮想ネットワーク サブネットにデプロイされます。 トラブルシューティングと管理のために、既定では SSH が有効になっています。 この SSH アクセスは、内部 IP アドレスでのみ使用できるようにします。

ストレージを提供するために、ノードは Azure Managed Disks を使用します。 ほとんどの VM ノードのサイズでは、これらは高パフォーマンスの SSD によってサポートされる Premium ディスクです。 マネージド ディスクに格納されたデータは、Azure プラットフォームへの保存時に自動的に暗号化されます。 冗長性を高めるためには、これらのディスクも Azure データ センター内で安全にレプリケートされます。

現在、AKS またはその他の場所にある Kubernetes 環境は、悪意のあるマルチテナント使用に対して完全に安全ではありません。 ノードに対して *Pod Security Policy* やより高度なロール ベースのアクセス制御 (RBAC) などの追加のセキュリティ機能を使用すると、セキュリティ上の弱点を悪用されにくくなります。 ただし、悪意のあるマルチテナント ワークロードの実行に対して真のセキュリティを実現するために信頼できる唯一のセキュリティ レベルはハイパーバイザーです。 Kubernetes 用のセキュリティ ドメインは、個々のノードではなく、クラスター全体になります。 この種の悪意のあるマルチテナント ワークロードでは、物理的に分離されたクラスターを使用する必要があります。 ワークロードを分離する方法については、「[AKS でのクラスターの分離に関するベスト プラクティス][cluster-isolation]」を参照してください。

## <a name="cluster-upgrades"></a>クラスターのアップグレード

セキュリティとコンプライアンスのため、または最新の機能を使用するために、Azure には、AKS クラスターとコンポーネントのアップグレードを調整するためのツールが用意されています。 このアップグレードの調整には、Kubernetes のマスターとエージェントの両方のコンポーネントが含まれます。 AKS クラスターで[使用可能な Kubernetes バージョンの一覧](supported-kubernetes-versions.md)を表示できます。 アップグレード プロセスを開始するには、これらの使用可能なバージョンのいずれかを指定します。 その後、Azure が各 AKS ノードを安全に切断およびドレインし、アップグレードを実行します。

### <a name="cordon-and-drain"></a>切断およびドレイン

アップグレード プロセス中、AKS ノードはクラスターから個別に切断されるため、新しいポッドはそれらに対してはスケジュールされません。 ノードはその後でドレインされ、次のようにアップグレードされます。

- 既存のポッドが正常に終了し、残りのノード上にスケジュールされます。
- ノードが再起動され、アップグレード プロセスが完了すると、AKS クラスターに再度結合されます。
- ポッドは、もう一度そこで実行されるようにスケジュールされます。
- すべてのノードが正常にアップグレードされるまで、同じプロセスを使用してクラスター内の次のノードが切断およびドレインされます。

詳細については、「[AKS クラスターのアップグレード][aks-upgrade-cluster]」を参照してください。

## <a name="network-security"></a>ネットワークのセキュリティ

オンプレミス ネットワークの接続とセキュリティのために、既存の Azure 仮想ネットワーク サブネットに AKS クラスターをデプロイすることができます。 これらの仮想ネットワークには、オンプレミス ネットワークへの Azure サイト間 VPN または Express Route 接続がある場合があります。 Kubernetes イングレス コントローラーはプライベートの内部 IP アドレスで定義できるため、サービスはこの内部のネットワーク接続経由でのみアクセスすることができます。

### <a name="azure-network-security-groups"></a>Azure ネットワーク セキュリティ グループ

仮想ネットワークでのトラフィックのフローをフィルター処理するため、Azure はネットワーク セキュリティ グループ規則を使用します。 これらの規則は、リソースへのアクセスを許可または拒否する発信元と宛先の IP 範囲、ポート、およびプロトコルを定義します。 Kubernetes API サーバーへの TLS トラフィックを許可する、既定の規則が作成されます。 ロード バランサー、ポート マッピング、またはイングレス ルートでサービスを作成すると、AKS が自動的に、トラフィックが適切にフローするようにネットワーク セキュリティ グループを変更します。

## <a name="kubernetes-secrets"></a>Kubernetes シークレット

Kubernetes *シークレット*は、アクセス資格情報やキーなどの機密データをポッドに挿入するために使用します。 まず Kubernetes API を使用してシークレットを作成します。 ポッドまたはデプロイを定義するとき、特定のシークレットを要求できます。 シークレットは、シークレットを必要とするポッドがスケジュールされているノードのみに提供されます。シークレットは *tmpfs* に格納され、ディスクには書き込まれません。 シークレットを必要とする最後のポッドがノードから削除されると、シークレットはノードの tmpfs から削除されます。 シークレットは、指定した名前空間内に格納され、同じ名前空間内のポッドによってのみアクセスできます。

シークレットを使用すると、ポッドやサービスの YAML マニフェストに定義されている機密情報が減少します。 代わりに、YAML マニフェストの一部として Kubernetes API サーバーに格納されたシークレットを要求します。 この方法により、シークレットへのアクセス権が特定のポッドにのみ提供されます。

## <a name="next-steps"></a>次の手順

AKS クラスターのセキュリティでの保護を開始するには「[AKS クラスターのアップグレード][aks-upgrade-cluster]」を参照してください。

関連付けられているベスト プラクティスについては、[AKS でのクラスターのセキュリティとアップグレードに関するベスト プラクティス][operator-best-practices-cluster-security]のページを参照してください。

Kubernetes と AKS の中心概念の追加情報については、次の記事を参照してください。

- [Kubernetes/AKS クラスターとワークロード][aks-concepts-clusters-workloads]
- [Kubernetes/AKS の ID][aks-concepts-identity]
- [Kubernetes/AKS の仮想ネットワーク][aks-concepts-network]
- [Kubernetes/AKS のストレージ][aks-concepts-storage]
- [Kubernetes/AKS のスケール][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
