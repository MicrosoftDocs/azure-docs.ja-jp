---
title: SQL Server 可用性グループ - Azure 仮想マシン - 前提条件 | Microsoft Docs
description: このチュートリアルでは、Azure VM で SQL Server Always On 可用性グループを作成するための前提条件の構成方法について説明します。
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: c492db4c-3faa-4645-849f-5a1a663be55a
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/29/2018
ms.author: mikeray
ms.openlocfilehash: 1d0f3bfa03eb4bafdd10222e28782c318848b7f7
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "57995166"
---
# <a name="complete-the-prerequisites-for-creating-always-on-availability-groups-on-azure-virtual-machines"></a>Azure 仮想マシンで Always On 可用性グループを作成するための前提条件を満たす

このチュートリアルでは、[Azure 仮想マシン (VM) で SQL Server Always On 可用性グループ](virtual-machines-windows-portal-sql-availability-group-tutorial.md)を作成するための前提条件を満たす方法を説明します。 前提条件を満たすと、1 つのリソース グループ内に 1 つのドメイン コントローラー、2 つの SQL Server VM、および 1 つの監視サーバーがあることになります。

**推定所要時間**: 前提条件を満たすまでには数時間かかる場合があります。 この時間の大半は、仮想マシンの作成に費やされます。

次の図に、チュートリアルで構築するものを示します。

