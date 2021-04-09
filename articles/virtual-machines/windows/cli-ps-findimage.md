---
title: Azure Marketplace イメージとプランを検索して使用する
description: Azure PowerShell を使用して、Marketplace VM イメージの発行元、オファー、SKU、バージョン、およびプランの情報を検索して使用します。
author: cynthn
ms.service: virtual-machines
ms.subservice: imaging
ms.topic: how-to
ms.workload: infrastructure
ms.date: 12/07/2020
ms.author: cynthn
ms.openlocfilehash: 45e6b157dba5ef7410d8a5c0223fd3ecb52f39d0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96906269"
---
# <a name="find-and-use-azure-marketplace-vm-images-with-azure-powershell"></a>Azure PowerShell を使用して Azure Marketplace VM イメージを検索して使用する     

この記事では、Azure PowerShell を使用して Azure Marketplace 内で VM イメージを検索する方法について説明します。 その後、VM の作成時に Marketplace イメージとプランの情報を指定できるようになります。

また、[Azure Marketplace](https://azuremarketplace.microsoft.com/) のストアフロント、[Azure portal](https://portal.azure.com)、または [Azure CLI](../linux/cli-ps-findimage.md) を使用して、使用できるイメージとオファーを参照することもできます。 


[!INCLUDE [virtual-machines-common-image-terms](../../../includes/virtual-machines-common-image-terms.md)]


## <a name="create-a-vm-from-vhd-with-plan-information"></a>プランの情報を使用して VHD から VM を作成する

Azure Marketplace イメージを使用して作成された既存の VHD がある場合は、その VHD から新しい VM を作成するときに、購入プランの情報を指定することが必要になる場合があります。

元の VM、または同じイメージから作成した他の VM がある場合は、Get-AzVM を使用して、そこからプラン名、発行元、および製品情報を取得できます。 この例では、*myResourceGroup* リソース グループの *myVM* という名前の VM を取得し、購入プラン情報を表示します。

```azurepowershell-interactive
$vm = Get-azvm `
   -ResourceGroupName myResourceGroup `
   -Name myVM
$vm.Plan
```

元の VM が削除される前にプラン情報を取得しなかった場合は、[サポート リクエスト](https://ms.portal.azure.com/#create/Microsoft.Support)を提出できます。 VM 名、サブスクリプション ID、および削除操作のタイム スタンプが必要になります。

VHD を使用して VM を作成するには、「[特殊化した VHD から VM を作成する](create-vm-specialized.md)」の記事を参照して、[Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) を使用して以下のように VM の設定にプラン情報を追加する行を追加します。

```azurepowershell-interactive
$vmConfig = Set-AzVMPlan `
   -VM $vmConfig `
   -Publisher "publisherName" `
   -Product "productName" `
   -Name "planName"
```

## <a name="create-a-new-vm-from-a-marketplace-image"></a>Marketplace イメージから VM を作成する

使用するイメージに関する情報が既にある場合は、その情報を [Set-AzVMSourceImage](/powershell/module/az.compute/set-azvmsourceimage) コマンドレットに渡して、イメージ情報を VM 構成に追加することができます。 Marketplace で入手できるイメージの検索と一覧表示については、次のセクションを参照してください。

一部の有料イメージでは、[Set-AzVMPlan](/powershell/module/az.compute/set-azvmplan) を使用して購入プランの情報も提供する必要があります。 

```powershell
...

$vmConfig = New-AzVMConfig -VMName "myVM" -VMSize Standard_D1

# Set the Marketplace image
$offerName = "windows-data-science-vm"
$skuName = "windows2016"
$version = "19.01.14"
$vmConfig = Set-AzVMSourceImage -VM $vmConfig -PublisherName $publisherName -Offer $offerName -Skus $skuName -Version $version

# Set the Marketplace plan information, if needed
$publisherName = "microsoft-ads"
$productName = "windows-data-science-vm"
$planName = "windows2016"
$vmConfig = Set-AzVMPlan -VM $vmConfig -Publisher $publisherName -Product $productName -Name $planName

...
```

その後、VM 構成と他の構成オブジェクトを `New-AzVM` コマンドレットに渡します。 PowerShell で VM 構成を使用する詳細な例については、この[スクリプト](https://github.com/Azure/azure-docs-powershell-samples/blob/master/virtual-machine/create-vm-detailed/create-windows-vm-detailed.ps1)を参照してください。

イメージの使用条件への同意に関するメッセージが表示された場合は、この記事で後述する「[使用条件への同意](#accept-the-terms)」セクションを参照してください。

## <a name="list-images"></a>イメージの一覧表示

特定の場所にあるイメージを検索するための 1 つの方法として、シーケンス内で [Get-AzVMImagePublisher](/powershell/module/az.compute/get-azvmimagepublisher)、[Get-AzVMImageOffer](/powershell/module/az.compute/get-azvmimageoffer)、および [Get-AzVMImageSku](/powershell/module/az.compute/get-azvmimagesku) コマンドレットを順に実行します。

1. イメージの発行元を一覧表示する。
2. 指定された発行元について、そのプランを一覧表示する。
3. 指定されたプランについて、その SKU を一覧表示する。

その後、選択された SKU について [Get-AzVMImage](/powershell/module/az.compute/get-azvmimage) を実行して、デプロイするバージョンを一覧表示します。

1. 発行元を一覧表示します。

    ```powershell
    $locName="<Azure location, such as West US>"
    Get-AzVMImagePublisher -Location $locName | Select PublisherName
    ```

2. 選択した発行元の名前を入力して、プランを一覧表示します。

    ```powershell
    $pubName="<publisher>"
    Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
    ```

3. 選択したプランの名前を入力して、SKU を一覧表示します。

    ```powershell
    $offerName="<offer>"
    Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
    ```

4. 選択した SKU の名前を入力して、イメージのバージョンを取得します。

    ```powershell
    $skuName="<SKU>"
    Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
    ```
    
`Get-AzVMImage` コマンドの出力から、バージョン イメージを選択して新しい仮想マシンをデプロイできます。

次の例は、コマンドとその出力の完全な順序を示しています。

```powershell
$locName="West US"
Get-AzVMImagePublisher -Location $locName | Select PublisherName
```

出力の一部を次に示します。

```
PublisherName
-------------
...
abiquo
accedian
accellion
accessdata-group
accops
Acronis
Acronis.Backup
actian-corp
actian_matrix
actifio
activeeon
adgs
advantech
advantech-webaccess
advantys
...
```

*MicrosoftWindowsServer* が発行元の場合:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzVMImageOffer -Location $locName -PublisherName $pubName | Select Offer
```

出力:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServerSemiAnnual
```

*WindowsServer* プランの場合:

```powershell
$offerName="WindowsServer"
Get-AzVMImageSku -Location $locName -PublisherName $pubName -Offer $offerName | Select Skus
```

出力の一部を次に示します。

```
Skus
----
2008-R2-SP1
2008-R2-SP1-smalldisk
2012-Datacenter
2012-Datacenter-smalldisk
2012-R2-Datacenter
2012-R2-Datacenter-smalldisk
2016-Datacenter
2016-Datacenter-Server-Core
2016-Datacenter-Server-Core-smalldisk
2016-Datacenter-smalldisk
2016-Datacenter-with-Containers
2016-Datacenter-with-RDSH
2019-Datacenter
2019-Datacenter-Core
2019-Datacenter-Core-smalldisk
2019-Datacenter-Core-with-Containers
...
```

次に、*2019-Datacenter* SKU の場合:

```powershell
$skuName="2019-Datacenter"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Sku $skuName | Select Version
```

これで、選択した発行元、プラン、SKU、およびバージョンを URN (: で区切られた値) へと結合することができます。 [New-AzVM](/powershell/module/az.compute/new-azvm) コマンドレットで VM を作成するときに、この URN を `--image` パラメーターを使用して渡します。 必要に応じて、URN のバージョン番号を "latest" に置き換えて、最新バージョンのイメージを取得できます。

Resource Manager テンプレートを使って VM をデプロイする場合は、`imageReference` プロパティでイメージ パラメーターを個別に設定します。 [テンプレート リファレンス](/azure/templates/microsoft.compute/virtualmachines)をご覧ください。

[!INCLUDE [virtual-machines-common-marketplace-plan](../../../includes/virtual-machines-common-marketplace-plan.md)]

### <a name="view-plan-properties"></a>プランのプロパティの表示

イメージの購入プラン情報を表示するには、`Get-AzVMImage` コマンドレットを実行します。 出力内の `PurchasePlan` プロパティが `null` ではない場合、イメージには、プログラムによるデプロイの前に同意しなければならない使用条件があります。  

たとえば、*Windows Server 2016 Datacenter* イメージに追加条項がないのは、`PurchasePlan` 情報が `null` であるためです。

```powershell
$version = "2016.127.20170406"
Get-AzVMImage -Location $locName -PublisherName $pubName -Offer $offerName -Skus $skuName -Version $version
```

出力:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/MicrosoftWindowsServer/ArtifactTypes/VMImage/Offers/WindowsServer/Skus/2016-Datacenter/Versions/2019.0.20190115
Location         : westus
PublisherName    : MicrosoftWindowsServer
Offer            : WindowsServer
Skus             : 2019-Datacenter
Version          : 2019.0.20190115
FilterExpression :
Name             : 2019.0.20190115
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : null
DataDiskImages   : []

```

下の例は、*Data Science Virtual Machine - Windows 2016* イメージの同様のコマンドを示しています。次の `PurchasePlan` プロパティが表示されます: `name`、`product`、および `publisher`。 イメージによっては、`promotion code` プロパティもあります。 このイメージをデプロイするには、次のセクションを参照して使用条件に同意し、プログラムによるデプロイを有効にします。

```powershell
Get-AzVMImage -Location "westus" -PublisherName "microsoft-ads" -Offer "windows-data-science-vm" -Skus "windows2016" -Version "0.2.02"
```

出力:

```
Id               : /Subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/Providers/Microsoft.Compute/Locations/westus/Publishers/microsoft-ads/ArtifactTypes/VMImage/Offers/windows-data-science-vm/Skus/windows2016/Versions/19.01.14
Location         : westus
PublisherName    : microsoft-ads
Offer            : windows-data-science-vm
Skus             : windows2016
Version          : 19.01.14
FilterExpression :
Name             : 19.01.14
OSDiskImage      : {
                     "operatingSystem": "Windows"
                   }
PurchasePlan     : {
                     "publisher": "microsoft-ads",
                     "name": "windows2016",
                     "product": "windows-data-science-vm"
                   }
DataDiskImages   : []

```

### <a name="accept-the-terms"></a>使用条件への同意

ライセンス条項を表示するには、[Get-AzMarketplaceterms](/powershell/module/az.marketplaceordering/get-azmarketplaceterms) コマンドレットを使用し、購入プラン パラメーターを渡します。 出力には、Marketplace イメージの使用条件へのリンクと、以前その使用条件に同意したかどうかが表示されます。 パラメーターの値は全小文字で入力してください。

```powershell
Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"
```

出力:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DVM%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : 2UMWH6PHSAIM4U22HXPXW25AL2NHUJ7Y7GRV27EBL6SUIDURGMYG6IIDO3P47FFIBBDFHZHSQTR7PNK6VIIRYJRQ3WXSE6BTNUNENXA
Accepted          : False
Signdate          : 1/25/2019 7:43:00 PM
```

[Set-AzMarketplaceterms](/powershell/module/az.marketplaceordering/set-azmarketplaceterms) コマンドレットを使用して、使用条件に同意するか、または拒否します。 使用条件に同意する必要があるのは、イメージのサブスクリプションごとに 1 回だけです。 パラメーターの値は全小文字で入力してください。 

```powershell
$agreementTerms=Get-AzMarketplaceterms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016"

Set-AzMarketplaceTerms -Publisher "microsoft-ads" -Product "windows-data-science-vm" -Name "windows2016" -Terms $agreementTerms -Accept
```

出力:

```
Publisher         : microsoft-ads
Product           : windows-data-science-vm
Plan              : windows2016
LicenseTextLink   : https://storelegalterms.blob.core.windows.net/legalterms/3E5ED_legalterms_MICROSOFT%253a2DADS%253a24WINDOWS%253a2DDATA%253a2DSCIENCE%253a2DV
                    M%253a24WINDOWS2016%253a24OC5SKMQOXSED66BBSNTF4XRCS4XLOHP7QMPV54DQU7JCBZWYFP35IDPOWTUKXUC7ZAG7W6ZMDD6NHWNKUIVSYBZUTZ245F44SU5AD7Q.txt
PrivacyPolicyLink : https://www.microsoft.com/EN-US/privacystatement/OnlineServices/Default.aspx
Signature         : XXXXXXK3MNJ5SROEG2BYDA2YGECU33GXTD3UFPLPC4BAVKAUL3PDYL3KBKBLG4ZCDJZVNSA7KJWTGMDSYDD6KRLV3LV274DLBXXXXXX
Accepted          : True
Signdate          : 2/23/2018 7:49:31 PM
```



## <a name="next-steps"></a>次のステップ

基本イメージ情報を使用し、`New-AzVM` コマンドレットを使って仮想マシンをすばやく作成するには、「[PowerShell で Windows 仮想マシンを作成する](quick-create-powershell.md)」をご覧ください。

Azure Marketplace イメージを使用して Shared Image Gallery にカスタム イメージを作成する方法の詳細については、[イメージ作成時に Azure Marketplace 購入プラン情報を提供する](../marketplace-images.md)方法に関する記事を参照してください。
