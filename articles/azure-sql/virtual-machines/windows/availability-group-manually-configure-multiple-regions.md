---
title: 複数のリージョンにまたがる SQL Server Always On 可用性グループを構成する
description: この記事では、別のリージョンのレプリカを使って、Azure Virtual Machines に SQL Server Always On 可用性グループを構成する方法について説明します。
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.assetid: 388c464e-a16e-4c9d-a0d5-bb7cf5974689
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/02/2017
ms.author: mathoma
ms.custom: seo-lt-2019
ms.openlocfilehash: 7ef3535158c99226da135ad3726266023ac0690f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102509406"
---
# <a name="configure-a-sql-server-always-on-availability-group-across-different-azure-regions"></a>複数の Azure リージョンにまたがる SQL Server Always On 可用性グループを構成する

[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

この記事では、リモートの Azure の場所にある Azure Virtual Machines に SQL Server Always On 可用性グループのレプリカを構成する方法を説明しています。 障害復旧をサポートするには、この構成を使います。

この記事は、Resource Manager モードの Azure Virtual Machines に適用されます。

次の図は、Azure Virtual Machines での可用性グループの一般的なデプロイを示したものです。

   !["Windows Server フェールオーバー クラスター" と "Always On 可用性グループ" を使用した Azure ロード バランサーと可用性セットを示す図。](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic.png)

このデプロイでは、すべての仮想マシンが 1 つの Azure リージョンに存在します。 可用性グループ レプリカは、SQL-1 および SQL-2 上の自動フェールオーバーとの同期コミットを持つことができます。 このアーキテクチャを構築するには、[可用性グループのテンプレートまたはチュートリアルに関する記事](availability-group-overview.md)をご覧ください。

このアーキテクチャは、Azure リージョンにアクセスできなくなった場合、ダウンタイムに対して脆弱です。 この脆弱性に対処するには、別の Azure リージョンにレプリカを追加します。 次の図は、新しいアーキテクチャの概要を示したものです。

   ![可用性グループ DR](./media/availability-group-manually-configure-multiple-regions/00-availability-group-basic-dr.png)

上の図では、SQL-3 という名前の新しい仮想マシンが示されています。 SQL-3 は別の Azure リージョンにあります。 SQL-3 は、Windows Server フェールオーバー クラスターに追加されています。 SQL-3 は可用性グループのレプリカをホストできます。 最後に、SQL-3 の Azure リージョンに新しい Azure Load Balancer があることに注意してください。

>[!NOTE]
> 同じリージョン内に複数の仮想マシンがある場合は、Azure 可用性セットが必要です。 リージョン内の仮想マシンが 1 つだけの場合は、可用性セットは必要ありません。 可用性セットに仮想マシンを配置できるのは作成時のみです。 可用性セットに仮想マシンが既に存在する場合は、後で追加レプリカ用の仮想マシンを追加できます。

このアーキテクチャでは、通常、リモート リージョン内のレプリカは非同期コミット可用性モードおよび手動フェールオーバー モードで構成されます。

可用性グループのレプリカが異なる Azure リージョン内の Azure Virtual Machines 上にある場合、各リージョンには次のものが必要です。

* 仮想ネットワーク ゲートウェイ
* 仮想ネットワーク ゲートウェイ接続

次の図では、データ センター間のネットワークの通信方法を示します。

   ![VPN ゲートウェイを使用して通信する異なる Azure リージョンの 2 つの仮想ネットワークを示す図。](./media/availability-group-manually-configure-multiple-regions/01-vpngateway-example.png)

>[!IMPORTANT]
>このアーキテクチャでは、Azure リージョン間でレプリケートされるデータに対して送信データ料金がかかります。 「[帯域幅の料金詳細](https://azure.microsoft.com/pricing/details/bandwidth/)」をご覧ください。  

## <a name="create-remote-replica"></a>リモート レプリカを作成する

リモート データ センターにレプリカを作成するには、次の手順のようにします。

1. [新しいリージョンに仮想ネットワークを作成します](../../../virtual-network/manage-virtual-network.md#create-a-virtual-network)。

1. [Azure Portal を使って VNet 間接続を構成します](../../../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md)。

   >[!NOTE]
   >場合によっては、PowerShell を使って VNet 間接続を作成する必要があります。 たとえば、異なる Azure アカウントを使っている場合、ポータルで接続を構成することはできません。 その場合は、[PowerShell を使って VNet 間接続を構成します](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)。

1. [新しいリージョンにドメイン コントローラーを作成します](/windows-server/identity/ad-ds/introduction-to-active-directory-domain-services-ad-ds-virtualization-level-100)。

   このドメイン コントローラーは、プライマリ サイトのドメイン コントローラーが利用できない場合に認証を提供します。

1. [新しいリージョンに SQL Server 仮想マシンを作成します](create-sql-vm-portal.md)。

1. [新しいリージョンのネットワークに Azure Load Balancer を作成します](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)。

   このロード バランサーでは次のことが必要です。

   - 新しい仮想マシンと同じネットワーク内およびサブネット内であること。
   - 可用性グループ リスナー用の静的 IP アドレスがあること。
   - ロード バランサーと同じリージョン内の仮想マシンのみで構成されるバックエンド プールを含むこと。
   - IP アドレスに固有の TCP ポート プローブを使用していること。
   - 同じリージョン内の SQL Server に固有の負荷分散ルールがあること。  
   - バックエンド プール内の仮想マシンが単一の可用性セットまたは仮想マシン スケール セットの一部でない場合は、Standard ロード バランサーであること。 詳細については、「[Azure Load Balancer Standard の概要](../../../load-balancer/load-balancer-overview.md)」をご覧ください。
   - 2 つの異なるリージョンにある 2 つの仮想ネットワークがグローバル VNet ピアリングを介してピアリングされている場合は、Standard Load Balancer であること。 詳細については、「[Azure Virtual Network についてよく寄せられる質問 (FAQ)](../../../virtual-network/virtual-networks-faq.md#what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers)」を参照してください。

1. [新しい SQL Server にフェールオーバー クラスタリング機能を追加します](availability-group-manually-configure-prerequisites-tutorial.md#add-failover-clustering-features-to-both-sql-server-vms)。

1. [新しい SQL Server をドメインに参加させます](availability-group-manually-configure-prerequisites-tutorial.md#joinDomain)。

1. [ドメイン アカウントを使うように新しい SQL Server サービス アカウントを設定します](availability-group-manually-configure-prerequisites-tutorial.md#setServiceAccount)。

1. [Windows Server フェールオーバー クラスターに新しい SQL Server を追加します](availability-group-manually-configure-tutorial.md#addNode)。

1. クラスターに IP アドレス リソースを追加します。

   IP アドレス リソースはフェールオーバー クラスター マネージャーで作成できます。 クラスターの名前を選択し、 **[クラスター コア リソース]** の下でクラスター名を右クリックして、 **[プロパティ]** を選択します。 

   ![クラスター名、[サーバー名]、[プロパティ] が選択された [フェールオーバー クラスター マネージャー] を示すスクリーンショット。](./media/availability-group-manually-configure-multiple-regions/cluster-name-properties.png)

   **[プロパティ]** ダイアログ ボックスで、 **[IP アドレス]** の下にある **[追加]** を選択し、リモート ネットワーク領域からクラスター名の IP アドレスを追加します。 **[IP アドレス]** ダイアログ ボックスで **[OK]** を選択し、 **[クラスターのプロパティ]** ダイアログ ボックスでもう一度 **[OK]** を選択して、新しい IP アドレスを保存します。 

   ![クラスター IP を追加する](./media/availability-group-manually-configure-multiple-regions/add-cluster-ip-address.png)


1. コア クラスター名の依存関係として IP アドレスを追加します。

   クラスターのプロパティをもう一度開き、 **[依存関係]** タブを選択します。2 つの IP アドレスの OR 依存関係を構成します。 

   ![クラスターのプロパティ](./media/availability-group-manually-configure-multiple-regions/cluster-ip-dependencies.png)

1. クラスター内の可用性グループ ロールに IP アドレス リソースを追加します。 

   フェールオーバー クラスター マネージャーで可用性グループ ロールを右クリックし、 **[リソースの追加]** 、 **[その他のリソース]** を選択し、 **[IP アドレス]** を選択します。

   ![IP アドレスを作成する](./media/availability-group-manually-configure-multiple-regions/20-add-ip-resource.png)

   この IP アドレスを次のように構成します。

   - リモート データ センターからのネットワークを使います。
   - 新しい Azure Load Balancer からの IP アドレスを割り当てます。 

1. リスナー クライアント アクセス ポイント (ネットワーク名) クラスターに対する依存関係として、IP アドレス リソースを追加します。

   次のスクリーンショットは、適切に構成された IP アドレス クラスター リソースを示したものです。

   ![可用性グループ](./media/availability-group-manually-configure-multiple-regions/50-configure-dependency-multiple-ip.png)

   >[!IMPORTANT]
   >クラスター リソース グループには、両方の IP アドレスが含まれます。 両方の IP アドレスが、リスナー クライアント アクセス ポイントの依存関係です。 クラスターの依存関係の構成では、**OR** 演算子を使います。

1. [PowerShell でクラスターのパラメーターを設定する](availability-group-manually-configure-tutorial.md#setparam)。

   新しいリージョンのロード バランサーに構成したクラスター ネットワーク名、IP アドレス、およびプローブ ポートを指定して、PowerShell スクリプトを実行します。

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # The cluster name for the network in the new region (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name).
   $IPResourceName = "<IPResourceName>" # The cluster name for the new IP Address resource.
   $ILBIP = "<n.n.n.n>" # The IP Address of the Internal Load Balancer (ILB) in the new region. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn> # The probe port you set on the ILB.

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

1. SQL Server 構成マネージャーで、新しい SQL Server の [Always On 可用性グループを有効](/sql/database-engine/availability-groups/windows/enable-and-disable-always-on-availability-groups-sql-server)にします。

1. [新しい SQL Server でファイアウォール ポートを開きます](availability-group-manually-configure-prerequisites-tutorial.md#endpoint-firewall)。

   開く必要があるポート番号は、環境に依存します。 ミラーリング エンドポイントと Azure Load Balancer 正常性プローブのポートを開きます。


1. [新しい SQL Server の可用性グループにレプリカを追加します](/sql/database-engine/availability-groups/windows/use-the-add-replica-to-availability-group-wizard-sql-server-management-studio)。

   リモート Azure リージョンのレプリカについては、手動フェールオーバーでの非同期レプリケーション用に設定します。  

## <a name="set-connection-for-multiple-subnets"></a>複数のサブネットに対する接続を設定する

リモート データ センターのレプリカは、可用性グループの一部ですが、異なるサブネット内にあります。 このレプリカがプライマリ レプリカになった場合、アプリケーション接続のタイムアウトが発生する可能性があります。 この動作は、マルチサブネット デプロイでのオンプレミスの可用性グループと同じです。 クライアント アプリケーションからの接続を許可するには、クライアント接続を更新するか、またはクラスター ネットワーク名リソースに名前解決キャッシュを構成します。

可能であれば、`MultiSubnetFailover=Yes` を設定するようにクライアント接続文字列を更新します。 「[MultiSubnetFailover を使用した接続](/sql/relational-databases/native-client/features/sql-server-native-client-support-for-high-availability-disaster-recovery#Anchor_0)」をご覧ください。

接続文字列を変更できない場合は、名前解決キャッシュを構成できます。 「[Time-out error and you cannot connect to a SQL Server 2012 AlwaysOn availability group listener in a multi-subnet environment](https://support.microsoft.com/help/2792139/time-out-error-and-you-cannot-connect-to-a-sql-server-2012-alwayson-av)」 (タイムアウト エラーおよび複数サブネット環境で SQL Server 2012 AlwaysOn 可用性グループ リスナーに接続できない) を参照してください。

## <a name="fail-over-to-remote-region"></a>リモート リージョンにフェールオーバーする

リモート リージョンへのリスナー接続をテストするには、リモート リージョンにレプリカをフェールオーバーしてみます。 レプリカは非同期ですが、フェールオーバーはデータ損失の可能性に対して脆弱です。 データを失わずにフェールオーバーするには、可用性モードを同期に変更し、フェールオーバー モードを自動に設定します。 次の手順に従います。

1. **オブジェクト エクスプローラー** で、プライマリ レプリカをホストする SQL Server のインスタンスに接続します。
1. **[AlwaysOn 可用性グループ]** の **[可用性グループ]** で、可用性グループを右クリックして **[プロパティ]** を選択します。
1. **[全般]** ページの **[可用性レプリカ]** で、DR サイトのセカンダリ レプリカを、 **[同期コミット]** 可用性モードと **[自動]** フェールオーバー モードを使うように設定します。
1. 高可用性のためにプライマリ レプリカと同じサイトにセカンダリ レプリカがある場合は、このレプリカを **[非同期コミット]** および **[手動]** に設定します。
1. [OK] を選択します。
1. **オブジェクト エクスプローラー** で、可用性グループを右クリックし、 **[ダッシュボードの表示]** を選択します。
1. ダッシュボードで、DR サイトのレプリカが同期されていることを確認します。
1. **オブジェクト エクスプローラー** で、可用性グループを右クリックし、 **[フェールオーバー]** を選択します。SQL Server Management Studio で、SQL Server をフェールオーバーするためのウィザードが開きます。  
1. **[次へ]** を選択し、DR サイトの SQL Server インスタンスを選択します。 もう一度 **[次へ]** を選択します
1. DR サイトの SQL Server インスタンスに接続し、 **[次へ]** を選択します。
1. **[概要]** ページで設定を確認し、 **[完了]** を選択します。

接続をテストした後は、プライマリ レプリカをプライマリ データ センターに戻し、可用性モードを通常の動作設定に戻します。 次の表では、このドキュメントで説明されているアーキテクチャの通常の動作設定を示します。

| 場所 | サーバー インスタンス | Role | 可用性モード | フェールオーバー モード
| ----- | ----- | ----- | ----- | -----
| プライマリ データ センター | SQL-1 | プライマリ | 同期 | 自動
| プライマリ データ センター | SQL-2 | セカンダリ | 同期 | 自動
| セカンダリまたはリモート データ センター | SQL-3 | セカンダリ | 非同期 | マニュアル


### <a name="more-information-about-planned-and-forced-manual-failover"></a>計画的および強制的な手動フェールオーバーについての詳細

詳細については、次のトピックを参照してください。

- [可用性グループの計画的な手動フェールオーバーの実行 (SQL Server)](/sql/database-engine/availability-groups/windows/perform-a-planned-manual-failover-of-an-availability-group-sql-server)
- [可用性グループの強制手動フェールオーバーの実行 (SQLServer)](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server)

## <a name="next-steps"></a>次のステップ

* [Always On 可用性グループ](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server)
* [Azure Virtual Machines](../../../virtual-machines/index.yml)
* [Azure Load Balancer](availability-group-manually-configure-tutorial.md#configure-internal-load-balancer)
* [Azure の可用性セット](../../../virtual-machines/availability.md)