![可用性グループ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="review-availability-group-documentation"></a>可用性グループのドキュメントの確認

このチュートリアルでは、SQL Server Always On 可用性グループに関する基本的な知識があることを前提としています。 このテクノロジに詳しくない場合は、「[Overview of Always On Availability Groups (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx)」(Always On 可用性グループの概要 (SQL Server)) を参照してください。


## <a name="create-an-azure-account"></a>Azure アカウントの作成
Azure アカウントが必要です。 [無料の Azure アカウントを作成する](https://signup.azure.com/signup?offer=ms-azr-0044p&appId=102&ref=azureplat-generic&redirectURL=https:%2F%2Fazure.microsoft.com%2Fget-started%2Fwelcome-to-azure%2F&correlationId=24f9d452-1909-40d7-b609-2245aa7351a6&l=en-US)か、[Visual Studio サブスクライバーの特典を有効にする](https://docs.microsoft.com/visualstudio/subscriptions/subscriber-benefits)ことができます。

## <a name="create-a-resource-group"></a>リソース グループの作成
1. [Azure ポータル](https://portal.azure.com)にサインインします。
2. ポータルで、**[+]** をクリックして新しいオブジェクトを作成します。

   ![新しいオブジェクト](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-portalplus.png)

3. **Marketplace** の検索ウィンドウに「**リソース グループ**」と入力します。

   ![リソース グループ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroupsymbol.png)
4. **[リソース グループ]** をクリックします。
5. **Create** をクリックしてください。
6. **[リソース グループ名]** には、リソース グループの名前を入力します。 たとえば、「**sql-ha-rg**」と入力します。
7. Azure サブスクリプションが複数ある場合は、可用性グループの作成先にする Azure サブスクリプションを選択します。
8. 場所を選択します。 場所とは、可用性グループを作成する Azure リージョンのことです。 この記事では、すべてのリソースを 1 つの Azure の場所に作成します。
9. **[ダッシュボードにピン留めする]** がオンになっていることを確認します。 これは、Azure ポータルのダッシュボードにリソース グループのショートカットを作成するためのオプションです。この設定は省略してもかまいません。

   ![リソース グループ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/01-resourcegroup.png)

10. **[作成]** をクリックしてリソース グループを作成します。

リソース グループが Azure に作成され、そのショートカットがポータルにピン留めされます。

## <a name="create-the-network-and-subnets"></a>ネットワークとサブネットの作成
次は、Azure リソース グループにネットワークとサブネットを作成します。

ここでは、1 つの仮想ネットワークと 2 つのサブネットを使用します。 Azure におけるネットワークの詳細については、[仮想ネットワークの概要](../../../virtual-network/virtual-networks-overview.md)に関するページを参照してください。

仮想ネットワークを作成するには、次の手順に従います。

1. Azure Portal のリソース グループで、**[+ 追加]** をクリックします。 

   ![新しい項目](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/02-newiteminrg.png)
2. 「 **仮想ネットワーク**」を検索します。

     ![仮想ネットワークの検索](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/04-findvirtualnetwork.png)
3. **[仮想ネットワーク]** をクリックします。
4. **[仮想ネットワーク]** で **[Resource Manager]** デプロイ モデルをクリックし、**[作成]** をクリックします。

    次の表に、仮想ネットワークの設定を示します。

   | **フィールド** | 値 |
   | --- | --- |
   | **名前** |autoHAVNET |
   | **アドレス空間** |10.33.0.0/24 |
   | **サブネット名** |[Admin] |
   | **サブネットのアドレス範囲** |10.33.0.0/29 |
   | **サブスクリプション** |使用するサブスクリプションを指定します。 ご利用のサブスクリプションが 1 つだけの場合、**[サブスクリプション]** は空になります。 |
   | **[リソース グループ]** |**[既存のものを使用]** を選択し、リソース グループの名前を選択します。 |
   | **場所** |Azure の場所を指定します。 |

   実際のアドレス空間とサブネット アドレス範囲は、この表とは異なる場合があります。 サブスクリプションによっては、使用できるアドレス空間とそれに対応するサブネット アドレス範囲がポータルで提示されます。 アドレス空間が足りない場合は、異なるサブスクリプションを使用してください。

   この例では、**Admin** というサブネット名を使用します。このサブネットは、ドメイン コントローラー用です。

5. **Create** をクリックしてください。

   ![仮想ネットワークの構成](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/06-configurevirtualnetwork.png)

新しいネットワークが作成されると、画面がポータル ダッシュボードに切り替わって通知が表示されます。

### <a name="create-a-second-subnet"></a>2 つ目のサブネットの作成
新しい仮想ネットワークには、**Admin** という名前のサブネットが 1 つあります。このサブネットはドメイン コントローラーが使用します。 SQL Server VM には、**SQL** というもう 1 つのサブネットを使用します。 このサブネットを構成するには、次のようにします。

1. ダッシュボードで、先ほど作成したリソース グループ ( **SQL-HA-RG**) をクリックします。 このリソース グループ内のネットワークを **[リソース]** から探します。

    **SQL-HA-RG** が表示されない場合は、**[リソース グループ]** をクリックし、リソース グループ名でフィルター処理を行ってください。
2. リソースの一覧で **[autoHAVNET]** をクリックします。 
3. **autoHAVNET** 仮想ネットワークの **[設定]** で、**[サブネット]** を選択します。

    既に作成してあるサブネットに注目してください。

   ![仮想ネットワークの構成](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/07-addsubnet.png)
5. 2 つ目のサブネットを作成します。 **[+ サブネット]** をクリックします。
6. **[サブネットの追加]** で、**[名前]** に「**sqlsubnet**」と入力してサブネットを構成します。 有効な **アドレス範囲**が Azure によって自動的に指定されます。 このアドレス範囲に含まれるアドレスが 10 個以上あることを確認してください。 運用環境では、さらに多くのアドレスが必要になる場合があります。
7. **[OK]** をクリックします。

    ![仮想ネットワークの構成](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/08-configuresubnet.png)

次の表は、ネットワーク構成の設定をまとめたものです。

| **フィールド** | 値 |
| --- | --- |
| **名前** |**autoHAVNET** |
| **アドレス空間** |この値は、サブスクリプションで使用できるアドレス空間によって異なります。 標準的な値は 10.0.0.0/16 です。 |
| **サブネット名** |**admin** |
| **サブネットのアドレス範囲** |この値は、サブスクリプションで使用できるアドレス範囲によって異なります。 標準的な値は 10.0.0.0/24 です。 |
| **サブネット名** |**sqlsubnet** |
| **サブネットのアドレス範囲** |この値は、サブスクリプションで使用できるアドレス範囲によって異なります。 標準的な値は 10.0.1.0/24 です。 |
| **サブスクリプション** |使用するサブスクリプションを指定します。 |
| **リソース グループ** |**SQL-HA-RG** |
| **場所** |リソース グループと同じ場所を指定します。 |

## <a name="create-availability-sets"></a>可用性セットを作成する

仮想マシンを作成する前に、可用性セットを作成する必要があります。 可用性セットにより、計画済みメンテナンスや計画外メンテナンスが発生した場合のダウンタイムを短縮できます。 Azure 可用性セットとは、リソースの論理的なグループです。このグループに基づいてリソースが物理的な障害ドメインと更新ドメインに自動的に配置されます。 可用性セットに属している仮想マシンの電源やネットワーク リソースは、障害ドメインによって確実に分離されます。 一方、可用性セットに属している複数の仮想マシンがメンテナンスによって同時に中断されることを防止する働きをするのが更新ドメインです。 詳細については、[仮想マシンの可用性の管理](../manage-availability.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)に関するページを参照してください。

可用性セットは 2 つ必要です。 1 つ目はドメイン コントローラー用です。 2 つ目は SQL Server VM 用です。

可用性セットを作成するには、リソース グループに移動して **[追加]** をクリックします。 「**可用性セット**」と入力して結果をフィルター処理し、 その中の **[可用性セット]** をクリックして、**[作成]** をクリックします。

次の表のパラメーターに従って 2 つの可用性セットを構成します。

| **フィールド** | ドメイン コントローラーの可用性セット | SQL Server の可用性セット |
| --- | --- | --- |
| **名前** |adavailabilityset |sqlavailabilityset |
| **[リソース グループ]** |SQL-HA-RG |SQL-HA-RG |
| **障害ドメイン** |3 |3 |
| **更新ドメイン** |5 |3 |

可用性セットを作成したら、Azure ポータルでリソース グループに戻ります。

## <a name="create-domain-controllers"></a>ドメイン コントローラーを作成する
ネットワーク、サブネット、可用性セット、インターネット接続用のロード バランサーを作成したら、ドメイン コントローラー用の仮想マシンを作成する準備は完了です。

### <a name="create-virtual-machines-for-the-domain-controllers"></a>ドメイン コントローラーに使用する仮想マシンの作成
ドメイン コントローラーを作成して構成するために、 **SQL-HA-RG** リソース グループに戻ります。

1. **[追加]** をクリックします。 
2. 「**Windows Server 2016 Datacenter**」と入力します。
3. **[Windows Server 2016 Datacenter]** をクリックします。 **[Windows Server 2016 Datacenter]** でデプロイメント モデルが **[Resource Manager]** に設定されていることを確認し、**[作成]** をクリックします。 

前の手順を繰り返して、2 つの仮想マシンを作成します。 2 つの仮想マシンには、次のように名前を付けます。

* ad-primary-dc
* ad-secondary-dc

  > [!NOTE]
  > **ad-secondary-dc** はオプションの仮想マシンであり、Active Directory Domain Services の高可用性を確保するためのものです。
  >
  >

次の表に、この 2 つのマシンの設定を示します。

| **フィールド** | 値 |
| --- | --- |
| **名前** |最初のドメイン コントローラー: *ad-primary-dc*。</br>2 番目のドメイン コントローラー: *ad-secondary-dc*。 |
| **VM ディスクの種類** |SSD |
| **ユーザー名** |DomainAdmin |
| **パスワード** |Contoso!0000 |
| **サブスクリプション** |*該当するサブスクリプション* |
| **[リソース グループ]** |SQL-HA-RG |
| **場所** |*該当する場所* |
| **サイズ** |DS1_V2 |
| **Storage** | **マネージド ディスクを使用** - **Yes** |
| **Virtual Network** |autoHAVNET |
| **サブネット** |admin |
| **パブリック IP アドレス** |*VM と同じ名前* |
| **ネットワーク セキュリティ グループ** |*VM と同じ名前* |
| **可用性セット** |adavailabilityset </br>**障害ドメイン**: 2 </br>**更新ドメイン**: 2|
| **診断** |有効 |
| **診断ストレージ アカウント** |*自動的に作成* |

   >[!IMPORTANT]
   >VM を可用性セットに配置できるのは、VM の作成時のみです。 VM の作成後に可用性セットを変更することはできません。 「[仮想マシンの可用性管理](../manage-availability.md)」を参照してください。

Azure で仮想マシンが作成されます。

仮想マシンの作成後、ドメイン コントローラーを構成します。

### <a name="configure-the-domain-controller"></a>ドメイン コントローラーの構成
次の手順では、 **ad-primary-dc** マシンを corp.contoso.com のドメイン コントローラーとして構成します。

1. ポータルで **SQL-HA-RG** リソース グループを開き、**ad-primary-dc** マシンを選択します。 **ad-primary-dc** の **[接続]** をクリックして、リモート デスクトップ アクセス用の RDP ファイルを開きます。

    ![仮想マシンへの接続](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/20-connectrdp.png)
2. 構成した管理者アカウント (**\DomainAdmin**) とパスワード (**Contoso!0000**) を使用してサインインします。
3. 既定では、 **[サーバー マネージャー]** ダッシュボードが表示されます。
4. ダッシュボードの **[役割と機能の追加]** リンクをクリックします。

    ![サーバー マネージャー - 役割の追加](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
5. **[サーバーの役割]** セクションが表示されるまで **[次へ]** を選択します。
6. **[Active Directory Domain Services]** と **[DNS サーバー]** という役割を選択します。 メッセージが表示されたら、これらの役割に必要なその他の機能を追加します。

   > [!NOTE]
   > Windows から、静的 IP アドレスがないという警告が出されます。 構成をテストしている場合は、**[続行]** をクリックします。 運用シナリオの場合は、Azure Portal で IP アドレスを静的アドレスに設定するか、[PowerShell を使用してドメイン コントローラー コンピューターの静的 IP アドレスを設定](../../../virtual-network/virtual-networks-reserved-private-ip.md)してください。
   >
   >

    ![役割の追加のダイアログ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/23-addroles.png)
7. **[確認]** セクションが表示されるまで **[次へ]** をクリックします。 **[必要に応じてターゲット サーバーを自動的に再起動する]** チェック ボックスをオンにします。
8. **[インストール]** をクリックします。
9. 機能のインストールが完了したら、 **[サーバー マネージャー]** ダッシュボードに戻ります。
10. 左側のウィンドウで新しい **[AD DS]** オプションを選択します。
11. 黄色の警告バーにある **[その他]** リンクをクリックします。

    ![DMS サーバー VM 上の AD DS を示すダイアログ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/24-addsmore.png)
12. **[すべてのサーバー タスクの詳細]** ダイアログ ボックスの **[操作]** 列で、**[このサーバーをドメイン コントローラーに昇格する]** をクリックします。
13. **Active Directory Domain Services の構成ウィザード**で、次の値を使用します。

    | **ページ** | Setting |
    | --- | --- |
    | **デプロイ構成** |**[新しいフォレストの追加]**<br/> **[ルート ドメイン名]** = corp.contoso.com |
    | **ドメイン コントローラー オプション** |**[DSRM パスワード]** = Contoso!0000<br/>**[パスワードの確認]** = Contoso!0000 |
14. **[次へ]** をクリックして、ウィザード内の他のページを進めます。 **[前提条件のチェック]** ページで、"**すべての前提条件のチェックに合格しました**" というメッセージが表示されることを確認します。 該当する警告メッセージを確認してください。ただし、インストールは続行できます。
15. **[インストール]** をクリックします。 **ad-primary-dc** 仮想マシンが自動的に再起動します。

### <a name="note-the-ip-address-of-the-primary-domain-controller"></a>プライマリ ドメイン コントローラーの IP アドレスを記録します。

DNS 用に、プライマリ ドメイン コントローラーを使用します。 プライマリ ドメイン コントローラーの IP アドレスを記録しておきます。

プライマリ ドメイン コントローラーの IP アドレスを取得する方法の 1 つでは、Azure Portal を使用します。

1. Azure Portal で、リソース グループを開きます。

2. プライマリ ドメイン コントローラーをクリックします。

3. プライマリ ドメイン コントローラーで、**[ネットワーク インターフェイス]** をクリックします。

![ネットワーク インターフェイス](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/25-primarydcip.png)

このサーバーのプライベート IP アドレスを記録してください。

### <a name="configure-the-virtual-network-dns"></a>仮想ネットワーク DNS の構成
最初のドメイン コント ローラーを作成し、最初のサーバーで DNS を有効にしたら、DNS にこのサーバーを使用するように仮想ネットワークを構成します。

1. Azure Portal でその仮想ネットワークをクリックします。

2. **[設定]** の下で **[DNS サーバー]** をクリックします。

3. **[カスタム]** をクリックして、プライマリ ドメイン コント ローラーのプライベート IP アドレスを入力します。

4. **[Save]** をクリックします。

### <a name="configure-the-second-domain-controller"></a>2 番目のドメイン コントローラーの構成
プライマリ ドメイン コントローラーの再起動後、2 つ目のドメイン コントローラーの構成を行うことができます。 これは高可用性を確保するための任意の手順です。 次の手順に従って 2 つ目のドメイン コントローラーを構成します。

1. ポータルで **SQL-HA-RG** リソース グループを開き、**ad-secondary-dc** マシンを選択します。 **ad-secondary-dc** の **[接続]** をクリックして、リモート デスクトップ アクセス用の RDP ファイルを開きます。
2. 構成した管理者アカウント (**BUILTIN\DomainAdmin**) とパスワード (**Contoso!0000**) を使用して、VM にサインインします。
3. 優先 DNS サーバーのアドレスをドメイン コントローラーのアドレスに変更します。
4. **[ネットワークと共有センター]** でネットワーク インターフェイスをクリックします。
   ![ネットワーク インターフェイス](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/26-networkinterface.png)

5. **[プロパティ]** をクリックします。
6. **[インターネット プロトコル バージョン 4 (TCP/IPv4)]** を選択し、**[プロパティ]** をクリックします。
7. **[次の DNS サーバーのアドレスを使う]** を選択し、**[優先 DNS サーバー]** にプライマリ ドメイン コントローラーのアドレスを指定します。
8. **[OK]**、**[閉じる]** の順にクリックして変更を適用します。 これで、VM を **corp.contoso.com**に参加させることができるようになりました。

   >[!IMPORTANT]
   >DNS 設定の変更後、リモート デスクトップへの接続が失われた場合は、Azure Portal に移動し、仮想マシンを再起動してください。

9. リモート デスクトップからセカンダリ ドメイン コントローラーに向けて、**[サーバー マネージャー]** ダッシュボードを開きます。
10. ダッシュボードの **[役割と機能の追加]** リンクをクリックします。

    ![サーバー マネージャー - 役割の追加](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
11. **[サーバーの役割]** セクションが表示されるまで **[次へ]** を選択します。
12. **[Active Directory Domain Services]** と **[DNS サーバー]** という役割を選択します。 メッセージが表示されたら、これらの役割に必要なその他の機能を追加します。
13. 機能のインストールが完了したら、 **[サーバー マネージャー]** ダッシュボードに戻ります。
14. 左側のウィンドウで新しい **[AD DS]** オプションを選択します。
15. 黄色の警告バーにある **[その他]** リンクをクリックします。
16. **[すべてのサーバー タスクの詳細]** ダイアログ ボックスの **[操作]** 列で、**[このサーバーをドメイン コントローラーに昇格する]** をクリックします。
17. **[デプロイ構成]** で **[既存のドメインにドメイン コントローラーを追加する]** を選択します。
    ![デプロイ構成](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/28-deploymentconfig.png)
18. **[選択]** をクリックします。
19. 管理者アカウント (**CORP.CONTOSO.COM\domainadmin**) とパスワード (**Contoso!0000**) を使用して接続します。
20. **[フォレストからのドメインの選択]** で使用するドメインをクリックし、**[OK]** をクリックします。
21. **[ドメイン コントローラー オプション]** で、既定値を使用し、DSRM パスワードを設定します。

    >[!NOTE]
    >**[DNS オプション]** ページでは、この DNS サーバーの委任を作成できないという警告が表示される場合があります。 この警告は、非運用環境では無視してかまいません。
22. **前提条件**の確認のダイアログ ボックスが表示されるまで、**[次へ]** をクリックします。 その後、 **[インストール]** をクリックします。

サーバーによる構成の変更が完了したら、サーバーを再起動します。

### <a name="add-the-private-ip-address-to-the-second-domain-controller-to-the-vpn-dns-server"></a>VPN DNS サーバーの 2 番目のドメイン コント ローラーにプライベート IP アドレスを追加する

Azure Portal の仮想ネットワークの下で、セカンダリ ドメイン コント ローラーの IP アドレスを含めるように DNS サーバーを変更します。 この設定により、DNS サービスの冗長性が可能になります。

### <a name="DomainAccounts"></a> ドメイン アカウントの構成

次に、Active Directory アカウントを構成します。 次の表は、各アカウントを示しています。

| |インストール アカウント<br/> |sqlserver-0 <br/>SQL Server と SQL エージェント サービス アカウント |sqlserver-1<br/>SQL Server と SQL エージェント サービス アカウント
| --- | --- | --- | ---
|**名** |[インストール] |SQLSvc1 | SQLSvc2
|**User SamAccountName** |Install |SQLSvc1 | SQLSvc2

次の手順に従って、各アカウントを作成します。

1. **ad-primary-dc** コンピューターにサインインします。
2. **[サーバー マネージャー]** で、**[ツール]** を選択し、**[Active Directory 管理センター]** をクリックします。   
3. 左側のウィンドウで **[corp (ローカル)]** を選択します。
4. 右側の **[タスク]** ウィンドウで **[新規]** を選択し、**[ユーザー]** をクリックします。
   ![Active Directory 管理センター](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/29-addcnewuser.png)

   >[!TIP]
   >各アカウントには複雑なパスワードを設定してください。<br/> 非運用環境では、ユーザー アカウントを期限なしに設定してください。

5. **[OK]** をクリックして、ユーザーを作成します。
6. 3 つのアカウントそれぞれに、上の手順を繰り返します。

### <a name="grant-the-required-permissions-to-the-installation-account"></a>インストール アカウントへの必要なアクセス許可の付与
1. **[Active Directory 管理センター]** で、左側のウィンドウの **[corp (ローカル)]** を選択します。 その後、右側の **[タスク]** ウィンドウで、**[プロパティ]** をクリックします。

    ![corp のユーザー プロパティ](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/31-addcproperties.png)
2. **[拡張機能]** を選択し、**[セキュリティ]** タブの **[詳細設定]** をクリックします。
3. **[corp のセキュリティの詳細設定]** ダイアログで、**[追加]** をクリックします。
4. **[プリンシパルの選択]** をクリックし、**CORP\Install** を検索して、**[OK]** をクリックします。
5. **[すべてのプロパティの読み取り]** チェック ボックスをオンにします。

6. **[Create Computer objects] \(コンピューター オブジェクトの作成)** チェック ボックスをオンにします。

     ![corp のユーザーのアクセス許可](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/33-addpermissions.png)
7. **[OK]** をクリックし、もう一度 **[OK]** をクリックします。 **corp** のプロパティ ウィンドウを閉じます。

Active Directory オブジェクトとユーザー オブジェクトの構成が終了したので、次に 2 つの SQL Server VM と 1 つの監視サーバー VM を作成します。 その後、3 つすべてをドメインに参加させます。

## <a name="create-sql-server-vms"></a>SQL Server VM の作成

3 つの仮想マシンを追加で作成します。 ソリューションには、SQL Server のインスタンスを持つ 2 つの仮想マシンが必要です。 3 番目の仮想マシンは、監視として機能します。 Windows Server 2016 は、[クラウド監視](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness)を使用できますが、以前のオペレーティング システムとの整合性のため、このドキュメントでは監視に仮想マシンを使用します。  

続行する前に、次の設計上の決定を検討してください。

* **ストレージ: Azure Managed Disks**

   仮想マシンのストレージには、Azure Managed Disks を使用します。 SQL Server 仮想マシンには、Managed Disks の使用をお勧めします。 Managed Disks はバックグラウンドでストレージを管理します。 さらに、仮想マシンと Managed Disks が同じ可用性セットにある場合、Azure は適切な冗長性を提供するためにストレージ リソースを分散させます。 詳細については、「[Azure Managed Disks の概要](../managed-disks-overview.md)」を参照してください。 可用性セットのマネージド ディスクの詳細は、「[可用性セット内の VM に管理ディスクを使用する](../manage-availability.md#use-managed-disks-for-vms-in-an-availability-set)」を参照してください。

* **ネットワーク: 運用中のプライベート IP アドレス**

   このチュートリアルでは、仮想マシンにパブリック IP アドレスを使用します。 パブリック IP アドレスを使うと、インターネット経由で仮想マシンに直接リモート接続できるため、構成手順が簡素化されます。 運用環境では、SQL Server インスタンス VM リソースの脆弱性の範囲を縮小するため、プライベート IP アドレスのみを使用することをお勧めします。

### <a name="create-and-configure-the-sql-server-vms"></a>SQL Server VM の作成と構成
次に、2 つの SQL Server VM と追加のクラスター ノード用 VM の 1 つで合計 3 つの VM を作成します。 それぞれの VM を作成するには、**SQL-HA-RG** リソース グループに戻って **[追加]** をクリックし、適切なギャラリー アイテム (**仮想マシン**) を検索して、**[ギャラリーから]** をクリックします。 次の表の情報を使用すると、VM の作成に役立ちます。


| ページ | VM1 | VM2 | VM3 |
| --- | --- | --- | --- |
| 適切なギャラリー アイテムの選択 |**Windows Server 2016 Datacenter** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |**SQL Server 2016 SP1 Enterprise on Windows Server 2016** |
| [Virtual machine configuration (仮想マシンの構成)] の **[基本]** |**[名前]** = cluster-fsw<br/>**[ユーザー名]** = DomainAdmin<br/>**パスワード** = Contoso!0000<br/>**[サブスクリプション]** = 自分のサブスクリプション<br/>**[リソース グループ]** = SQL-HA-RG<br/>**[場所]** = 該当する Azure の場所 |**[名前]** = sqlserver-0<br/>**[ユーザー名]** = DomainAdmin<br/>**パスワード** = Contoso!0000<br/>**[サブスクリプション]** = 自分のサブスクリプション<br/>**[リソース グループ]** = SQL-HA-RG<br/>**[場所]** = 該当する Azure の場所 |**[名前]** = sqlserver-1<br/>**[ユーザー名]** = DomainAdmin<br/>**パスワード** = Contoso!0000<br/>**[サブスクリプション]** = 自分のサブスクリプション<br/>**[リソース グループ]** = SQL-HA-RG<br/>**[場所]** = 該当する Azure の場所 |
| [Virtual machine configuration (仮想マシンの構成)] の **[サイズ]** |**[サイズ]** = DS1\_V2 (1 vCPU、3.5 GB) |**[サイズ]** = DS2\_V2 (2 vCPU、7 GB)</br>SSD ストレージをサポートするサイズが必要 (Premium ディスクのサポート )) |**[サイズ]** = DS2\_V2 (2 vCPU、7 GB) |
| [Virtual machine configuration (仮想マシンの構成)] の **[設定]** |**ストレージ**:マネージド ディスクを使用します。<br/>**[仮想ネットワーク]** = autoHAVNET<br/>**[サブネット]** = sqlsubnet(10.1.1.0/24)<br/>**[パブリック IP アドレス]** 自動的に生成。<br/>**[ネットワーク セキュリティ グループ]** = なし<br/>**[監視診断]** = 有効<br/>**[診断ストレージ アカウント]** = 自動的に生成されたストレージ アカウントを使用<br/>**[可用性セット]** = sqlAvailabilitySet<br/> |**ストレージ**:マネージド ディスクを使用します。<br/>**[仮想ネットワーク]** = autoHAVNET<br/>**[サブネット]** = sqlsubnet(10.1.1.0/24)<br/>**[パブリック IP アドレス]** 自動的に生成。<br/>**[ネットワーク セキュリティ グループ]** = なし<br/>**[監視診断]** = 有効<br/>**[診断ストレージ アカウント]** = 自動的に生成されたストレージ アカウントを使用<br/>**[可用性セット]** = sqlAvailabilitySet<br/> |**ストレージ**:マネージド ディスクを使用します。<br/>**[仮想ネットワーク]** = autoHAVNET<br/>**[サブネット]** = sqlsubnet(10.1.1.0/24)<br/>**[パブリック IP アドレス]** 自動的に生成。<br/>**[ネットワーク セキュリティ グループ]** = なし<br/>**[監視診断]** = 有効<br/>**[診断ストレージ アカウント]** = 自動的に生成されたストレージ アカウントを使用<br/>**[可用性セット]** = sqlAvailabilitySet<br/> |
| [Virtual machine configuration (仮想マシンの構成)] の **[SQL Server の設定]** |適用不可 |**[SQL 接続]** = プライベート (仮想ネットワーク内)<br/>**[ポート]** = 1433<br/>**[SQL 認証]** = 無効<br/>**[ストレージの構成]** = 全般<br/>**[自動修正]**= 日曜日 2:00<br/>**[自動バックアップ]** = 無効</br>**[Azure Key Vault の統合]** = 無効 |**[SQL 接続]** = プライベート (仮想ネットワーク内)<br/>**[ポート]** = 1433<br/>**[SQL 認証]** = 無効<br/>**[ストレージの構成]** = 全般<br/>**[自動修正]**= 日曜日 2:00<br/>**[自動バックアップ]** = 無効</br>**[Azure Key Vault の統合]** = 無効 |

<br/>

> [!NOTE]
> ここで示されるマシンのサイズは、Azure VM での可用性グループのテストに対応しています。 運用時のワークロードに対して最適なパフォーマンスを得る方法については、「[Azure Virtual Machines における SQL Server のパフォーマンスのベスト プラクティス](virtual-machines-windows-sql-performance.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)」の SQL Server マシンのサイズと構成に関する推奨事項を参照してください。
>
>

3 つの VM が完全にプロビジョニングされたら、**corp.contoso.com** ドメインに参加させて、CORP\Install 管理者権限を付与する必要があります。

### <a name="joinDomain"></a>ドメインへのサーバーの参加

これで、VM を **corp.contoso.com** に参加させることができるようになりました。 両方の SQL Server VM とファイル共有監視サーバーに対して、次の手順を実行します。

1. **BUILTIN\DomainAdmin** で、仮想マシンにリモートで接続します。
2. **[サーバー マネージャー]** で **[ローカル サーバー]** をクリックします。
3. **[ワークグループ]** リンクをクリックします。
4. **[コンピューター名]** セクションで **[変更]** をクリックします。
5. **[ドメイン]** チェック ボックスをオンにし、テキスト ボックスに「**corp.contoso.com**」を入力します。 **[OK]** をクリックします。
6. **[Windows セキュリティ]** ポップアップ ダイアログで、既定のドメイン管理者の資格情報であるアカウント (**CORP\DomainAdmin**) とパスワード (**Contoso!0000**) を指定します。
7. "corp.contoso.com ドメインへようこそ" のメッセージが表示されたら、 **[OK]** をクリックします。
8. **[閉じる]** をクリックし、ポップアップ ダイアログの **[今すぐ再起動]** をクリックします。

### <a name="add-the-corpinstall-user-as-an-administrator-on-each-cluster-vm"></a>各クラスター VM に Corp\Install ユーザーを管理者として追加する

各仮想マシンをドメインのメンバーとして再起動した後で、**CORP\Install** をローカルの Administrators グループのメンバーとして追加します。

1. VM が再起動されるまで待って、プライマリ ドメイン コントローラーからもう一度 RDP ファイルを起動し、**CORP\DomainAdmin** アカウントを使用して **sqlserver-0** にサインインします。
   >[!TIP]
   >必ずドメイン管理者アカウントでサインインしてください。 前の手順では、BUILTIN 管理者アカウントを使用していました。 これでサーバーはドメイン内に存在するため、ドメイン アカウントを使用します。 RDP セッションで、*DOMAIN*\\*username* を指定します。

2. **[サーバー マネージャー]** で、**[ツール]** を選択し、**[コンピューターの管理]** をクリックします。
3. **[コンピューターの管理]** ウィンドウで、**[ローカル ユーザーとグループ]** を展開し、**[グループ]** を選択します。
4. **[Administrators]** グループをダブルクリックします。
5. **[Administrators のプロパティ]** ダイアログ ボックスで、**[追加]** をクリックします。
6. ユーザーに「**CORP\Install**」を入力し、**[OK]** をクリックします。
7. **[OK]** をクリックして **[管理者のプロパティ]** ダイアログ ボックスを閉じます。
8. 以上の手順を **sqlserver-1** と **cluster-fsw** にも繰り返します。

### <a name="setServiceAccount"></a>SQL Server サービス アカウントの設定

各 SQL Server VM で、SQL Server サービス アカウントを設定します。 ドメイン アカウントの構成時に作成したアカウントを使用します。

1. **SQL Server 構成マネージャー**を開きます。
2. SQL Server サービスを右クリックし、**[プロパティ]** をクリックします。
3. アカウントとパスワードを設定します。
4. もう一方の SQL Server VM についても同じ手順を繰り返します。  

SQL Server 可用性グループでは、各 SQL Server VM をドメイン アカウントとして実行する必要があります。

### <a name="create-a-sign-in-on-each-sql-server-vm-for-the-installation-account"></a>各 SQL Server VM にインストール アカウント用のサインインを作成する

可用性グループを構成するには、インストール アカウント (CORP\install) を使用します。 このアカウントは、各 SQL Server VM の **sysadmin** 固定サーバー ロールのメンバーである必要があります。 次の手順で、インストール アカウント用のサインインを作成します。

1. リモート デスクトップ プロトコル (RDP) を使用して、*\<マシン名\>\<ドメイン管理者>* アカウントでサーバーに接続します。

1. SQL Server Management Studio を開き、SQL Server のローカル インスタンスに接続します。

1. **オブジェクト エクスプローラー**で、**[セキュリティ]** をクリックします。

1. **[ログイン]** を右クリックします。 **[新しいログイン]** をクリックします。

1. **[ログイン - 新規作成]** で **[検索]** をクリックします。

1. **[場所]** をクリックします。

1. ドメイン管理者のネットワーク資格情報を入力します。

1. インストール アカウントを使用します。

1. サインインを **sysadmin** 固定サーバー ロールのメンバーに設定します。

1. **[OK]** をクリックします。

もう一方の SQL Server VM についても、上の手順を繰り返します。

## <a name="add-failover-clustering-features-to-both-sql-server-vms"></a>両方の SQL Server VM にフェールオーバー クラスター機能を追加する

フェールオーバー クラスター機能を追加するには、両方の SQL Server VM で次の手順を実行します。

1. *CORP\install* アカウントを使用して、リモート デスクトップ プロトコル (RDP) を通じて SQL Server 仮想マシンに接続します。 **[サーバー マネージャー] ダッシュボード**を開きます。
2. ダッシュボードの **[役割と機能の追加]** リンクをクリックします。

    ![サーバー マネージャー - 役割の追加](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/22-addfeatures.png)
3. **[Server Features (サーバーの機能)]** セクションが表示されるまで **[次へ]** を選択します。
4. **[機能]** で **[フェールオーバー クラスタリング]** を選択します。
5. その他の必要な機能を追加します。
6. **[インストール]** をクリックして機能を追加します。

もう一方の SQL Server VM についても同じ手順を繰り返します。

  >[!NOTE]
  > 実際に SQL Server VM をフェールオーバー クラスターに参加させる手順と共に、この手順は、[Azure SQL VM CLI](virtual-machines-windows-sql-availability-group-cli.md) と [Azure クイック スタート テンプレート](virtual-machines-windows-sql-availability-group-quickstart-template.md)を使用して自動化できるようになりました。


## <a name="a-nameendpoint-firewall-configure-the-firewall-on-each-sql-server-vm"></a><a name="endpoint-firewall"> 各 SQL Server VM でのファイアウォールの構成

このソリューションでは、ファイアウォールで次の TCP ポートが開いている必要があります。

- **SQL Server VM**:<br/>
   SQL Server の既定のインスタンス用のポート 1433。
- **Azure ロード バランサー プローブ:**<br/>
   使用可能な任意のポート。 例では 59999 がよく使用されます。
- **データベース ミラーリング エンドポイント:** <br/>
   使用可能な任意のポート。 例では 5022 がよく使用されます。

両方の SQL Server VM 上でファイアウォールのポートを開く必要があります。

ポートを開く方法は、使用するファイアウォール ソリューションによって異なります。 次のセクションでは、Windows ファイアウォールでポートを開く方法について説明します。 各 SQL Server VM で、必要なポートを開きます。

### <a name="open-a-tcp-port-in-the-firewall"></a>ファイアウォールで TCP ポートを開く

1. 最初の SQL サーバーの**スタート**画面で、**[セキュリティが強化された Windows ファイアウォール]** を開きます。
2. 左側のウィンドウで、**[受信の規則]** を選択します。 右側のウィンドウで、 **[新しい規則]** をクリックします。
3. **[規則の種類]** で **[ポート]** を選択します。
4. ポートに対して **[TCP]** を選択し、適切なポート番号を入力します。 次の例を参照してください。

   ![SQL ファイアウォール](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/35-tcpports.png)

5. **[次へ]** をクリックします。
6. **[操作]** ページで、**[接続を許可する]** をオンにしたまま、**[次へ]** をクリックします。
7. **[プロファイル]** ページで、既定の設定をそのまま使用し、**[次へ]** をクリックします。
8. **[名前]** ページで、**[名前]** ボックスに規則の名前 (**Azure LB Probe** など) を指定し、**[完了]** をクリックします。

2 つ目の SQL Server VM についても同じ手順を繰り返します。

## <a name="configure-system-account-permissions"></a>システム アカウントのアクセス許可を構成する

システム アカウント用のアカウントを作成し、適切なアクセス許可を付与するには、各 SQL Server インスタンスで次の手順を実行します。

1. 各 SQL Server インスタンスで、`[NT AUTHORITY\SYSTEM]` 用のアカウントを作成します。 次のスクリプトはこのアカウントを作成します。

   ```sql
   USE [master]
   GO
   CREATE LOGIN [NT AUTHORITY\SYSTEM] FROM WINDOWS WITH DEFAULT_DATABASE=[master]
   GO 
   ```

1. 各 SQL Server インスタンスで、次のアクセス許可を `[NT AUTHORITY\SYSTEM]` に付与します。

   - `ALTER ANY AVAILABILITY GROUP`
   - `CONNECT SQL`
   - `VIEW SERVER STATE`

   次のスクリプトは、これらのアクセス許可を付与します。

   ```sql
   GRANT ALTER ANY AVAILABILITY GROUP TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT CONNECT SQL TO [NT AUTHORITY\SYSTEM]
   GO
   GRANT VIEW SERVER STATE TO [NT AUTHORITY\SYSTEM]
   GO 
   ```

## <a name="next-steps"></a>次の手順

* [Azure 仮想マシンで SQL Server Always On 可用性グループを作成する](virtual-machines-windows-portal-sql-availability-group-tutorial.md)
