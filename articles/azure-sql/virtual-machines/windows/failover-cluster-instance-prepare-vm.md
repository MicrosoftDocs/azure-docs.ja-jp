---
title: FCI 用の仮想マシンを準備する
description: Azure Virtual Machines 上でフェールオーバー クラスター インスタンス (FCI) と SQL Server を使用するように Azure Virtual Machines を準備します。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 10f01fd5943928eda1f1e4518f30c8e3ccf56b46
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98737797"
---
# <a name="prepare-virtual-machines-for-an-fci-sql-server-on-azure-vms"></a>FCI 用に仮想マシンを準備する (Azure VM 上の SQL Server)
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、Azure 仮想マシン (VM) を SQL Server フェールオーバー クラスター インスタンス (FCI) で使用するように準備する方法について説明します。 構成設定は FCI ストレージ ソリューションによって異なるため、環境やビジネスに適した構成を選択していることを確認してください。 

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[クラスターのベスト プラクティス](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 

## <a name="prerequisites"></a>前提条件 

- Microsoft Azure サブスクリプションが必要です。 [無料](https://azure.microsoft.com/free/)で開始できます。 
- Azure 仮想マシン上の Windows ドメイン、または仮想ネットワーク ペアリングで Azure に拡張されたオンプレミスのデータセンター。
- Azure 仮想マシンと Active Directory にオブジェクトを作成するためのアクセス許可を持つアカウント。
- 次のコンポーネント用の十分な IP アドレス空間を持つ Azure 仮想ネットワークとサブネット。
   - 両方の仮想マシン
   - Windows フェールオーバー クラスターの IP アドレス
   - 各 FCI の IP アドレス
- Azure ネットワーク上で構成された、ドメイン コントローラーを指す DNS。

## <a name="choose-an-fci-storage-option"></a>FCI ストレージ オプションを選択する

仮想マシンの構成設定は、SQL Server フェールオーバー クラスター インスタンスに使用する予定のストレージ オプションによって異なります。 仮想マシンを準備する前に、[使用可能な FCI ストレージ オプション](failover-cluster-instance-overview.md#storage)を確認し、環境やビジネス ニーズに最も適したオプションを選択します。 次に、ストレージの選択に基づいて、この記事全体で適切な VM 構成オプションを選択します。 

## <a name="configure-vm-availability"></a>VM の可用性を構成する 

フェールオーバー クラスター機能を使用するには、仮想マシンを[可用性セット](../../../virtual-machines/linux/tutorial-availability-sets.md)または[可用性ゾーン](../../../availability-zones/az-overview.md#availability-zones)に配置する必要があります。 可用性セットを選択する場合は、[近接配置グループ](../../../virtual-machines/co-location.md#proximity-placement-groups)を使用して、VM を近くに配置することができます。 実際、近接配置グループは、Azure 共有ディスクを使用するための前提条件です。 

目的のクラスター構成に適した VM 可用性オプションを慎重に選択します。 

- **Azure 共有ディスク**: Premium SSD または UltraDisk を使用している場合、使用可能なオプションは異なります。
   - Premium SSD:[近接配置グループ](../../../virtual-machines/windows/proximity-placement-groups-portal.md)内に配置された Premium SSD のさまざまな異なる障害/更新ドメインにある[可用性セット](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)。
   - Ultra Disk:[可用性ゾーン](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)ですが、VM を同じ可用性ゾーンに配置する必要があり、これによりクラスターの可用性が 99.9% に低下します。 
- **Premium ファイル共有**: [可用性セット](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)または [可用性ゾーン](../../../virtual-machines/windows/create-portal-availability-zone.md#confirm-zone-for-managed-disk-and-ip-address)。
- **記憶域スペース ダイレクト**: [可用性セット](../../../virtual-machines/windows/tutorial-availability-sets.md#create-an-availability-set)。

> [!IMPORTANT]
> 仮想マシンを作成した後に可用性セットを設定または変更することはできません。

## <a name="create-the-virtual-machines"></a>仮想マシンの作成

VM の可用性の構成が済むと、仮想マシンを作成する準備が整います。 SQL Server が既にインストールされている、またはインストールされていない、Azure Marketplace のイメージを使用できます。 ただし、Azure VM に SQL Server が付属するイメージを選択する場合は、フェールオーバー クラスター インスタンスを構成する前に、仮想マシンから SQL Server をアンインストールする必要があります。 

### <a name="considerations"></a>考慮事項

Azure VM ゲスト フェールオーバー クラスターでは、サーバー (クラスター ノード) ごとに 1 つの NIC、および 1 つのサブネットを推奨しています。 Azure ネットワークは物理的な冗長性を備えているので、Azure IaaS VM ゲスト クラスターで NIC とサブネットを追加する必要はありません。 クラスター検証レポートでは、1 つのネットワークでしかノードに到達できないという警告が出ますが、Azure IaaS VM ゲスト フェールオーバー クラスターではこの警告を無視しても安全です。

両方の仮想マシンを配置します。

- 可用性セットを使用している場合は、可用性セットと同じ Azure リソース グループ内。
- ドメイン コントローラーと同じ仮想ネットワーク上、またはドメイン コントローラーへの適切な接続がある仮想ネットワーク上。
- 両方の仮想マシン、およびこのクラスター上で最終的に使用するすべての FCI 用に十分な IP アドレス空間を持つサブネット内。
- Azure 可用性セット内または可用性ゾーン内。

SQL Server がプレインストールされて[いる](sql-vm-create-portal-quickstart.md)または[いない](../../../virtual-machines/windows/quick-create-portal.md)イメージを使用して、Azure 仮想マシンを作成できます。 SQL Server イメージを選択した場合は、フェールオーバー クラスター インスタンスをインストールする前に、手動で SQL Server インスタンスをアンインストールする必要があります。 


## <a name="uninstall-sql-server"></a>SQL Server のアンインストール

FCI 作成プロセスの一環として、SQL Server をクラスター化されたインスタンスとしてフェールオーバー クラスターにインストールします。 "*SQL Server のない Azure Marketplace イメージを使用して仮想マシンをデプロイした場合は、このステップを省略できます。* " SQL Server がプレインストールされたイメージをデプロイした場合は、SQL IaaS Agent 拡張機能から SQL Server VM の登録を解除し、SQL Server をアンインストールする必要があります。 

### <a name="unregister-from-the-sql-iaas-agent-extension"></a>SQL IaaS Agent 拡張機能からの登録解除

Azure Marketplace の SQL Server VM イメージは、SQL IaaS Agent 拡張機能に自動的に登録されます。 プレインストールされている SQL Server インスタンスをアンインストールする前に、まず [SQL IaaS Agent 拡張機能から各 SQL Server VM を登録解除する](sql-agent-extension-manually-register-single-vm.md#unregister-from-extension)必要があります。 

### <a name="uninstall-sql-server"></a>SQL Server のアンインストール

拡張機能から登録を解除した後、SQL Server をアンインストールできます。 各仮想マシンで、次の手順のようにします。 

1. RDP を使用して仮想マシンに接続します。

   RDP を使用して最初に仮想マシンに接続する際、この PC をネットワーク上で検出可能にするかどうかを確認するメッセージが表示されます。 **[はい]** を選択します。

1. SQL Server ベースの仮想マシン イメージの 1 つを使用している場合は、SQL Server インスタンスを削除します。

   1. **[プログラムと機能]** で **[Microsoft SQL Server 201_ (64 ビット)]** を右クリックし、 **[アンインストールと変更]** を選択します。
   1. **[削除]** を選択します。
   1. 既定のインスタンスを選択します。
   1. **[データベース エンジン サービス]** のすべての機能を削除します。 **[共有機能]** の下にあるものは何も削除しないでください。 次のスクリーンショットのように表示されます。

      ![機能を選択する](./media/failover-cluster-instance-prepare-vm/03-remove-features.png)

   1. **[次へ]** 、 **[削除]** の順に選択します。
   1. インスタンスが正常に削除されたら、仮想マシンを再起動します。 

## <a name="open-the-firewall"></a>ファイアウォールを開く 

各仮想マシンで、SQL Server で使用されている Windows ファイアウォール TCP ポートを開きます。 既定では、このポートは 1433 です。 ただし、Azure VM のデプロイで SQL Server のポートを変更できるので、お使いの環境で使用している SQL Server ポートを開きます。 このポートは、Azure Marketplace からデプロイされた SQL Server イメージでは自動的に開かれます。 

[ロード バランサー](failover-cluster-instance-vnn-azure-load-balancer-configure.md)を使用する場合は、正常性プローブで使用されるポートも開く必要があります。 既定では、このポートは 59999 です。 ただし、ロード バランサーの作成時に指定する任意の TCP ポートを使用できます。 

次の表では、FCI の構成に応じて、開くことが必要になる場合があるポートについて詳しく説明します。 

   | 目的 | Port | Notes
   | ------ | ------ | ------
   | SQL Server | TCP 1433 | SQL Server の既定のインスタンスの通常のポートです。 ギャラリーからイメージを使用した場合、このポートが自動的に開きます。 </br> </br> **使用元**: すべての FCI 構成。 |
   | 正常性プローブ | TCP 59999 | 開いている任意の TCP ポートです。 このポートを使用するように、ロード バランサーの[正常性プローブ](failover-cluster-instance-vnn-azure-load-balancer-configure.md#configure-health-probe)とクラスターを構成します。 </br> </br> **使用元**: FCI とロード バランサー。 |
   | ファイル共有 | UDP 445 | ファイル共有サービスによって使用されるポート。 </br> </br> **使用元**: FCI と Premium ファイル共有。 |

## <a name="join-the-domain"></a>ドメインに参加する

また、仮想マシンをドメインに参加させる必要があります。 [クイックスタート テンプレート](../../../active-directory-domain-services/join-windows-vm-template.md#join-an-existing-windows-server-vm-to-a-managed-domain)を使用して、それを行うことができます。 

## <a name="review-storage-configuration"></a>ストレージの構成を確認する

Azure Marketplace から作成された仮想マシンには、アタッチされたストレージが付属しています。 Premium ファイル共有または Azure 共有ディスクを使用して FCI ストレージを構成する場合は、ローカル ストレージはフェールオーバー クラスター インスタンスに使用されないため、アタッチされたストレージを削除してコストを節約できます。 ただし、アタッチされたストレージは記憶域スペース ダイレクトの FCI ソリューションに使用できるので、その場合は削除しても役に立たない可能性があります。 お使いの FCI ストレージ ソリューションを確認して、アタッチされたストレージの削除がコストの節約に最適かどうかを判断します。 


## <a name="next-steps"></a>次のステップ

仮想マシン環境の準備ができたので、フェールオーバー クラスター インスタンスを構成できる状態になりました。 

次のいずれかのガイドを選択して、ビジネスに適した FCI 環境を構成します。 
- [Azure 共有ディスクを使用して FCI を構成する](failover-cluster-instance-azure-shared-disks-manually-configure.md)
- [Premium ファイル共有を使用して FCI を構成する](failover-cluster-instance-premium-file-share-manually-configure.md)
- [記憶域スペース ダイレクトを使用して FCI を構成する](failover-cluster-instance-storage-spaces-direct-manually-configure.md)

詳細については、[Azure VM 上の SQL Server を使用した FCI](failover-cluster-instance-overview.md) および[サポートされる HADR 構成](hadr-cluster-best-practices.md)の概要に関する記事をご覧ください。 

追加情報については、次を参照してください。 
- [Windows クラスター テクノロジ](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server フェールオーバー クラスター インスタンス](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)