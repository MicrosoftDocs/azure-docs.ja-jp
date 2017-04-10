---
title: "PowerShell を使ってアプリを仮想ネットワークに接続する"
description: "PowerShell を使って仮想ネットワークに接続し、それを操作する手順"
services: app-service
documentationcenter: 
author: ccompy
manager: erikre
editor: cephalin
ms.assetid: a5c76e77-972a-431c-b14b-3611dae1631b
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2016
ms.author: ccompy
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: e8763f1ab7e51e10ae59cf2b6b2c609f0f45dcd3
ms.lasthandoff: 12/08/2016


---
# <a name="connect-your-app-to-your-virtual-network-by-using-powershell"></a>PowerShell を使ってアプリを仮想ネットワークに接続する
## <a name="overview"></a>Overview
Azure App Service では、アプリ (Web、モバイル、API) を自分のサブスクリプションの Azure 仮想ネットワーク (VNET) に接続できます。 この機能は VNET 統合と呼ばれています。 VNET 統合機能を App Service Environment 機能と混同しないでください。App Service Environment 機能の場合、仮想ネットワークで Azure App Service のインスタンスを実行できます。

新しいポータルに、VNET 統合機能のユーザー インターフェイス (UI) が用意されています。これを使って、クラシック デプロイ モデルまたは Azure Resource Manager デプロイ モデルでデプロイされた仮想ネットワークと統合できます。 この機能の詳細については、「[アプリを Azure 仮想ネットワークに統合する](web-sites-integrate-with-vnet.md)」を参照してください。

この記事で取り上げるのは UI の使い方ではなく、PowerShell を使って統合を実現する方法です。 コマンドはデプロイ モデルごとに異なるため、この記事には、デプロイ モデルごとにセクションが用意されています。  

この記事を読み進める前に、次の用意ができていることをご確認ください。

* 最新の Azure PowerShell SDK インストールしていること。 これは Web Platform Installer でインストールできます。
* Standard または Premium SKU で実行されている Azure App Service のアプリ。

## <a name="classic-virtual-networks"></a>クラシック仮想ネットワーク
このセクションでは、クラシック デプロイ モデルが使われた仮想ネットワーク向けの 3 つのタスクについて説明します。

1. ゲートウェイがあり、ポイント対サイト接続向けに構成されている既存の仮想ネットワークにアプリを接続する。
2. アプリの仮想ネットワーク統合情報を更新する。
3. 仮想ネットワークからアプリを切断する。

### <a name="connect-an-app-to-a-classic-vnet"></a>アプリをクラシック VNET に接続する
アプリを仮想ネットワークに接続するには、次の 3 つの手順に従います。

1. Web アプリに対して、特定の仮想ネットワークに参加することを宣言します。 ポイント対サイト接続のために仮想ネットワークに提供される証明書がアプリによって生成されます。
2. 仮想ネットワークに Web アプリ証明書をアップロードし、ポイント対サイト VPN パッケージ URI を取得します。
3. ポイント対サイトのパッケージ URI で Web アプリの仮想ネットワーク接続を更新します。

手順 1. と手順 3. は完全にスクリプト化できるものの、手順 2. の場合、ポータルで 1 回限りの手動アクションが必要になります。または、仮想ネットワークの Azure Resource Manager エンドポイントで **PUT** または **PATCH** アクションを実行するためのアクセスが必要になります。 これを有効にするには、Azure サポートにお問い合わせください。 開始する前に、ポイント対サイト接続が有効になっているクラシック仮想ネットワークとデプロイ済みのゲートウェイがあることを確認してください。 ゲートウェイを作成し、ポイント対サイト接続を有効にするには、[VPN Gateway の作成][createvpngateway]に関する記事の説明に基づいてポータルを使用する必要があります。

クラシック仮想ネットワークのサブスクリプションは、統合するアプリを保持する App Service プランのサブスクリプションと同じである必要があります。

##### <a name="set-up-azure-powershell-sdk"></a>Azure PowerShell SDK を設定する
PowerShell ウィンドウを開き、次のコマンドで Azure のアカウントとサブスクリプションを設定します。

    Login-AzureRmAccount

そのコマンドでは Azure 資格情報を取得するためのプロンプトが開きます。 サインインした後で、次のコマンドのいずれかを使って、使用するサブスクリプションを選択します。 お使いの仮想ネットワークと App Service プランが含まれるサブスクリプションを使用してください。

    Select-AzureRmSubscription –SubscriptionName [WebAppSubscriptionName]

