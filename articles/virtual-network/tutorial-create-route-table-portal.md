---
title: ネットワーク トラフィックをルーティングする - チュートリアル - Azure portal
titlesuffix: Azure Virtual Network
description: このチュートリアルでは、Azure Portal を使用してルート テーブルでネットワーク トラフィックをルーティングする方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: KumudD
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 03/13/2020
ms.author: kumud
ms.openlocfilehash: d630a41f9b83a852605ffad2a85ad6dd14bbac73
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/08/2020
ms.locfileid: "86079651"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>チュートリアル:Azure Portal を使用してルート テーブルでネットワーク トラフィックをルーティングする

既定では、仮想ネットワーク内のすべてのサブネット間でトラフィックがルーティングされます。 Azure の既定のルーティングは、独自のルートを作成してオーバーライドすることができます。 カスタム ルートは、たとえば、サブネット間でネットワーク仮想アプライアンス (NVA) を越えてトラフィックをルーティングしたい場合に便利です。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * トラフィックをルーティングする NVA を作成する
> * ルート テーブルの作成
> * ルートの作成
> * サブネットへのルート テーブルの関連付け
> * 仮想マシン (VM) を異なるサブネットに展開する
> * NVA を介して、あるサブネットから別のサブネットにトラフィックをルーティングする

