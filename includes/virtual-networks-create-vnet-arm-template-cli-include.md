## <a name="deploy-the-arm-template-by-using-the-azure-cli"></a>Azure CLI を使用して ARM テンプレートをデプロイする
Azure CLI を使用してダウンロードした ARM テンプレートをデプロイするには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../articles/cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、 **`azure config mode`** コマンドを実行してリソース マネージャー モードに切り替えます。
   
        azure config mode arm
   
    上記のコマンドで想定される出力を次に示します。
   
        info:    New mode is arm
3. 必要に応じて、次のように **`azure group create`** を実行して新しいリソース グループを作成します。 コマンドの出力が表示されます。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。 リソース グループの詳細については、「 [Azure リソース マネージャーの概要](../articles/azure-resource-manager/resource-group-overview.md)」を参照してください。
   
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
4. 上記でダウンロードして変更したテンプレート ファイルとパラメーター ファイルを使用して、 **`azure group deployment create`** コマンドレットを実行して新しい VNet をデプロイします。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。
   
        azure group deployment create -g TestRG -n TestVNetDeployment -f C:\ARM\azuredeploy.json -e C:\ARM\azuredeploy-parameters.json
   
    上記のコマンドで想定される出力を次に示します。
   
        info:    Executing command group deployment create
        + Initializing template configurations and parameters
        + Creating a deployment
        info:    Created template deployment "TestVNetDeployment"
        + Registering providers
        info:    Registering provider microsoft.network
        + Waiting for deployment to complete
        data:    DeploymentName     : TestVNetDeployment
        data:    ResourceGroupName  : TestRG
        data:    ProvisioningState  : Succeeded
        data:    Timestamp          : 2015-08-14T21:56:11.152759Z
        data:    Mode               : Incremental
        data:    Name           Type    Value
        data:    -------------  ------  --------------
        data:    location       String  Central US
        data:    vnetName       String  TestVNet
        data:    addressPrefix  String  192.168.0.0/16
        data:    subnet1Prefix  String  192.168.1.0/24
        data:    subnet1Name    String  FrontEnd
        data:    subnet2Prefix  String  192.168.2.0/24
        data:    subnet2Name    String  BackEnd
        info:    group deployment create command OK
   
   * **-g (または --resource-group)**。 新しい VNet の作成場所となるリソース グループの名前です。
   * **-f (または --template-file)**。 ARM テンプレート ファイルへのパスです。
   * **-e (または--parameters-file)**。 ARM パラメーター ファイルへのパスです。
5. 次に示すように、 **`azure network vnet show`** コマンドを実行して新しい VNet のプロパティを表示します。
   
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

