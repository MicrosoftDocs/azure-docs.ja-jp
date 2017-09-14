---
title: "Azure で Windows VM イメージを選択する | Microsoft Docs"
description: "Azure PowerShell を使用して、Marketplace VM イメージの発行元、プラン、SKU、バージョンを判別する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/12/2017
ms.author: danlep
ms.translationtype: HT
ms.sourcegitcommit: bde1bc7e140f9eb7bb864c1c0a1387b9da5d4d22
ms.openlocfilehash: 630f555b003b0efc45b372a7009dbf036aa8c737
ms.contentlocale: ja-jp
ms.lasthandoff: 07/21/2017

---
# <a name="how-to-find-windows-vm-images-in-the-azure-marketplace-with-azure-powershell"></a>Azure PowerShell を使用して Azure Marketplace で Windows VM イメージを検索する方法

このトピックでは、Azure PowerShell を使用して Azure Marketplace 内で VM イメージを検索する方法について説明します。 この情報は、Windows VM の作成時に Marketplace イメージを指定するためにご利用ください。

最新の [Azure PowerShell モジュール](/powershell/azure/install-azurerm-ps)がインストールおよび構成されていることを確認してください。



## <a name="table-of-commonly-used-windows-images"></a>一般的に使用される Windows イメージの表
| 発行元 | プラン | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-Server-Core |
| MicrosoftWindowsServer |WindowsServer |2016-Datacenter-with-Containers |
| MicrosoftWindowsServer |WindowsServer |2016-Nano-Server |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftDynamicsNAV |DynamicsNAV |2017 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2016 |
| MicrosoftSQLServer |SQL2016-WS2016 |Enterprise |
| MicrosoftSQLServer |SQL2014SP2-WS2012R2 |Enterprise |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |

## <a name="find-specific-images"></a>特定のイメージを検索する


Azure リソース マネージャーを使用して新しい仮想マシンを作成するとき、場合によっては、以下のイメージ プロパティの組み合わせによりイメージを指定する必要があります。

* 発行元
* プラン
* SKU

たとえば、これらの値は、[Set-AzureRMVMSourceImage](/powershell/module/azurerm.compute/set-azurermvmsourceimage) PowerShell コマンドレットや、作成する VM の種類を指定する必要のあるリソース グループのテンプレートで使用します。

これらの値を特定する必要がある場合は、[Get-AzureRMVMImagePublisher](/powershell/module/azurerm.compute/get-azurermvmimagepublisher)、[Get-AzureRMVMImageOffer](/powershell/module/azurerm.compute/get-azurermvmimageoffer)、[Get-AzureRMVMImageSku](/powershell/module/azurerm.compute/get-azurermvmimagesku) コマンドレットを実行してイメージを移動します。 次のような値を特定できます。

1. イメージの発行元を一覧表示する。
2. 指定された発行元について、そのプランを一覧表示する。
3. 指定されたプランについて、その SKU を一覧表示する。

最初に、次のコマンドを使用して発行元を一覧表示します。

```powershell
$locName="<Azure location, such as West US>"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName
```

選択した発行元の名前を入力して、次のコマンドを実行します。

```powershell
$pubName="<publisher>"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

選択したプランの名前を入力して、次のコマンドを実行します。

```powershell
$offerName="<offer>"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

`Get-AzureRMVMImageSku` コマンドの出力から、新しい仮想マシンのイメージを指定するために必要なすべての情報が得られます。

完全な例を次に示します。

```powershell
$locName="West US"
Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

```

出力:

```
PublisherName
-------------
a10networks
aiscaler-cache-control-ddos-and-url-rewriting-
alertlogic
AlertLogic.Extension
Barracuda.Azure.ConnectivityAgent
barracudanetworks
basho
boxless
bssw
Canonical
...
```

"MicrosoftWindowsServer" が発行元の場合:

```powershell
$pubName="MicrosoftWindowsServer"
Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer
```

出力:

```
Offer
-----
Windows-HUB
WindowsServer
WindowsServer-HUB
```

"WindowsServer" プランの場合:

```powershell
$offerName="WindowsServer"
Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus
```

出力:

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
2016-Nano-Server
```

この一覧から選択した SKU の名前をコピーすれば、 `Set-AzureRMVMSourceImage` PowerShell コマンドレットやリソース グループ テンプレートに必要なすべての情報が得られます。

## <a name="next-steps"></a>次のステップ
これで、使用するイメージを正確に選択できます。 今調べたイメージの情報を使用して仮想マシンをすばやく作成するには、「[PowerShell で Windows 仮想マシンを作成する](quick-create-powershell.md)」をご覧ください。

