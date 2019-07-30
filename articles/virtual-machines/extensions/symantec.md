---
title: Azure の Windows VM に Symantec Endpoint Protection をインストールする | Microsoft Docs
description: クラシック デプロイ モデルで作成された新しいまたは既存の Azure VM に Symantec Endpoint Protection のセキュリティ拡張機能をインストールして構成する方法を説明します。
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: roiyz
ms.openlocfilehash: d79e46467c24277200ef72bb64e8c5b7427bf269
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705890"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Windows VM に Symantec Endpoint Protection をインストールし、構成する方法
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../azure-resource-manager/resource-manager-deployment-model.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。

この記事では、Windows Server を実行している既存の仮想マシン (VM) に Symantec Endpoint Protection クライアントをインストールし、構成する方法を説明します。 このクライアントには、ウイルスおよびスパイウェア対策、ファイアウォール、侵入防止などのサービスが含まれています。 このクライアントは、VM エージェントを使用することにより、セキュリティ拡張機能としてインストールできます。

Symantec のオンプレミス ソリューション用サブスクリプションが既にある場合には、それを使用して Azure の仮想マシンを保護できます。 サブスクリプションがない場合には、サインアップして試用サブスクリプションを利用できます。 このソリューションの詳細については、[Microsoft Azure プラットフォームでの Symantec Endpoint Protection][Symantec] に関するページを参照してください。 このページには、Symantec の顧客向けのライセンス情報とクライアントをインストールする方法の説明へのリンクもあります。

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>既存の VM に Symantec Endpoint Protection をインストールする
インストールを開始するには、次の条件を満たしている必要があります。

* Azure PowerShell モジュール Version 0.8.2 以降がコンピューター上に存在すること。 インストールした Azure PowerShell のバージョンは、 **Get-Module azure | format-table version** コマンドで確認できます。 最新バージョンの説明とダウンロード用リンクについては、[Azure PowerShell のインストールおよび構成方法][PS]に関するページを参照してください。 `Add-AzureAccount`を使用して Azure サブスクリプションにログインします。
* Azure 仮想マシンで実行されている VM エージェント。

最初に、VM エージェントが仮想マシンに既にインストールされていることを確認します。 クラウド サービス名と仮想マシン名を入力して、管理者レベルの Azure PowerShell のコマンド プロンプトで、次のコマンドを実行します。 引用符内のすべての文字 (< および > を含む) を置き換えます。

> [!TIP]
> クラウド サービスや仮想マシンの名前がわからない場合は、**Get-AzureVM** を実行します。現在のサブスクリプションに含まれるすべての仮想マシンの名前が表示されます。

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

**write-host** コマンドで **True** が表示される場合は、VM エージェントがインストールされています。 **False** が表示される場合は、Azure ブログの [VM エージェントと拡張機能に関する記事のパート 2][Agent] で、手順とダウンロード用リンクをご確認ください。

VM エージェントがインストールされている場合、これらのコマンドを実行して Symantec Endpoint Protection エージェントをインストールします。

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Symantec のセキュリティ拡張機能がインストールされ、最新の状態であることを確認するには:

1. 仮想マシンにログオンします。 詳細については、「[Windows Server が実行されている仮想マシンにログオンする方法][Logon]」を参照してください。
2. Windows Server 2008 R2 の場合は、 **[スタート]、[Symantec Endpoint Protection]** の順にクリックします。 Windows Server 2012 または Windows Server 2012 R2 で、スタート画面から、「**Symantec**」と入力し、 **[Symantec Endpoint Protection]** をクリックします。
3. **[状態: Symantec Endpoint Protection]** ウィンドウの **[状態]** タブで、更新を適用するか、必要に応じて再起動します。

## <a name="additional-resources"></a>その他のリソース
[Windows Server が実行されている仮想マシンにログオンする方法][Logon]

[Azure VM 拡張機能とその機能][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
