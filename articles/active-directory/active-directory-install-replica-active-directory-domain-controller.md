---
title: "Azure でのレプリカ Active Directory ドメイン コントローラーのインストール | Microsoft Docs"
description: "このチュートリアルでは、Azure の仮想マシンにオンプレミス Active Directory フォレストからドメイン コントローラーをインストールする方法について説明します。"
services: virtual-network
documentationcenter: 
author: curtand
manager: femila
editor: 
ms.assetid: 8c9ebf1b-289a-4dd6-9567-a946450005c0
ms.service: virtual-network
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2017
ms.author: curtand
translationtype: Human Translation
ms.sourcegitcommit: 4f2230ea0cc5b3e258a1a26a39e99433b04ffe18
ms.openlocfilehash: ccec356c653cf5d3c767fd273c37ea5685607135
ms.lasthandoff: 03/25/2017


---
# <a name="install-a-replica-active-directory-domain-controller-in-an-azure-virtual-network"></a>Azure の仮想ネットワークでのレプリカ Active Directory ドメイン コントローラーのインストール
このトピックでは、Azure の仮想ネットワークの Azure 仮想マシン (VM) 上に、オンプレミス Active Directory ドメインの追加のドメイン コントローラー (レプリカ DC とも呼ばれます) をインストールする方法を説明します。

必要に応じて次の関連するトピックも参照してください。

