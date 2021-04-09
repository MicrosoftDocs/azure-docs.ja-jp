---
title: Azure PowerShell を使用して DevTest Labs で仮想マシンを作成する
description: Azure DevTest Labs で Azure PowerShell を使用して仮想マシンを作成および管理する方法を説明します。
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: f79b6ff92d633cf63477cddaabec918df352bec8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102499256"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Azure PowerShell を使用して DevTest Labs で仮想マシンを作成する
この記事では、Azure PowerShell を使用して Azure DevTest Labs で仮想マシンを作成する方法について説明します。 PowerShell スクリプトを使用して、Azure DevTest Labs のラボで仮想マシンの作成を自動化できます。 

## <a name="prerequisites"></a>前提条件
作業を開始する前に、次のことを行います。

- この記事のスクリプトやコマンドのテストに既存のラボを使用しない場合は、[ラボを作成してください](devtest-lab-create-lab.md)。 
- [Azure PowerShell をインストールする](/powershell/azure/install-az-ps)か、Azure portal に統合されている Azure Cloud Shell を使用してください。 

## <a name="powershell-script"></a>PowerShell スクリプト
このセクションのサンプル スクリプトでは [Invoke-AzResourceAction](/powershell/module/az.resources/invoke-azresourceaction) コマンドレットを使用します。  このコマンドレットは、ラボのリソース ID、実行するアクションの名前 (`createEnvironment`)、そのアクションを実行するために必要なパラメーターを取得します。 それらのパラメーターは、すべての仮想マシン記述プロパティが含まれたハッシュ テーブルにあります。 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

上記のスクリプトの仮想マシンのプロパティを使用すると、Windows Server 2016 DataCenter を OS として設定して仮想マシンを作成できます。 仮想マシンの種類ごとに、これらのプロパティの内容は多少異なります。 [[仮想マシンの定義]](#define-virtual-machine) セクションには、このスクリプトで使用するプロパティを決定する方法が示されています。

次のコマンドは、ファイル名で保存されたスクリプトを実行する例を示しています。Create-LabVirtualMachine.ps1 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>仮想マシンの定義
このセクションでは、作成する仮想マシンの種類に固有のプロパティを取得する方法について説明します。 

### <a name="use-azure-portal"></a>Azure Portal の使用
Azure portal で VM を作成するときに、Azure Resource Manager テンプレートを生成できます。 VM の作成プロセスを完了する必要はありません。 テンプレートが表示されるまでの手順を実行するだけで構いません。 ラボの VM をまだ作成していない場合に必要な JSON 記述を取得するには、この方法が最適です。 

1. [Azure Portal](https://portal.azure.com) に移動します。
2. 左側のナビゲーション メニューで、 **[すべてのサービス]** を選択します。
3. サービスの一覧で **[DevTest Labs]** を検索して選択します。 
4. **DevTest Labs** ページで、ラボのリストから使用するラボを選択します。
5. ラボのホーム ページで、ツール バーの **[+ 追加]** を選択します。 
6. VM の **[基本イメージ]** を選択します。 
7. ページ下部の **[送信]** ボタンの上にある **[Automation オプション]** を選択します。 
8. 仮想マシンを作成するための **Azure Resource Manager テンプレート** が表示されます。 
9. **[リソース]** セクションの JSON セグメントには、前に選択したイメージの種類の定義があります。 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

この例では、Azure Marketplace イメージの定義を取得する方法が示されています。 カスタム イメージ、数式、環境の定義も同じ方法で取得できます。 仮想マシンに必要な成果物を追加し、必要な詳細設定を行います。 必須フィールドおよびオプションのフィールドに値を指定した後に **[Automation オプション]** ボタンを選択してください。

### <a name="use-azure-rest-api"></a>Azure REST API の使用
次の手順では、REST API を使用してイメージのプロパティを取得する方法について説明します。これらの手順は、ラボの既存の VM に対してのみ有効です。 

1. [[Virtual Machines - list]\(仮想マシン - 一覧\)](/rest/api/dtl/virtualmachines/list) ページに移動し、 **[Try it]\(使ってみる\)** ボタンを選択します。 
2. **Azure サブスクリプション** を選択します。
3. **ラボのリソース グループ** を入力します。
4. **ラボの名前** を入力します。 
5. **[実行]** を選択します。
6. VM の作成のベースとなった **イメージのプロパティ** が表示されます。 

## <a name="set-expiration-date"></a>有効期限を設定する
トレーニング、デモ、試用版などのシナリオでは、不要なコストが発生しないように、仮想マシンを作成し、固定期間後に自動的にそれらを削除することができます。 サンプルの [PowerShell スクリプト](#powershell-script) セクションに示されているように、PowerShell を使用して VM の作成時に有効期限を設定できます。

ラボ内のすべての既存の VM の有効期限を設定する PowerShell スクリプトの例を次に示します。

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>次のステップ
次のコンテンツをご覧ください: [Azure DevTest Labs の Azure PowerShell ドキュメント](/powershell/module/az.devtestlabs/)
