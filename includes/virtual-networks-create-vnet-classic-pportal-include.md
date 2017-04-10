## <a name="how-to-create-a-classic-vnet-in-the-azure-portal"></a>Azure ポータルで従来の VNet を作成する方法
上記のシナリオに基づいて従来の VNet を作成するには、次の手順に従います。

1. ブラウザーから http://portal.azure.com に移動し、必要に応じて Azure アカウントでサインインします。
2. 次の図に示すように、**[新規]** > **[ネットワーク]** > **[仮想ネットワーク]** の順にクリックし、**[デプロイ モデルの選択]** の一覧に既に **[クラシック]** と表示されているのを確認してから **[作成]** をクリックします。
   
    ![Azure ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure1.gif)
3. **[仮想ネットワーク]** ブレードで、VNet の **[名前]** を入力してから **[アドレス空間]** をクリックします。 VNet と最初のサブネットのアドレス空間の設定を構成してから [ **OK**] をクリックします。 次の図では、このシナリオでの CIDR ブロック設定を示しています。
   
    ![アドレス空間のブレード](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure2.png)
4. **[リソース グループ]** をクリックし、VNet を追加するリソース グループを選択してから **[新しいリソース グループを作成する]** をクリックして VNet を新しいリソース グループに追加します。 次の図は、 **TestRG**という新しいリソース グループのリソース グループ設定を示しています。 リソース グループの詳細については、「[Azure Resource Manager の概要](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)」を参照してください。
   
    ![リソース グループ ブレードの作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure3.png)
5. 必要に応じて、VNet の **[サブスクリプション]** と **[場所]** の設定を変更します。 
6. VNet を**スタート画面**にタイルとして表示しない場合は、**[スタート画面にピン留めする]** を無効にします。 
7. **[作成]** をクリックすると、次の図に示す **[仮想ネットワークの作成]** という名前のタイルが表示されます。
   
    ![ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure4.png)
8. VNet が作成されるのを待ち、以下のタイルが表示されたら、クリックしてサブネットを追加します。
   
    ![ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure5.png)
9. 次に示すように、VNet の [ **構成** ] が表示されます。 
   
    ![ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure6.png)
10. **[サブネット]** > **[追加]** の順にクリックしてからサブネットの **[名前]** を入力し、**[アドレス範囲 (CIDR ブロック)]** を指定して **[OK]** をクリックします。 次の図は、このシナリオの設定を示しています。
    
    ![Azure ポータルでの VNet の作成](./media/virtual-networks-create-vnet-classic-pportal-include/vnet-create-pportal-figure7.gif)



<!--HONumber=Nov16_HO3-->


