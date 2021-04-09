---
title: Azure Virtual Machines 上の SQL Server に関するドキュメントの変更
description: Azure Virtual Machines 上の SQL Server のさまざまなリリースにおける新機能と機能改善について説明します。
services: virtual-machines-windows
author: MashaMSFT
ms.author: mathoma
tags: azure-service-management
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: virtual-machines-sql
ms.topic: reference
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 10/15/2020
ms.openlocfilehash: ff4e6e0451b57046fb8f07f5a1051235e1f6d0f5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96325725"
---
# <a name="documentation-changes-for-sql-server-on-azure-virtual-machines"></a>Azure Virtual Machines 上の SQL Server に関するドキュメントの変更
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Azure では、SQL Server のイメージを組み込んだ仮想マシン (VM) をデプロイできます。 この記事では、[Azure Virtual Machines 上の SQL Server](https://azure.microsoft.com/services/virtual-machines/sql-server/) の最新リリースで導入された新機能と機能強化に関連するドキュメントの変更をまとめます。 

## <a name="october-2020"></a>2020 年 10 月

| [変更点] | 詳細 |
| --- | --- |
| **AG の DNN** | SQL Server 2019 CU8 以降向けに[分散ネットワーク名 (DNN) リスナー](availability-group-distributed-network-name-dnn-listener-configure.md)を構成して、従来の [VNN リスナー](availability-group-overview.md#connectivity)を置き換えることができるようになりました。これにより、Azure Load Balancer が必要なくなります。   | 

## <a name="september-2020"></a>2020 年 9 月

| [変更点] | 詳細 |
| --- | --- |
| **自動拡張機能登録** | [自動登録](sql-agent-extension-automatic-registration-all-vms.md)機能を有効にして、サブスクリプションに既にデプロイされているすべての SQL Server VM を、[SQL IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に自動的に登録できるようになりました。 これは既存のすべての VM に適用され、今後追加されるすべての SQL Server VM も自動的に登録されます。   | 


## <a name="august-2020"></a>2020 年 8 月

| [変更点] | 詳細 |
| --- | --- |
| **ポータルでの ag の構成** | [Azure portal を使用して可用性グループを構成する](availability-group-azure-portal-configure.md)ことができるようになりました。 この機能は現在プレビュー段階であり、デプロイの途中です。目的のリージョンで利用できない場合は、後日に再度ご確認ください。 | 


## <a name="july-2020"></a>2020 年 7 月


| [変更点] | 詳細 |
| --- | --- |
| **Ultra Disk へのログの移行** | ハイ パフォーマンスと低待機時間を活用するために、[ログ ファイルを Ultra Disk に移行する](storage-migrate-to-ultradisk.md)方法について学習します。 | 
| **Azure PowerShell を使用した AG の作成** | [Azure PowerShell](availability-group-az-commandline-configure.md) と Azure CLI を使用して、可用性グループの作成を簡略化できるようになりました。 | 


## <a name="june-2020"></a>2020 年 6 月

| [変更点] | 詳細 |
| --- | --- |
| **分散ネットワーク名 (DNN)** | Windows Server 2016 以降の SQL Server 2019 では、Azure Load Balancer を使用する代わりに[分散ネットワーク名](./failover-cluster-instance-distributed-network-name-dnn-configure.md)を使用してフェールオーバー クラスター インスタンス (FCI) にトラフィックをルーティングするためのサポートをプレビュー公開しています。 このサポートにより、Azure の高可用性 (HA) ソリューションへの接続が簡素化および合理化されます。 | 
| **Azure 共有ディスクを使用した FCI** | [Azure 共有ディスク](failover-cluster-instance-azure-shared-disks-manually-configure.md)を使用し、[フェールオーバー クラスター インスタンス (FCI)](failover-cluster-instance-overview.md) をデプロイできるようになりました。 |
| **再構成された FCI のドキュメント** | 書き直しおよび再構成により、[Azure VM 上の SQL Server を使用したフェールオーバー クラスター インスタンス](failover-cluster-instance-overview.md)に関するドキュメントがわかりやすくなりました。 [クラスター構成のベスト プラクティス](hadr-cluster-best-practices.md)、[SQL Server FCI 用の仮想マシン](failover-cluster-instance-prepare-vm.md)を準備する方法、[Azure Load Balancer](./availability-group-vnn-azure-load-balancer-configure.md) を構成する方法など、構成コンテンツの一部が分けられました。 | 
| &nbsp; | &nbsp; |


## <a name="may-2020"></a>2020 年 5 月 

| [変更点] | 詳細 |
| --- | --- |
| **Azure SQL ファミリ** | Azure Virtual Machines 上の SQL Server は、[Azure SQL 製品ファミリ](../../azure-sql-iaas-vs-paas-what-is-overview.md)の一部になりました。 [最新の状況](../index.yml)を確認してください。 製品に変更はありませんが、ドキュメントでは Azure SQL 製品に関する決定を容易にすることを目的としています。 | 


## <a name="january-2020"></a>2020 年 1 月

| [変更点] | 詳細 |
| --- | --- |
| **Azure Government のサポート** | [Azure Government](https://azure.microsoft.com/global-infrastructure/government/) クラウドでホストされている仮想マシンの SQL IaaS Agent 拡張機能に SQL Server 仮想マシンを登録できるようになりました。 | 
| &nbsp; | &nbsp; |

## <a name="2019"></a>2019

|[変更点] | 詳細 |
 --- | --- |
| **Azure での無料 DR レプリカ** | [ソフトウェア アシュアランス](https://www.microsoft.com/licensing/licensing-programs/software-assurance-default?rtc=1&activetab=software-assurance-default-pivot:primaryr3)がある場合は、オンプレミスの SQL Server インスタンス用に Azure でディザスター リカバリー用の[無料のパッシブ インスタンス](business-continuity-high-availability-disaster-recovery-hadr-overview.md#free-dr-replica-in-azure)をホストできます。 | 
| **一括 SQL IaaS 拡張機能登録** | [SQL IaaS Agent 拡張機能](sql-server-iaas-agent-extension-automate-management.md)に SQL Server 仮想マシンを[一括登録](sql-agent-extension-manually-register-vms-bulk.md)できるようになりました。 | 
|**パフォーマンスが最適化されたストレージ構成** | 新しい SQL Server VM を作成するときに、[ストレージの構成を完全にカスタマイズする](storage-configuration.md#new-vms)ことができるようになりました。 |
|**FCI 用の Premium ファイル共有** | [記憶域スペース ダイレクト](failover-cluster-instance-storage-spaces-direct-manually-configure.md)の元の方法ではなく、[Premium ファイル共有](failover-cluster-instance-premium-file-share-manually-configure.md)を使用してフェールオーバー クラスター インスタンスを作成できるようになりました。 
| **Azure Dedicated Host** | 対象の SQL Server VM を [Azure Dedicated Host](dedicated-host.md) で実行できます。 | 
| **別のリージョンへの SQL Server VM の移行** | Azure Site Recovery を使用して、[異なるリージョン間で SQL Server VM を移行](move-sql-vm-different-region.md)します。 |
|  **新しい SQL IaaS インストール モード** | SQL Server サービスの再開を回避するため、SQL Server IaaS 拡張機能を[軽量モード](sql-server-iaas-agent-extension-automate-management.md)でインストールできるようになりました。  |
| **SQL Server エディションの変更** | SQL Server VM の [エディション プロパティ](change-sql-server-edition.md)を変更できるようになりました。 |
| **SQL IaaS Agent 拡張機能への変更** | 新しい SQL IaaS モードを使用して、[SQL Server VM を SQL IaaS Agent 拡張機能に登録](sql-agent-extension-manually-register-single-vm.md)できます。 この機能には、[Windows Server 2008 のイメージ](sql-server-iaas-agent-extension-automate-management.md#management-modes)が含まれます。|
| **Azure ハイブリッド特典を使用するライセンス持ち込みイメージ** | Azure Marketplace からデプロイされたライセンス持ち込みイメージを使用して、[ライセンスの種類を従量課金制に](licensing-model-azure-hybrid-benefit-ahb-change.md#remarks)切り替えることができるようになりました。| 
| **Azure portal での新しい SQL Server VM の管理** | Azure portal で SQL Server VM を管理する新しい方法が導入されました。 詳細については、「[Azure portal で SQL Server VM を管理する](manage-sql-vm-portal.md)」を参照してください。  | 
| **SQL Server 2008 および 2008 R2 の延長サポート** | "*そのまま*" Azure VM に移行することで、SQL Server 2008 および SQL Server 2008 R2 の [サポートを延長](sql-server-2008-extend-end-of-support.md)します。 | 
| **カスタム イメージのサポートの可否** | OS と SQL Server イメージをカスタマイズするために、[SQL Server IaaS 拡張機能](sql-server-iaas-agent-extension-automate-management.md#installation)をインストールできるようになりました。これにより、機能が制限された[柔軟なライセンス](licensing-model-azure-hybrid-benefit-ahb-change.md)が提供されます。 対象のカスタム イメージを SQL IaaS Agent 拡張機能に登録するときに、ライセンスの種類として "AHUB" を指定します。 そうしないと、登録は失敗します。 | 
| **名前付きインスタンスのサポートの可否** | 既定のインスタンスが適切にアンインストールされている場合、名前付きインスタンスで [SQL Server IaaS 拡張機能](sql-server-iaas-agent-extension-automate-management.md#installation)を使用できるようになりました。 | 
| **ポータルの機能強化** | SQL Server VM をデプロイするための Azure portal エクスペリエンスは、より使いやすくなるように改良されています。 詳細については、SQL Server VM のデプロイに関する簡単な[クイックスタート](sql-vm-create-portal-quickstart.md)と、より包括的な[ハウツー](create-sql-vm-portal.md) ガイドを参照してください。|
| **ポータルの改善** | [Azure portal](licensing-model-azure-hybrid-benefit-ahb-change.md#change-license-model) を使用して、SQL Server VM のライセンス モデルを従量課金制からライセンス持ち込みに変更できるようになりました。|
| **Azure CLI を使用した SQL Server VM への可用性グループのデプロイの簡素化** | 可用性グループを Azure の SQL Server VM にデプロイすることが以前より簡単になりました。 [Azure CLI](/cli/azure/sql/vm?view=azure-cli-2018-03-01-hybrid&preserve-view=true) を使用すると、Windows フェールオーバー クラスター、内部ロード バランサー、および可用性グループのリスナーのすべてをコマンド ラインから作成できます。 詳細については、「[Azure CLI を使用して Azure VM で SQL Server の Always On 可用性グループを構成する](./availability-group-az-commandline-configure.md)」を参照してください。 | 
| &nbsp; | &nbsp; |

## <a name="2018"></a>2018 

 [変更点] | 詳細 |
| --- | --- |
|  **SQL Server クラスターの新しいリソース プロバイダー** | Windows フェールオーバー クラスターのメタデータを定義する新しいリソース プロバイダー (Microsoft.SqlVirtualMachine/SqlVirtualMachineGroups)。 SQL Server VM を *SqlVirtualMachineGroups* に参加させることで、Windows Server フェールオーバー クラスター (WSFC) サービスをブートストラップし、VM をクラスターに参加させます。  |
| **Azure クイックスタート テンプレートを使用して自動化された可用性グループのデプロイの設定** |2 つの Azure クイックスタート テンプレートを使用して、Windows フェールオーバー クラスターを作成し、SQL Server VM をそれに参加させ、リスナーを作成し、内部ロード バランサーを構成することができるようになりました。 詳細については、「[Azure クイックスタート テンプレートを使用して Azure VM で SQL Server の Always On 可用性グループを構成する](availability-group-quickstart-template-configure.md)」を参照してください。 | 
| **SQL IaaS Agent 拡張機能への自動登録** | 今月より後にデプロイされる SQL Server VM は、自動的に新しい SQL IaaS Agent 拡張機能に登録されます。 今月より前にデプロイされた SQL Server VM は、まだ手動で登録する必要があります。 詳細については、「[Azure の SQL Server 仮想マシンを SQL IaaS Agent 拡張機能に登録する](sql-agent-extension-manually-register-single-vm.md)」を参照してください。|
|**新しい SQL IaaS Agent 拡張機能** |  新しいリソース プロバイダー (Microsoft.SqlVirtualMachine) を使用すると、SQL Server VM の管理が強化されます。 VM の登録の詳細については、「[Azure の SQL Server 仮想マシンを SQL IaaS Agent 拡張機能に登録する](sql-agent-extension-manually-register-single-vm.md)」を参照してください。 |
|**ライセンス モデルを切り替える** | Azure CLI または PowerShell を使用して、SQL Server VM のライセンス モデルを従量制と持ち込みの間で切り替えることができるようになりました。 詳細については、[Azure の SQL Server 仮想マシンのライセンス モデルを変更する方法](licensing-model-azure-hybrid-benefit-ahb-change.md)に関するページを参照してください。 | 
| &nbsp; | &nbsp; |

## <a name="additional-resources"></a>その他のリソース

**Windows VM**:

* [Windows VM における SQL Server の概要](sql-server-on-azure-vm-iaas-what-is-overview.md)
* [Windows VM での SQL Server のプロビジョニング](create-sql-vm-portal.md)
* [Azure VM の SQL Server へのデータベースの移行](migrate-to-vm-from-sql-server.md)
* [Azure 仮想マシンにおける SQL Server の高可用性とディザスター リカバリー](business-continuity-high-availability-disaster-recovery-hadr-overview.md)
* [Azure Virtual Machines における SQL Server のパフォーマンスに関するベスト プラクティス](performance-guidelines-best-practices.md)
* [Azure Virtual Machines における SQL Server のアプリケーション パターンと開発計画](application-patterns-development-strategies.md)

**Linux VM**:

* [Linux VM における SQL Server の概要](../linux/sql-server-on-linux-vm-what-is-iaas-overview.md)
* [Linux 仮想マシンでの SQL Server のプロビジョニング](../linux/sql-vm-create-portal-quickstart.md)
* [よく寄せられる質問 (Linux)](../linux/frequently-asked-questions-faq.md)
* [SQL Server on Linux のドキュメント](/sql/linux/sql-server-linux-overview)