または

    Select-AzureRmSubscription –SubscriptionId [WebAppSubscriptionId]

##### <a name="variables-used-in-this-article"></a>この記事で使用される変数
コマンドを簡潔にするために、特定の構成で **$Configuration** PowerShell 変数を設定します。

次のパラメーターを利用し、PowerShell に次のような変数を設定します。

    $Configuration = @{}
    $Configuration.WebAppResourceGroup = "[Your web app resource group]"
    $Configuration.WebAppName = "[Your web app name]"
    $Configuration.VnetSubscriptionId = "[Your vnet subscription id]"
    $Configuration.VnetResourceGroup = "[Your vnet resource group]"
    $Configuration.VnetName = "[Your vnet name]"

アプリの場所にはスペースを使用しません。 たとえば、West US は westus になります。

    $Configuration.WebAppLocation = "[Your web app Location]"

次の項目は、証明書が書き込まれる場所です。 ローカル コンピューター上の書き込み可能なパスになります。 最後に .cer を追加してください。

    $Configuration.GeneratedCertificatePath = "[C:\Path\To\Certificate.cer]"

設定内容を確認するには、「 **$Configuration**」と入力します。

    > $Configuration

    Name                           Value
    ----                           -----
    GeneratedCertificatePath       C:\vnetcert.cer
    VnetSubscriptionId             efc239a4-88f9-2c5e-a9a1-3034c21ad496
    WebAppResourceGroup            vnetdemo-rg
    VnetResourceGroup              testase1-rg
    VnetName                       TestNetwork
    WebAppName                     vnetintdemoapp
    WebAppLocation                 centralus

このセクションの残りの部分では、今までの説明に基づいて変数が作成されているものと想定しています。

##### <a name="declare-the-virtual-network-to-the-app"></a>アプリに対して仮想ネットワークを宣言する
次のコマンドを利用して、この特定の仮想ネットワークを使用することをアプリに通知します。 その通知に基づき、アプリは必要な証明書を生成します。

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -PropertyObject @{"VnetResourceId" = "/subscriptions/$($Configuration.VnetSubscriptionId)/resourceGroups/$($Configuration.VnetResourceGroup)/providers/Microsoft.ClassicNetwork/virtualNetworks/$($Configuration.VnetName)"} -Location $Configuration.WebAppLocation -ApiVersion 2015-07-01

このコマンドが完了すると、**$vnet** に **Properties** 変数が追加されます。 **Properties** 変数には、証明書の拇印と証明書のデータの両方を含めます。

##### <a name="upload-the-web-app-certificate-to-the-virtual-network"></a>仮想ネットワークに Web アプリ証明書をアップロードする
サブスクリプションと仮想ネットワークの組み合わせごとに 1 回限りの手動の手順が必要になります。 つまり、サブスクリプション A のアプリを仮想ネットワーク A に接続する場合、構成するアプリの数に関係なく、この手順を 1 回だけ行う必要があります。 別の仮想ネットワークに新しいアプリを追加する場合は、この手順をもう一度実行する必要があります。 その理由は、証明書セットは Azure App Service のサブスクリプション レベルで、アプリの接続先となる仮想ネットワークごとに 1 回生成されるためです。

これらの手順を既に実行している場合や、ポータルを使って同じ仮想ネットワークと統合してある場合、証明書は既に設定されています。

最初の手順は .cer ファイルを生成することです。 次の手順では、.cer ファイルを仮想ネットワークにアップロードします。 先の手順で API 呼び出しから .cer ファイルを生成するには、次のコマンドを実行します。

    $certBytes = [System.Convert]::FromBase64String($vnet.Properties.certBlob)
    [System.IO.File]::WriteAllBytes("$($Configuration.GeneratedCertificatePath)", $certBytes)

証明書は **$Configuration.GeneratedCertificatePath** で指定した場所にあります。

証明書を手動でアップロードするには、[Azure Portal][azureportal] を使用し、**[仮想ネットワークの参照 (クラシック)]** > **[VPN 接続]** > **[ポイント対サイト]** > **[証明書の管理]** の順に選択します。 ここから証明書をアップロードします。

##### <a name="get-the-point-to-site-package"></a>ポイント対サイトのパッケージを取得する
Web アプリで仮想ネットワーク接続を設定するための次の手順は、ポイント対サイトのパッケージを取得し、それを Web に提供することです。

