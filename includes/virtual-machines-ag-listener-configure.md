可用性グループ リスナーとは、SQL Server 可用性グループがリッスンする IP アドレスとネットワーク名のことです。 可用性グループ リスナーを作成するには、次の手順を実行します。

1. [クラスター ネットワーク リソースの名前を取得する](#getnet)。

1. [クライアント アクセス ポイントを追加する](#addcap)。

1. [可用性グループの IP リソースを構成する](#congroup)。

1. [SQL Server 可用性グループ リソースがクライアント アクセス ポイントに依存するように設定する。](#dependencyGroup)

1. [クライアント アクセス ポイント リソースが IP アドレスに依存するように設定する](#listname)。

1. [PowerShell でクラスターのパラメーターを設定する](#setparam)。

以下の各セクションで、個々の手順の詳細を示します。 

#### <a name="a-namegetnetaget-the-name-of-the-cluster-network-resource"></a><a name="getnet"></a>クラスター ネットワーク リソースの名前を取得する

1. プライマリ レプリカのホストとなっている Azure 仮想マシンに RDP で接続します。 

1. フェールオーバー クラスター マネージャーを開きます。

1. **Networks** ノードを選択し、クラスター ネットワーク名をメモします。 この名前は、PowerShell スクリプトの `$ClusterNetworkName` 変数に使用します。

   次の図では、クラスター ネットワーク名は **Cluster Network 1** です。

   ![クラスター ネットワーク名](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

#### <a name="a-nameaddcapaadd-the-client-access-point"></a><a name="addcap"></a>クライアント アクセス ポイントを追加する

クライアント アクセス ポイントは、アプリケーションが可用性グループ内のデータベースに接続するために使用するネットワーク名です。 クライアント アクセス ポイントは、フェールオーバー クラスター マネージャーで作成します。 

1. クラスター名を展開して、 **[ロール]**をクリックします。

1. **[ロール]** ウィンドウで、可用性グループ名を右クリックし、**[リソースの追加]** > **[クライアント アクセス ポイント]** の順に選択します。

   ![クライアント アクセス ポイント](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

1. **[名前]** ボックスで、この新しいリスナーの名前を指定します。 

   この新しいリスナーの名前が、アプリケーションが SQL Server 可用性グループ内のデータベースへの接続に使用するネットワーク名になります。
   
   **[次へ]** を 2 回クリックしてから **[完了]** をクリックし、リスナーの作成を完了します。 この時点では、リスナーまたはリソースをオンラインにしないでください。
   
#### <a name="a-namecongroupaconfigure-the-ip-resource-for-the-availability-group"></a><a name="congroup"></a>可用性グループの IP リソースを構成する

1. **[リソース]** タブをクリックして、作成したクライアント アクセス ポイントを展開します。 クライアント アクセス ポイントはオフラインになっています。

   ![クライアント アクセス ポイント](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

1. IP リソースを右クリックし、[プロパティ] をクリックします。 IP アドレスの名前をメモします。 この名前は、PowerShell スクリプトの `$IPResourceName` 変数に使用します。

1. **[IP アドレス]** で **[静的 IP アドレス]** をクリックします。 静的 IP アドレスを、Azure Portal でロード バランサーのアドレス設定時に使用したものと同じアドレスに設定します。

   ![IP リソース](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

<!-----------------------I don't see this option on server 2016
1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
------------------------->

#### <a name="a-name--dependencygroupamake-the-sql-server-availability-group-resource-dependent-on-the-client-access-point"></a><a name = "dependencyGroup"></a>SQL Server 可用性グループ リソースがクライアント アクセス ポイントに依存するよう設定する

1. フェールオーバー クラスター マネージャーで、**[ロール]**、可用性グループの順にクリックします。

1. **[リソース]** タブで **[サーバー名]** の下にある可用性リソース グループを右クリックし、**[プロパティ]** をクリックします。 

1. [依存関係] タブで、名前リソースを追加します。 このリソースがクライアント アクセス ポイントになります。 

   ![IP リソース](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

1. **[OK]**をクリックします。

#### <a name="a-namelistnameamake-the-client-access-point-resource-dependent-on-the-ip-address"></a><a name="listname"></a>クライアント アクセス ポイント リソースが IP アドレスに依存するよう設定する

1. フェールオーバー クラスター マネージャーで、**[ロール]**、可用性グループの順にクリックします。 

1. **[リソース]** タブで **[サーバー名]** の下にあるクライアント アクセスポイント リソースを右クリックし、**[プロパティ]** をクリックします。 

   ![IP リソース](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

1. **[依存関係]** タブをクリックします。 リスナー リソース名への依存関係を設定します。 複数のリソースが一覧表示される場合は、IP アドレスに OR (AND ではなく) 依存関係があることを確認します。 **[OK]**をクリックします。 

   ![IP リソース](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

1. リスナー名を右クリックし、 **[オンラインにする]**をクリックします。 

#### <a name="a-namesetparamaset-the-cluster-parameters-in-powershell"></a><a name="setparam"></a>PowerShell でクラスターのパラメーターを設定する

1. いずれかの SQL Server に次の PowerShell スクリプトをコピーします。 環境に合わせて変数を更新してください。     
   ```PowerShell
   $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
   $IPResourceName = "<IPResourceName>" # the IP Address resource name
   $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
   [int]$ProbePort = <nnnnn>

   Import-Module FailoverClusters

   Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
   ```

2. いずれかのクラスター ノード上で PowerShell スクリプトを実行して、クラスター パラメーターを設定します。  

> [!NOTE]
> SQL Server が別個のリージョンに存在する場合は、PowerShell スクリプトを 2 回実行する必要があります。 1 回目の実行では、1 番目のリージョンの `$ILBIP` と `$ProbePort` を使用します。 2 回目の実行では、2 番目のリージョンの `$ILBIP` と `$ProbePort` を使用します。 クラスター ネットワーク名とクラスター IP リソース名は同じものを使用します。 




<!--HONumber=Jan17_HO2-->


