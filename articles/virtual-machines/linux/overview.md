---
title: Azure での Linux VM の概要 | Microsoft Docs
description: Linux 仮想マシンでの Azure Compute、Storage、Network サービスについて説明します。
services: virtual-machines-linux
documentationcenter: virtual-machines-linux
author: rickstercdn
manager: gwallace
editor: ''
ms.assetid: 7965a80f-ea24-4cc2-bc43-60b574101902
ms.service: virtual-machines-linux
ms.topic: overview
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 11/29/2017
ms.author: rclaus
ms.custom: H1Hack27Feb2017, mvc
ms.openlocfilehash: 14ed21efec47e15663724bd782ad931903fb8f13
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70082417"
---
# <a name="azure-and-linux"></a>Azure と Linux
Microsoft Azure は、分析、仮想マシン、データベース、モバイル、ネットワーク、ストレージ、Web など、多様なパブリック クラウド サービスを一元化した集合体として成長し続け、さまざまなソリューションのホストとして理想的な形態となっています。  Microsoft Azure のスケーラブルなコンピューティング プラットフォームによって、必要なときに使用した分だけを支払う料金体系が実現され、オンプレミスのハードウェアに投資する必要がありません。  貴社の顧客のニーズに応えるうえで必要なレベルにまでソリューションをいつでもスケールアップまたはスケールアウトできる柔軟性が Azure にはあります。