コンピューター上の GetNetworkPackageUri.json という名前のファイルに次のテンプレートを保存します (C:\Azure\Templates\GetNetworkPackageUri.json など)。

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "certData": {
                "type": "string"
            },
            "certThumbprint": {
                "type": "string"
            },
            "networkName": {
                "type": "string"
            }
        },
        "variables": {
            "legacyVnetName": "[concat('Group ', resourceGroup().name, ' ', parameters('networkName'))]"
            },
            "resources": [
            ],
        "outputs" : {
            "PackageUri" :
            {
            "value" : "[listPackage(resourceId('Microsoft.ClassicNetwork/virtualNetworks/gateways/clientRootCertificates', parameters('networkName'), 'primary', parameters('certThumbprint')), '2014-06-01').packageUri]", "type" : "string"
            }
        }
    }


入力パラメーターの設定:

    $parameters = @{"certData" = $vnet.Properties.certBlob ;
    certThumbprint = $vnet.Properties.certThumbprint ;
    "networkName" = $Configuration.VnetName }

スクリプトの呼び出し:

    $output = New-AzureRmResourceGroupDeployment -Name unused -ResourceGroupName $Configuration.VnetResourceGroup -TemplateParameterObject $parameters -TemplateFile C:\PATH\TO\GetNetworkPackageUri.json


これで変数 **$output.Outputs.packageUri** に Web アプリに提供するパッケージ URI が含まれます。

##### <a name="upload-the-point-to-site-package-to-your-app"></a>ポイント対サイトのパッケージをアプリにアップロードする
最後の手順は、このパッケージをアプリに提供することです。 単純に、次のコマンドを実行します。

    $vnet = New-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)/primary" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-07-01 -PropertyObject @{"VnetName" = $Configuration.VnetName ; "VpnPackageUri" = $($output.Outputs.packageUri).Value } -Location $Configuration.WebAppLocation

既存のリソースの上書きを確認するメッセージが表示された場合は、上書きを許可します。

このコマンドが正常に実行されると、アプリが仮想ネットワークに接続された状態になります。 それを確認するには、アプリのコンソールに移動して次のコマンドを入力します。

    SET WEBSITE_

WEBSITE_VNETNAME という名前の環境変数の値がターゲット仮想ネットワークの名前と一致している場合、構成はすべて完了です。

### <a name="update-classic-vnet-integration-information"></a>クラシック VNET 統合情報を更新する
情報を更新または再同期するには、最初に統合を作成したときの手順を繰り返してください。 次のような手順です。

1. 構成情報を定義します。
2. アプリに対して仮想ネットワークを宣言します。
3. ポイント対サイトのパッケージを取得します。
4. ポイント対サイトのパッケージをアプリにアップロードします。

### <a name="disconnect-your-app-from-a-classic-vnet"></a>クラシック VNET からアプリを切断する
アプリを切断するには、仮想ネットワークの統合中に設定した構成情報が必要になります。 その情報を利用し、コマンドを使って仮想ネットワークからアプリを切断します。

    $vnet = Remove-AzureRmResource -Name "$($Configuration.WebAppName)/$($Configuration.VnetName)" -ResourceGroupName $Configuration.WebAppResourceGroup -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-07-01

## <a name="resource-manager-virtual-networks"></a>Resource Manager の仮想ネットワーク
Resource Manager の仮想ネットワークには、クラシック仮想ネットワークの場合よりも一部のプロセスを簡略化できる Azure Resource Manager API があります。 次のタスクの実行に役立つスクリプトがあります。

* Resource Manager の仮想ネットワークを作成し、それにアプリを統合する。
* ゲートウェイを作成し、既存の Resource Manager の仮想ネットワークにポイント対サイトの接続を構成して、それにアプリを統合する。
* ゲートウェイとポイント対サイトの接続が有効になっている既存の Resource Manager の仮想ネットワークにアプリを統合する。
* 仮想ネットワークからアプリを切断する。

