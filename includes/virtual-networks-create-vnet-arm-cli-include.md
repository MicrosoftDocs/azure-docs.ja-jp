## <a name="how-to-create-a-vnet-using-the-azure-cli"></a>Azure CLI を使用して VNet を作成する方法
Azure CLI を使用すると、Windows、Linux、または OSX を実行している任意のコンピューターのコマンド プロンプトから Azure のリソースを管理することができます。 Azure CLl を使用して VNet を作成するには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../articles/cli-install-nodejs.md) 」を参照して、指示に従い Azure のアカウントとサブスクリプションを選択します。
2. 次に示すように、 **azure config mode** コマンドを実行してリソース マネージャー モードに切り替えます。
   
        azure config mode arm
   
    上記のコマンドで想定される出力を次に示します。
   
        info:    New mode is arm
3. 必要に応じて、次のように **azure group create** で新しいリソース グループを作成します。 コマンドの出力が表示されます。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。 リソース グループの詳細については、「 [Azure リソース マネージャーの概要](../articles/azure-resource-manager/resource-group-overview.md#resource-groups)」を参照してください。
   
        azure group create -n TestRG -l centralus
   
    上記のコマンドで想定される出力を次に示します。
   
        info:    Executing command group create
        + Getting resource group TestRG
        + Creating resource group TestRG
        info:    Created resource group TestRG
        data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG
        data:    Name:                TestRG
        data:    Location:            centralus
        data:    Provisioning State:  Succeeded
        data:    Tags: null
        data:
        info:    group create command OK
   
   * **-n (または --name)**。 新しいリソース グループの名前です。 ここでは、 *TestRG*です。
   * **-l (または --location)**。 新しいリソース グループが作成される Azure リージョンです。 ここでは、 *centralus*です。
4. 次に示すように **azure network vnet create** コマンドを実行し、VNet とサブネットを作成します。 
   
        azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l centralus
   
    上記のコマンドで想定される出力を次に示します。
   
        info:    Executing command network vnet create
        + Looking up virtual network "TestVNet"
        + Creating virtual network "TestVNet"
        + Loading virtual network state
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet2
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        info:    network vnet create command OK
   
   * **-g (または --resource-group)**。 VNet の作成場所となるリソース グループの名前です。 ここでは、 *TestRG*です。
   * **-n (または --name)**。 作成する VNet の名前です。 ここでは、 *TestVNet*
   * **-a (または --address-prefixes)**。 VNet のアドレス空間に使用される CIDR ブロックの一覧です。 ここでは、 *192.168.0.0/16*
   * **-l (または --location)**。 VNet が作成される Azure リージョンです。 ここでは、 *centralus*です。
5. 次に示すように、 **azure network vnet subnet create** コマンドを実行してサブネットを作成します。 コマンドの出力が表示されます。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。
   
        azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
   
    上記のコマンドで想定される出力を次に示します。
   
        info:    Executing command network vnet subnet create
        + Looking up the subnet "FrontEnd"
        + Creating subnet "FrontEnd"
        + Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:
        info:    network vnet subnet create command OK
   
   * **-e (または --vnet-name)**。 サブネットの作成先となる VNet の名前です。 ここでは、 *TestVNet*です。
   * **-n (または --name)**。 新しいサブネットの名前です。 ここでは、 *FrontEnd*です。
   * **-a (または --address-prefix)**。 サブネットの CIDR ブロックです。 ここでは、 *192.168.1.0/24*です。
6. 必要に応じて、上記の手順 5. を繰り返して他のサブネットを作成します。 このシナリオでは、次のコマンドを実行して *BackEnd* サブネットを作成します。
   
        azure network vnet subnet create -g TestRG -e TestVNet -n BackEnd -a 192.168.2.0/24
7. 次に示すように、 **azure network vnet show** コマンドを実行して新しい VNet のプロパティを表示します。
   
        azure network vnet show -g TestRG -n TestVNet
   
    上記のコマンドで想定される出力を次に示します。
   
        info:    Executing command network vnet show
        + Looking up virtual network "TestVNet"
        data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        data:    Name                            : TestVNet
        data:    Type                            : Microsoft.Network/virtualNetworks
        data:    Location                        : centralus
        data:    ProvisioningState               : Succeeded
        data:    Address prefixes:
        data:      192.168.0.0/16
        data:    Subnets:
        data:      Name                          : FrontEnd
        data:      Address prefix                : 192.168.1.0/24
        data:
        data:      Name                          : BackEnd
        data:      Address prefix                : 192.168.2.0/24
        data:
        info:    network vnet show command OK

