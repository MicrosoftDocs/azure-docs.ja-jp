---
title: Azure Arc を使用してハイブリッド インフラストラクチャを大規模に管理する
description: Azure の外部で顧客のマシンと Kubernetes クラスターを効果的に管理する方法について説明します。
ms.date: 03/12/2021
ms.topic: how-to
ms.openlocfilehash: ec32389cd7444405580530a00c8b7c5bc48bcd56
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103419332"
---
# <a name="manage-hybrid-infrastructure-at-scale-with-azure-arc"></a>Azure Arc を使用してハイブリッド インフラストラクチャを大規模に管理する

サービス プロバイダーは、[Azure Lighthouse](../overview.md) に複数の顧客テナントをオンボードしている場合があります。 Azure Lighthouse を使用すると、サービス プロバイダーは一度に複数の Azure Active Directory (Azure AD) テナントにわたって大規模に操作を実行できるため、管理タスクがより効率的になります。

[Azure Arc](../../azure-arc/overview.md) を使用すると、オンプレミス、エッジ、マルチクラウドにまたがる複雑な分散環境を簡素化し、任意の場所に Azure サービスをデプロイし、Azure の管理を任意のインフラストラクチャに拡張することができます。

[Azure Arc 対応サーバー](../../azure-arc/servers/overview.md)を使用すると、顧客は、ネイティブの Azure 仮想マシンを管理する場合と同じ方法で、企業ネットワーク上の Azure の外部でホストされている Windows および Linux マシンを管理できます。 ハイブリッド マシンを Azure にリンクすると、接続され、Azure でリソースとして扱われるようになります。 サービス プロバイダーでは、このような非 Azure マシンを顧客の Azure リソースと共に管理できます。

[Azure Arc 対応 Kubernetes](../../azure-arc/kubernetes/overview.md) を使用すると、顧客は、Azure の内部または外部で Kubernetes クラスターを接続して構成できるようになります。 Kubernetes クラスターを Azure Arc に接続すると、Azure portal に表示され、Azure Resource Manager ID とマネージド ID が付与されます。 クラスターは、標準の Azure サブスクリプションに接続され、リソース グループ内に存在し、他の Azure リソースと同様にタグを受け取ることができます。

このトピックでは、サービス プロバイダーが Azure Arc 対応サーバーと Azure Arc 対応 Kubernetes をスケーラブルな方法で使用し、管理対象のすべての顧客テナントを可視化して、顧客のハイブリッド環境を管理する方法の概要について説明します。

> [!TIP]
> このトピックではサービスのプロバイダーと顧客について触れますが、このガイドラインは、[Azure Lighthouse を使用して複数のテナントを管理する企業](../concepts/enterprise.md)にも当てはまります。

## <a name="manage-hybrid-servers-at-scale-with-azure-arc-enabled-servers"></a>ハイブリッド サーバーを大規模に管理する - Azure Arc 対応サーバー

サービス プロバイダーの場合、顧客が [Azure Connected Machine エージェント](../../azure-arc/servers/agent-overview.md)を使用してサブスクリプションに接続する、Azure の外部のオンプレミスの Windows Server または Linux マシンを管理できます。

Azure portal で委任されたサブスクリプションのリソースを表示すると、これらの接続されたマシンに **Azure Arc** というラベルが付いていることがわかります。これらの接続されたマシンは、顧客の Azure リソースを管理するのと同じ方法で、Azure Policy やタグ付けなどの Azure の構成概念を使用して管理できます。 また、顧客のテナント全体について作業し、接続されているすべてのハイブリッド マシンをまとめて管理することもできます。

たとえば、[顧客のハイブリッド マシン全体に同じポリシー セットを適用することができます](../../azure-arc/servers/learn/tutorial-assign-policy-portal.md)。 また、Azure Security Center を使用して、すべての顧客のハイブリッド環境全体のコンプライアンスを監視することや、[Azure Monitor を使用してハイブリッド マシンから直接データを収集し](../../azure-arc/servers/learn/tutorial-enable-vm-insights.md)、Log Analytics ワークスペースに取り込むこともできます。 [仮想マシン拡張機能](../../azure-arc/servers/manage-vm-extensions.md)は、Azure 以外の Windows および Linux VM にデプロイできるため、顧客のハイブリッド マシンの管理が簡素化されます。

## <a name="manage-hybrid-kubernetes-clusters-at-scale-with-azure-arc-enabled-kubernetes"></a>Azure Arc 対応 Kubernetes を使用してハイブリッド Kubernetes クラスターを大規模に管理する

[Azure Arc を使用して顧客のサブスクリプションに接続されている](../../azure-arc/kubernetes/connect-cluster.md) Kubernetes クラスターは、Azure で実行されている場合と同様に管理できます。

顧客が [Kubernetes クラスターを Azure Arc にオンボードするためにサービス プリンシパル アカウント](../../azure-arc/kubernetes/create-onboarding-service-principal.md)を作成した場合は、このサービス プリンシパル アカウントにアクセスしてクラスターをオンボードおよび管理することができます。 これを実行できるのは、サービス プリンシパル アカウントを含むサブスクリプションが [Azure Lighthouse](onboard-customer.md) にオンボードされたときに、"Kubernetes クラスター - Azure Arc のオンボード" Azure 組み込みロールを付与された管理テナントのユーザーです。

接続されたクラスターの GitOps を使用して、[構成](../../azure-arc/kubernetes/use-gitops-connected-cluster.md)と [Helm Chart](../../azure-arc/kubernetes/use-gitops-with-helm.md) をデプロイできます。

接続されたクラスターを Azure Monitor を使用して監視し、[Azure Policy を使用してクラスター構成を大規模に適用することもできます](../../azure-arc/kubernetes/use-azure-policy.md)。

## <a name="next-steps"></a>次の手順

- [Azure Arc GitHub リポジトリ](https://github.com/microsoft/azure_arc)ですぐに使える項目とサンプルを確認します。
- [Azure Arc 対応サーバーのサポートされるシナリオ](../../azure-arc/servers/overview.md#supported-scenarios)を確認します。
- [Azure Arc でサポートされている Kubernetes ディストリビューション](../../azure-arc/kubernetes/overview.md#supported-kubernetes-distributions)を確認します。
- [ポリシーを大規模にデプロイする](policy-at-scale.md)方法を確認します。
- [Azure Monitor ログを大規模に使用する](monitor-at-scale.md)方法を確認します。