### <a name="resource-manager-vnet-app-service-integration-script"></a>Resource Manager VNET App Service 統合スクリプト
次のスクリプトをコピーし、ファイルに保存します。 スクリプトを使用しない場合でも、スクリプトから Resource Manager の仮想ネットワークの設定方法を確認できます。

    function ReadHostWithDefault($message, $default)
    {
        $result = Read-Host "$message [$default]"
        if($result -eq "")
        {
            $result = $default
        }
            return $result
        }

    function PromptCustom($title, $optionValues, $optionDescriptions)
    {
        Write-Host $title
        Write-Host
        $a = @()
        for($i = 0; $i -lt $optionValues.Length; $i++){
            Write-Host "$($i+1))" $optionDescriptions[$i]
        }
        Write-Host

        while($true)
        {
            Write-Host "Choose an option: "
            $option = Read-Host
            $option = $option -as [int]

            if($option -ge 1 -and $option -le $optionValues.Length)
            {
                return $optionValues[$option-1]
            }
        }
    }

    function PromptYesNo($title, $message, $default = 0)
    {
        $yes = New-Object System.Management.Automation.Host.ChoiceDescription "&Yes", ""
        $no = New-Object System.Management.Automation.Host.ChoiceDescription "&No", ""
        $options = [System.Management.Automation.Host.ChoiceDescription[]]($yes, $no)
        $result = $host.ui.PromptForChoice($title, $message, $options, $default)
        return $result
    }

    function CreateVnet($resourceGroupName, $vnetName, $vnetAddressSpace, $vnetGatewayAddressSpace, $location)
    {
        Write-Host "Creating a new VNET"
        $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
        New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName -Location $location -AddressPrefix $vnetAddressSpace -Subnet $gatewaySubnet
    }

    function CreateVnetGateway($resourceGroupName, $vnetName, $vnetIpName, $location, $vnetIpConfigName, $vnetGatewayName, $certificateData, $vnetPointToSiteAddressSpace)
    {
        $vnet = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $resourceGroupName
        $subnet=Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet

        Write-Host "Creating a public IP address for this VNET"
        $pip = New-AzureRmPublicIpAddress -Name $vnetIpName -ResourceGroupName $resourceGroupName -Location $location -AllocationMethod Dynamic
        $ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $vnetIpConfigName -Subnet $subnet -PublicIpAddress $pip

        Write-Host "Adding a root certificate to this VNET"
        $root = New-AzureRmVpnClientRootCertificate -Name "AppServiceCertificate.cer" -PublicCertData $certificateData

        Write-Host "Creating Azure VNET Gateway. This may take up to an hour."
        New-AzureRmVirtualNetworkGateway -Name $vnetGatewayName -ResourceGroupName $resourceGroupName -Location $location -IpConfigurations $ipconf -GatewayType Vpn -VpnType RouteBased -EnableBgp $false -GatewaySku Basic -VpnClientAddressPool $vnetPointToSiteAddressSpace -VpnClientRootCertificates $root
    }

    function AddNewVnet($subscriptionId, $webAppResourceGroup, $webAppName)
    {
        Write-Host "Adding a new Vnet"
        Write-Host
        $vnetName = Read-Host "Specify a name for this Virtual Network"

        $vnetGatewayName="$($vnetName)-gateway"
        $vnetIpName="$($vnetName)-ip"
        $vnetIpConfigName="$($vnetName)-ipconf"

        # Virtual Network settings
        $vnetAddressSpace="10.0.0.0/8"
        $vnetGatewayAddressSpace="10.5.0.0/16"
        $vnetPointToSiteAddressSpace="172.16.0.0/16"

        $changeRequested = 0
        $resourceGroupName = $webAppResourceGroup

        while($changeRequested -eq 0)
        {
            Write-Host
            Write-Host "Currently, I will create a VNET with the following settings:"
            Write-Host
            Write-Host "Virtual Network Name: $vnetName"
            Write-Host "Resource Group Name:  $resourceGroupName"
            Write-Host "Gateway Name: $vnetGatewayName"
            Write-Host "Vnet IP name: $vnetIpName"
            Write-Host "Vnet IP config name:  $vnetIpConfigName"
            Write-Host "Address Space:$vnetAddressSpace"
            Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
            Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
            Write-Host
            $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

            if($changeRequested -eq 0)
            {
                $vnetName = ReadHostWithDefault "Virtual Network Name" $vnetName
                $resourceGroupName = ReadHostWithDefault "Resource Group Name" $resourceGroupName
                $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                $vnetAddressSpace = ReadHostWithDefault "Vnet Address Space" $vnetAddressSpace
                $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
            }
        }

        $ErrorActionPreference = "Stop";

        # We create the virtual network and add it here. The way this works is:
        # 1) Add the VNET association to the App. This allows the App to generate certificates, etc. for the VNET.
        # 2) Create the VNET and VNET gateway, add the certificates, create the public IP, etc., required for the gateway
        # 3) Get the VPN package from the gateway and pass it back to the App.

        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup
        $location = $webApp.Location

        Write-Host "Creating App association to VNET"
        $propertiesObject = @{
         "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($resourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
        }
        $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        CreateVnet $resourceGroupName $vnetName $vnetAddressSpace $vnetGatewayAddressSpace $location

        CreateVnetGateway $resourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $resourceGroupName -VirtualNetworkGatewayName $vnetGatewayName -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $VirtualNetworkName; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnetName)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $webAppResourceGroup -Force

        Write-Host "Finished!"
    }

    function AddExistingVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $ErrorActionPreference = "Stop";

        # At this point, the gateway should be able to be joined to an App, but may require some minor tweaking. We will declare to the App now to use this VNET
        Write-Host "Getting App information"
        $webApp = Get-AzureRmResource -ResourceName $webAppName -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $location = $webApp.Location

        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"
        }

        # Display existing vnets
        $vnets = Get-AzureRmVirtualNetwork
        $vnetNames = @()
        foreach($vnet in $vnets)
        {
            $vnetNames += $vnet.Name
        }

        Write-Host
        $vnet = PromptCustom "Select a VNET to integrate with" $vnets $vnetNames

        # We need to check if this VNET is able to be joined to a App, based on following criteria
            # If there is no gateway, we can create one.
            # If there is a gateway:
                # It must be of type Vpn
                # It must be of VpnType RouteBased
                # If it doesn't have the right certificate, we will need to add it.
                # If it doesn't have a point-to-site range, we will need to add it.

        $gatewaySubnet = $vnet.Subnets | Where-Object { $_.Name -eq "GatewaySubnet" }

        if($gatewaySubnet -eq $null -or $gatewaySubnet.IpConfigurations -eq $null -or $gatewaySubnet.IpConfigurations.Count -eq 0)
        {
            $ErrorActionPreference = "Continue";
            # There is no gateway. We need to create one.
            Write-Host "This Virtual Network has no gateway. I will need to create one."

            $vnetName = $vnet.Name
            $vnetGatewayName="$($vnetName)-gateway"
            $vnetIpName="$($vnetName)-ip"
            $vnetIpConfigName="$($vnetName)-ipconf"

            # Virtual Network settings
            $vnetAddressSpace="10.0.0.0/8"
            $vnetGatewayAddressSpace="10.5.0.0/16"
            $vnetPointToSiteAddressSpace="172.16.0.0/16"

            $changeRequested = 0

            Write-Host "Your VNET is in the address space $($vnet.AddressSpace.AddressPrefixes), with the following Subnets:"
            foreach($subnet in $vnet.Subnets)
            {
                Write-Host "$($subnet.Name): $($subnet.AddressPrefix)"
            }

            $vnetGatewayAddressSpace = Read-Host "Please choose a GatewaySubnet address space"

            while($changeRequested -eq 0)
            {
                Write-Host
                Write-Host "Currently, I will create a VNET gateway with the following settings:"
                Write-Host
                Write-Host "Virtual Network Name: $vnetName"
                Write-Host "Resource Group Name:  $($vnet.ResourceGroupName)"
                Write-Host "Gateway Name: $vnetGatewayName"
                Write-Host "Vnet IP name: $vnetIpName"
                Write-Host "Vnet IP config name:  $vnetIpConfigName"
                Write-Host "Address Space:$($vnet.AddressSpace.AddressPrefixes)"
                Write-Host "Gateway Address Space:$vnetGatewayAddressSpace"
                Write-Host "Point-To-Site Address Space:  $vnetPointToSiteAddressSpace"
                Write-Host
                $changeRequested = PromptYesNo "" "Do you wish to change these settings?" 1

                if($changeRequested -eq 0)
                {
                    $vnetGatewayName = ReadHostWithDefault "Vnet Gateway Name" $vnetGatewayName
                    $vnetIpName = ReadHostWithDefault "Vnet IP name" $vnetIpName
                    $vnetIpConfigName = ReadHostWithDefault "Vnet IP configuration name" $vnetIpConfigName
                    $vnetGatewayAddressSpace = ReadHostWithDefault "Vnet Gateway Address Space" $vnetGatewayAddressSpace
                    $vnetPointToSiteAddressSpace = ReadHostWithDefault "Vnet Point-to-site Address Space" $vnetPointToSiteAddressSpace
                }
            }

            $ErrorActionPreference = "Stop";

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnetName)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # If there is no gateway subnet, we need to create one.
            if($gatewaySubnet -eq $null)
            {
                $gatewaySubnet = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix $vnetGatewayAddressSpace
                $vnet.Subnets.Add($gatewaySubnet);
                Set-AzureRmVirtualNetwork -VirtualNetwork $vnet
            }

            CreateVnetGateway $vnet.ResourceGroupName $vnetName $vnetIpName $location $vnetIpConfigName $vnetGatewayName $virtualNetwork.Properties.CertBlob $vnetPointToSiteAddressSpace

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $vnetGatewayName
        }
        else
        {
            $uriParts = $gatewaySubnet.IpConfigurations[0].Id.Split('/')
            $gatewayResourceGroup = $uriParts[4]
            $gatewayName = $uriParts[8]

            $gateway = Get-AzureRmVirtualNetworkGateway -ResourceGroupName $vnet.ResourceGroupName -Name $gatewayName

            # validate gateway types, etc.
            if($gateway.GatewayType -ne "Vpn")
            {
                Write-Error "This gateway is not of the Vpn type. It cannot be joined to an App."
                return
            }

            if($gateway.VpnType -ne "RouteBased")
            {
                Write-Error "This gateways Vpn type is not RouteBased. It cannot be joined to an App."
                return
            }

            if($gateway.VpnClientConfiguration -eq $null -or $gateway.VpnClientConfiguration.VpnClientAddressPool -eq $null)
            {
                Write-Host "This gateway does not have a Point-to-site Address Range. Please specify one in CIDR notation, e.g. 10.0.0.0/8"
                $pointToSiteAddress = Read-Host "Point-To-Site Address Space"
                Set-AzureRmVirtualNetworkGatewayVpnClientConfig -VirtualNetworkGateway $gateway.Name -VpnClientAddressPool $pointToSiteAddress
            }

            Write-Host "Creating App association to VNET"
            $propertiesObject = @{
             "vnetResourceId" = "/subscriptions/$($subscriptionId)/resourceGroups/$($vnet.ResourceGroupName)/providers/Microsoft.Network/virtualNetworks/$($vnet.Name)"
            }

            $virtualNetwork = New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

            # We need to check if the certificate here exists in the gateway.
            $certificates = $gateway.VpnClientConfiguration.VpnClientRootCertificates

            $certFound = $false
            foreach($certificate in $certificates)
            {
                if($certificate.PublicCertData -eq $virtualNetwork.Properties.CertBlob)
                {
                    $certFound = $true
                    break
                }
            }

            if(-not $certFound)
            {
                Write-Host "Adding certificate"
                Add-AzureRmVpnClientRootCertificate -VpnClientRootCertificateName "AppServiceCertificate.cer" -PublicCertData $virtualNetwork.Properties.CertBlob -VirtualNetworkGatewayName $gateway.Name
            }
        }

        # Now finish joining by getting the VPN package and giving it to the App
        Write-Host "Retrieving VPN Package and supplying to App"
        $packageUri = Get-AzureRmVpnClientPackage -ResourceGroupName $vnet.ResourceGroupName -VirtualNetworkGatewayName $gateway.Name -ProcessorArchitecture Amd64

        # Put the VPN client configuration package onto the App
        $PropertiesObject = @{
        "vnetName" = $vnet.Name; "vpnPackageUri" = $packageUri
        }

        New-AzureRmResource -Location $location -Properties $PropertiesObject -ResourceName "$($webAppName)/$($vnet.Name)/primary" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections/gateways" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName -Force

        Write-Host "Finished!"
    }

    function RemoveVnet($subscriptionId, $resourceGroupName, $webAppName)
    {
        $webAppConfig = Get-AzureRmResource -ResourceName "$($webAppName)/web" -ResourceType "Microsoft.Web/sites/config" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        $currentVnet = $webAppConfig.Properties.VnetName
        if($currentVnet -ne $null -and $currentVnet -ne "")
        {
            Write-Host "Currently connected to VNET $currentVnet"

            Remove-AzureRmResource -ResourceName "$($webAppName)/$($currentVnet)" -ResourceType "Microsoft.Web/sites/virtualNetworkConnections" -ApiVersion 2015-08-01 -ResourceGroupName $resourceGroupName
        }
            else
        {
            Write-Host "Not connected to a VNET."
        }
    }

    Write-Host "Please Login"
    Login-AzureRmAccount

    # Choose subscription. If there's only one we will choose automatically

    $subs = Get-AzureRmSubscription
    $subscriptionId = ""

    if($subs.Length -eq 0)
    {
        Write-Error "No subscriptions bound to this account."
        return
    }

    if($subs.Length -eq 1)
    {
        $subscriptionId = $subs[0].SubscriptionId
    }
    else
    {
        $subscriptionChoices = @()
        $subscriptionValues = @()

        foreach($subscription in $subs){
        $subscriptionChoices += "$($subscription.SubscriptionName) ($($subscription.SubscriptionId))";
        $subscriptionValues += ($subscription.SubscriptionId);
        }

        $subscriptionId = PromptCustom "Choose a subscription" $subscriptionValues $subscriptionChoices
    }

    Select-AzureRmSubscription -SubscriptionId $subscriptionId

    $resourceGroup = Read-Host "Please enter the Resource Group of your App"

    $appName = Read-Host "Please enter the Name of your App"

    $options = @("Add a NEW Virtual Network to an App", "Add an EXISTING Virtual Network to an App", "Remove a Virtual Network from an App");
    $optionValues = @(0, 1, 2)
    $option = PromptCustom "What do you want to do?" $optionValues $options

    if($option -eq 0)
    {
        AddNewVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 1)
    {
        AddExistingVnet $subscriptionId $resourceGroup $appName
    }
    if($option -eq 2)
    {
        RemoveVnet $subscriptionId $resourceGroup $appName
    }