Amazon の AWS のさまざまな機能を使い慣れている場合は、Azure と AWS の [定義マッピング ドキュメント](https://azure.microsoft.com/campaigns/azure-vs-aws/mapping/)を調べることができます。

## <a name="regions"></a>リージョン
Microsoft Azure のリソースは、世界各国複数の地理的リージョンに分散されます。  "リージョン" とは、1 つの地域に存在する複数のデータ センターを表します。 現時点 (2018 年 8 月現在) で、Azure には一般公開されているリージョンが世界各地に 42 個あり、さらに 12 リージョンの追加が発表されています。これは他のクラウド プロバイダーよりも多いグローバル リージョン数です。 既存のリージョンと新しく発表されたリージョンの最新の一覧は次のページで確認できます。

* [Azure リージョン](https://azure.microsoft.com/regions/)

## <a name="availability"></a>可用性
Azure は、単一インスタンス仮想マシン向けに、業界をリードする 99.9% というサービス レベル アグリーメントを発表しました。ただし、すべてのディスクに Premium Storage を使用した VM をデプロイすることが条件となります。  デプロイが、VM に適用される 99.95% という標準のサービス レベル アグリーメントの要件を満たすためには、可用性セット内でワークロードを実行する複数の VM をデプロイする必要があります。 可用性セットにより、Azure データ センターにある複数の障害ドメインに VM を分散すると共に、メンテナンス期間の異なるホストにデプロイすることができます。 完全な [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) では、全体としての Azure の可用性の確保について説明します。

## <a name="managed-disks"></a>Managed Disks

Managed Disks により、Azure Storage アカウントの作成および管理はバックグラウンドで処理されるため、ストレージ アカウントのスケーラビリティの制限について心配する必要がありません。 ディスク サイズとパフォーマンス レベル (Standard または Premium) を指定すると、Azure によってディスクが作成および管理されます。 ディスクの追加や VM のスケールアップとスケールダウンを行うときに、使用されているストレージについて心配する必要はありません。 新しい VM を作成する場合は、[Azure CLI](quick-create-cli.md) または Azure portal を使用して、管理 OS とデータ ディスクで VM を作成します。 VM に非管理対象ディスクがある場合は、[VM を変換して Managed Disks でバックアップ](convert-unmanaged-to-managed-disks.md)できます。

また、Azure リージョンごとに 1 つのストレージ アカウントでカスタム イメージを管理することができます。このカスタム イメージを使用すると、同じサブスクリプション内で何百もの VM を作成することができます。 Managed Disks の詳細については、[Managed Disks の概要](../linux/managed-disks-overview.md)に関するページをご覧ください。

## <a name="azure-virtual-machines--instances"></a>Azure Virtual Machines とインスタンス
Microsoft Azure は、現在普及しているさまざまな Linux ディストリビューションに対応します。Microsoft の多数のパートナーが、それらのディストリビューションを提供、管理しています。  Azure Marketplace には、Red Hat Enterprise、CentOS、SUSE Linux Enterprise、Debian、Ubuntu、CoreOS、RancherOS、FreeBSD などのディストリビューションがあります。 Microsoft はさまざまな Linux コミュニティと積極的に連携し、[Azure 動作保証済み Linux ディストリビューション](endorsed-distros.md) リストを拡充しています。

必要な Linux ディストリビューションが現時点でギャラリーに存在しない場合は、[Azure で Linux VHD を作成およびアップロード](create-upload-generic.md)することで、必要とする Linux の VM を導入できます。

Azure Virtual Machines を使用すると、さまざまなコンピューティング ソリューションを俊敏にデプロイできます。 事実上、すべてのワークロードをすべての言語でほぼすべてのオペレーティング システム上 (Windows、Linux、または増え続けるパートナー各社でカスタム作成されたもの) にデプロイできます。 たとえ目的に合った仮想マシンが見つからなくても、 ご安心ください。  オンプレミスからカスタム イメージを取り込むことができます。

## <a name="vm-sizes"></a>VM サイズ
使用する VM の[サイズ](sizes.md)は、実行するワークロードによって決まります。 さらに、選択したサイズによって、処理能力、メモリ、ストレージの容量などの要素が決まります。 Azure では、さまざまな種類の使用をサポートするために、さまざまなサイズを用意しています。

Azure では、VM のサイズおよびオペレーティング システムに基づいて[時間単位の料金](https://azure.microsoft.com/pricing/details/virtual-machines/linux/)が請求されます。 時間単位を満たさない場合は、分単位でのみ請求されます。 ストレージは別料金で、別個に請求されます。

## <a name="automation"></a>Automation
適切な DevOps カルチャを実現するには、すべてのインフラストラクチャがコードである必要があります。  すべてのインフラストラクチャがコードである場合、簡単に再作成できます (Phoenix サーバー)。  Azure は、Ansible、Chef、SaltStack、Puppet などのすべての主要なオートメーション ツールと連携します。  Azure には、自動化のための独自のツールもあります。

* [Azure テンプレート](create-ssh-secured-vm-from-template.md)
* [Azure VMAccess](using-vmaccess-extension.md)

Azure は Linux ディストリビューションの多くで、 [cloud-init](https://cloud-init.io/) のサポートを展開しています。  現在、既定で有効になっている cloud-init で、Canonical の Ubuntu VM がデプロイされています。  RedHat の RHEL、CentOS、Fedora は cloud-init をサポートしますが、現在、RedHat で保持される Azure のイメージに cloud-init はインストールされていません。  RedHat family OS で cloud-init を使用するには、インストールされている cloud-init でカスタム イメージを作成する必要があります。

* [Azure Linux VM 上で cloud-init を使用する](using-cloud-init.md)

## <a name="quotas"></a>Quotas (クォータ)
Azure サブスクリプションにはそれぞれ既定のクォータ制限が設けられており、プロジェクトで多数の VM をデプロイする場合に、その点が影響する可能性があります。 現在は、リージョンあたり 20 VM の制限がサブスクリプションごとに設けられています。  クォータ制限は、制限の引き上げを要求するサポート チケットを申請することで、迅速かつ簡単に引き上げることができます。  クォータ制限の詳細については、次を参照してください。

* [Azure サブスクリプション サービスの制限](../../azure-subscription-service-limits.md)

## <a name="partners"></a>パートナー
Microsoft はパートナーと連携し、利用可能なイメージが Azure ランタイム用に更新、最適化されたことを確認します。  Azure パートナーの詳細については、次のリンクを参照してください。

* [Azure での動作保証済み Linux ディストリビューション](endorsed-distros.md)
* SUSE - [Azure Marketplace - SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps/SUSE.SLES?tab=Overview)
* Red Hat - [Azure Marketplace - Red Hat Enterprise Linux 7.2](https://azure.microsoft.com/marketplace/partners/redhat/redhatenterpriselinux72/)
* Canonical - [Azure Marketplace - Ubuntu Server 16.04 LTS](https://azure.microsoft.com/marketplace/partners/canonical/ubuntuserver1604lts/)
* Debian - [Azure Marketplace - Debian 8 "Jessie"](https://azure.microsoft.com/marketplace/partners/credativ/debian8/)
* FreeBSD - [Azure Marketplace - FreeBSD 10.4](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.FreeBSD104)
* CoreOS - [Azure Marketplace - CoreOS (Stable)](https://azure.microsoft.com/marketplace/partners/coreos/coreosstable/)
* RancherOS - [Azure Marketplace - RancherOS](https://azure.microsoft.com/marketplace/partners/rancher/rancheros/)
* Bitnami - [Bitnami Library for Azure](https://azure.bitnami.com/)
* Mesosphere - [Azure Marketplace - Mesosphere DC/OS on Azure](https://azure.microsoft.com/marketplace/partners/mesosphere/dcosdcos/)
* Docker - [Azure Marketplace - Azure Container Service with Docker Swarm](https://azure.microsoft.com/marketplace/partners/microsoft/acsswarms/)
* Jenkins - [Azure Marketplace - CloudBees Jenkins Platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cloudbees.cloudbees-core-contact)

## <a name="getting-started-with-linux-on-azure"></a>Microsoft Azure における Linux の概要
Azure の使用を開始するには、Azure アカウント、Azure CLI のインストール、および SSH 公開キーと秘密キーのペアが必要です。

### <a name="sign-up-for-an-account"></a>アカウントにサインアップする
Azure クラウドを使用する最初の手順は、Azure アカウントにサインアップすることです。  開始するには、 [Azure アカウントのサインアップ](https://azure.microsoft.com/pricing/free-trial/) ページに移動します。

### <a name="install-the-cli"></a>CLI をインストールする
新しい Azure アカウントを使用すると、Web ベースの管理パネルである Azure Portal の使用を直ちに開始することができます。  コマンド ラインから Azure クラウドを管理するには、`azure-cli` をインストールします。  Mac または Linux ワークステーションに [Azure CLI](/cli/azure/install-azure-cli) をインストールします。

### <a name="create-an-ssh-key-pair"></a>SSH キー ペアの作成
これで Azure アカウント、Azure Web Portal、Azure CLI の準備ができました。  次の手順では、パスワードを使用せずに Linux で SSH を使用するための SSH キー ペアを作成します。  [Linux および Mac で SSH キーを作成](mac-create-ssh-keys.md) し、パスワードのないログインとセキュリティの強化を実現します。

### <a name="create-a-vm-using-the-cli"></a>CLI を使用して VM を作成する
CLI を使用して Linux VM を作成すると、作業中のターミナルを離れることなく、すばやく VM をデプロイできます。  Web Portal で指定できるものは、コマンド ライン フラグまたはスイッチからも使用できます。  

* [CLI を使用して Linux VM を作成する](quick-create-cli.md)

### <a name="create-a-vm-in-the-portal"></a>ポータルで VM を作成する
Azure Web ポータルで Linux VM を作成すると、デプロイのためのさまざまなオプションから簡単にポイントおよびクリックすることができます。  コマンド ライン フラグまたはスイッチを使用する代わりに、さまざまなオプションと設定の Web レイアウトを表示できます。  コマンド ライン インターフェイスから使用できるものは、ポータルでも使用可能です。

* [ポータルを使用して Linux VM を作成する](quick-create-portal.md)

### <a name="log-in-using-ssh-without-a-password"></a>パスワードを使わずに SSH を使用してログインする
これで VM が Azure 上で実行され、ログインする準備ができました。  パスワードを使用して SSH からログインする方法は、安全ではなく、時間がかかります。  ログインの手段として最も安全性が高く、かつすばやい方法は、SSH キーを使用することです。  ポータルまたは CLI から Linux VM を作成する場合、認証の選択肢が 2 つあります。  SSH のパスワードを選択した場合、Azure はパスワードによるログインを許可するように VM を構成します。  SSH 公開キーを選択した場合、Azure は SSH キーを使用したログインのみを許可するように VM を構成し、パスワードのログインは無効になります。 SSH キーのログインのみを許可することにより Linux VM をセキュリティで保護するには、Portal または CLI で VM を作成するときに SSH 公開キーのオプションを使用します。

## <a name="related-azure-components"></a>関連する Azure のコンポーネント
## <a name="storage"></a>Storage
* [Microsoft Azure Storage の概要](../../storage/common/storage-introduction.md)
* [Azure CLI を使用して Linux VM にディスクを追加する](add-disk.md)
* [Azure Portal で Linux VM にデータ ディスクを接続する方法](attach-disk-portal.md)

## <a name="networking"></a>ネットワーク
* [Virtual Network の概要](../../virtual-network/virtual-networks-overview.md)
* [Azure 内の IP アドレス](../../virtual-network/virtual-network-ip-addresses-overview-arm.md)
* [Azure での Linux VM へのポートの開放](nsg-quickstart.md)
* [Azure Portal での完全修飾ドメイン名の作成](portal-create-fqdn.md)

## <a name="containers"></a>Containers
* [Virtual Machines とコンテナーが Azure にもたらすメリット](containers.md)
* [Azure Container Service の概要](../../container-service/container-service-intro.md)
* [Azure コンテナー サービス クラスターのデプロイ](../../container-service/dcos-swarm/container-service-deployment.md)

## <a name="next-steps"></a>次の手順
これで、Azure での Linux の概要の説明が終わりました。  次の手順では、VM の作成について詳しく説明します。

* [Azure CLI を使用した一般的なタスクの増え続けるサンプル スクリプトの一覧を確認する](cli-samples.md)