このチュートリアルでは、[Azure portal](https://portal.azure.com) を使用します。 [Azure CLI](tutorial-create-route-table-cli.md) または [Azure PowerShell](tutorial-create-route-table-powershell.md) を使用することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-an-nva"></a>NVA を作成する

ネットワーク仮想アプライアンス (NVA) は、ルーティングやファイアウォールの最適化などのネットワーク機能に役立つ仮想マシンです。 このチュートリアルでは、**Windows Server 2016 Datacenter** を使用していることを前提としています。 必要に応じて、別のオペレーティング システムを選択することもできます。

1. [[Azure portal]](https://portal.azure.com) メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。

1. **[セキュリティ]**  >  **[Windows Server 2016 Datacenter]** の順に選択します。

    ![Windows Server 2016 Datacenter、VM の作成、Azure portal](./media/tutorial-create-route-table-portal/vm-ws2016-datacenter.png)

1. **[仮想マシンの作成]** ページの **[基本]** で、次の情報を入力または選択します。

    | Section | 設定 | アクション |
    | ------- | ------- | ----- |
    | **プロジェクトの詳細** | サブスクリプション | サブスクリプションを選択します。 |
    | | Resource group | **[新規作成]** を選択し、「*myResourceGroup*」と入力して、 **[OK]** を選択します。 |
    | **インスタンスの詳細** | 仮想マシン名 | 「*myVmNva*」と入力します。 |
    | | リージョン | **[(米国) 米国東部]** を選択します。 |
    | | 可用性のオプション | **[インフラストラクチャ冗長は必要ありません]** を選択します。 |
    | | Image | **[Windows Server 2016 Datacenter]** を選択します。 |
    | | サイズ | 既定値 **[Standard DS1 v2]** のままにします。 |
    | **管理者アカウント** | ユーザー名 | 任意のユーザー名を入力します。 |
    | | Password | 任意のパスワードを入力します。パスワードは 12 文字以上で、[定義されている複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。 |
    | | パスワードの確認 | パスワードをもう一度入力します。 |
    | **受信ポートの規則** | パブリック受信ポート | **[なし]** を選択します。 |
    | **コストの削減** | Windows Server ライセンスを既にお持ちの場合 | **[いいえ]** を選択します。 |

    ![基本、仮想マシンの作成、Azure portal](./media/tutorial-create-route-table-portal/basics-create-virtual-machine.png)

    次に、 **[次へ:Disks >]\(次へ: ディスク >\)** をクリックします。

1. **[ディスク]** で、自分のニーズに合った設定を選択して、 **[Next: Networking >]\(次へ: ネットワーク >\)** をクリックします。

1. **[ネットワーク]** で次の手順を実行します。

    1. **[仮想ネットワーク]** で、 **[新規作成]** を選択します。
    
    1. **[仮想ネットワークの作成]** ダイアログ ボックスで、 **[名前]** に「*myVirtualNetwork*」と入力します。

    1. **[アドレス空間]** に入力されているアドレス範囲を、*10.0.0.0/16* に置き換えます。

    1. **[サブネット]** で、 **[削除]** アイコンを選択して既存のサブネットを削除してから、次に示す **[サブネット名]** と **[アドレス範囲]** の組み合わせを入力します。 有効な名前と範囲を入力すると、新しい空白行がその下に表示されます。

        | サブネット名 | アドレス範囲 |
        | ----------- | ------------- |
        | *パブリック* | *10.0.0.0/24* |
        | *プライベート* | *10.0.1.0/24* |
        | *DMZ* | *10.0.2.0/24* |

    1. **[OK]** を選択してダイアログ ボックスを閉じます。

    1. **[サブネット]** で **[DMZ (10.0.2.0/24)]** を選択します。

    1. この VM はインターネット経由で接続しないので、 **[パブリック IP]** で **[なし]** を選択します。

    1. **[次へ :Management >]\(次へ: 管理 >\)** をクリックします。

1. **[管理]** で次の手順を実行します。

    1. **[診断ストレージ アカウント]** で、 **[新規作成]** を選択します。
    
    1. **[ストレージ アカウントの作成]** ダイアログ ボックスで、次の情報を入力または選択します。

        | 設定 | 値 |
        | ------- | ----- |
        | 名前 | *mynvastorageaccount* |
        | アカウントの種類 | **ストレージ (汎用 v1)** |
        | パフォーマンス | **Standard** |
        | レプリケーション | **ローカル冗長ストレージ (LRS)** |
    
    1. **[OK]** を選択してダイアログ ボックスを閉じます。

    1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認と作成]** ページが表示され、Azure によって構成が検証されます。

1. "**証に成功しました**" というメッセージが表示されたら、 **[作成]** を選択します。

    VM の作成には数分かかります。 Azure で VM の作成が完了するまでお待ちください。 **[デプロイが進行中です]** ページにデプロイの詳細が表示されます。

1. VM の準備が完了したら、 **[リソースに移動]** を選択します。

## <a name="create-a-route-table"></a>ルート テーブルの作成

1. [[Azure portal]](https://portal.azure.com) メニュー上または **[ホーム]** ページから **[リソースの作成]** を選択します。

2. 検索ボックスに、「"*ルート テーブル*"」と入力します。 検索結果に **[ルート テーブル]** が表示されたら、それを選択します。

3. **[ルート テーブル]** ページで、 **[作成]** を選択します。

4. **[ルート テーブルの作成]** に次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | 名前 | *myRouteTablePublic* |
    | サブスクリプション | 該当するサブスクリプション |
    | Resource group | **myResourceGroup** |
    | 場所 | **(米国) 米国東部** |
    | 仮想ネットワーク ゲートウェイのルート伝達 | **有効** |

    ![ルート テーブルの作成、Azure portal](./media/tutorial-create-route-table-portal/create-route-table.png)

5. **［作成］** を選択します

## <a name="create-a-route"></a>ルートの作成

1. [Azure portal](https://portal.azure.com) に移動して、ルート テーブルを管理します。 **ルート テーブル**を検索して選択します。

1. 目的のルート テーブルの名前 (**myRouteTablePublic**) を選択します。

1. **[ルート]**  >  **[追加]** の順に選択します。

    ![ルートの追加、ルート テーブル、Azure portal](./media/tutorial-create-route-table-portal/add-route.png)

1. **[ルートの追加]** に次の情報を入力または選択します。

    | 設定 | 値 |
    | ------- | ----- |
    | ルート名 | *ToPrivateSubnet* |
    | アドレス プレフィックス | *10.0.1.0/24* (先ほど作成した "*プライベート*" サブネットのアドレス範囲) |
    | ネクストホップの種類 | **仮想アプライアンス** |
    | 次ホップ アドレス | *10.0.2.4* (*DMZ* サブネットのアドレス範囲に含まれるアドレス) |

1. **[OK]** を選択します。

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

1. [Azure portal](https://portal.azure.com) に移動して、仮想ネットワークを管理します。 **仮想ネットワーク**を検索して選択します。

1. 目的の仮想ネットワークの名前を選択します (**myVirtualNetwork**)。

1. 仮想ネットワークのメニュー バーで **[サブネット]** を選択します。

1. 仮想ネットワークのサブネット一覧で **[パブリック]** を選択します。

1. **[ルート テーブル]** で、作成済みのルート テーブル (**myRouteTablePublic**) を選択して **[保存]** を選択し、"*パブリック*" サブネットにルート テーブルを関連付けます。

    ![ルート テーブルの関連付け、サブネット一覧、仮想ネットワーク、Azure portal](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="turn-on-ip-forwarding"></a>IP 転送を有効にする

次に、新しい NVA 仮想マシン (*myVmNva*) の IP 転送を有効にします。 Azure からネットワーク トラフィックが *myVmNva* に送信され、そのトラフィックが別の IP アドレス宛ての場合、IP 転送でトラフィックが正しい場所に送信されます。

1. [Azure portal](https://portal.azure.com) に移動して、VM を管理します。 **[仮想マシン]** を検索して選択します。

1. 目的の VM の名前 (**myVmNva**) を選択します。

1. NVA 仮想マシンのメニュー バーで **[ネットワーク]** を選択します。

1. **[myvmnva123]** を選択します。 これは、Azure によって VM のために作成されたネットワーク インターフェイスです。 数字は、一意の名前となるように Azure により追加されたものです。

    ![ネットワーク、ネットワーク仮想アプライアンス (NVA) の仮想マシン (VM)、Azure portal](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. ネットワーク インターフェイスのメニュー バーで **[IP 構成]** を選択します。

1. **[IP 構成]** ページで、 **[IP 転送]** を **[有効]** に設定して **[保存]** を選択します。

    ![IP 転送の有効化、IP 構成、ネットワーク インターフェイス、ネットワーク仮想アプライアンス (NVA) の仮想マシン (VM)、Azure portal](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>パブリックおよびプライベートの仮想マシンを作成する

仮想ネットワーク内にパブリック VM とプライベート VM を作成します。 後の手順でこれらを使用して、Azure によって *Public* サブネット トラフィックが NVA を介して *Private* サブネットにルーティングされることを確認します。

パブリック VM とプライベート VM の作成する場合は、上記「[NVA を作成する](#create-an-nva)」の手順に従ってください。 デプロイの完了を待ったり、VM リソースに移動したりする必要はありません。 下記を除いて、設定の大部分はそのまま使用します。

**[作成]** を選択してパブリック VM またはプライベート VM を作成する前に、変更すべき値を示した次の 2 つのサブセクション (「[パブリック VM](#public-vm)」と「[プライベート VM](#private-vm)」) を参照してください。 Azure で両方の VM のデプロイが完了したら、次のセクション (「[NVA を経由するトラフィックのルーティング](#route-traffic-through-an-nva)」) に進んでください。

### <a name="public-vm"></a>パブリック VM

| タブ | 設定 | 値 |
| --- | ------- | ----- |
| 基本 | Resource group | **myResourceGroup** |
| | 仮想マシン名 | *myVmPublic* |
| | パブリック受信ポート | **[選択したポートを許可する]** |
| | 受信ポートの選択 | **RDP** |
| ネットワーク | 仮想ネットワーク | **myVirtualNetwork** |
| | Subnet | **[パブリック (10.0.0.0/24)]** |
| | パブリック IP アドレス | 既定値 |
| 管理 | 診断ストレージ アカウント | **mynvastorageaccount** |

### <a name="private-vm"></a>プライベート VM

| タブ | 設定 | 値 |
| --- | ------- | ----- |
| 基本 | Resource group | **myResourceGroup** |
| | 仮想マシン名 | *myVmPrivate* |
| | パブリック受信ポート | **[選択したポートを許可する]** |
| | 受信ポートの選択 | **RDP** |
| ネットワーク | 仮想ネットワーク | **myVirtualNetwork** |
| | Subnet | **[プライベート (10.0.1.0/24)]** |
| | パブリック IP アドレス | 既定値 |
| 管理 | 診断ストレージ アカウント | **mynvastorageaccount** |

## <a name="route-traffic-through-an-nva"></a>NVA を経由するトラフィックのルーティング

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>リモート デスクトップ経由で myVmPrivate にサインインします。

1. [Azure portal](https://portal.azure.com) に移動して、プライベート VM を管理します。 **[仮想マシン]** を検索して選択します。

1. 目的のプライベート VM の名前 (**myVmPrivate**) を選択します。

1. VM のメニュー バーで **[接続]** を選択して、プライベート VM へのリモート デスクトップ接続を作成します。

1. **[Connect with RDP]\(RDP を使用した接続\)** ページで **[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル ( *.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. ダウンロードされた *.rdp* ファイルを開きます。 メッセージが表示されたら、 **[Connect]** を選択します。 **[その他]**  >  **[別のアカウントを使用する]** の順に選択して、プライベート VM の作成時に指定したユーザー名とパスワードを入力します。

1. **[OK]** を選択します。

1. サインイン プロセス中に証明書警告が表示された場合は、 **[はい]** を選択して VM に接続します。

### <a name="enable-icmp-through-the-windows-firewall"></a>ICMP が Windows ファイアウォールを通過できるようにする

後の手順では、ルーティングのテストにトレース ルート ツールを使用します。 トレース ルートはインターネット制御メッセージ プロトコル (ICMP) を使用していますが、Windows ファイアウォールでは既定で拒否されます。 ICMP が Windows ファイアウォールを通過できるようにします。

1. *myVmPrivate* のリモート デスクトップで、PowerShell を開きます。

1. 次のコマンドを入力します。

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

    このチュートリアルでは、トレース ルートを使用してルーティングをテストします。 運用環境では、Windows ファイアウォール経由の ICMP はお勧めしません。

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>myVmNva 内で IP 転送を有効にする

Azure を使用して VM のネットワーク インターフェイスの [IP 転送を有効にしました](#turn-on-ip-forwarding)。 VM のオペレーティング システムでも、ネットワーク トラフィックを転送する必要があります。 これらのコマンドを使用して、*myVmNva* VM のオペレーティング システムの IP 転送を有効にします。

1. *myVmPrivate* VM でコマンド プロンプトを使用して、*myVmNva* VM へのリモート デスクトップ接続を開きます。

    ```cmd
    mstsc /v:myvmnva
    ```

1. *myVmNva* VM 上の PowerShell で次のコマンドを入力して、IP 転送を有効にします。

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. *myVmNva* VM を再起動します。タスク バーで **[スタート]**  >  **[電源]** 、 **[その他 (計画済)]**  >  **[続行]** の順に選択します。

    これで、リモート デスクトップ セッションも切断されます。

1. *myVmNva* VM が再起動したら、*myVmPublic* VM へのリモート デスクトップ セッションを作成します。 *myVmPrivate* VM に接続したまま、コマンド プロンプトを開き、次のコマンドを実行します。

    ```cmd
    mstsc /v:myVmPublic
    ```
1. *myVmPublic* のリモート デスクトップで、PowerShell を開きます。

1. 次のコマンドを入力して、ICMP が Windows ファイアウォールを通過できるようにします。

    ```powershell
    New-NetFirewallRule –DisplayName "Allow ICMPv4-In" –Protocol ICMPv4
    ```

## <a name="test-the-routing-of-network-traffic"></a>ネットワーク トラフィックのルーティングをテストする

まず、*myVmPublic* VM から *myVmPrivate* VM へのネットワーク トラフィックのルーティングをテストしましょう。

1. *myVmPublic* VM 上の PowerShell から、次のコマンドを入力します。

    ```powershell
    tracert myVmPrivate
    ```

    応答は次の例のようになります。

    ```powershell
    Tracing route to myVmPrivate.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.1.4]
    over a maximum of 30 hops:

    1    <1 ms     *        1 ms  10.0.2.4
    2     1 ms     1 ms     1 ms  10.0.1.4

    Trace complete.
    ```

    最初のホップ先が 10.0.2.4 であることを確認できます。これは、NVA のプライベート IP アドレスです。 2 番目のホップ先は、*myVmPrivate* VM のプライベート IP アドレスである10.0.1.4 です。 以前の手順で、このルートを *myRouteTablePublic* ルート テーブルに追加し、それを *Public* サブネットに関連付けました。 その結果、Azure によってトラフィックが *Private* サブネットに直接送信されず、NVA 経由で送信されました。

1. *myVmPublic* VM へのリモート デスクトップ セッションを閉じます。*myVmPrivate* VM にはまだ接続されたままです。

1. *myVmPrivate* VM のコマンド プロンプトから、次のコマンドを入力します。

    ```cmd
    tracert myVmPublic
    ```

    このコマンドでは、*myVmPrivate* VM から *myVmPublic* VM へのネットワーク トラフィックのルーティングをテストします。 応答は次の例のようになります。

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Azure によって *myVmPrivate* VM から *myVmPublic* VM にトラフィックが直接ルーティングされていることがわかります。 既定では、サブネット間でトラフィックが直接ルーティングされます。

1. *myVmPrivate* VM へのリモート デスクトップ セッションを閉じます。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループが不要になったら、*myResourceGroup* とそれに含まれるすべてのリソースを削除します。

1. [Azure portal](https://portal.azure.com) に移動して、リソース グループを管理します。 **リソース グループ**を検索して選択します。

1. 目的のリソース グループの名前 (**myResourceGroup**) を選択します。

1. **[リソース グループの削除]** を選択します。

1. 確認ダイアログ ボックスで、 **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「*myResourceGroup*」と入力して、 **[削除]** を選択します。 Azure により、*myResourceGroup* とそのリソース グループに関連付けられているすべてのリソース (ルート テーブル、ストレージ アカウント、仮想ネットワーク、VM、ネットワーク インターフェイス、パブリック IP アドレスなど) が削除されます。

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、ルート テーブルを作成し、それをサブネットに関連付けました。 トラフィックをパブリック サブネットからプライベート サブネットにルーティングする単純な NVA を作成しました。 これで、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) から、便利なネットワーク機能を多く備えた別の構成済み NVA をデプロイする準備が整いました。 ルーティングの詳細については、[ルーティングの概要](virtual-networks-udr-overview.md)と[ルート テーブルの管理](manage-route-table.md)に関する記事をご覧ください。

仮想ネットワーク内では多数の Azure リソースをデプロイできますが、Azure では、一部の PaaS サービスのリソースを仮想ネットワークにデプロイできません。 一部の Azure PaaS サービスのリソースへのアクセスを制限することは可能ですが、制限対象は仮想ネットワーク サブネットからのトラフィックに限定する必要があります。 Azure PaaS リソースへのネットワーク アクセスを制限する方法を学習するには、次のチュートリアルを参照してください。

> [!div class="nextstepaction"]
> [PaaS リソースへのネットワーク アクセスを制限する](tutorial-restrict-network-access-to-resources.md)

> [!NOTE] 
> Azure サービスは有料です。 Azure Cost Management を使用すると、予算を設定して、支出を制御するためのアラートを構成できます。 Cost Management で Azure のコストを分析、管理、最適化しましょう。 詳しくは、[コスト分析に関するクイックスタート](https://docs.microsoft.com/azure/cost-management-billing/costs/quick-acm-cost-analysis?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)を参照してください。