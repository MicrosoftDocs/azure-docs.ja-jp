---
title: "Virtual Network を作成する | Azure Resource Manager テンプレート | Microsoft Docs"
description: "Azure Resource Manager テンプレートを使用して仮想ネットワークを作成する方法を説明します。"
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: 69530861-2f97-4a6e-b336-a7baf2690044
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 1e1ca336987082e06f172f7eb89036c64d1fec37
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-virtual-network-using-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用した仮想ネットワークの作成

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure には、Azure Resource Manager とクラシックという&2; 種類のデプロイメント モデルがあります。 Resource Manager デプロイメント モデルを使用してリソースを作成することをお勧めします。 2 つのモデルの違いの詳細については、[Azure のデプロイメント モデルの概要](../azure-resource-manager/resource-manager-deployment-model.md)に関する記事を参照してください。
 
この記事では、Azure Resource Manager テンプレートを使用して Resource Manager デプロイメント モデルで VNet を作成する方法について説明します。 他のツールを使用して Resource Manager で VNet を作成することや、次の一覧から別のオプションを選択してクラシック デプロイメント モデルで VNet を作成することもできます。

> [!div class="op_single_selector"]
- [ポータル](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [CLI](virtual-networks-create-vnet-arm-cli.md)
- [テンプレート](virtual-networks-create-vnet-arm-template-click.md)
- [ポータル (クラシック)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (クラシック)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [CLI (クラシック)](virtual-networks-create-vnet-classic-cli.md)

GitHub から既存の ARM テンプレートをダウンロードして変更し、そのテンプレートを GitHub、PowerShell、および Azure CLI からデプロイする方法を説明します。

GitHub から直接 ARM テンプレートをデプロイするだけで、変更を加えない場合は、 [Github からのテンプレートのデプロイ](#deploy-the-arm-template-by-using-click-to-deploy)に進んでください。

[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="download-and-understand-the-azure-resource-manager-template"></a>Azure リソース マネージャー テンプレートのダウンロードと理解
Github から既存のテンプレートをダウンロードして VNet と&2; つのサブネットを作成し、そのテンプレートに変更を加えて再利用することができます。 これを行うには次の手順を実行します。

1. [サンプル テンプレート ページ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)に移動します。
2. **[azuredeploy.json]**、**[RAW]** の順にクリックします。
3. お使いのコンピューター上のローカル フォルダーにファイルを保存します。
4. テンプレートを使用したことがある場合は、手順 7. に進みます。
5. 保存したファイルを開き、 5 行目にある **parameters** の内容を参照します。 ARM テンプレートのパラメーターでは、デプロイ中に入力できる、値のプレース ホルダーが用意されています。
   
   | パラメーター | 説明 |
   | --- | --- |
   | **location** |VNet が作成される Azure リージョン |
   | **vnetName** |新しい VNet の名前 |
   | **addressPrefix** |VNet のアドレス空間 (CIDR 形式) |
   | **subnet1Name** |最初の VNet の名前 |
   | **subnet1Prefix** |最初のサブネットの CIDR ブロック |
   | **subnet2Name** |2 番目の VNet の名前 |
   | **subnet2Prefix** |2 番目のサブネットの CIDR ブロック |
   
   > [!IMPORTANT]
   > GitHub で管理される Azure リソース マネージャー テンプレートは、今後変更される可能性があります。 使用する前に、必ずテンプレートを確認してください。
   > 
   > 
6. **resources** の内容を確認し、次を参照します。
   
   * **type**。 テンプレートによって作成されるリソースのタイプ。 この場合は **Microsoft.Network/virtualNetworks**にあたり、VNet を指しています。
   * **name**。 リソースの名前です。 **[parameters('vnetName')]**が使用されているため、名前はデプロイ中にユーザーまたはパラメーター ファイルによって入力されます。
   * **properties**。 リソースのプロパティの一覧です。 VNet の作成中、このテンプレートではアドレス空間とサブネットのプロパティが使用されます。
7. [サンプル テンプレート ページ](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vnet-two-subnets)に戻ります。
8. **[azuredeploy-paremeters.json]**、**[RAW]** の順にクリックします。
9. お使いのコンピューター上のローカル フォルダーにファイルを保存します。
10. 保存したファイルを開き、パラメーターの値を編集します。 次の値を使用して、このシナリオで説明した VNet をデプロイします。

    ```json
        {
          "location": {
            "value": "Central US"
          },
          "vnetName": {
              "value": "TestVNet"
          },
          "addressPrefix": {
              "value": "192.168.0.0/16"
          },
          "subnet1Name": {
              "value": "FrontEnd"
          },
          "subnet1Prefix": {
            "value": "192.168.1.0/24"
          },
          "subnet2Name": {
              "value": "BackEnd"
          },
          "subnet2Prefix": {
              "value": "192.168.2.0/24"
          }
        }
    ```

11. ファイルを保存します。


## <a name="deploy-the-template-using-powershell"></a>PowerShell を使用してテンプレートをデプロイする

PowerShell を使用してダウンロードしたテンプレートをデプロイするには、次の手順に従います。

1. 「[Azure PowerShell のインストールと構成](/powershell/azureps-cmdlets-docs) 」の手順を実行し、PowerShell をインストールして構成します。
2. 次のコマンドを実行して、新しいリソース グループを作成します。

    ```powershell
    New-AzureRmResourceGroup -Name TestRG -Location centralus
    ```

    コマンドを実行すると、*米国中部* Azure リージョンに *TestRG* というリソース グループが作成されます。 リソース グループの詳細については、「[Azure Resource Manager の概要](../azure-resource-manager/resource-group-overview.md)」を参照してください。

    予想される出力:

        ResourceGroupName : TestRG
        Location          : centralus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                            Actions  NotActions
                            =======  ==========
                            *
        ResourceId        : /subscriptions/[Id]/resourceGroups/TestRG

3. 上記でダウンロードして変更したテンプレート ファイルとパラメーター ファイルを使用し、次のコマンドを実行して新しい VNet をデプロイします。

    ```powershell
    New-AzureRmResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
    -TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
    ```

    予想される出力:
   
        DeploymentName    : TestVNetDeployment
        ResourceGroupName : TestRG
        ProvisioningState : Succeeded
        Timestamp         : [Date and time]
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
                            Name             Type                       Value
                            ===============  =========================  ==========
                            location         String                     Central US
                            vnetName         String                     TestVNet
                            addressPrefix    String                     192.168.0.0/16
                            subnet1Prefix    String                     192.168.1.0/24
                            subnet1Name      String                     FrontEnd
                            subnet2Prefix    String                     192.168.2.0/24
                            subnet2Name      String                     BackEnd
   
        Outputs           :
4. 次のコマンドを実行して新しい VNet のプロパティを表示します。

    ```powershell
    Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
    ```

    予想される出力:

        Name              : TestVNet
        ResourceGroupName : TestRG
        Location          : centralus
        Id                : /subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
        Etag              : W/"[Id]"
        ProvisioningState : Succeeded
        Tags              :
        AddressSpace      : {
                              "AddressPrefixes": [
                                "192.168.0.0/16"
                              ]
                            }
        DhcpOptions       : {
                              "DnsServers": null
                            }
        NetworkInterfaces : null
        Subnets           : [
                              {
                                "Name": "FrontEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/[Id]/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
                                "AddressPrefix": "192.168.1.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              },
                              {
                                "Name": "BackEnd",
                                "Etag": "W/\"[Id]\"",
                                "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                                "AddressPrefix": "192.168.2.0/24",
                                "IpConfigurations": [],
                                "NetworkSecurityGroup": null,
                                "RouteTable": null,
                                "ProvisioningState": "Succeeded"
                              }
                            ]

## <a name="deploy-the-template-using-click-to-deploy"></a>”クリックしてデプロイ” を使用してテンプレートをデプロイする

Microsoft によって管理され、コミュニティにも公開されている Github リポジトリにアップロードされた定義済み Azure Resource Manager テンプレートは、再利用できます。 これらのテンプレートは、Github から直接デプロイすることもできるほか、ダウンロードしてユーザーのニーズに合わせて変更することもできます。 2 つのサブネットを含む VNet を作成するテンプレートをデプロイするには、次の手順に従います。

1. ブラウザーから [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates)に移動します。
2. テンプレートの一覧を下へスクロールし、 **101-vnet-two-subnets**をクリックします。 次のように **README.md** ファイルを参照します。

    ![Github の READEME.md ファイル](./media/virtual-networks-create-vnet-arm-template-click-include/figure1.png)

3. [ **Azure へのデプロイ**] をクリックします。 必要な場合は、Azure ログイン資格情報を入力します。 
4. **[パラメーター]** ブレードで、新しい VNet の作成に使用する値を入力して、**[OK]** をクリックします。 次の図は、このシナリオの値を示しています。
   
    ![ARM テンプレートのパラメーター](./media/virtual-networks-create-vnet-arm-template-click-include/figure2.png)

5. **[リソース グループ]** をクリックし、VNet を追加するリソース グループを選択するか、**[新規作成]** をクリックして VNet を新しいリソース グループに追加します。 次の図は、 **TestRG**という新しいリソース グループのリソース グループ設定を示しています。

    ![リソース グループ](./media/virtual-networks-create-vnet-arm-template-click-include/figure3.png)

6. 必要に応じて、VNet の **[サブスクリプション]** と **[場所]** の設定を変更します。
7. VNet を**スタート画面**にタイルとして表示しない場合は、**[スタート画面にピン留めする]** を無効にします。
8. **[法律条項]** をクリックして条件を読み、**[購入]** をクリックして同意します。 
9. **[作成]** をクリックして VNet を作成します。
   
    ![プレビュー ポータルでのデプロイ タイルの送信](./media/virtual-networks-create-vnet-arm-template-click-include/figure4.png)

10. デプロイが完了したら、Azure Portal で **[その他のサービス]** をクリックし、フィルター ボックスに「*仮想ネットワーク*」と入力し、[仮想ネットワーク] をクリックして仮想ネットワーク ブレードを表示します。 ブレードで、*[TestVNet]* をクリックします。 *[TestVNet]* ブレードで **[サブネット]** をクリックし、下図のように作成したサブネットを表示します。
    
     ![プレビュー ポータルでの VNet の作成](./media/virtual-networks-create-vnet-arm-template-click-include/figure5.png)

## <a name="next-steps"></a>次のステップ

次の接続方法を確認してください。

- 仮想マシン (VM) を仮想ネットワークに接続する方法。[Windows VM の作成](../virtual-machines/virtual-machines-windows-hero-tutorial.md)に関する記事または [Linux VM の作成](../virtual-machines/virtual-machines-linux-quick-create-portal.md)に関する記事をご覧ください。 この記事の手順で VNet とサブネットを作成する代わりに、VM を接続する既存の VNet とサブネットを選択できます。
- 仮想ネットワークを他の仮想ネットワークに接続する方法。[VNet の接続](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md)に関する記事をご覧ください。
- サイト間の仮想プライベート ネットワーク (VPN) または ExpressRoute 回線を使用して、仮想ネットワークをオンプレミスのネットワークに接続する方法。 詳しくは、[サイト間 VPN を使用したオンプレミスのネットワークへの VNet の接続](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md)に関する記事と「[ExpressRoute 回線への VNet のリンク](../expressroute/expressroute-howto-linkvnet-arm.md)」をご覧ください。

