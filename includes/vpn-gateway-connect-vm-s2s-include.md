---
title: インクルード ファイル
description: インクルード ファイル
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/01/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 820a6a4da9f5c466e694f247d09393474d8464ee
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/19/2019
ms.locfileid: "56418288"
---
リモート デスクトップ接続を作成すると、VNet にデプロイされている VM に接続できます。 VM に接続できるかどうかを初めて確認する際に最も良い方法は、その VM のコンピューター名ではなく、プライベート IP アドレスを使って接続してみることです。 この方法であれば、名前の解決が適切に構成されているかではなく、VM に接続できるかどうかをテストすることができます。

1. プライベート IP アドレスを特定します。 VM のプライベート IP アドレスは、複数の方法で見つけることができます。 次に、Azure ポータルと PowerShell での手順を示します。

   - Azure Portal を使用する場合: Azure Portal で仮想マシンを探します。 VM のプロパティを表示すると、 プライベート IP アドレスが表示されます。

   - PowerShell を使用する場合: 以下の例に示したコマンドを使用すると、リソース グループに含まれる VM とプライベート IP アドレスの一覧が表示されます。 このコマンドは、使用前に変更を加える必要はありません。

     ```azurepowershell-interactive
     $VMs = Get-AzVM
     $Nics = Get-AzNetworkInterface | Where VirtualMachine -ne $null

     foreach($Nic in $Nics)
     {
      $VM = $VMs | Where-Object -Property Id -eq $Nic.VirtualMachine.Id
      $Prv = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAddress
      $Alloc = $Nic.IpConfigurations | Select-Object -ExpandProperty PrivateIpAllocationMethod
      Write-Output "$($VM.Name): $Prv,$Alloc"
     }
     ```

2. VPN 接続を使って VNet に接続していることを確認します。
3. タスク バーの検索ボックスに「RDP」または「リモート デスクトップ接続」と入力してリモート デスクトップ接続を開き、**リモート デスクトップ接続**を選択します。 このほか、PowerShell で "mstsc" コマンドを使ってリモート デスクトップ接続を開くこともできます。 
4. リモート デスクトップ接続で、VM のプライベート IP アドレスを入力します。 必要に応じて [オプションの表示] をクリックして追加の設定を済ませたら、接続します。

### <a name="to-troubleshoot-an-rdp-connection-to-a-vm"></a>VM に対する RDP 接続をトラブルシューティングするには

VPN 接続を使って仮想マシンに接続する際に問題が発生した場合には、次のことを確認してください。

- VPN 接続が成功したことを確認します。
- VM のプライベート IP アドレスに接続できていることを確認します。
- プライベート IP アドレスを使って VM に接続できるものの、コンピューター名では接続できない場合には、DNS が正しく構成されているかどうかを確認します。 VM の名前解決の動作について詳しくは、[VM の名前解決](../articles/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md)に関するページを参照してください。
- 詳細については、[VM に対するリモート デスクトップ接続のトラブルシューティング](../articles/virtual-machines/windows/troubleshoot-rdp-connection.md)に関するページを参照してください。