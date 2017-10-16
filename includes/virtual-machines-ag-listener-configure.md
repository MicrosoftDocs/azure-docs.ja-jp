可用性グループ リスナーとは、SQL Server 可用性グループがリッスンする IP アドレスとネットワーク名のことです。 可用性グループ リスナーを作成するには、次の手順を実行します。

1. <a name="getnet"></a>クラスター ネットワーク リソースの名前を取得します。

    a. プライマリ レプリカのホストとなっている Azure 仮想マシンに RDP で接続します。 

    b. フェールオーバー クラスター マネージャーを開きます。

    c. **Networks** ノードを選択し、クラスター ネットワーク名をメモします。 この名前は、PowerShell スクリプトの `$ClusterNetworkName` 変数に使用します。 次の図では、クラスター ネットワーク名は **Cluster Network 1** です。

   ![クラスター ネットワーク名](./media/virtual-machines-ag-listener-configure/90-clusternetworkname.png)

2. <a name="addcap"></a>クライアント アクセス ポイントを追加します。  
    クライアント アクセス ポイントは、アプリケーションが可用性グループ内のデータベースに接続するために使用するネットワーク名です。 クライアント アクセス ポイントは、フェールオーバー クラスター マネージャーで作成します。

    a. クラスター名を展開して、 **[ロール]**をクリックします。

    b. **[ロール]** ウィンドウで、可用性グループ名を右クリックし、**[リソースの追加]** > **[クライアント アクセス ポイント]** の順にクリックします。

   ![クライアント アクセス ポイント](./media/virtual-machines-ag-listener-configure/92-addclientaccesspoint.png)

    c. **[名前]** ボックスで、この新しいリスナーの名前を指定します。 
   新しいリスナーの名前は、アプリケーションが SQL Server 可用性グループ内のデータベースへの接続に使用するネットワーク名です。
   
    d. **[次へ]** を 2 回クリックしてから **[完了]** をクリックし、リスナーの作成を完了します。 この時点では、リスナーまたはリソースをオンラインにしないでください。

3. <a name="congroup"></a>可用性グループの IP リソースを構成します。

    a. **[リソース]** タブをクリックして、作成したクライアント アクセス ポイントを展開します。  
    クライアント アクセス ポイントはオフラインになっています。

   ![クライアント アクセス ポイント](./media/virtual-machines-ag-listener-configure/94-newclientaccesspoint.png) 

    b. IP リソースを右クリックし、プロパティをクリックします。 この IP アドレス名はメモしておき、PowerShell スクリプトの `$IPResourceName` 変数に使用します。

    c. **[IP アドレス]** で **[静的 IP アドレス]** をクリックします。 静的 IP アドレスを、Azure Portal でロード バランサーのアドレス設定時に使用したものと同じアドレスに設定します。

   ![IP リソース](./media/virtual-machines-ag-listener-configure/96-ipresource.png) 

    <!-----------------------I don't see this option on server 2016
    1. Disable NetBIOS for this address and click **OK**. Repeat this step for each IP resource if your solution spans multiple Azure VNets. 
    ------------------------->

4. <a name = "dependencyGroup"></a>SQL Server 可用性グループ リソースがクライアント アクセス ポイントに依存するように設定します。

    a. フェールオーバー クラスター マネージャーで、**[ロール]**、可用性グループの順にクリックします。

    b. **[その他のリソース]** の下の **[リソース]** タブで可用性リソース グループを右クリックし、**[プロパティ]** をクリックします。 

    c. [依存関係] タブで、クライアント アクセス ポイント (リスナー) リソースの名前を追加します。

   ![IP リソース](./media/virtual-machines-ag-listener-configure/97-propertiesdependencies.png) 

    d. **[OK]**をクリックします。

5. <a name="listname"></a>クライアント アクセス ポイント リソースが IP アドレスに依存するように設定します。

    a. フェールオーバー クラスター マネージャーで、**[ロール]**、可用性グループの順にクリックします。 

    b. **[リソース]** タブで **[サーバー名]** の下にあるクライアント アクセスポイント リソースを右クリックし、**[プロパティ]** をクリックします。 

   ![IP リソース](./media/virtual-machines-ag-listener-configure/98-dependencies.png) 

    c. **[依存関係]** タブをクリックします。IP アドレスが依存関係の要素であることを確認します。 そうでない場合は、IP アドレスへの依存関係を設定します。 複数のリソースが一覧表示される場合は、IP アドレスに OR (AND ではなく) 依存関係があることを確認します。 **[OK]**をクリックします。 

   ![IP リソース](./media/virtual-machines-ag-listener-configure/98-propertiesdependencies.png) 

    d. リスナー名を右クリックし、**[オンラインにする]** をクリックします。 

    >[!TIP]
    >依存関係が正しく構成されていることを確認できます。 フェールオーバー クラスター マネージャーで [ロール] に移動します。次に、可用性グループを右クリックし、**[他の操作]** をクリックしてから、**[依存関係レポートの表示]** をクリックします。 依存関係が正しく構成されると、可用性グループはネットワーク名に依存し、ネットワーク名は IP アドレスに依存します。 


6. <a name="setparam"></a>PowerShell でクラスターのパラメーターを設定します。
    
    a. いずれかの SQL Server インスタンスに次の PowerShell スクリプトをコピーします。 環境に合わせて変数を更新してください。     
    
    ```PowerShell
    $ClusterNetworkName = "<MyClusterNetworkName>" # the cluster network name (Use Get-ClusterNetwork on Windows Server 2012 of higher to find the name)
    $IPResourceName = "<IPResourceName>" # the IP Address resource name
    $ILBIP = “<n.n.n.n>” # the IP Address of the Internal Load Balancer (ILB). This is the static IP address for the load balancer you configured in the Azure portal.
    [int]$ProbePort = <nnnnn>
    
    Import-Module FailoverClusters
    
    Get-ClusterResource $IPResourceName | Set-ClusterParameter -Multiple @{"Address"="$ILBIP";"ProbePort"=$ProbePort;"SubnetMask"="255.255.255.255";"Network"="$ClusterNetworkName";"EnableDhcp"=0}
    ```

    b. いずれかのクラスター ノード上で PowerShell スクリプトを実行して、クラスター パラメーターを設定します。  

    > [!NOTE]
    > SQL Server インスタンスが別個のリージョンに存在する場合は、PowerShell スクリプトを 2 回実行する必要があります。 1 回目の実行では、1 番目のリージョンの `$ILBIP` と `$ProbePort` を使用します。 2 回目の実行では、2 番目のリージョンの `$ILBIP` と `$ProbePort` を使用します。 クラスター ネットワーク名とクラスター IP リソース名は同じものを使用します。 
