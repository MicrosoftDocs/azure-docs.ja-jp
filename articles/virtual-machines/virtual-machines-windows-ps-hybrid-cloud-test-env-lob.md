---
title: "LOB アプリケーションのテスト環境 | Microsoft Docs"
description: "IT プロまたは開発のテスト用のハイブリッド クラウド環境で Web ベースの基幹業務アプリケーションを作成する方法について説明します。"
services: virtual-machines-windows
documentationcenter: 
author: JoeDavies-MSFT
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 92d2d8ce-60ed-4512-95e5-a7fe3b0ca00b
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 09/30/2016
ms.author: josephd
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: c90b3ca27877babb5b1f82aa810d0197417f7e70


---
# <a name="set-up-a-web-based-lob-application-in-a-hybrid-cloud-for-testing"></a>テスト用のハイブリッド クラウドでの Web ベース LOB アプリケーションの設定
このトピックでは、Microsoft Azure でホストされる Web ベースの基幹業務 (LOB) アプリケーションをテストするためにシミュレートされたハイブリッド クラウド環境を作成する手順について説明します。 完成すると次のような構成になります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

構成は次のとおりです。

* Azure でホストされているシミュレートされたオンプレミスのネットワーク (TestLab VNet)。
* Azure でホストされているクロスプレミス仮想ネットワーク (TestVNET)。
* VNet 間 VPN 接続。
* TestVNET 仮想ネットワーク内の Web ベース LOB サーバー、SQL Server、セカンダリ ドメイン コントローラー。

この構成を基盤や共通の出発点にして以下を実行できます。

* Azure の SQL Server 2014 データベース バックエンドを使用してインターネット インフォメーション サービス (IIS) でホストされている LOB アプリケーションを開発し、テストする。
* このシミュレートされたハイブリッド クラウドをベースとした IT ワークロードのテストを実行する。

このハイブリッド クラウド テスト環境を設定する手順は、大きく次の 3 つのフェーズに分かれています。

1. シミュレートされたハイブリッド クラウド環境を設定する。
2. SQL Server コンピューター (SQL1) を構成する。
3. LOB サーバー (LOB1) を構成する。

このワークロードには、Azure サブスクリプションが必要です。 MSDN または Visual Studio サブスクリプションをお持ちの場合は、「 [Visual Studio サブスクライバー向けの月単位の Azure クレジット](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/)」を参照してください。

Azure でホストされる運用 LOB アプリケーションの例については、「 **アーキテクチャ ブループリント** 」で [基幹業務アプリケーション](http://msdn.microsoft.com/dn630664)のアーキテクチャ ブループリントをご覧ください。

## <a name="phase-1-set-up-the-simulated-hybrid-cloud-environment"></a>フェーズ 1: シミュレートされたハイブリッド クラウド環境を設定する
[シミュレートされたハイブリッド クラウド テスト環境](virtual-machines-windows-ps-hybrid-cloud-test-env-sim.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)を作成します。 このテスト環境では Corpnet サブネット上に APP1 サーバーを配置する必要がないため、シャットダウンできます。

現在の構成は次のようになります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph1.png)

## <a name="phase-2-configure-the-sql-server-computer-sql1"></a>フェーズ 2: SQL Server コンピューター (SQL1) を構成する
DC2 コンピューターが起動されていない場合は、Azure ポータルから起動します。

続いて、ローカル コンピューターで Azure PowerShell コマンド プロンプトから次のコマンドを実行して、SQL1 用に仮想マシンを作成します。 これらのコマンドを実行する前に、変数の値を入力し、< と > の文字を削除します。

    $rgName="<your resource group name>"
    $locName="<the Azure location of your resource group>"
    $saName="<your storage account name>"

    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name SQL1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName SQL1 -VMSize Standard_A4
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-SQLDataDisk.vhd"
    Add-AzureRMVMDataDisk -VM $vm -Name "Data" -DiskSizeInGB 100 -VhdUri $vhdURI  -CreateOption empty

    $cred=Get-Credential -Message "Type the name and password of the local administrator account for the SQL Server computer." 
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName SQL1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftSQLServer -Offer SQL2014-WS2012R2 -Skus Standard -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/SQL1-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name "OSDisk" -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

Azure ポータルを使用して、SQL1 のローカル管理者アカウントで SQL1 に接続します。

