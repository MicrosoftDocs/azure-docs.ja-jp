---
title: "SQL Server 可用性グループ リスナーの作成 - Azure Virtual Machines | Microsoft Docs"
description: "Azure Virtual Machines に SQL Server の AlwaysOn 可用性グループのリスナーを作成する手順を説明します。"
services: virtual-machines
documentationcenter: na
author: MikeRayMSFT
manager: jhubbard
editor: monicar
ms.assetid: d1f291e9-9af2-41ba-9d29-9541e3adcfcf
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/28/2016
ms.author: mikeray
translationtype: Human Translation
ms.sourcegitcommit: 407b189af12116d633ed505facf4bcfde9be5822
ms.openlocfilehash: 6a37e9e786a4e399c49cb77758a23793790888c9


---
# <a name="configure-an-internal-load-balancer-for-an-always-on-availability-group-in-azure"></a>Azure の AlwaysOn 可用性グループに使用する内部ロード バランサーの構成
このトピックでは、Resource Manager モデルで動作する Azure 仮想マシンに、SQL Server AlwaysOn 可用性グループの内部ロード バランサーを作成する方法について説明します。 SQL Server インスタンスが Azure 仮想マシン上で実行されている場合、可用性グループにロード バランサーが必要となります。 ロード バランサーには、可用性グループ リスナーの IP アドレスが格納されます。 可用性グループが複数のリージョンにまたがっている場合は、各リージョンにロード バランサーが必要です。

この作業を行うには、SQL Server 可用性グループが Resource Manager モデルの Azure 仮想マシンにデプロイされている必要があります。 両方の SQL Server 仮想マシンが同じ可用性セットに属している必要があります。 [Microsoft のテンプレート](virtual-machines-windows-portal-sql-alwayson-availability-groups.md) を使用すると、Azure Resource Manager で自動的に可用性グループを作成することができます。 内部ロード バランサーは、このテンプレートによって自動的に作成されます。 

必要に応じて [可用性グループを手動で構成](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)することもできます。

このトピックは、可用性グループの構成が既に済んでいることを前提としています。  

関連トピック:

* [Azure VM での AlwaysOn 可用性グループの構成 (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md)   
* [Azure リソース マネージャーと PowerShell を使用した VNet 間の接続の構成](../../../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)

このドキュメントでは、Azure Portal でロード バランサーを作成し、必要な構成を行います。 その作業が済んだら、可用性グループ リスナーのロード バランサーの IP アドレスを使用するようにクラスターを構成します。

## <a name="create-and-configure-the-load-balancer-in-the-azure-portal"></a>Azure ポータルでのロード バランサーの作成と構成
この作業の一環として、Azure Portal で次の手順を実行します。

1. ロード バランサーを作成して IP アドレスを構成する
2. バックエンド プールを構成する
3. プローブを作成する 
4. 負荷分散規則を設定する

> [!NOTE]
> SQL Server が異なるリソース グループやリージョンに存在する場合、このすべての手順を&2; 回 (リソース グループごとに&1; 回) 行う必要があります。
> 
> 

### <a name="1-create-the-load-balancer-and-configure-the-ip-address"></a>1.ロード バランサーを作成して IP アドレスを構成する
最初に行う作業は、ロード バランサーの作成です。 Azure ポータルで、SQL Server の仮想マシンを含んだリソース グループを開きます。 リソース グループで **[追加]**をクリックします。

1. " **ロード バランサー**" を検索します。 検索結果から **[ロード バランサー]** (**Microsoft** が公開) を選択します。
1. **[ロード バランサー]** ブレードで **[作成]** をクリックします。
1. **[Create load balancer (ロード バランサーの作成)]** で、次のようにロード バランサーを構成します。

   | Setting | 値 |
   | --- | --- |
   | **名前** |ロード バランサーを表すテキスト名  (例: **sqlLB**)。 |
   | **型** |**内部** |
   | **Virtual Network** |SQL Server が存在する仮想ネットワークを選択します。 |
   | **サブネット** |SQL Server が存在するサブネットを選択します。 |
   | **IP アドレスの割り当て** |**静的** |
   | **プライベート IP アドレス** |サブネット内の利用可能な IP アドレスを指定します。 この IP アドレスは、クラスターにリスナーを作成するときに使用します。 この記事で後ほど示す PowerShell スクリプトの `$ILBIP` 変数には、このアドレスを使用してください。 |
   | **サブスクリプション** |複数のサブスクリプションを所有している場合、このフィールドが表示されます。 このリソースに関連付けられているサブスクリプションを選択します。 通常は、可用性グループのすべてのリソースについて同じサブスクリプションを選択してください。 |
   | **[リソース グループ]** |SQL Server が存在するリソース グループを選択します。 |
   | **場所** |SQL Server が存在する Azure の場所を選択します。 |

1. **[作成]**をクリックします。 

ロード バランサーが Azure によって作成されます。 このロード バランサーは、特定のネットワーク、サブネット、リソース グループ、場所に従属します。 Azure の処理が完了したら、ロード バランサーの設定を Azure で確認してください。 

### <a name="2-configure-the-backend-pool"></a>2.バックエンド プールを構成する
次の手順は、バックエンド アドレス プールの作成です。 Azure では、バックエンド アドレス プールを " *バックエンド プール*" と呼んでいます。 この例では、可用性グループに含まれる&2; つの SQL Server のアドレスがバックエンド プールとなります。 

1. リソース グループで、作成したロード バランサーをクリックします。 
1. **[設定]** の **[バックエンド プール]** をクリックします。
1. **[バックエンド プール]** の **[追加]** をクリックしてバックエンド アドレス プールを作成します。 
1. **[バックエンド プールの追加]** under **名前**に、バックエンド プールの名前を入力します。
1. **[仮想マシン]** の **[+ 仮想マシンの追加]** をクリックします。 
1. **[仮想マシンの選択]** の **[可用性セットの選択]** をクリックし、SQL Server 仮想マシンの追加先となる可用性セットを指定します。
1. 可用性セットを選択したら、 **[仮想マシンの選択]**をクリックします。 可用性グループ内の SQL Server インスタンスのホストとなる&2; つの仮想マシンをクリックします。 **[選択]**をクリックします。 
1. **[OK]** をクリックして、**[仮想マシンの選択]** のブレードと、**[バックエンド プールの追加]** を閉じます。 

バックエンド アドレス プールの設定が Azure で更新されます。 これで&2; つの SQL Server から成るプールが可用性セットに作成されました。

### <a name="3-create-a-probe"></a>3.プローブを作成する
次の手順はプローブの作成です。 このプローブで定義された方法で、現在どの SQL Server が可用性グループ リスナーを所有しているかが Azure によって確認されます。 Azure は、プローブの作成時に定義されたポートで、IP アドレスに基づいてサービスをプローブします。

1. ロード バランサーの **[設定]** ブレードで **[Health probes (正常性のプローブ)]** をクリックします。 
1. **[Health probes (正常性のプローブ)]** ブレードで **[追加]** をクリックします。
1. **[プローブの追加]** ブレードでプローブを構成します。 次の値を使用してプローブを構成します。

   | 設定 | 値 |
   | --- | --- |
   | **名前** |プローブを表すテキスト名  (例: **SQLAlwaysOnEndPointProbe**)。 |
   | **プロトコル** |**TCP** |
   | **ポート** |空いている任意のポートを使用できます  (例: *59999*)。 |
   | **間隔** |*5* |
   | **異常のしきい値** |*2* |

1.  **[OK]**をクリックします。 

> [!NOTE]
> 指定したポートは、両方の SQL Server のファイアウォールで必ず開放してください。 使用する TCP ポートに対する入力方向の規則が両方のサーバーに必要となります。 詳細については、「[ファイアウォール規則を追加または編集する](http://technet.microsoft.com/library/cc753558.aspx)」をご覧ください。 
> 
> 

指定したプローブが Azure によって作成されます。 Azure はこのプローブを使用して、どの SQL Server が可用性グループのリスナーを所有しているかを判定します。

### <a name="4-set-the-load-balancing-rules"></a>4.負荷分散規則を設定する
負荷分散規則を設定します。 SQL Server へのトラフィックをロード バランサーでどのようにルーティングするかは、負荷分散規則で設定します。 2 つの SQL Server のうち可用性グループ リスナー リソースを所有できるのは一度に&1; つだけであるため、このロード バランサーでは Direct Server Return を有効にします。

1. ロード バランサーの **[設定]** ブレードで **[負荷分散規則]** をクリックします。 
1. **[負荷分散規則]** ブレードで、**[追加]** をクリックします。
1. **[負荷分散規則の追加]** ブレードを使用して負荷分散規則を構成します。 次の設定を使用します。 

   | 設定 | 値 |
   | --- | --- |
   | **名前** |負荷分散規則を表すテキスト名  (例: **SQLAlwaysOnEndPointListener**)。 |
   | **プロトコル** |**TCP** |
   | **ポート** |*1433* |
   | **バックエンド ポート** |*1433*この規則には **[フローティング IP (ダイレクト サーバー リターン)]** が使用されるため、この値は無視されます。 |
   | **プローブ** |このロード バランサーに対して作成したプローブの名前を使用します。 |
   | **セッション永続化** |**なし** |
   | **アイドル タイムアウト (分)** |*4* |
   | **フローティング IP (ダイレクト サーバー リターン)** |**有効** |

   > [!NOTE]
   > ブレード上で一部の設定が隠れて見えないときは下へスクロールしてください。
   > 

1. **[OK]**をクリックします。 
1. 負荷分散規則が Azure によって構成されます。 以上、可用性グループのリスナーのホストとなっている SQL Server にトラフィックをルーティングするようにロード バランサーを構成しました。 

この時点で、リソース グループのロード バランサーが両方の SQL Server マシンに接続されています。 またロード バランサーには、可用性グループへの要求にいずれかのマシンが応答できるよう、SQL Server AlwaysOn 可用性グループ リスナーの IP アドレスが格納されます。

> [!NOTE]
> SQL Server が&2; つの異なるリージョンに存在する場合、もう一方のリージョンについても以上の手順を繰り返す必要があります。 ロード バランサーはリージョンごとに必要です。 
> 
> 

## <a name="configure-the-cluster-to-use-the-load-balancer-ip-address"></a>ロード バランサーの IP アドレスを使用するようにクラスターを構成する
次に、クラスター上のリスナーを構成し、リスナーをオンラインに切り替えます。 次の手順を実行します。 

1. 可用性グループ リスナーをフェールオーバー クラスターで作成する 
2. リスナーをオンラインにする

### <a name="5-create-the-availability-group-listener-on-the-failover-cluster"></a>5.可用性グループ リスナーをフェールオーバー クラスターで作成する
この手順では、フェールオーバー クラスター マネージャーおよび SQL Server Management Studio (SSMS) で可用性グループ リスナーを手動で作成します。

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

<!---------------------------
* Use RDP to connect to the Azure virtual machine that hosts the primary replica. 
* Open Failover Cluster Manager.
* Select the **Networks** node, and note the cluster network name. This name will be used in the `$ClusterNetworkName` variable in the PowerShell script.
* Expand the cluster name, and then click **Roles**.
* In the **Roles** pane, right-click the availability group name and then select **Add Resource** > **Client Access Point**.
* In the **Name** box, create a name for this new listener, then click **Next** twice, and then click **Finish**. Do not bring the listener or resource online at this point.
  
  > [!NOTE]
  > The name for the new listener is the network name that applications will use to connect to databases in the SQL Server availability group.
  > 
  > 
* Click the **Resources** tab, then expand the Client Access Point you just created. Right-click the IP resource and click properties. Note the name of the IP address. You will use this name in the `$IPResourceName` variable in the PowerShell script.
* Under **IP Address** click **Static IP Address** and set the static IP address to the same address that you used when you set the load balancer IP address on the Azure portal. Enable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
* On the cluster node that currently hosts the primary replica, open an elevated PowerShell ISE and paste the following commands into a new script.
  
        $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
        $IPResourceName = "<IPResourceName>" # the IP Address resource name
        $ILBIP = “<X.X.X.X>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
  
        Import-Module FailoverClusters
  
        Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"="59999";"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
* Update the variables and run the PowerShell script to configure the IP address and port for the new listener.
  
  > [!NOTE]
  > If your SQL Servers are in separate regions, you need to run the PowerShell script twice. The first time use the cluster network name, cluster IP resource name, and load balancer IP address from the first resource group. The second time use the cluster network name, cluster IP resource name, and load balancer IP address from the second resource group.
  > 
  > 

Now the cluster has an availability group listener resource.

### 2. Bring the listener online
With the availability group listener resource configured, you can bring the listener online so that applications can connect to databases in the availability group with the listener.

* Navigate back to Failover Cluster Manager. Expand **Roles** and then highlight your Availability Group. On the **Resources** tab, right-click the listener name and click **Properties**.
* Click the **Dependencies** tab. If there are multiple resources listed, verify that the IP addresses have OR, not AND, dependencies. Click **OK**.
* Right-click the listener name and click **Bring Online**.
* Once the listener is online, from the **Resources** tab, right-click the availability group and click **Properties**.
* Create a dependency on the listener name resource (not the IP address resources name). Click **OK**.
* Launch SQL Server Management Studio and connect to the primary replica.
* Navigate to **AlwaysOn High Availability** | **Availability Groups** | **Availability Group Listeners**. 
* You should now see the listener name that you created in Failover Cluster Manager. Right-click the listener name and click **Properties**.
* In the **Port** box, specify the port number for the availability group listener by using the $EndpointPort you used earlier (1433 was the default), then click **OK**.

------------------------------->

### <a name="verify-the-configuration-of-the-listener"></a>リスナーの構成を確認します。

クラスター リソースと依存関係が正しく構成されていれば、SQL Server Management Studio にリスナーが表示されます。 次の手順を実行して、リスナーのポートを設定します。

1. SQL Server Management Studio を起動し、プライマリ レプリカに接続します。
2. **[AlwaysOn 高可用性]** | 、**[可用性グループ]** | 、**[可用性グループ リスナー]** の順に移動します。 
1. フェールオーバー クラスター マネージャーで作成したリスナー名が表示されます。 リスナー名を右クリックし、 **[プロパティ]**をクリックします。
1. **[ポート]** ボックスで、以前に使用した $EndpointPort (既定値は&1433;) を使用し、可用性グループ リスナーのポート番号を指定して、**[OK]** をクリックします。

これで、SQL Server AlwaysOn 可用性グループが Resource Manager モデルの Azure 仮想マシンにデプロイされました。 

## <a name="test-the-connection-to-the-listener"></a>リスナーへの接続をテストする
接続をテストするには、次の手順に従います。

1. 同じ仮想ネットワークに存在する、レプリカを所有していない SQL Server に RDP で接続します。 クラスター内の別の SQL Server がそれに該当します。
2. **sqlcmd** ユーティリティを使用して接続をテストします。 たとえば、次のスクリプトはリスナー経由で Windows 認証を使用し、プライマリ レプリカとの **sqlcmd** 接続を確立しています。
   
        sqlcmd -S <listenerName> -E

SQLCMD 接続では、プライマリ レプリカをホストしている SQL Server インスタンスに対して自動的に接続されます。 

## <a name="guidelines-and-limitations"></a>ガイドラインと制限
内部ロード バランサーを使用した Azure の可用性グループ リスナーに関する次のガイドラインを確認してください。

* サポートされる内部可用性グループ リスナーは、クラウド サービスごとに&1; つだけです。リスナーはロード バランサーに対して構成されており、内部ロード バランサーは&1; つしか存在しないためです。 ただし、複数の外部リスナーを作成することができます。 
* 内部ロード バランサーでは、同じ仮想ネットワーク内からしかリスナーにアクセスできません。


<!--HONumber=Jan17_HO2-->