* オプションで、Azure の仮想ネットワーク上に新しい Active Directory フォレストをインストールすることもできます。 これらの手順については、「[Azure の仮想ネットワークでの Active Directory フォレストのインストール](active-directory-new-forest-virtual-machine.md)」をご覧ください。
* Azure の仮想ネットワークに Active Directory ドメイン サービス (AD DS) をインストールする方法に関する概念的なガイダンスについては、「 [Azure Virtual Machines での Windows Server Active Directory のデプロイ ガイドライン](https://msdn.microsoft.com/library/azure/jj156090.aspx)」を参照してください。

## <a name="scenario-diagram"></a>シナリオ図
このシナリオでは、外部ユーザーは、ドメインに参加しているサーバーで稼働するアプリケーションにアクセスする必要があります。 アプリケーション サーバーとレプリカ DC を実行する VM は、Azure の仮想ネットワーク内にインストールされています。 次の図に示すように、仮想ネットワークは[サイト間 VPN](../vpn-gateway/vpn-gateway-site-to-site-create.md) 接続で、オンプレミス ネットワークに接続できます。また、接続を高速化するために、[ExpressRoute](../expressroute/expressroute-locations-providers.md) を使用することもできます。

アプリケーション サーバーと DC は、コンピューティング処理を分散するために別々のクラウド サービス内にデプロイされます。また、フォールト トレランスを強化するために[可用性セット](../virtual-machines/virtual-machines-windows-manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)内にデプロイされます。
DC は、Active Directory レプリケーションを使用して、DC 同士で相互にレプリケートします。また、オンプレミス DC でレプリケートされます。 同期ツールは必要ありません。

![Azure vnet におけるレプリカ Active Directory ドメイン コントローラーの図][1]

## <a name="create-an-active-directory-site-for-the-azure-virtual-network"></a>Azure の仮想ネットワークの Active Directory サイトを作成する
仮想ネットワークに対応するネットワーク地域の Active Directory にサイトを作成することをお勧めします。 認証、レプリケーション、他の DC の検出オペレーションが最適化されます。 次の手順では、サイトの作成方法を説明しています。背景情報については、「[Adding a New Site (新しいサイトの追加)](https://technet.microsoft.com/library/cc781496.aspx)」をご覧ください。

1. **[サーバー マネージャー]** > **[ツール]** > **[Active Directory サイトとサービス]** の順に選択して、Active Directory サイトとサービスを開きます。
2. Azure Virtual Network を作成したリージョンを表すサイトを作成します。**[サイト]** > **[アクション]** > **[新しいサイト]** の順にクリックし、「Azure US West」などの新しいサイトの名前を入力して、サイト リンクを選択し、**[OK]** をクリックします。
3. サブネットを作成し、新しいサイトに関連付けます。**[サイト]** をダブルクリックして、**[サブネット]** > **[新しいサブネット]** の順に右クリックし、仮想ネットワークの IP アドレスの範囲 (例: シナリオ図の 10.1.0.0/16) を入力し、新しい Azure サイトを選択して、**[OK]** をクリックします。

## <a name="create-an-azure-virtual-network"></a>Azure の仮想ネットワークを作成する
1. [Azure クラシック ポータル](https://manage.windowsazure.com)で、**[新規]** > **[Network Services]** > **[Virtual Network]** > **[カスタム作成]** の順にクリックし、次の値を使用して、ウィザードを完了します。

   | ウィザードのページ | 指定する値 |
   | --- | --- |
   |  **Virtual Network の詳細** |<p>名前: 「WestUSVNet」などの仮想ネットワークの名前を入力します。</p><p>リージョン: 最寄りのリージョンを選択します。</p> |
   |  **DNS と VPN 接続** |<p>DNS サーバー: 1 つまたは複数のオンプレミス DNS サーバーの名前と IP アドレスを指定します。</p><p>接続: **[サイト間 VPN の構成]** を選択します。</p><p>ローカル ネットワーク: 新しいローカル ネットワークを指定します。</p><p>VPN ではなく ExpressRoute を使用している場合は、[Exchange プロバイダーによる ExpressRoute 接続の構成](../expressroute/expressroute-locations-providers.md)に関する記事を参照してください。</p> |
   |  **サイト間接続** |<p>名前: オンプレミス ネットワークの名前を入力します。</p><p>VPN デバイスの IP アドレス: 仮想ネットワークに接続するデバイスのパブリック IP アドレスを指定します。 VPN デバイスは NAT の内側に配置することはできません。</p><p>アドレス: オンプレミス ネットワークのアドレスの範囲 (例: シナリオ図の 192.168.0.0/16) を指定します。</p> |
   |  **仮想ネットワーク アドレス空間** |<p>アドレス空間: Azure の仮想ネットワークで実行する VM のIP アドレスの範囲 (例: シナリオ図の 10.1.0.0/16) を指定します。 このアドレス範囲を、内部設置型ネットワークのアドレスの範囲と重複させることはできません。</p><p>サブネット: アプリケーション サーバーのサブネットの名前とアドレス (例: Frontend、10.1.1.0/24) と、DC のサブネットの名前とアドレス (例: Backend、10.1.2.0/24) を指定します。</p><p>**[ゲートウェイ サブネットの追加]**をクリックします。</p> |
2. 次に、仮想ネットワーク ゲートウェイを構成して、セキュリティで保護されたサイト間 VPN 接続を作成します。 方法については、「 [管理ポータルでの仮想ネットワーク ゲートウェイの構成](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) 」をご覧ください。
3. 新しい仮想ネットワークと内部設置型 VPN デバイスの間に、サイト間 VPN 接続を作成します。 方法については、「 [管理ポータルでの仮想ネットワーク ゲートウェイの構成](../vpn-gateway/vpn-gateway-configure-vpn-gateway-mp.md) 」をご覧ください。

## <a name="create-azure-vms-for-the-dc-roles"></a>DC ロールの Azure VM を作成する
次の手順を繰り返して、必要に応じて、DC ロールをホストする VM を作成します。 フォールト トレランスと冗長性を確保するには 2 つ以上の仮想 DC をデプロイする必要があります。 Azure の仮想ネットワークに、類似した構成の DC (いずれも GCで、DNS サーバーを実行し、FSMO ロールを保持していないなど) が 2 つ以上あると、それらの DC を実行する仮想マシンを可用性セットに置くとフォールト トレランスが向上します。
UI ではなく Windows PowerShell を使用して VM を作成する方法については、「 [UAzure PowerShell を使用して Windows ベースの仮想マシンを作成と事前構成する](../virtual-machines/windows/classic/create-powershell.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。

1. [Azure クラシック ポータル](https://manage.windowsazure.com)で、**[新規]** > **[Compute]** > **[仮想マシン]** > **[ギャラリーから]** の順にクリックします。 次の値を使用して、ウィザードを完了します。 別の値が推奨されたり、要求される場合を除いては、既定の設定値を受け入れます。

   | ウィザードのページ | 指定する値 |
   | --- | --- |
   |  **イメージの選択** |Windows Server 2012 R2 Datacenter |
   |  **仮想マシンの構成** |<p>仮想マシン名: 単一ラベルの名前 (例: AzureDC1) を入力します。</p><p>新しいユーザー名: ユーザーの名前を入力します。 このユーザーは、VM 上のローカルの Administrators グループのメンバーになります。 最初に VM にサインインするときは、この名前でサインインする必要があります。 Administrator という名前の組み込みのアカウントは機能しません。</p><p>新しいパスワード/確認: パスワードを入力します。</p> |
   |  **仮想マシンの構成** |<p>クラウド サービス: 1 台目の VM の作成時には <b>[新しいクラウド サービスの作成]</b> を選択します。DC ロールをホストする VM の追加作成時には、1 台目の VM と同じクラウド サービス名を選択します。</p><p>クラウド サービス DNS 名: グローバルに一意の名前を指定します。</p><p>リージョン/アフィニティ グループ/仮想ネットワーク: 仮想ネットワーク名 (例: WestUSVNet) を指定します。</p><p>ストレージ アカウント: 1 台目の VM の作成時には <b>[自動的に生成されたストレージ アカウントを使用]</b> を選択します。DC ロールをホストする VM の追加作成時には、1 台目の VM と同じストレージ アカウント名を選択します。</p><p>可用性セット: <b>[可用性セットの作成]</b> を選択します。</p><p>可用性セット名: 1 台目の VM の作成時に、可用性セットの名前を入力します。その後、VM を追加で作成するときには、1 台目の VM と同じ可用性セット名を選択します。</p> |
   |  **仮想マシンの構成** |<p><b>[VM エージェントのインストール]</b> と、必要な他の拡張機能を選択します。</p> |
2. DC サーバーのロールを実行する各 VM にディスクを接続します。 AD データベース、ログ、SYSVOL を格納するには、追加のディスクが必要です。 ディスクのサイズを指定して (10 GB など)、**[ホスト キャッシュ設定]** は **[None]** のままにします。 手順については、「 [データ ディスクを Windows 仮想マシンに追加する方法](../virtual-machines/windows/classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)」を参照してください。
3. 初めて VM にサインインした後、**[サーバー マネージャー]** > **[ファイル サービスとストレージ サービス]** の順に開いて、NTFS を使用してこのディスクにボリュームを作成します。
4. DC ロールを実行する VM の静的 IP アドレスを予約します。 静的 IP アドレスを予約するには、Microsoft Web Platform Installer をダウンロードして、 [Azure PowerShell をインストール](/powershell/azureps-cmdlets-docs) し、Set-AzureStaticVNetIP コマンドレットを実行します。 次に例を示します。

    'Get-AzureVM -ServiceName AzureDC1 -Name AzureDC1 | Set-AzureStaticVNetIP -IPAddress 10.0.0.4 | Update-AzureVM

静的 IP アドレスを設定する方法の詳細については、「 [VM 用の静的内部 IP アドレスを構成する](../virtual-network/virtual-networks-reserved-private-ip.md)」をご覧ください。

## <a name="install-ad-ds-on-azure-vms"></a>Azure VM に AD DS をインストールする
VM にサインインし、サイト間 VPN 接続または ExpressRoute 接続で、オンプレミス ネットワーク上のリソースに接続できることを確認します。 次に、Azure VM に AD DS をインストールします。 オンプレミス ネットワーク上に追加の DC をインストールするために使用するのと同じプロセス (UI、Windows PowerShell、応答ファイル) を使用できます。 AD DS をインストールするときには、AD データベース、ログ、SYSVOL の場所の新しいボリュームを必ず指定します。 AD DS のインストール方法がわからない場合は、「[Active Directory ドメイン サービスをインストールする (レベル 100)](https://technet.microsoft.com/library/hh472162.aspx)」、または、「[Windows Server 2012 のレプリカ ドメイン コントローラーを既存のドメインにインストールする (レベル 200)](https://technet.microsoft.com/library/jj574134.aspx)」をご覧ください。

## <a name="reconfigure-dns-server-for-the-virtual-network"></a>仮想ネットワークの DNS サーバーを再構成する
1. [Azure クラシック ポータル](https://manage.windowsazure.com)で、仮想ネットワークの名前をクリックし、次に **[構成]** タブをクリックして、[仮想ネットワークの DNS サーバーの IP アドレスを再構成](../virtual-network/virtual-networks-manage-dns-in-vnet.md)し、オンプレミス DNS サーバーの IP アドレスではなく、レプリカ DC に割り当てられた静的 IP アドレスを使用します。
2. 仮想ネットワークのすべてのレプリカ DC VM が仮想ネットワーク上の DNS サーバーを使用するように構成されていることを確認には、**[仮想マシン]** クリックし、VM ごとの [状態] 列をクリックして、**[再起動]** をクリックします。 サインインする前に、VM で **実行** 状態が表示されるまで待機します。

## <a name="create-vms-for-application-servers"></a>アプリケーション サーバー用の VM を作成する
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

## <a name="additional-resources"></a>その他のリソース
* [Azure Virtual Machines での Windows Server Active Directory の展開ガイドライン](https://msdn.microsoft.com/library/azure/jj156090.aspx)
* [Azure PowerShell を使用して既存のオンプレミス ハイパー-V のドメイン コント ローラーを Azure にアップロードする方法](http://support.microsoft.com/kb/2904015)
* [Azure の仮想ネットワークでの Active Directory フォレストのインストール](active-directory-new-forest-virtual-machine.md)
* [Azure Virtual Network](../virtual-network/virtual-networks-overview.md)
* [Microsoft Azure IT Pro IaaS: (01) 仮想マシンの基礎](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/01)
* [Microsoft Azure IT Pro IaaS: (05) 仮想ネットワークとクロスプレミス接続の作成](http://channel9.msdn.com/Series/Windows-Azure-IT-Pro-IaaS/05)
* [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)
* [Azure の管理コマンドレット](https://msdn.microsoft.com/library/azure/jj152841)

<!--Image references-->
[1]: ./media/active-directory-install-replica-active-directory-domain-controller/ReplicaDCsOnAzureVNet.png

