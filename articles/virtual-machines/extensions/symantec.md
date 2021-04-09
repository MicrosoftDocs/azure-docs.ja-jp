---
title: Azure の Windows VM に Symantec Endpoint Protection をインストールする
description: クラシック デプロイ モデルで作成された新しいまたは既存の Azure VM に Symantec Endpoint Protection のセキュリティ拡張機能をインストールして構成する方法を説明します。
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/31/2017
ms.openlocfilehash: 9a25e9eb27111a450f787f4efb3e0d39456fc757
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559649"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Windows VM に Symantec Endpoint Protection をインストールし、構成する方法
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

Azure には、リソースの作成と操作に関して、2 種類のデプロイ モデルがあります。[Resource Manager とクラシック](../../azure-resource-manager/management/deployment-models.md)です。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイメントでは、リソース マネージャー モデルを使用することをお勧めします。

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
2. Windows Server 2008 R2 の場合は、**[スタート]、[Symantec Endpoint Protection]** の順にクリックします。 Windows Server 2012 または Windows Server 2012 R2 で、スタート画面から、「**Symantec**」と入力し、**[Symantec Endpoint Protection]** をクリックします。
3. **[状態: Symantec Endpoint Protection]** ウィンドウの **[状態]** タブで、更新を適用するか、必要に応じて再起動します。

## <a name="additional-resources"></a>その他のリソース
[Windows Server が実行されている仮想マシンにログオンする方法][Logon]

[Azure VM 拡張機能とその機能][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azure/

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: features-windows.md