スクリプトのコピーを保存します。 この記事では V2VnetAllinOne.ps1 という名前ですが、別の名前を使用することもできます。 このスクリプトには引数がありません。 スクリプトを実行します。 スクリプトを実行すると、最初にサインインが求められます。 サインイン後、スクリプトはアカウントの詳細を取得し、サブスクリプションの一覧を返します。 資格情報の要求を除いた最初のスクリプトの実行は次のようになります。

    PS C:\Users\ccompy\Documents\VNET> .\V2VnetAllInOne.ps1
    Please Login

    Environment           : AzureCloud
    Account               : ccompy@microsoft.com
    TenantId              : 722278f-fef1-499f-91ab-2323d011db47
    SubscriptionId        : af5358e1-acac-2c90-a9eb-722190abf47a
    CurrentStorageAccount :

    Choose a subscription

    1) Demo Subscription (af5358e1-acac-2c90-a9eb-722190abf47a)
    2) MS Test (a5350f55-dd5a-41ec-2ddb-ff7b911bb2ef)
    3) Purple Demo Subscription (2d4c99a4-57f9-4d5e-a0a1-0034c52db59d)

    Choose an option: 3

    Account      : ccompy@microsoft.com Environment  : AzureCloud Subscription : 2d4c99a4-57f9-4d5e-a0a1-0034c52db59d Tenant       : 722278f-fef1-499f-91ab-2323d011db47

    Please enter the Resource Group of your App: hcdemo-rg Please enter the Name of your App: v2vnetpowershell What do you want to do?

    1) Add a NEW Virtual Network to an App
    2) Add an EXISTING Virtual Network to an App
    3) Remove a Virtual Network from an App