続いて、基本的な接続テストと SQL Server トラフィックを許可するように Windows ファイアウォールの規則を構成します。 SQL1 で管理者レベルの Windows PowerShell コマンド プロンプトから次のコマンドを実行します。

    New-NetFirewallRule -DisplayName "SQL Server" -Direction Inbound -Protocol TCP -LocalPort 1433,1434,5022 -Action allow 
    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

ping コマンドで IP アドレス 192.168.0.4 からの応答が 4 回成功する必要があります。

次に、SQL1 で余っているデータ ディスクを新しいボリュームとして追加し、ドライブ文字 F: を割り当てます。

1. サーバー マネージャーの左側のウィンドウで、**[ファイル サービスと記憶域サービス]** をクリックし、**[ディスク]** をクリックします。
2. コンテンツ ウィンドウの **[ディスク]** グループで、(**[パーティション]** が **[不明]** に設定されている) **[ディスク 2]** をクリックします。
3. **[タスク]** をクリックし**[ボリューム]** をクリックします。
4. 新しいボリューム ウィザードの [開始する前に] ページで **[次へ]**をクリックします。
5. [サーバーとディスクの選択] ページで、**[ディスク 2]** をクリックし、**[次へ]** をクリックします。 メッセージが表示されたら、 **[OK]**をクリックします。
6. [ボリュームのサイズの指定] ページで、 **[次へ]**をクリックします。
7. [ドライブ文字またはフォルダーへの割り当て] ページで、 **[次へ]**をクリックします。
8. [ファイル システム形式の選択] ページで、 **[次へ]**をクリックします。
9. [選択内容の確認] ページで、 **[作成]**をクリックします。
10. 作成されたら、 **[閉じる]**をクリックします。

SQL1 の Windows PowerShell コマンド プロンプトで、次のコマンドを実行します。

    md f:\Data
    md f:\Log
    md f:\Backup

次に、SQL1 で Windows PowerShell プロンプトでこれらのコマンドを使用して、CORP Windows Server Active Directory ドメインに SQL1 を参加させます。

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

**Add-Computer** コマンドでドメイン アカウントの資格情報を入力するよう求められたら、CORP\User1 アカウントを使用します。

再起動したら、Azure ポータルで "SQL1 のローカル管理者アカウント" **を使用して SQL1 に接続します。

次に、新しいデータベースとユーザー アカウント アクセス許可に F: ドライブを使用するように SQL Server 2014 を構成します。

1. スタート画面で「**SQL Server Management**」と入力し、**[SQL Server 2014 Management Studio]** をクリックします。
2. **[サーバーに接続]** で、**[接続]** をクリックします。
3. [オブジェクト エクスプローラー] ツリー ウィンドウで、 **SQL1** **[プロパティ]**」をご覧ください。
4. **[サーバーのプロパティ]** ウィンドウで、**[データベースの設定]** をクリックします。
5. **[データベースの既定の場所]** で、次の値を設定します。 
   * **[データ]** に、パス「**f:\Data**」を入力します。
   * **[ログ]** に、パス「**f:\Log**」を入力します。
   * **[バックアップ]** に、パス「**f:\Backup**」を入力します。
   * 注: 新しいデータベースでのみ、これらの場所が使用されます。
6. **[OK]** をクリックして、ウィンドウを閉じます。
7. **[オブジェクト エクスプローラー]** ツリー ウィンドウで、**[セキュリティ]** を開きます。
8. **[ログイン]** を右クリックし、**[新しいログイン]** をクリックします。
9. **[ログイン名]** に「**CORP\User1**」と入力します。
10. **[サーバーの役割]** ページで、**[sysadmin]** をクリックし、**[OK]** をクリックします。
11. Microsoft SQL Server Management Studio を閉じます。

現在の構成は次のようになります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph2.png)

