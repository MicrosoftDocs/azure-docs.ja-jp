---
title: Azure での Linux VM の概要
description: Azure における Linux 仮想マシンの概要です。
author: cynthn
ms.service: virtual-machines
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: fe1db967dac8a76119733f930e54a38160395932
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102549840"
---
# <a name="linux-virtual-machines-in-azure"></a>Azure の Linux 仮想マシン

Azure Virtual Machines (VM) は、Azure が提供する[スケーラブルなオンデマンド コンピューティング リソース](/azure/architecture/guide/technology-choices/compute-decision-tree)の 1 つです。 通常、コンピューティング環境を他の手段より細かく管理する必要がある場合に、VM を選択します。 この記事では、VM を作成する前に検討する必要のある事項、VM の作成方法、VM の管理方法に関する情報を提供します。

Azure VM は、VM を実行する物理的なハードウェアを購入して維持する手間を省き、仮想化がもたらす柔軟性を提供します。 ただし、VM のメンテナンス、つまり VM 上で動作するソフトウェアの構成、その修正プログラムの適用、インストールは必要です。

Azure の仮想マシンは、さまざまな方法で利用できます。 いくつかの例を次に示します。

* **開発とテスト** – Azure VM は、アプリケーションのコーディングとテストに必要な特定の構成でコンピューターをすばやく簡単に作成する手段を提供します。
* **クラウドのアプリケーション** – アプリケーションの需要は変動する可能性があるため、Azure の VM でアプリケーションを実行することは経済的に理に適っています。 VM が必要になったら追加分の料金を支払い、不要になったらシャットダウンすることができます。
* **データセンターの拡張** – Azure 仮想ネットワーク内の仮想マシンは、組織のネットワークに簡単に接続できます。

アプリケーションで使用する VM の数は、ニーズに応じてスケールアップおよびスケールアウトできます。

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>VM の作成前に検討する必要のある事項
Azure でアプリケーション インフラストラクチャを構築する際には、多数の[設計上の考慮事項](/azure/architecture/reference-architectures/n-tier/linux-vm)が必ず存在します。 開始する前に、VM の次の側面を考慮することが重要です。

* アプリケーション リソースの名前
* リソースが格納される場所
* VM のサイズ
* 作成できる VM の最大数
* VM で実行されるオペレーティング システム
* 開始した後の VM の構成
* VM で必要な関連リソース

### <a name="locations"></a>場所
Azure で作成されるすべてのリソースは、世界各地の複数の[地理的リージョン](https://azure.microsoft.com/regions/)に分散されます。 通常、このリージョンは VM の作成時には **場所** と呼ばれます。 VM の場合、この場所によって仮想ハード ディスクの格納場所を指定します。

次の表に、利用可能な場所の一覧を取得する方法の一部を示します。

| Method | 説明 |
| --- | --- |
| Azure portal |VM を作成するときに一覧から場所を選択します。 |
| Azure PowerShell |[Get-AzLocation](/powershell/module/az.resources/get-azlocation) コマンドを使用します。 |
| REST API |[場所の一覧表示](/rest/api/resources/subscriptions)操作を使用します。 |
| Azure CLI |[az account list-locations](/cli/azure/account) 操作を使用します。 |

## <a name="availability"></a>可用性
Azure は、単一インスタンス仮想マシン向けに、業界をリードする 99.9% というサービス レベル アグリーメントを発表しました。ただし、すべてのディスクに Premium Storage を使用した VM をデプロイすることが条件となります。  デプロイが、VM に適用される 99.95% という標準のサービス レベル アグリーメントの要件を満たすためには、可用性セット内でワークロードを実行する複数の VM をデプロイする必要があります。 可用性セットにより、Azure データ センターにある複数の障害ドメインに VM を分散すると共に、メンテナンス期間の異なるホストにデプロイすることができます。 完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) では、全体としての Azure の可用性の確保について説明します。

## <a name="vm-size"></a>[VM サイズ]
使用する VM の[サイズ](../sizes.md)は、実行するワークロードによって決まります。 さらに、選択したサイズによって、処理能力、メモリ、ストレージの容量などの要素が決まります。 Azure では、さまざまな種類の使用をサポートするために、さまざまなサイズを用意しています。

Azure では、VM のサイズおよびオペレーティング システムに基づいて[時間単位の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)が請求されます。 時間単位を満たさない場合は、分単位でのみ請求されます。 ストレージは別料金で、別個に請求されます。

## <a name="vm-limits"></a>VM の制限
サブスクリプションにはそれぞれ既定の[クォータ制限](../../azure-resource-manager/management/azure-subscription-service-limits.md)が設けられており、プロジェクトで多数の VM をデプロイする場合に、その点が影響する可能性があります。 現在は、リージョンあたり 20 VM の制限がサブスクリプションごとに設けられています。 制限は、[サポート チケットで引き上げを依頼する](../../azure-portal/supportability/resource-manager-core-quotas-request.md)ことによって引き上げることができます。