このセクションの残りの部分では、これら 3 つのオプションについて個別に説明します。

### <a name="create-a-resource-manager-vnet-and-integrate-with-it"></a>Resource Manager の VNET を作成し、それと統合する
Resource Manager デプロイ モデルを使用する仮想ネットワークを新たに作成し、アプリと統合するには、 **[1) Add a NEW Virtual Network to an App]**を選びます。 その操作により、仮想ネットワークの名前の入力を求めるメッセージが表示されます。 ここでは、次の設定からわかるように、v2pshell という名前を使いました。

スクリプトにより、作成される仮想ネットワークの詳細が提供されます。 必要であれば、値を変更できます。 この例では、次の設定の仮想ネットワークを作成しました。

    Virtual Network Name:         v2pshell
    Resource Group Name:          hcdemo-rg
    Gateway Name:                 v2pshell-gateway
    Vnet IP name:                 v2pshell-ip
    Vnet IP config name:          v2pshell-ipconf
    Address Space:                10.0.0.0/8
    Gateway Address Space:        10.5.0.0/16
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):

いずれかの値を変更する場合は、「 **Y** 」と入力し、必要な変更を行います。 仮想ネットワークの設定に問題がなければ、設定の変更に関するメッセージが表示されたときに「 **N** 」と入力するか、Enter キーを押します。 その時点から完了するまでの間に、スクリプトにより、仮想ネットワーク ゲートウェイの作成が開始されるまでに実行される処理の内容の一部が示されます。 その手順には最大 1 時間かかることがあります。 この段階では進捗状況は表示されませんが、ゲートウェイの作成完了時に通知されます。

