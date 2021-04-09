---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: fd635d4c0563c35979f8d85c33dfbde35f05f9e6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91401059"
---
可用性グループ リスナーとは、SQL Server 可用性グループがリッスンする IP アドレスとネットワーク名のことです。 可用性グループ リスナーを作成するには、次の手順を実行します。

1. <a name="getnet"></a>クラスター ネットワーク リソースの名前を取得します。

    a. プライマリ レプリカのホストとなっている Azure 仮想マシンに RDP で接続します。 

    b. フェールオーバー クラスター マネージャーを開きます。

    c. **Networks** ノードを選択し、クラスター ネットワーク名をメモします。 この名前は、PowerShell スクリプトの `$ClusterNetworkName` 変数に使用します。 次の図では、クラスター ネットワーク名は **Cluster Network 1** です。

   ![クラスター ネットワーク名](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

1. <a name="addcap"></a>クライアント アクセス ポイントを追加します。  
    クライアント アクセス ポイントは、アプリケーションが可用性グループ内のデータベースに接続するために使用するネットワーク名です。 クライアント アクセス ポイントは、フェールオーバー クラスター マネージャーで作成します。

    a. クラスター名を展開して、 **[ロール]** をクリックします。

    b. **[ロール]** ウィンドウで、可用性グループ名を右クリックし、 **[リソースの追加]**  >  **[クライアント アクセス ポイント]** の順にクリックします。

   ![[クライアント アクセス ポイント] メニュー オプションのスクリーンショット。](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. **[名前]** ボックスで、この新しいリスナーの名前を指定します。 
   新しいリスナーの名前は、アプリケーションが SQL Server 可用性グループ内のデータベースへの接続に使用するネットワーク名です。

    d. **[次へ]** を 2 回クリックしてから **[完了]** をクリックし、リスナーの作成を完了します。 この時点では、リスナーまたはリソースをオンラインにしないでください。

1. 可用性グループ クラスター ロールをオフラインにします。 **[フェールオーバー クラスター マネージャー]** の **[ロール]** で該当するロールを右クリックし、 **[ロールの停止]** を選択します。

1. <a name="congroup"></a>可用性グループの IP リソースを構成します。

    a. **[リソース]** タブをクリックして、作成したクライアント アクセス ポイントを展開します。  
    クライアント アクセス ポイントはオフラインになっています。

   ![クライアント アクセス ポイント](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. IP リソースを右クリックし、[プロパティ] をクリックします。 この IP アドレス名はメモしておき、PowerShell スクリプトの `$IPResourceName` 変数に使用します。

    c. **[IP アドレス]** で **[静的 IP アドレス]** をクリックします。 静的 IP アドレスを、Azure Portal でロード バランサーのアドレス設定時に使用したものと同じアドレスに設定します。

   ![IP アドレスの設定場所を示すスクリーンショット。](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

1. <a name = "dependencyGroup"></a>SQL Server 可用性グループ リソースがクライアント アクセス ポイントに依存するように設定します。

    a. フェールオーバー クラスター マネージャーで、 **[ロール]** 、可用性グループの順にクリックします。

    b. **[その他のリソース]** の下の **[リソース]** タブで可用性リソース グループを右クリックし、 **[プロパティ]** をクリックします。 

    c. [依存関係] タブで、クライアント アクセス ポイント (リスナー) リソースの名前を追加します。

   ![[依存関係] タブにおける名前の追加先を示すスクリーンショット。](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. **[OK]** をクリックします。

1. <a name="listname"></a>クライアント アクセス ポイント リソースが IP アドレスに依存するように設定します。

    a. フェールオーバー クラスター マネージャーで、 **[ロール]** 、可用性グループの順にクリックします。 

    b. **[リソース]** タブで **[サーバー名]** の下にあるクライアント アクセスポイント リソースを右クリックし、 **[プロパティ]** をクリックします。 

   ![サーバーの名前の [プロパティ] メニュー オプションを示すスクリーンショット。](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. **[依存関係]** タブをクリックします。IP アドレスが依存関係の要素であることを確認します。 そうでない場合は、IP アドレスへの依存関係を設定します。 複数のリソースが一覧表示される場合は、IP アドレスに OR (AND ではなく) 依存関係があることを確認します。 **[OK]** をクリックします。 

   ![IP リソース](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    >[!TIP]
    >依存関係が正しく構成されていることを確認できます。 フェールオーバー クラスター マネージャーで [ロール] に移動します。次に、可用性グループを右クリックし、 **[他の操作]** をクリックしてから、 **[依存関係レポートの表示]** をクリックします。 依存関係が正しく構成されると、可用性グループはネットワーク名に依存し、ネットワーク名は IP アドレスに依存します。 


1. <a name="setparam"></a>PowerShell でクラスターのパラメーターを設定します。

   a. いずれかの SQL Server インスタンスに次の PowerShell スクリプトをコピーします。 環境に合わせて変数を更新してください。

   - `$ListenerILBIP` は、Azure Load Balancer に対して作成した、可用性グループ リスナーの IP アドレスです。
    
   - `$ListenerProbePort` は、Azure Load Balancer に対して構成した、可用性グループ リスナーのポートです。

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ListenerILBIP = "<n.n.n.n>" # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ListenerProbePort = <nnnnn>
  
   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ListenerILBIP";"ProbePort"=$ListenerProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. いずれかのクラスター ノード上で PowerShell スクリプトを実行して、クラスター パラメーターを設定します。  

   > [!NOTE]
   > SQL Server インスタンスが別個のリージョンに存在する場合は、PowerShell スクリプトを 2 回実行する必要があります。 1 回目の実行では、1 番目のリージョンの `$ListenerILBIP` と `$ListenerProbePort` を使用します。 2 回目の実行では、2 番目のリージョンの `$ListenerILBIP` と `$ListenerProbePort` を使用します。 クラスター ネットワーク名とクラスター IP リソース名も、リージョンごとに異なります。

1. 可用性グループ クラスター ロールをオンラインにします。 **[フェールオーバー クラスター マネージャー]** の **[ロール]** で該当するロールを右クリックし、 **[ロールの起動]** を選択します。

必要に応じて上記の手順を繰り返し、WSFC クラスター IP アドレスのクラスター パラメーターを設定します。

1. WSFC クラスター IP アドレスの IP アドレス名を取得します。 **[フェールオーバー クラスター マネージャー]** の **[クラスター コア リソース]** で、 **[サーバー名]** を見つけます。

1. **[IP アドレス]** を右クリックし、 **[プロパティ]** を選択します。

1. IP アドレスの **[名前]** をコピーします。 たとえば `Cluster IP Address` です。 

1. <a name="setwsfcparam"></a>PowerShell でクラスターのパラメーターを設定します。
  
   a. いずれかの SQL Server インスタンスに次の PowerShell スクリプトをコピーします。 環境に合わせて変数を更新してください。

   - `$ClusterCoreIP` は、Azure Load Balancer に対して作成した、WSFC コア クラスター リソースの IP アドレスです。 可用性グループ リスナーの IP アドレスとは異なります。

   - `$ClusterProbePort` は、Azure Load Balancer に対して構成した、WSFC 正常性プローブのポートです。 可用性グループ リスナーのプローブとは異なります。

   ```powershell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<ClusterIPResourceName>" # the IP Address resource name
   $ClusterCoreIP = "<n.n.n.n>" # the IP Address of the Cluster IP resource. This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ClusterProbePort = <nnnnn> # The probe port from the WSFCEndPointprobe in the Azure portal. This port must be different from the probe port for the availability group listener probe port.
  
   Import-Module FailoverClusters
  
   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ClusterCoreIP";"ProbePort"=$ClusterProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

   b. いずれかのクラスター ノード上で PowerShell スクリプトを実行して、クラスター パラメーターを設定します。  

>[!WARNING]
>可用性グループ リスナーの正常性プローブのポートは、クラスター コア IP アドレスの正常性プローブ ポートとは違っている必要があります。 これらの例では、リスナー ポートが 59999 で、クラスター コア IP アドレスの正常性プローブ ポートが 58888 です。 どちらのポートも、受信を許可するようにファイアウォール規則が設定されている必要があります。