## <a name="managed-disks"></a>Managed Disks

Managed Disks により、Azure Storage アカウントの作成および管理はバックグラウンドで処理されるため、ストレージ アカウントのスケーラビリティの制限について心配する必要がありません。 ディスク サイズとパフォーマンス レベル (Standard または Premium) を指定すると、Azure によってディスクが作成および管理されます。 ディスクの追加や VM のスケールアップとスケールダウンを行うときに、使用されているストレージについて心配する必要はありません。 新しい VM を作成する場合は、[Azure CLI](quick-create-cli.md) または Azure portal を使用して、管理 OS とデータ ディスクで VM を作成します。 VM に非管理対象ディスクがある場合は、[VM を変換して Managed Disks でバックアップ](convert-unmanaged-to-managed-disks.md)できます。

また、Azure リージョンごとに 1 つのストレージ アカウントでカスタム イメージを管理することができます。このカスタム イメージを使用すると、同じサブスクリプション内で何百もの VM を作成することができます。 Managed Disks の詳細については、[Managed Disks の概要](../managed-disks-overview.md)に関するページをご覧ください。

## <a name="distributions"></a>ディストリビューション 
Microsoft Azure は、現在普及しているさまざまな Linux ディストリビューションに対応します。Microsoft の多数のパートナーが、それらのディストリビューションを提供、管理しています。  Azure Marketplace で利用可能なディストリビューションを検索できます。 Microsoft はさまざまな Linux コミュニティと積極的に連携し、[Azure 動作保証済み Linux ディストリビューション](endorsed-distros.md) リストを拡充しています。

必要な Linux ディストリビューションが現時点でギャラリーに存在しない場合は、[Azure で Linux VHD を作成およびアップロード](create-upload-generic.md)することで、必要とする Linux の VM を導入できます。

Microsoft はパートナーと連携し、利用可能なイメージが Azure ランタイム用に更新、最適化されたことを確認します。  Azure パートナーのオファーの詳細については、次のリンクを参照してください。

* [Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=suse)
* Red Hat - [Azure Marketplace - Red Hat Enterprise Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Red%20Hat%20Enterprise%20Linux)
* Canonical - [Azure Marketplace - Ubuntu Server](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Debian - [Azure Marketplace - Debian](https://azuremarketplace.microsoft.com/marketplace/apps?search=Debian&page=1)
* FreeBSD - [Azure Marketplace - FreeBSD](https://azuremarketplace.microsoft.com/marketplace/apps?search=freebsd&page=1)
* Flatcar - [Azure Marketplace - Flatcar Container Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Flatcar&page=1)
* RancherOS - [Azure Marketplace - RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Docker - [Azure Marketplace - Docker イメージ](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>cloud-init 

適切な DevOps カルチャを実現するには、すべてのインフラストラクチャがコードである必要があります。  すべてのインフラストラクチャがコードである場合、簡単に再作成できます。  Azure は、Ansible、Chef、SaltStack、Puppet などのすべての主要なオートメーション ツールと連携します。  Azure には、自動化のための独自のツールもあります。

* [Azure テンプレート](create-ssh-secured-vm-from-template.md)
* [Azure `VMaccess`](../extensions/vmaccess.md)

Azure は Linux ディストリビューションの多くで、[cloud-init](https://cloud-init.io/) をサポートしています。  Microsoft は、動作保証済み Linux ディストリビューションのパートナーと協力して、cloud-init 対応のイメージを Azure Marketplace で利用できるようにする作業を行っています。 これらのイメージによって、cloud-init のデプロイと構成が、VM および仮想マシン スケール セット とシームレスに動作するようになります。

* [Azure Linux VM 上で cloud-init を使用する](using-cloud-init.md)

## <a name="storage"></a>ストレージ
* [Microsoft Azure Storage の概要](../../storage/common/storage-introduction.md)
* [Azure CLI を使用して Linux VM にディスクを追加する](add-disk.md)
* [Azure Portal で Linux VM にデータ ディスクを接続する方法](attach-disk-portal.md)

## <a name="networking"></a>ネットワーク
* [Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)
* [Azure 内の IP アドレス](../../virtual-network/public-ip-addresses.md)
* [Azure での Linux VM へのポートの開放](nsg-quickstart.md)
* [Azure Portal での完全修飾ドメイン名の作成](../create-fqdn.md)


## <a name="data-residency"></a>データの保存場所

Azure では、顧客データを 1 つのリージョンに格納できるようにする機能は現在、アジア太平洋地域の東南アジア リージョン (シンガポール) と、ブラジル地域のブラジル南部リージョン (サンパウロ州) でのみ使用できます。 その他のすべてのリージョンでは、顧客データは geo 内に格納されます。 詳細については、[セキュリティ センター](https://azure.microsoft.com/global-infrastructure/data-residency/)に関するページを参照してください。


## <a name="next-steps"></a>次のステップ

最初の VM を作成する

- [ポータル](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