スクリプトが完了すると、 **"Finished"**と表示されます。 この時点で、選択した名前と設定を持つ Resource Manager の仮想ネットワークが作成されます。 また、この新しい仮想ネットワークは、アプリに統合されます。

### <a name="integrate-your-app-with-a-preexisting-resource-manager-vnet"></a>アプリを既存の Resource Manager VNET に統合する
既存の仮想ネットワークと統合する際に、ゲートウェイまたはポイント対サイト接続がない Resource Manager の仮想ネットワークを指定すると、スクリプトによってそれが設定されます。 VNET にそれらが既に設定されている場合は、スクリプトの処理はすぐにアプリの統合に入ります。 このプロセスを開始するには、 **[2) Add an EXISTING Virtual Network to an App]**を選択します。

このオプションは、アプリと同じサブスクリプションにある Resource Manager の仮想ネットワークが存在する場合にのみ使用できます。 このオプションを選択すると、Resource Manager の仮想ネットワークの一覧が表示されます。   

    Select a VNET to integrate with

    1) v2demonetwork
    2) v2pshell
    3) v2vnetintdemo
    4) v2asenetwork
    5) v2pshell2

    Choose an option: 5

統合する仮想ネットワークを選択します。 ポイント対サイト接続が有効なゲートウェイを既に用意している場合、スクリプトによってアプリが仮想ネットワークと統合されます。 ゲートウェイがない場合は、ゲートウェイ サブネットを指定する必要があります。 ゲートウェイ サブネットは仮想ネットワークのアドレス空間に配置する必要があります。他のサブネットに配置することはできません。 仮想ネットワークにゲートウェイがない場合に、この手順を実行すると、次のようになります。

    This Virtual Network has no gateway. I will need to create one.
    Your VNET is in the address space 172.16.0.0/16, with the following Subnets:
    default: 172.16.0.0/24
    Please choose a GatewaySubnet address space: 172.16.1.0/26

