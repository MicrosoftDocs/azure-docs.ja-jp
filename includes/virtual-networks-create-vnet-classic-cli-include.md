## <a name="how-to-create-a-classic-vnet-using-azure-cli"></a>Azure CLI を使用して従来の VNet を作成する方法
Azure CLI を使用すると、Windows、Linux、または OSX を実行している任意のコンピューターのコマンド プロンプトから Azure のリソースを管理することができます。 Azure CLI を使用して VNet を作成するには、次の手順に従います。

1. Azure CLI を初めて使用する場合は、「 [Azure CLI のインストール](../articles/cli-install-nodejs.md) 」を参照して、Azure のアカウントとサブスクリプションを選択する時点までの指示に従います。
2. 次に示すように、 **azure network vnet create** コマンドを実行し、 VNet とサブネットを作成します。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。
   
            azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
   
    予想される出力:
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK
   
   * **--vnet**。 作成する VNet の名前です。 ここでは、 *TestVNet*
   * **-e (または --address-space)**。 VNet のアドレス空間です。 ここでは、 *192.168.0.0*
   * **-i (または -cidr)**。 CIDR 形式のネットワーク マスクです。 ここでは、 *16*です。
   * **-n (または --subnet-name**)。 最初のサブネットの名前です。 ここでは、 *FrontEnd*です。
   * **-p (または --subnet-start-ip)**。 サブネットの開始 IP アドレス、またはサブネット アドレス空間です。 ここでは、 *192.168.1.0*です。
   * **-r (または --subnet-cidr)**。 サブネットの CIDR 形式のネットワーク マスクです。 ここでは、 *24*です。
   * **-l (または --location)**。 VNet が作成される Azure リージョンです。 ここでは、 *Central US*です。
3. 次に示すように、 **azure network vnet subnet create** コマンドを実行してサブネットを作成します。 出力の後に表示される一覧では、使用されたパラメーターについて説明されています。
   
            azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
   
    上記のコマンドで想定される出力を次に示します。
   
            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK
   
   * **-t (または --vnet-name)**。 サブネットの作成先となる VNet の名前です。 ここでは、 *TestVNet*です。
   * **-n (または --name)**。 新しいサブネットの名前です。 ここでは、 *BackEnd*です。
   * **-a (または --address-prefix)**。 サブネットの CIDR ブロックです。 ここでは、 *192.168.2.0/24*です。
4. 次に示すように、 **azure network vnet show** コマンドを実行して新しい VNet のプロパティを表示します。
   
            azure network vnet show
   
    上記のコマンドで想定される出力を次に示します。
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

