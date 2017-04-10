---
title: "Azure Virtual Network での Active Directory フォレストのインストール | Microsoft Docs"
description: "Azure Virtual Network 上の仮想マシン (VM) に新しい Active Directory フォレストを作成する手順について説明したチュートリアルです。"
services: active-directory, virtual-network
keywords: "Active Directory 仮想マシン, Active Directory フォレストのインストール, Azure Active Directory ビデオ  "
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
tags: 
ms.assetid: eb7170d0-266a-4caa-adce-1855589d65d1
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/06/2017
ms.author: joflore
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: 46e248112985b2e8f58f90e15cf885839d3cfcc8
ms.lasthandoff: 03/25/2017


---
# <a name="install-a-new-active-directory-forest-on-an-azure-virtual-network"></a>Azure Virtual Network での新しい Active Directory フォレストのインストール
このトピックでは、 [Azure の仮想ネットワーク](../virtual-network/virtual-networks-overview.md)上の仮想マシン (VM) に新しい Windows Server Active Directory 環境を作成する方法を示します。 この場合、Azure の仮想ネットワークはオンプレミスのネットワークには接続されません。

必要に応じて次の関連するトピックも参照してください。

* これらの手順については、ビデオ「 [How to install a new Active Directory forest on an Azure virtual network (Azure Virtual Network に新しい Active Directory フォレストをインストールする方法)](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* 必要であれば、 [サイト間 VPN を構成](../vpn-gateway/vpn-gateway-site-to-site-create.md) した後、新しいフォレストをインストールするか、オンプレミスのフォレストを Azure Virtual Network に拡張することもできます。 これらの手順については、「 [Azure Virtual Network でのレプリカ Active Directory ドメイン コントローラーのインストール](active-directory-install-replica-active-directory-domain-controller.md)」を参照してください。
* Azure の仮想ネットワークに Active Directory ドメイン サービス (AD DS) をインストールする方法に関する概念的なガイダンスについては、「 [Azure Virtual Machines での Windows Server Active Directory のデプロイ ガイドライン](https://msdn.microsoft.com/library/azure/jj156090.aspx)」を参照してください。

## <a name="scenario-diagram"></a>シナリオ図
このシナリオでは、外部ユーザーは、ドメインに参加しているサーバーで稼働するアプリケーションにアクセスする必要があります。 アプリケーション サーバーを実行する VM およびドメイン コントローラーを実行する VM は、Azure Virtual Network の専用のクラウド サービスにインストールされます。 また、フォールト トレランス向上のため、可用性セットにも含まれます。

![Azure Virtual Network の仮想マシンでの Active Directory フォレスト][1] 7

## <a name="how-does-this-differ-from-on-premises"></a>オンプレミスの場合との違い
ドメイン コントローラーのオンプレミスへのインストールと Azure へのインストールにそれほど大きな違いはありません。 主な違いを次の表に示します。

| 構成対象 | オンプレミスの | Azure の仮想ネットワーク |
| --- | --- | --- |
| **ドメイン コントローラーの IP アドレス** |ネットワーク アダプターのプロパティの静的 IP アドレスを割り当てる |Set-AzureStaticVNetIP コマンドレットを実行して、静的 IP アドレスを割り当てる |
| **DNS クライアント リゾルバー** |ドメイン メンバーのネットワーク アダプターのプロパティの優先および代替 DNS サーバー アドレスを設定する |仮想ネットワークのプロパティの DNS サーバー アドレスを設定する |
| **Active Directory データベース ストレージ** |既定の保存先を C:\ から変更する (省略可能) |既定の保存先を C:\ から変更する (必須) |

## <a name="create-an-azure-virtual-network"></a>Azure の仮想ネットワークを作成する
1. Azure クラシック ポータルにサインインします。
2. 仮想ネットワークを作成します。 **[ネットワーク]** > **[Virtual Network の作成]** の順にクリックします。 次の表の値を使用してウィザードの手順を完了します。

   | ウィザードのページ | 指定する値 |
   | --- | --- |
   |  **Virtual Network の詳細** |<p>名前: 仮想ネットワークの名前を入力する</p><p>リージョン: 最寄りのリージョンを選択する</p> |
   |  **DNS と VPN** |<p>[DNS サーバー] は空白のままにする</p><p>いずれの VPN オプションも選択しない</p> |
   |  **仮想ネットワーク アドレス空間** |<p>サブネット名: サブネットの名前を入力する</p><p>開始 IP: <b>10.0.0.0</b></p><p>CIDR: <b>/24 (256)</b></p> |

## <a name="create-vms-to-run-the-domain-controller-and-dns-server-roles"></a>ドメイン コントローラーと DNS サーバーのロールを実行する仮想マシンを作成する
次の手順を繰り返して、必要に応じて、DC ロールをホストする VM を作成します。 フォールト トレランスと冗長性を確保するには 2 つ以上の仮想 DC をデプロイする必要があります。 Azure の仮想ネットワークに、類似した構成の DC (いずれも GCで、DNS サーバーを実行し、FSMO ロールを保持していないなど) が 2 つ以上あると、それらの DC を実行する仮想マシンを可用性セットに置くとフォールト トレランスが向上します。

UI ではなく Windows PowerShell を使用して VM を作成する方法については、「 [UAzure PowerShell を使用して Windows ベースの仮想マシンを作成と事前構成する](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

1. クラシック ポータルで、**[新規]** > **[Compute]** > **[仮想マシン]** > **[ギャラリーから]** の順にクリックします。 次の値を使用して、ウィザードを完了します。 別の値が推奨されたり、要求される場合を除いては、既定の設定値を受け入れます。

   | ウィザードのページ | 指定する値 |
   | --- | --- |
   |  **イメージの選択** |Windows Server 2012 R2 Datacenter |
   |  **仮想マシンの構成** |<p>仮想マシン名: 単一ラベルの名前 (例: AzureDC1) を入力します。</p><p>新しいユーザー名: ユーザーの名前を入力します。 このユーザーは、VM 上のローカルの Administrators グループのメンバーになります。 最初に VM にサインインするときは、この名前でサインインする必要があります。 Administrator という名前の組み込みのアカウントは機能しません。</p><p>新しいパスワード/確認: パスワードを入力します。</p> |
   |  **仮想マシンの構成** |<p>クラウド サービス: 1 台目の VM の作成時には <b>[新しいクラウド サービスの作成]</b> を選択します。DC ロールをホストする VM の追加作成時には、1 台目の VM と同じクラウド サービス名を選択します。</p><p>クラウド サービス DNS 名: グローバルに一意の名前を指定します。</p><p>リージョン/アフィニティ グループ/仮想ネットワーク: 仮想ネットワーク名 (例: WestUSVNet) を指定します。</p><p>ストレージ アカウント: 1 台目の VM の作成時には <b>[自動的に生成されたストレージ アカウントを使用]</b> を選択します。DC ロールをホストする VM の追加作成時には、1 台目の VM と同じストレージ アカウント名を選択します。</p><p>可用性セット: <b>[可用性セットの作成]</b> を選択します。</p><p>可用性セット名: 1 台目の VM の作成時に、可用性セットの名前を入力します。その後、VM を追加で作成するときには、1 台目の VM と同じ可用性セット名を選択します。</p> |
   |  **仮想マシンの構成** |<p><b>[VM エージェントのインストール]</b> と、必要な他の拡張機能を選択します。</p> |
2. DC サーバーのロールを実行する各 VM にディスクを接続します。 AD データベース、ログ、SYSVOL を格納するには、追加のディスクが必要です。 ディスクのサイズを指定して (10 GB など)、**[ホスト キャッシュ設定]** は **[None]** のままにします。 手順については、「 [データ ディスクを Windows 仮想マシンに追加する方法](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。
3. 初めて VM にサインインした後、**[サーバー マネージャー]** > **[ファイル サービスとストレージ サービス]** の順に開いて、NTFS を使用してこのディスクにボリュームを作成します。
4. DC ロールを実行する VM の静的 IP アドレスを予約します。 静的 IP アドレスを予約するには、Microsoft Web Platform Installer をダウンロードして、 [Azure PowerShell をインストール](/powershell/azureps-cmdlets-docs) し、Set-AzureStaticVNetIP コマンドレットを実行します。 次に例を示します。

    `Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM`

静的 IP アドレスを設定する方法の詳細については、「 [VM 用の静的内部 IP アドレスを構成する](../virtual-network/virtual-networks-reserved-private-ip.md)」をご覧ください。

## <a name="install-windows-server-active-directory"></a>Windows Server Active Directory をインストールする
同じルーチンを使用して、オンプレミスで使用する [AD DSをインストール](https://technet.microsoft.com/library/jj574166.aspx) します (UI、応答ファイルや Windows PowerShell を使用できるようになります)。 管理者の資格情報を指定して新しいフォレストをインストールする必要があります。 Active Directory データベースの場所、ログ、SYSVOL を指定するには、既定の保存先をオペレーティング システム ドライブから、VM にアタッチした追加のデータ ディスクに変更します。

DC のインストールが完了したら、仮想マシンに再び接続し、DC にログオンします。 必ずドメインの資格情報の指定してください。

## <a name="reset-the-dns-server-for-the-azure-virtual-network"></a>Azure 仮想ネットワークの DNS サーバーをリセットする
1. 新しい DC/DNS サーバーでDNS フォワーダー設定をリセットします。
   1. サーバー マネージャーで、**[ツール]** > **[DNS]** の順にクリックします。
   2. **[DNS マネージャー]** で、DNS サーバーの名前を右クリックして **[プロパティ]** をクリックします。
   3. **[フォワーダー]** タブで、フォワーダーの IP アドレスをクリックして **[編集]** をクリックします。  IP アドレスを選択して、 **[削除]**をクリックします。
   4. **[OK]** をクリックしてエディターを閉じ、もう一度 **[OK]** をクリックして DNS サーバーのプロパティを閉じます。
2. 仮想ネットワークの DNS サーバーの設定を更新します。
   1. **[Virtual Network]** をクリックして、作成した仮想ネットワークをダブルクリックし、**[構成]** > **[DNS サーバー]** の順にクリックします。DC/DNS サーバー ロールを実行する仮想マシンの名前と DIP を入力して、**[保存]** をクリックします。
   2. VM を選択し、**[再起動]** をクリックして VM を起動します。新しい DNS サーバーの IP アドレスを使用して DNS リゾルバーの設定を構成します。

## <a name="create-vms-for-domain-members"></a>ドメイン メンバーの VM を作成する
1. 次の手順を繰り返して、アプリケーション サーバーとして実行するように VM を設定します。 別の値が推奨されたり、要求される場合を除いては、既定の設定値を受け入れます。

   | ウィザードのページ | 指定する値 |
   | --- | --- |
   |  **イメージの選択** |Windows Server 2012 R2 Datacenter |
   |  **仮想マシンの構成** |<p>仮想マシン名: 単一ラベルの名前 (例: AppServer1) を入力します。</p><p>新しいユーザー名: ユーザーの名前を入力します。 このユーザーは、VM 上のローカルの Administrators グループのメンバーになります。 最初に VM にサインインするときは、この名前でサインインする必要があります。 Administrator という名前の組み込みのアカウントは機能しません。</p><p>新しいパスワード/確認: パスワードを入力します。</p> |
   |  **仮想マシンの構成** |<p>クラウド サービス: 1 台目の VM の作成時には **[新しいクラウド サービスの作成]** を選択します。アプリケーションをホストする VM の追加作成時には、1 台目の VM と同じクラウド サービス名を選択します。</p><p>クラウド サービス DNS 名: グローバルに一意の名前を指定します。</p><p>リージョン/アフィニティ グループ/仮想ネットワーク: 仮想ネットワーク名 (例: WestUSVNet) を指定します。</p><p>ストレージ アカウント: 1 台目の VM の作成時には **[自動的に生成されたストレージ アカウントを使用]** を選択します。アプリケーションをホストする VM の追加作成時には、1 台目の VM と同じストレージ アカウント名を選択します。</p><p>可用性セット: **[可用性セットの作成]** を選択します。</p><p>可用性セット名: 1 台目の VM の作成時に、可用性セットの名前を入力します。その後、VM を追加で作成するときには、1 台目の VM と同じ可用性セット名を選択します。</p> |
   |  **仮想マシンの構成** |<p><b>[VM エージェントのインストール]</b> と、必要な他の拡張機能を選択します。</p> |
2. 各 VM がプロビジョニングされたら、サインインし、ドメインに参加させます。 **サーバー マネージャー**で、**[ローカル サーバー]** > **[ワークグループ]** > **[変更...]** の順にクリックし、 **[ドメイン]** を選択して、オンプレミス ドメインの名前を入力します。 ドメイン ユーザーの資格情報を入力し、VM を再起動して、ドメインへの参加を完了します。

UI ではなく Windows PowerShell を使用して VM を作成する方法については、「 [UAzure PowerShell を使用して Windows ベースの仮想マシンを作成と事前構成する](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

Windows PowerShell の使い方の詳細については、「[Azure コマンドレットの概要](https://msdn.microsoft.com/library/azure/jj554332.aspx)」と「[Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/jj554330.aspx)」をご覧ください。

## <a name="see-also"></a>関連項目
* [How to install a new Active Directory forest on an Azure virtual network (Azure Virtual Network に新しい Active Directory フォレストをインストールする方法)](http://channel9.msdn.com/Series/Microsoft-Azure-Tutorials/How-to-install-a-new-Active-Directory-forest-on-an-Azure-virtual-network)
* [Azure Virtual Machines での Windows Server Active Directory のデプロイ ガイドライン](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [サイト間 VPN の構成](../vpn-gateway/vpn-gateway-site-to-site-create.md)
* [Azure Virtual Network でのレプリカ Active Directory ドメイン コントローラーのインストール](active-directory-install-replica-active-directory-domain-controller.md)
* [Microsoft Azure IT Pro IaaS: (01) 仮想マシンの基礎](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) Virtual Network とクロスプレミス接続の作成](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Virtual Network の概要](../virtual-network/virtual-networks-overview.md)
* [Azure PowerShell のインストールおよび構成方法](/powershell/azureps-cmdlets-docs)
* [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
* [Azure コマンドレット リファレンス](https://msdn.microsoft.com/library/azure/jj554330.aspx)
* [Set Azure VM Static IP Address (Azure の VM の静的 IP アドレスの設定)](http://windowsitpro.com/windows-azure/set-azure-vm-static-ip-address)
* [Azure VM への静的 IP アドレスの割り当て方法](http://www.bhargavs.com/index.php/2014/03/13/how-to-assign-static-ip-to-azure-vm/)
* [Install a New Active Directory Forest (新しい Active Directory フォレストのインストール)](https://technet.microsoft.com/library/jj574166.aspx)
* [Active Directory ドメイン サービス (AD DS) の仮想化 (レベル 100) の概要](https://technet.microsoft.com/library/hh831734.aspx)

<!--Image references-->
[1]: ./media/active-directory-new-forest-virtual-machine/AD_Forest.png