この例では、次の設定の仮想ネットワーク ゲートウェイを作成しました。

    Virtual Network Name:         v2pshell2
    Resource Group Name:          vnetdemo-rg
    Gateway Name:                 v2pshell2-gateway
    Vnet IP name:                 v2pshell2-ip
    Vnet IP config name:          v2pshell2-ipconf
    Address Space:                172.16.0.0/16
    Gateway Address Space:        172.16.1.0/26
    Point-To-Site Address Space:  172.16.0.0/12

    Do you wish to change these settings?
    [Y] Yes  [N] No  [?] Help (default is "N"):
    Creating App association to VNET

これらの設定は変更することもできます。 変更しない場合は、Enter キーを押してください。ゲートウェイが作成され、アプリが仮想ネットワークにアタッチされます。 ゲートウェイの作成にはやはり 1 時間かかるため、注意してください。 すべてが完了すると、"**Finished**" と表示されます。

### <a name="disconnect-your-app-from-a-resource-manager-vnet"></a>アプリを Resource Manager の VNET から切断する
仮想ネットワークからアプリを切断してもゲートウェイは停止せず、ポイント対サイト接続は無効になりません。 他の用途で使用していることがあります。 自分が指定した以外のアプリから切断されることもありません。 このアクションを実行するには、 **[3) Remove a Virtual Network from an App]**を選択します。 選択後、次のように表示されます。

    Currently connected to VNET v2pshell

    Confirm
    Are you sure you want to delete the following resource:
    /subscriptions/edcc99a4-b7f9-4b5e-a9a1-3034c51db496/resourceGroups/hcdemo-rg/providers/Microsoft.Web/sites/v2vnetpowers
    hell/virtualNetworkConnections/v2pshell
    [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

スクリプトには "削除" と表示されますが、仮想ネットワークは削除されません。 統合を削除するだけです。 実行する操作を確定すると、コマンドがすぐに処理され、完了後に **"True"** と表示されます。

<!--Links-->
[createvpngateway]: http://azure.microsoft.com/documentation/articles/vpn-gateway-point-to-site-create/
[azureportal]: http://portal.azure.com

