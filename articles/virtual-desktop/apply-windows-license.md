---
title: セッション ホスト仮想マシンに Windows ライセンスを適用する - Azure
description: Windows Virtual Desktop VM への Windows ライセンスの適用方法について説明します。
author: ChristianMontoya
ms.topic: how-to
ms.date: 08/14/2019
ms.author: chrimo
ms.openlocfilehash: 5f3749be36f5f035e49fcb862f92180e4902101f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88010142"
---
# <a name="apply-windows-license-to-session-host-virtual-machines"></a>セッション ホスト仮想マシンに Windows ライセンスを適用する

Windows Virtual Desktop のワークロードを実行するライセンスが適切に付与されているお客様は、Windows ライセンスをセッション ホスト仮想マシンに適用し、別のライセンスの料金を支払うことなくそれらを実行できます。 詳細については、「[Windows Virtual Desktop の価格](https://azure.microsoft.com/pricing/details/virtual-desktop/)」を参照してください。

## <a name="ways-to-use-your-windows-virtual-desktop-license"></a>Windows Virtual Desktop ライセンスの使用方法
Windows Virtual Desktop ライセンスを使用すると、ホスト プールにセッション ホストとして登録され、ユーザー接続を受け取る任意の Windows または Windows Server 仮想マシンにライセンスを適用できます。 このライセンスは、ファイル共有サーバー、ドメイン コントローラーなどとして実行されている仮想マシンには適用されません。

Windows Virtual Desktop ライセンスを使用する方法は、いくつかあります。
- [Azure Marketplace オファリング](./create-host-pools-azure-marketplace.md)を使用して、ホスト プールとそのセッション ホスト仮想マシンを作成できます。 この方法で作成された仮想マシンには、ライセンスが自動的に適用されます。
- [GitHub の Azure Resource Manager テンプレート](./virtual-desktop-fall-2019/create-host-pools-arm-template.md)を使用して、ホスト プールとそのセッション ホスト仮想マシンを作成できます。 この方法で作成された仮想マシンには、ライセンスが自動的に適用されます。
- 既存のセッション ホスト仮想マシンにライセンスを適用できます。 これを行うには、まず「[PowerShell を使用してホスト プールを作成する](./create-host-pools-powershell.md)」の手順に従ってホスト プールと関連 VM を作成してから、この記事に戻ってライセンスを適用する方法を確認します。

## <a name="apply-a-windows-license-to-a-session-host-vm"></a>セッション ホスト VM に Windows ライセンスを適用する
[最新の Azure PowerShell がインストールおよび構成](/powershell/azure/)されていることを確認します。 次の PowerShell コマンドレットを実行して、Windows ライセンスを適用します。

```powershell
$vm = Get-AzVM -ResourceGroup <resourceGroupName> -Name <vmName>
$vm.LicenseType = "Windows_Client"
Update-AzVM -ResourceGroupName <resourceGroupName> -VM $vm
```

## <a name="verify-your-session-host-vm-is-utilizing-the-licensing-benefit"></a>セッション ホスト VM がライセンスの特典を利用していることを確認する
VM をデプロイした後、このコマンドレットを実行してライセンスの種類を確認します。
```powershell
Get-AzVM -ResourceGroupName <resourceGroupName> -Name <vmName>
```

Windows ライセンスが適用されたセッション ホスト VM では、次のような内容が表示されます。

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              : Windows_Client
```

Windows ライセンスが適用されていない VM では、次のような内容が表示されます。

```powershell
Type                     : Microsoft.Compute/virtualMachines
Location                 : westus
LicenseType              :
```

Azure サブスクリプションで Windows ライセンスが適用されているすべてのセッション ホスト VM の一覧を表示するには、次のコマンドレットを実行します。

```powershell
$vms = Get-AzVM
$vms | Where-Object {$_.LicenseType -like "Windows_Client"} | Select-Object ResourceGroupName, Name, LicenseType
```
