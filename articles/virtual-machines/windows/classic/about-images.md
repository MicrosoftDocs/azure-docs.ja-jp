---
title: "Windows 仮想マシンのイメージについて | Microsoft Docs"
description: "Azure で Windows 仮想マシンのイメージを使用する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: cynthn
manager: timlt
editor: tysonn
tags: azure-service-management
ms.assetid: 66ff3fab-8e7f-4dff-b8da-ab1c9c9c9af8
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 03/20/2017
ms.author: cynthn
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: d421cee0becabdf81d865036d0c98b12b077152b
ms.lasthandoff: 03/27/2017


---
# <a name="about-images-for-windows-virtual-machines"></a>Windows 仮想マシンのイメージについて
> [!IMPORTANT]
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルでのイメージの検索と使用の詳細については、[こちら](../../virtual-machines-windows-cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。

[!INCLUDE [virtual-machines-common-classic-about-images](../../../../includes/virtual-machines-common-classic-about-images.md)]

## <a name="working-with-images"></a>イメージの操作

Azure サブスクリプションに使用できるイメージの管理には、Azure PowerShell モジュールと Azure Portal を使用することができます。 Azure PowerShell モジュールの方が多くのコマンド オプションを提供しているため、参照対象や実行対象を厳密に特定できます。 Azure Portal には、日常的な管理タスクの多くに対して GUI が用意されています。

Azure PowerShell モジュールを使用する例をいくつか示します。

* **すべてのイメージを取得する**: `Get-AzureVMImage` は、現在のサブスクリプションで使用できるすべてのイメージ (自分のイメージと Azure またはパートナーが提供しているイメージ) の一覧を返します。 結果のリストは大きくなる可能性があります。 次の例では、取得する一覧を小さくする方法を示します。
* **イメージのファミリを取得する**: `Get-AzureVMImage | select ImageFamily` は、**ImageFamily** プロパティの文字列を表示して、イメージのファミリの一覧を取得します。
* **特定ファミリ内のすべてのイメージを取得する**: `Get-AzureVMImage | Where-Object {$_.ImageFamily -eq $family}`
* **VM イメージの検索**: `Get-AzureVMImage | where {(gm –InputObject $_ -Name DataDiskConfigurations) -ne $null} | Select -Property Label, ImageName` このコマンドレットは、VM イメージにのみ適用される DataDiskConfiguration プロパティをフィルター処理します。 この例では、ラベルとイメージの名前のみの出力もフィルタリングします。
* **一般化されたイメージを保存する**: `Save-AzureVMImage –ServiceName "myServiceName" –Name "MyVMtoCapture" –OSState "Generalized" –ImageName "MyVmImage" –ImageLabel "This is my generalized image"`
* **専用のイメージを保存する**: `Save-AzureVMImage –ServiceName "mySvc2" –Name "MyVMToCapture2" –ImageName "myFirstVMImageSP" –OSState "Specialized" -Verbose`

  > [!TIP]
  > オペレーティング システム ディスクと接続されたデータ ディスクを含む VM イメージを作成する場合は、OSState パラメーターが必要です。 パラメーターを使用しない場合、コマンドレットは、OS イメージを作成します。 パラメーターの値は、オペレーティング システム ディスクを再利用できるように準備されているかどうかによって、イメージを一般化するか、または専用化するかを示しています。

* **イメージを削除する**: `Remove-AzureVMImage –ImageName "MyOldVmImage"`

## <a name="next-steps"></a>次のステップ
[Azure Portal を使用して Windows マシンを作成する](tutorial.md)こともできます。