## <a name="phase-3-configure-the-lob-server-lob1"></a>フェーズ 3: LOB サーバー (LOB1) を構成する
まず、ローカル コンピューターの Azure PowerShell コマンド プロンプトで次のコマンドを使用して、LOB1 用に仮想マシンを作成します。

    $rgName="<your resource group name>"
    $locName="<your Azure location, such as West US>"
    $saName="<your storage account name>"

    $vnet=Get-AzureRMVirtualNetwork -Name "TestVNET" -ResourceGroupName $rgName
    $subnet=Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name "TestSubnet"
    $pip=New-AzureRMPublicIpAddress -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
    $nic=New-AzureRMNetworkInterface -Name LOB1-NIC -ResourceGroupName $rgName -Location $locName -Subnet $subnet -PublicIpAddress $pip
    $vm=New-AzureRMVMConfig -VMName LOB1 -VMSize Standard_A2
    $storageAcc=Get-AzureRMStorageAccount -ResourceGroupName $rgName -Name $saName
    $cred=Get-Credential -Message "Type the name and password of the local administrator account for LOB1."
    $vm=Set-AzureRMVMOperatingSystem -VM $vm -Windows -ComputerName LOB1 -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
    $vm=Set-AzureRMVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
    $vm=Add-AzureRMVMNetworkInterface -VM $vm -Id $nic.Id
    $osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/LOB1-TestLab-OSDisk.vhd"
    $vm=Set-AzureRMVMOSDisk -VM $vm -Name LOB1-TestVNET-OSDisk -VhdUri $osDiskUri -CreateOption fromImage
    New-AzureRMVM -ResourceGroupName $rgName -Location $locName -VM $vm

次に、Azure ポータルで LOB1 のローカル管理者アカウントの資格情報を使用して、LOB1 に接続します。

続いて、基本的な接続テストを行うためのトラフィックを許可するよう Windows ファイアウォール規則を構成します。 LOB1 で管理者レベルの Windows PowerShell コマンド プロンプトから、次のコマンドを実行します。

    Set-NetFirewallRule -DisplayName "File and Printer Sharing (Echo Request - ICMPv4-In)" -enabled True
    ping dc2.corp.contoso.com

ping コマンドで IP アドレス 192.168.0.4 からの応答が 4 回成功する必要があります。

次に、Windows PowerShell プロンプトでこれらのコマンドを使用して、CORP Active Directory ドメインに LOB1 を参加させます。

    Add-Computer -DomainName corp.contoso.com
    Restart-Computer

**Add-Computer** コマンドでドメイン アカウントの資格情報を入力するよう求められたら、CORP\User1 アカウントを使用します。

再起動したら、Azure ポータルで CORP\User1 アカウントとパスワードを使って LOB1 に接続します。

次に、IIS 用に LOB1 を構成し、CLIENT1 からアクセスをテストします。

1. サーバー マネージャーで、 **[役割と機能の追加]**をクリックします。
2. **[開始する前に]** ページで **[次へ]** をクリックします。
3. **[インストールの種類の選択]** ページで **[次へ]** をクリックします。
4. **[対象サーバーの選択]** ページで **[次へ]** をクリックします。
5. **[サーバーの役割]** ページの **[役割]** の一覧で、**[Web サーバー (IIS)]** をクリックします。
6. メッセージが表示されたら、**[機能の追加]** をクリックし、**[次へ]** をクリックします。
7. **[機能の選択]** ページで **[次へ] **をクリックします。
8. **[Web サーバー (IIS)]** ページで **[次へ]** をクリックします。
9. **[役割サービスの選択]** ページで、LOB アプリケーションをテストするための必要に応じて、サービスのチェック ボックスをオンまたはオフにし、 **[次へ]** をクリックします。
10. **[インストール オプションの確認]** ページで、**[インストール]** をクリックします。
11. コンポーネントのインストールが完了するまで待ってから、 **[閉じる]**をクリックします。
12. Azure ポータルで、CORP\User1 アカウントの資格情報を使用して CLIENT1 コンピューターに接続し、Internet Explorer を起動します。
13. アドレス バーに「**http://lob1/**」と入力し、Enter キーを押します。 既定の IIS 8 Web ページが表示されます。

現在の構成は次のようになります。

![](./media/virtual-machines-windows-ps-hybrid-cloud-test-env-lob/virtual-machines-windows-ps-hybrid-cloud-test-env-lob-ph3.png)

この環境で、Web ベース アプリケーションを LOB1 にデプロイし、Corpnet サブネットの CLIENT1 から機能をテストできます。

## <a name="next-step"></a>次のステップ
* [Azure Portal](virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) を使用して新しい仮想マシンを追加します。




<!--HONumber=Nov16_HO3-->


