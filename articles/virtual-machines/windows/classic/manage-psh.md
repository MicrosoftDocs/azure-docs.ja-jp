---
title: "Azure PowerShell を使用した仮想マシンの管理 | Microsoft Docs"
description: "仮想マシンの管理タスクを自動化するのに使用できるコマンドについて説明します。"
services: virtual-machines-windows
documentationcenter: windows
author: singhkays
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 7cdf9bd3-6578-4069-8a95-e8585f04a394
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 10/12/2016
ms.author: kasing
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: 44235e32b7cf5b2be90f6d460ac22d7c0350f894
ms.lasthandoff: 03/27/2017


---
# <a name="manage-your-virtual-machines-by-using-azure-powershell"></a>Azure PowerShell を使用した仮想マシンの管理
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。 Resource Manager モデルを使用した一般的な PowerShell コマンドについては、[こちら](../../virtual-machines-windows-ps-common-ref.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)をご覧ください。

Azure PowerShell コマンドレットを使用すると、VM を管理するために日常的に行う多くのタスクを自動化できます。 この記事では、比較的単純なタスクにはコマンド例を示し、より複雑なタスクには、コマンドが記載されている記事へのリンクを示します。

> [!NOTE]
> まだ Azure PowerShell をインストール、構成していない場合は、 [「Azure PowerShell のインストールおよび構成方法」](/powershell/azureps-cmdlets-docs)の記事に手順が記載されています。
> 
> 

## <a name="how-to-use-the-example-commands"></a>サンプル コマンドを使用する方法
コマンド内のテキストの一部を、使用する環境に適した別のテキストに置き換える必要があります。 < および > 記号は置き換える必要があるテキストであることを示しています。 テキストを置き換える場合、記号は削除し、引用符はそのままの位置に残します。

## <a name="get-a-vm"></a>VM の取得
これは頻繁に使用することになる基本的なタスクです。 VM に関する情報の取得、VM でのタスクの実行、変数に格納する出力の取得に使用します。

VM に関する情報を取得するには、このコマンドを実行し、引用符内のすべての文字 (< および > を含む) を置き換えます。

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

出力を $vm 変数に格納するには、次のように実行します。

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="log-on-to-a-windows-based-vm"></a>Windows ベースの VM へのログオン
次の各コマンドを実行します。

> [!NOTE]
> 仮想マシンとクラウド サービス名は、 **Get-azurevm** コマンドの表示から取得できます。
> 
> $svcName = "<cloud service name>" $vmName = "<virtual machine name>" $localPath = "<ダウンロードした RDP ファイルを保存するドライブとファイルの場所、例: c:\temp >" $localFile = $localPath + "\" + $vmname + ".rdp" Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch
> 
> 

## <a name="stop-a-vm"></a>VM の停止
次のコマンドを実行します。

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

> [!IMPORTANT]
> このパラメーターは、VM がクラウド サービス内の最後の VM である場合に、そのクラウド サービスの仮想 IP (VIP) を保持するために使用します。 <br><br> StayProvisioned パラメーターを使用する場合は、その VM に対して引き続き課金されます。
> 
> 

## <a name="start-a-vm"></a>VM の起動
次のコマンドを実行します。

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## <a name="attach-a-data-disk"></a>データ ディスクの接続
このタスクには、いくつかの手順が必要です。 まず、****Add-AzureDataDisk**** コマンドレットを使用して、$vm オブジェクトにディスクを追加します。 次に、 **Update-AzureVM** コマンドレットを使用して、仮想マシンの構成を更新します。

新しいディスクとデータを含むディスクのどちらを接続するかについても決める必要があります。 新しいディスクの場合、コマンドによって .vhd ファイルが作成、接続されます。

新しいディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -CreateNew -DiskSizeInGB 128 -DiskLabel "<main>" -LUN <0> -VM $vm | Update-AzureVM

既存のデータ ディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -Import -DiskName "<MyExistingDisk>" -LUN <0> | Update-AzureVM

BLOB ストレージにある既存の .vhd ファイルからデータ ディスクを接続するには、次のコマンドを実行します。

    Add-AzureDataDisk -ImportFrom -MediaLocation `
              "<https://mystorage.blob.core.windows.net/mycontainer/MyExistingDisk.vhd>" `
              -DiskLabel "<main>" -LUN <0> |
              Update-AzureVM

## <a name="create-a-windows-based-vm"></a>Windows ベースの VM の作成
Azure で Windows ベースの仮想マシンを新たに作成するには、「[Azure PowerShell を使用して Windows ベースの仮想マシンを作成し、事前構成する](create-powershell.md)」をご覧ください。 このトピックでは、次の項目を事前構成することができる Windows ベースの VM を作成するための Azure PowerShell コマンド セットを作成する手順を説明します。

* Active Directory ドメイン メンバーシップ。
* 追加のディスク。
* 既存の負荷分散セットのメンバーとしての設定。
* 静的 IP アドレス。


