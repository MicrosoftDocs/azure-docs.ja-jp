---
title: "Windows VM イメージへの移動と選択 | Microsoft Docs"
description: "リソース マネージャー デプロイ モデルで Windows 仮想マシンを作成するときに、イメージの発行元、プラン、および SKU を決定する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: squillace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 188b8974-fabd-4cd3-b7dc-559cbb86b98a
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 08/23/2016
ms.author: rasquill
translationtype: Human Translation
ms.sourcegitcommit: 2ae1ac5b56b127a371815ebedac45ab02df8817a
ms.openlocfilehash: ece2fa5032147a0062b4e23b7b8d498a6fbf3d61
ms.lasthandoff: 02/22/2017


---
# <a name="navigate-and-select-windows-virtual-machine-images-in-azure-with-powershell"></a>Powershell を使用した Azure での Windows 仮想マシン イメージへの移動と選択
このトピックでは、デプロイする可能性のある場所ごとに、VM イメージの発行元、プラン、SKU、およびバージョンを検索する方法について説明します。 たとえば、次のような Windows VM イメージが一般的に使用されます。

## <a name="table-of-commonly-used-windows-images"></a>一般的に使用される Windows イメージの表
| 発行元 | プラン | SKU |
|:--- |:--- |:--- |:--- |
| MicrosoftDynamicsNAV |DynamicsNAV |2015 |
| MicrosoftSharePoint |MicrosoftSharePointServer |2013 |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-DW |
| MicrosoftSQLServer |SQL2014-WS2012R2 |Enterprise-Optimized-for-OLTP |
| MicrosoftWindowsServer |WindowsServer |2012-R2-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2012-Datacenter |
| MicrosoftWindowsServer |WindowsServer |2008-R2-SP1 |
| MicrosoftWindowsServer |WindowsServer |Windows-Server-Technical-Preview |
| MicrosoftWindowsServerEssentials |WindowsServerEssentials |WindowsServerEssentials |
| MicrosoftWindowsServerHPCPack |WindowsServerHPCPack |2012R2 |

## <a name="find-azure-images-with-powershell"></a>PowerShell を使った Azure イメージの検索
> [!NOTE]
> [最新の Azure PowerShell](/powershell/azureps-cmdlets-docs)をインストールして構成します。 バージョン 1.0 より前の Azure PowerShell を使用する場合でも以下のコマンドを使用できますが、初めに `Switch-AzureMode AzureResourceManager`を実行する必要があります。 
> 
> 

Azure リソース マネージャーを使用して新しい仮想マシンを作成するとき、場合によっては、以下のイメージ プロパティの組み合わせによりイメージを指定する必要があります。

* 発行元
* プラン
* SKU

たとえば、これらの値は、 `Set-AzureRMVMSourceImage` PowerShell コマンドレットや、作成する仮想マシンの種類を指定する必要のあるリソース グループ テンプレート ファイルで必要となります。

これらの値を判別する必要がある場合は、以下の方法で、イメージをナビゲートしてそれらの値を判別できます。

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

`Get-AzureRMVMImageSku` コマンドの表示から、新しい仮想マシンのイメージを指定するために必要なすべての情報が得られます。

完全な例を次に示します。

```powershell
PS C:\> $locName="West US"
PS C:\> Get-AzureRMVMImagePublisher -Location $locName | Select PublisherName

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
PS C:\> $pubName="MicrosoftWindowsServer"
PS C:\> Get-AzureRMVMImageOffer -Location $locName -Publisher $pubName | Select Offer

Offer
-----
WindowsServer
```

"WindowsServer" プランの場合:

```powershell
PS C:\> $offerName="WindowsServer"
PS C:\> Get-AzureRMVMImageSku -Location $locName -Publisher $pubName -Offer $offerName | Select Skus

Skus
----
2008-R2-SP1
2012-Datacenter
2012-R2-Datacenter
2016-Nano-Server-Technical-Previe
2016-Technical-Preview-with-Conta
Windows-Server-Technical-Preview
```

この一覧から選択した SKU の名前をコピーすれば、 `Set-AzureRMVMSourceImage` PowerShell コマンドレットやリソース グループ テンプレートに必要なすべての情報が得られます。

## <a name="next-steps"></a>次のステップ
これで、使用するイメージを正確に選択できます。 検出したイメージ情報を使用して仮想マシンをすばやく作成する方法や、そのイメージ情報のあるテンプレートを使用する方法については、「[Resource Manager と PowerShell を使用して Windows VM を作成する](virtual-machines-windows-ps-create.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」をご覧ください。
