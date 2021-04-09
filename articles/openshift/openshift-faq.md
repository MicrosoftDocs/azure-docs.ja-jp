---
title: Azure Red Hat OpenShift に関してよく寄せられる質問
description: Microsoft Azure Red Hat OpenShift に関してよく寄せられる質問への回答を示します
author: jimzim
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 07/31/2020
ms.openlocfilehash: a3721083e48774963cd761178abdb552c93b15c7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100634347"
---
# <a name="azure-red-hat-openshift-faq"></a>Azure Red Hat OpenShift に関する FAQ

この記事では、Microsoft Azure Red Hat OpenShift に関するよくあるご質問 (FAQ) に回答します。

## <a name="installation-and-upgrade"></a>インストールとアップグレード

### <a name="which-azure-regions-are-supported"></a>どの Azure リージョンがサポートされていますか?

Azure Red Hat OpenShift 4.x でサポートされているリージョンのリストについては、[利用可能なリージョン](https://azure.microsoft.com/global-infrastructure/services/?products=openshift&regions=all)に関するページを参照してください。

Azure Red Hat OpenShift 3.11 でサポートされているリージョンのリストについては、[リージョン別の利用可能な製品](supported-resources.md#azure-regions)に関するページを参照してください。

### <a name="what-virtual-machine-sizes-can-i-use"></a>どの仮想マシンのサイズを使用できますか?

Azure Red Hat OpenShift 4 でサポートされている仮想マシンのサイズのリストについては、[Azure Red Hat OpenShift 4 でサポートされているリソース](support-policies-v4.md)に関するページを参照してください。

Azure Red Hat OpenShift 3.11 でサポートされている仮想マシンのサイズのリストについては、[Azure Red Hat OpenShift 3.11 でサポートされているリソース](supported-resources.md)に関するページを参照してください。

### <a name="what-is-the-maximum-number-of-pods-in-an-azure-red-hat-openshift-cluster--what-is-the-maximum-number-of-pods-per-node-in-azure-red-hat-openshift"></a>Azure Red Hat OpenShift クラスター内のポッドの最大数はいくつですか?  Azure Red Hat OpenShift クラスターのノードあたりのポッドの最大数はいくつですか?

サポートされるポッドの実際の数は、アプリケーションのメモリ、CPU、ストレージの要件によって異なります。

Azure Red Hat OpenShift 4.x には、ノードあたり 250 のポッド制限と 100 の計算ノードの制限があります。 これにより、1 つのクラスターでサポートされるポッドの最大数が 250&times;100 = 25,000 に制限されます。

Azure Red Hat OpenShift 3.11 には、ノードあたり 50 のポッド制限と 20 の計算ノードの制限があります。 これにより、1 つのクラスターでサポートされるポッドの最大数が 50&times;20 = 1,000 に制限されます。

### <a name="can-a-cluster-have-compute-nodes-across-multiple-azure-regions"></a>クラスターは複数の Azure リージョンにわたってコンピューティング ノードを持つことができますか?

いいえ。 Azure Red Hat OpenShift クラスターのすべてのノードは、同じ Azure リージョンに由来する必要があります。

### <a name="can-a-cluster-be-deployed-across-multiple-availability-zones"></a>1 つのクラスターを複数の可用性ゾーンにデプロイできますか?

はい。 これは、クラスターが可用性ゾーンをサポートする Azure リージョンにデプロイされる場合に自動的に発生します。 詳細については、[可用性ゾーン](../availability-zones/az-overview.md#availability-zones)に関するページをご覧ください。

### <a name="are-control-plane-nodes-abstracted-away-as-they-are-with-azure-kubernetes-service-aks"></a>コントロール プレーン ノードは、Azure Kubernetes Service (AKS) の場合と同様に抽象化されますか?

いいえ。 クラスター マスター ノードを含めすべてのリソースは、顧客サブスクリプションで実行されます。 これらの種類のリソースは、読み取り専用のリソース グループに置かれます。

### <a name="does-the-cluster-reside-in-a-customer-subscription"></a>クラスターは顧客のサブスクリプション内に存在しますか? 

Azure マネージド アプリケーションは、顧客のサブスクリプションを利用するロックされたリソース グループ内に存在します。 お客様は、そのリソース グループ内のオブジェクトを表示できますが、変更することはできません。

### <a name="is-there-any-element-in-azure-red-hat-openshift-shared-with-other-customers-or-is-everything-independent"></a>他の顧客と共有される Azure Red Hat OpenShift 内の要素はありますか? それとも、すべてが独立しているのでしょうか?

各 Azure Red Hat OpenShift クラスターは指定された顧客専用であり、その顧客のサブスクリプション内に存在します。 

### <a name="are-infrastructure-nodes-available"></a>インフラストラクチャ ノードは使用できますか?

Azure Red Hat OpenShift 4.x クラスターでは、インフラストラクチャ ノードは現在使用できません。

Azure Red Hat OpenShift 3.11 クラスターでは、インフラストラクチャ ノードが既定で含まれています。

## <a name="how-do-i-handle-cluster-upgrades"></a>クラスターのアップグレードはどのように処理しますか?

アップグレード、メンテナンス、およびサポートされているバージョンの詳細については、[サポート ライフサイクル ガイド](support-lifecycle.md)を参照してください。

### <a name="how-will-the-host-operating-system-and-openshift-software-be-updated"></a>ホスト オペレーティング システムと OpenShift ソフトウェアはどのように更新されますか?

ホスト オペレーティング システムと OpenShift ソフトウェアは、Azure Red Hat OpenShift が、上流の OpenShift Container Platform のマイナー リリース バージョンと修正プログラムを使用するときに更新されます。

### <a name="whats-the-process-to-reboot-the-updated-node"></a>更新されたノードを再起動するプロセスとは何ですか?

ノードは、アップグレードの一環として再起動されます。

## <a name="cluster-operations"></a>クラスターの操作

### <a name="can-i-use-prometheus-to-monitor-my-applications"></a>Prometheus を使用して自分のアプリケーションを監視できますか?

Prometheus は、プレインストールされ、Azure Red Hat OpenShift 4.x クラスター用に構成されています。 詳細については、[クラスターの監視](https://docs.openshift.com/container-platform/4.6/operators/operator_sdk/osdk-monitoring-prometheus.html)に関する記述を参照してください。

Azure Red Hat OpenShift 3.11 クラスターの場合は、Prometheus をご自分の名前空間にデプロイして、その名前空間内のアプリケーションを監視できます。 詳細については、[Azure Red Hat OpenShift クラスターに Prometheus インスタンスをデプロイする](howto-deploy-prometheus.md)方法に関するページを参照してください。

### <a name="can-i-use-prometheus-to-monitor-metrics-related-to-cluster-health-and-capacity"></a>Prometheus を使用して、クラスターの正常性と容量に関連するメトリックを監視できますか?

Azure Red Hat OpenShift 4.x:はい。

Azure Red Hat OpenShift 3.11:いいえ。

### <a name="can-logs-of-underlying-vms-be-streamed-out-to-a-customer-log-analysis-system"></a>基になる VM のログを顧客のログ分析システムにストリーミングできますか?

基になる VM からのログは、管理サービスによって処理され、顧客に公開されることはありません。

### <a name="how-can-a-customer-get-access-to-metrics-like-cpumemory-at-the-node-level-to-take-action-to-scale-debug-issues-etc-i-cannot-seem-to-run-kubectl-top-on-an-azure-red-hat-openshift-cluster"></a>顧客はどのようにしてノード レベルで CPU やメモリなどのメトリックにアクセスし、スケーリングや問題のデバッグなどを実行できますか? Azure Red Hat OpenShift クラスターで kubectl top を実行できないようです。

Azure Red Hat OpenShift 4.x クラスターの場合、OpenShift Web コンソールには、ノード レベルのすべてのメトリックが含まれます。 詳しくは、[クラスター情報の表示](https://docs.openshift.com/container-platform/4.6/web_console/using-dashboard-to-get-cluster-information.html)に関する Red Hat のドキュメントを参照してください。

Azure Red Hat OpenShift 3.11 クラスターの場合、顧客は顧客管理者クラスター ロールで `oc adm top nodes` または `kubectl top nodes` コマンドを使用することで、ノード レベルで CPU またはメモリ メトリックにアクセスできます。 また、`oc adm top pods` または `kubectl top pods` コマンドを使用して、`pods` の CPU またはメモリのメトリックにアクセスすることもできます。

### <a name="if-we-scale-up-the-deployment-how-do-azure-fault-domains-map-into-pod-placement-to-ensure-all-pods-for-a-service-do-not-get-knocked-out-by-a-failure-in-a-single-fault-domain"></a>デプロイをスケールアップする場合、サービスのすべてのポッドが 1 つの障害ドメインの障害によってダウンしないことを確実にするために、Azure 障害ドメインはポッド配置にどのようにマップされますか?

Azure で仮想マシン スケール セットを使用すると、既定で 5 つの障害ドメインがあります。 スケール セット内の各仮想マシン インスタンスは、これらの障害ドメインのいずれかに配置されます。 これにより、クラスター内のコンピューティング ノードにデプロイされたアプリケーションが別々の障害ドメインに確実に配置されるようになります。

詳細については、「[仮想マシン スケール セットに対する障害ドメインの適切な数を選択する](../virtual-machine-scale-sets/virtual-machine-scale-sets-manage-fault-domains.md)」を参照してください。

### <a name="is-there-a-way-to-manage-pod-placement"></a>ポッドの配置を管理する方法はありますか?

顧客は、顧客管理者としてノードを取得したり、ラベルを表示したりできます。これにより、スケール セット内の任意の VM を対象にする方法が提供されます。

特定のラベルを使用する場合は、次の点に注意してください。

- ホスト名を使うことはできません。 ホスト名はアップグレードと更新によって頻繁にローテーションされるため、変更が保証されます。
- 顧客に特定のラベルまたは配置戦略の要求がある場合は、これを実現できますが、エンジニアリングの作業が必要であり、現時点ではサポートされていません。

詳細については、[ポッドの配置の制御](https://docs.openshift.com/container-platform/4.6/nodes/scheduling/nodes-scheduler-about.html)に関するページを参照してください。

### <a name="is-the-image-registry-available-externally-so-i-can-use-tools-such-as-jenkins"></a>Jenkins などのツールを使用できるように、イメージ レジストリを外部で使用できますか?

4\.x クラスターの場合は、セキュリティで保護されたレジストリを公開し、認証を構成する必要があります。 詳しくは、次の Red Hat のドキュメントをご覧ください。

- [レジストリを公開する](https://docs.openshift.com/container-platform/4.6/registry/securing-exposing-registry.html)
- [レジストリにアクセスする](https://docs.openshift.com/container-platform/4.6/registry/accessing-the-registry.html)

3\.11 クラスターの場合は、Docker イメージ レジストリを使用できます。 Docker レジストリは `https://docker-registry.apps.<clustername>.<region>.azmosa.io/` から入手できます。 Azure Container Registry を使用することもできます。

## <a name="networking"></a>ネットワーク

### <a name="can-i-deploy-a-cluster-into-an-existing-virtual-network"></a>既存の仮想ネットワークにクラスターをデプロイできますか?

4\.x クラスターでは、既存の VNet にクラスターをデプロイできます。

3\.11 クラスターでは、既存の VNet にクラスターをデプロイすることはできません。 ピアリングを使って Azure Red Hat OpenShift 3.11 クラスターを既存の VNet に接続することはできます。

### <a name="is-cross-namespace-networking-supported"></a>名前空間間のネットワークはサポートされていますか?

顧客と個々のプロジェクト管理者は、`NetworkPolicy` オブジェクトを使用して、プロジェクトごとに名前空間の間のネットワークをカスタマイズできます (拒否を含む)。

### <a name="i-am-trying-to-peer-into-a-virtual-network-in-a-different-subscription-but-getting-failed-to-get-vnet-cidr-error"></a>別のサブスクリプションの仮想ネットワークを詳しく見ようとしていますが、Failed to get VNet CIDR (VNet CIDR の取得に失敗しました) というエラーが出ます。

仮想ネットワークを含むサブスクリプションでは、必ず `az provider register -n Microsoft.ContainerService --wait` のコマンドを使用して `Microsoft.ContainerService` プロバイダーを登録してください。

### <a name="can-we-specify-ip-ranges-for-deployment-on-the-private-vnet-avoiding-clashes-with-other-corporate-vnets-once-peered"></a>プライベート VNet にデプロイする IP 範囲を指定して、ピアリングされた他の企業 VNet との競合を回避することはできますか?

4\.x クラスターでは、独自の IP 範囲を指定できます。

3\.11 クラスターでは、Azure Red Hat OpenShift は、VNet ピアリングをサポートしており、顧客がピアリングする VNet と、OpenShift ネットワークが動作する VNet CIDR を提供することを許可します。

Azure Red Hat OpenShift によって作成された VNet は保護され、構成の変更は受け入れられません。 ピアリングされた VNet は顧客によって制御され、そのサブスクリプションに存在します。

### <a name="is-the-software-defined-network-module-configurable"></a>ソフトウェア定義ネットワーク モジュールは構成できますか?

ソフトウェア定義ネットワークは `openshift-ovs-networkpolicy` であり、構成できません。

### <a name="what-azure-load-balancer-is-used-by-azure-red-hat-openshift--is-it-standard-or-basic-and-is-it-configurable"></a>Azure Red Hat OpenShift では、どの Azure Load Balancer が使用されていますか?  Standard ですか、それとも Basic ですか、また、構成は可能ですか?

Azure Red Hat OpenShift では Standard Azure Load Balancer が使用され、構成することはできません。

## <a name="permissions"></a>アクセス許可

### <a name="can-an-admin-manage-users-and-quotas"></a>管理者はユーザーおよびクォータを管理できますか?

はい。 Azure Red Hat OpenShift 管理者は、ユーザーおよびクォータを管理するほか、ユーザーが作成したすべてのプロジェクトにアクセスできます。

### <a name="can-i-restrict-a-cluster-to-only-certain-azure-ad-users"></a>クラスターを特定の Azure AD ユーザーに制限できますか?

はい。 Azure AD アプリケーションを構成することで、クラスターにサインインできる Azure AD ユーザーを制限できます。 詳細については、[アプリを一連のユーザーに制限する方法](../active-directory/develop/howto-restrict-your-app-to-a-set-of-users.md)に関するページを参照してください。

### <a name="can-i-restrict-users-from-creating-projects"></a>ユーザーがプロジェクトを作成することを禁止できますか。

はい。 管理者としてクラスターにログインし、次のコマンドを実行します。

```
oc adm policy \
    remove-cluster-role-from-group self-provisioner \
    system:authenticated:oauth
```

詳細については、ご使用のクラスター バージョンのセルフプロビジョニングの無効化に関する OpenShift のドキュメントを参照してください。

- [4.6 クラスターでのセルフプロビジョニングの無効化](https://docs.openshift.com/container-platform/4.6/applications/projects/configuring-project-creation.html#disabling-project-self-provisioning_configuring-project-creation)
- [3.11 クラスターでのセルフプロビジョニングの無効化](https://docs.openshift.com/container-platform/3.11/admin_guide/managing_projects.html#disabling-self-provisioning)

### <a name="which-unix-rights-in-iaas-are-available-for-mastersinfraapp-nodes"></a>マスター/インフラストラクチャ/アプリ ノードでは、どの UNIX 権限 (IaaS 内) を使用できますか?

4\.x クラスターの場合、ノード アクセスはクラスター管理者ロールを介して利用できます。 詳細については、[Kubernetes RBAC の概要](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html)に関するページを参照してください。

3\.11 クラスターの場合、ノード アクセスは禁止されています。

### <a name="which-ocp-rights-do-we-have-cluster-admin-project-admin"></a>私たちには、どの OCP 権限がありますか? クラスター管理者ですか? プロジェクト管理者ですか?

4\.x クラスターの場合、クラスター管理者ロールを使用できます。 詳細については、[Kubernetes RBAC の概要](https://docs.openshift.com/container-platform/4.6/authentication/using-rbac.html)に関するページを参照してください。

3\.11 クラスターの場合、詳細については[クラスター管理の概要](https://docs.openshift.com/aro/admin_guide/index.html)を参照してください。

### <a name="which-identity-providers-are-available"></a>使用可能な ID プロバイダーはどれですか?

4\. x クラスターの場合は、独自の ID プロバイダーを構成します。 詳細については、[ID プロバイダーの構成](https://docs.openshift.com/container-platform/4.6/authentication/identity_providers/configuring-ldap-identity-provider.html)に関する Red Hat のドキュメントを参照してください。

3\.11 クラスターの場合は、Azure AD 統合を使用できます。 

## <a name="storage"></a>ストレージ

### <a name="is-data-on-my-cluster-encrypted"></a>クラスター上のデータは暗号化されていますか?

既定では、データは保存時に暗号化されます。 Azure Storage プラットフォームは、永続化する前に自動的にデータを暗号化し、取得前にデータの暗号化を解除します。 詳細については、「[保存データ向け Azure Storage Service Encryption](../storage/common/storage-service-encryption.md)」をご覧ください。

### <a name="is-data-stored-in-etcd-encrypted-on-azure-red-hat-openshift"></a>Azure Red Hat OpenShift では etcd に格納されるデータは暗号化されますか?

Azure Red Hat OpenShift 4 クラスターの場合、データは既定では暗号化されませんが、暗号化を有効にするオプションがあります。 詳細については、[etcd の暗号化](https://docs.openshift.com/container-platform/4.6/security/encrypting-etcd.html)に関するガイドを参照してください。

3\.11 クラスターの場合、etcd レベルではデータは暗号化されません。 現在、暗号化をオンにするオプションはサポートされていません。 OpenShift ではこの機能がサポートされますが、これをロードマップに加えるには、エンジニアリングの作業が必要です。 データはディスク レベルで暗号化されます。 詳細については、「[データストア層でのデータの暗号化](https://docs.openshift.com/container-platform/3.11/admin_guide/encrypting_data.html)」を参照してください。

### <a name="can-we-choose-any-persistent-storage-solution-like-ocs"></a>任意の永続ストレージ ソリューション (OCSなど) を選ぶことはできますか。 

4\.x クラスターの場合、Azure Disk (Premium_LRS) は既定のストレージ クラスとして構成されます。 追加のストレージ プロバイダー、および構成の詳細 (Azure File を含む) については、[永続ストレージ](https://docs.openshift.com/container-platform/4.6/storage/understanding-persistent-storage.html)に関する Red Hat のドキュメントを参照してください。

3\.11 クラスターの場合、Azure Disk (Premium_LRS) と Azure File の 2 つのストレージ クラスが既定で提供されます。

## <a name="does-aro-store-any-customer-data-outside-of-the-clusters-region"></a>ARO では、クラスターのリージョン外に格納される顧客データがありますか?

いいえ。 ARO クラスターで作成されたすべてのデータは、クラスターのリージョン内に維持されます。
