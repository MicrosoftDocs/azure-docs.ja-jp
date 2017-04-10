---
title: "VM に Trend Micro Deep Security をインストールする | Microsoft Docs"
description: "この記事では、Azure でクラシック デプロイ モデルを使用して作成された VM で Trend Micro のセキュリティをインストールして構成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 02/09/2017
ms.author: iainfou
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: cb10c04d76e957f88d67a791e3b4a19797704eea
ms.lasthandoff: 03/27/2017


---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Windows VM に Trend Micro Deep Security をサービスとしてインストールし、構成する方法
> [!IMPORTANT] 
> Azure には、リソースの作成と操作に関して、 [Resource Manager とクラシック](../../../resource-manager-deployment-model.md)の 2 種類のデプロイメント モデルがあります。 この記事では、クラシック デプロイ モデルの使用方法について説明します。 最新のデプロイでは、リソース マネージャー モデルを使用することをお勧めします。

この記事では、Windows Server を実行している既存の仮想マシン (VM) または新しい VM に Trend Micro Deep Security as a Service をインストールして構成する方法を説明します。 Deep Security as a Service には、ウイルス対策、ファイアウォール、侵入防止システム、変更監視が含まれています。

このクライアントは、VM エージェントを使用することにより、セキュリティ拡張機能としてインストールされます。 新しい仮想マシンの場合には、Deep Security Agent と同時に VM エージェントをインストールします。 既存の仮想マシンで、VM エージェントがインストールされていない場合には、最初に VM エージェントをダウンロードしてインストールする必要があります。 この記事では、この両方の場合について説明します。

Trend Micro のオンプレミスのソリューション用サブスクリプションが既にある場合には、それを使用して Azure の仮想マシンを保護できます。 サブスクリプションがない場合には、サインアップして試用サブスクリプションを利用できます。 このソリューションの詳細については、Trend Micro ブログの Deep Security 用の [Microsoft Azure VM エージェント拡張機能](http://go.microsoft.com/fwlink/p/?LinkId=403945)に関する投稿を参照してください。

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>新しい VM に Deep Security Agent をインストールする
[Azure クラシック ポータル](http://manage.windowsazure.com) を使用すると、 **[ギャラリーから]** オプションを使用して仮想マシンを作成する際に、VM エージェントと Trend Micro のセキュリティ拡張機能をインストールできます。 仮想マシンを 1 つだけ作成する場合には、この方法が Trend Micro の保護機能を追加する最も簡単な方法です。

この **[ギャラリーから]** をクリックすると、仮想マシンの設定を支援するウィザードが起動します。 ウィザードの最後のページで、VM エージェントと Trend Micro のセキュリティ拡張機能をインストールします。 一般的な手順については、「[Windows を実行する仮想マシンを Azure クラシック ポータルで作成する](tutorial.md)」をご覧ください。 ウィザードの最後のページで、次の手順を実行します。

1. **[VM エージェント]** で **[VM エージェントをインストールする]** チェック ボックスをオンにします。
2. **[セキュリティ拡張機能]** で **[Trend Micro Deep Security エージェント]** チェック ボックスをオンにします。
   
   ![VM エージェントと Deep Security エージェントのインストール](./media/install-trend/InstallVMAgentandTrend.png)
3. チェック マークをクリックして、仮想マシンを作成します。

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>既存の VM に Deep Security Agent をインストールする
既存の VM にエージェントをインストールするには、次の条件が必要です。

* Azure PowerShell モジュール Version 0.8.2 以降がローカル コンピューターにインストールされていること。 インストールした Azure PowerShell のバージョンは、 **Get-Module azure | format-table version** コマンドを使用して確認できます。 最新バージョンの説明とダウンロード用リンクについては、「 [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)」を参照してください。 `Add-AzureAccount`を使用して Azure サブスクリプションにログインします。
* VM エージェントがターゲットの仮想マシンにインストールされていること。

最初に、VM エージェントがインストールされていることを確認します。 クラウド サービス名と仮想マシン名を入力して、管理者レベルの Azure PowerShell のコマンド プロンプトで、次のコマンドを実行します。 引用符内のすべての文字 (< および > を含む) を置き換えます。

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

クラウド サービスや仮想マシンの名前がわからない場合は、**Get-AzureVM** を実行します。現在のサブスクリプションのすべての仮想マシンの情報が表示されます。

**write-host** コマンドによって **True** が返された場合は、VM エージェントがインストールされています。 **False**が返された場合は、Azure ブログの [VM エージェントと拡張機能に関する記事のパート 2](http://go.microsoft.com/fwlink/p/?LinkId=403947)で、手順とダウンロード用リンクをご確認ください。

VM エージェントがインストールされている場合は、次のコマンドを実行します。

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>次のステップ
エージェントがインストールされると、起動までに数分かかります。 起動後、仮想マシン上で Deep Security をアクティブにする必要があります。これは Deep Security Manager で管理できるようにするためです。 詳しくは、次の記事をご覧ください。

* このソリューションに関する Trend の記事「 [Instant-On Cloud Security for Microsoft Azure (Microsoft Azure 用の Instant-On クラウド セキュリティ)](http://go.microsoft.com/fwlink/?LinkId=404101)
* 仮想マシンを構成するための [Windows PowerShell サンプル スクリプト](http://go.microsoft.com/fwlink/?LinkId=404100)
* [説明](http://go.microsoft.com/fwlink/?LinkId=404099) 

## <a name="additional-resources"></a>その他のリソース
[Windows Server が実行されている仮想マシンにログオンする方法]

[Azure VM 拡張機能とその機能]

<!--Link references-->
[Windows Server が実行されている仮想マシンにログオンする方法]:connect-logon.md
[Azure VM 拡張機能とその機能]: http://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409

