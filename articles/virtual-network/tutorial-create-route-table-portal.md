---
title: ネットワーク トラフィックをルーティングする - チュートリアル - Azure portal
titlesuffix: Azure Virtual Network
description: このチュートリアルでは、Azure Portal を使用してルート テーブルでネットワーク トラフィックをルーティングする方法について説明します。
services: virtual-network
documentationcenter: virtual-network
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
Customer intent: I want to route traffic from one subnet, to a different subnet, through a network virtual appliance.
ms.assetid: ''
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: tutorial
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: jdial
ms.custom: mvc
ms.openlocfilehash: 855adccf036f731de12810fe0f5287186048ddb0
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/28/2019
ms.locfileid: "55095959"
---
# <a name="tutorial-route-network-traffic-with-a-route-table-using-the-azure-portal"></a>チュートリアル:Azure Portal を使用してルート テーブルでネットワーク トラフィックをルーティングする

既定では、仮想ネットワーク内のすべてのサブネット間でトラフィックがルーティングされます。 Azure の既定のルーティングは、独自のルートを作成してオーバーライドすることができます。 カスタム ルートを作成する機能は、たとえば、サブネット間でネットワーク仮想アプライアンス (NVA) を越えてトラフィックをルーティングしたい場合に便利です。 このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * ルート テーブルの作成
> * ルートの作成
> * 複数のサブネットを含んだ仮想ネットワークを作成する
> * サブネットへのルート テーブルの関連付け
> * トラフィックをルーティングする NVA を作成する
> * 仮想マシン (VM) を異なるサブネットに展開する
> * NVA を介して、あるサブネットから別のサブネットにトラフィックをルーティングする

好みに応じて、[Azure CLI](tutorial-create-route-table-cli.md) または [Azure PowerShell](tutorial-create-route-table-powershell.md) を使ってこのチュートリアルの手順を実行することもできます。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

[Azure Portal](https://portal.azure.com) にサインインします。

## <a name="create-a-route-table"></a>ルート テーブルの作成

1. 画面の左上で、**[リソースの作成]** > **[ネットワーキング]** > **[ルート テーブル]** の順に選択します。

1. **[ルート テーブルの作成]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | Name | 「*myRouteTablePublic*」と入力します。 |
    | サブスクリプション | サブスクリプションを選択します。 |
    | リソース グループ | **[新規作成]** を選択し、「*myResourceGroup*」と入力して、*[OK]* を選択します。 |
    | 場所 | 既定値 **[米国東部]** のままにします。
    | BGP ルート伝達 | 既定値 **[有効]** のままにします。 |

1. **作成**を選択します。

## <a name="create-a-route"></a>ルートの作成

1. ポータルの検索バーで、「*myRouteTablePublic*」と入力します。

1. 検索結果に **myRouteTablePublic** が表示されたら、それを選択してください。

1. **[myRouteTablePublic]** で、**[設定]** の下にある **[ルート]** > **[+ 追加]** を選択します。

    ![ルートを追加する](./media/tutorial-create-route-table-portal/add-route.png)

1. **[ルートの追加]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | ルート名 | 「*ToPrivateSubnet*」と入力します。 |
    | アドレス プレフィックス | 「*10.0.1.0/24*」と入力します。 |
    | 次ホップの種類 | **[仮想アプライアンス]** を選択します。 |
    | 次ホップ アドレス | 「*10.0.2.4*」と入力します。 |

1. **[OK]** を選択します。

## <a name="associate-a-route-table-to-a-subnet"></a>サブネットへのルート テーブルの関連付け

ルート テーブルをサブネットに関連付けるには、仮想ネットワークとサブネットを作成しておく必要があります。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. 画面の左上で、**[リソースの作成]** > **[ネットワーキング]** > **[仮想ネットワーク]** の順に選択します。

1. **[仮想ネットワークの作成]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | Name | 「*myVirtualNetwork*」と入力します。 |
    | アドレス空間 | 「*10.0.0.0/16*」と入力します。 |
    | サブスクリプション | サブスクリプションを選択します。 |
    | リソース グループ | ***[既存項目の選択]*** > **[myResourceGroup]** を選択します。 |
    | 場所 | 既定値 **[米国東部]** のままにします。 |
    | サブネット - 名前 | 「*Public*」と入力します。 |
    | サブネット アドレス範囲 | 「*10.0.0.0/24*」と入力します。 |

1. 残りの部分は既定値のままにし、**[作成]** を選択します。

### <a name="add-subnets-to-the-virtual-network"></a>仮想ネットワークにサブネットを追加します

1. ポータルの検索バーに「*myVirtualNetwork*」と入力します。

1. 検索結果に **[myVirtualNetwork]** が表示されたら、それを選択します。

1. **[myVirtualNetwork]** で、**[設定]** の下にある **[サブネット]** > **[+ サブネット]** を選択します。

    ![サブネットの追加](./media/tutorial-create-route-table-portal/add-subnet.png)

1. **[サブネットの追加]** に次の情報を入力します。

    | Setting | 値 |
    | ------- | ----- |
    | Name | 「*Private*」と入力します。 |
    | アドレス空間 | 「*10.0.1.0/24*」と入力します。 |

1. 残りの部分は既定値のままにし、**[OK]** を選択します。

1. **[+ サブネット]** をもう一度選択します。 今回は次の情報を入力します。

    | Setting | 値 |
    | ------- | ----- |
    | Name | 「*DMZ*」と入力します。 |
    | アドレス空間 | 「*10.0.2.0/24*」と入力します。 |

1. 前回と同様に、残りの部分は既定値のままにし、**[OK]** を選択します。

    Azure に 3 つのサブネット**Public**、**Private**、**DMZ** が表示されます。

### <a name="associate-myroutetablepublic-to-your-public-subnet"></a>myRouteTablePublic を Public サブネットに関連付けます。

1. **[パブリック]** を選択します。

1. **[Public]** で **[ルート テーブル]** > **[MyRouteTablePublic]** > **[保存]** の順に選択します。

    ![ルート テーブルを関連付ける](./media/tutorial-create-route-table-portal/associate-route-table.png)

## <a name="create-an-nva"></a>NVA を作成する

NVA は、ルーティングやファイアウォールの最適化などのネットワーク機能を支援する VM です。 必要に応じて、別のオペレーティング システムを選択することもできます。 このチュートリアルでは、**Windows Server 2016 Datacenter** を使用していることを前提としています。

1. 画面の左上で、**[リソースの作成]** > **[Compute]** > **[Windows Server 2016 Datacenter]** の順に選択します。

1. **[仮想マシンの作成 - 基本]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | **プロジェクトの詳細** | |
    | サブスクリプション | サブスクリプションを選択します。 |
    | リソース グループ | **[myResourceGroup]** を選択します。 |
    | **インスタンスの詳細** |  |
    | 仮想マシン名 | 「*myVmNva*」と入力します。 |
    | リージョン | **[米国東部]** を選択します。 |
    | 可用性のオプション | 既定値 **[インフラストラクチャ冗長は必要ありません]** をそのまま使用します。 |
    | イメージ | 既定値 **[Microsoft Windows Server 2016 Datacenter]** をそのまま使用します。 |
    | サイズ | 既定値 **[Standard DS1 v2]** をそのまま使用します。 |
    | **管理者アカウント** |  |
    | ユーザー名 | 任意のユーザー名を入力します。 |
    | パスワード | 任意のパスワードを入力します。 パスワードは 12 文字以上で、[定義された複雑さの要件](../virtual-machines/windows/faq.md?toc=%2fazure%2fvirtual-network%2ftoc.json#what-are-the-password-requirements-when-creating-a-vm)を満たす必要があります。|
    | パスワードの確認 | パスワードを再入力します。 |
    | **受信ポートの規則** |  |
    | パブリック受信ポート | 既定値 **[なし]** のままにします。
    | **コスト削減** |  |
    | Windows ライセンスを既にお持ちの場合 | 既定値 **[なし]** のままにします。 |

1. **[次へ :ディスク]** を選択します。

1. **[仮想マシンの作成 - ディスク]** で、ニーズに合った設定を選択します。

1. **[次へ :ネットワーク]** を選択します。

1. **[仮想マシンの作成 - ネットワーク]** で次の情報を選択します。

    | Setting | 値 |
    | ------- | ----- |
    | 仮想ネットワーク | 既定値 **[myVirtualNetwork]** のままにします。 |
    | サブネット | **[DMZ (10.0.2.0/24)]** を選択します。 |
    | パブリック IP | **[なし]** を選択します。 パブリック IP アドレスは必要ありません。 この VM がインターネット経由で接続することはありません。|

1. 残りの部分は既定値のままにし、**[次へ:管理]** を選択します。

1. **[仮想マシンの作成 - 管理]** の **[診断ストレージ アカウント]** で **[新規作成]** を選択します。

1. **[ストレージ アカウントの作成]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | Name | 「*mynvastorageaccount*」と入力します。 |
    | アカウントの種類 | 既定値 **[ストレージ (汎用 v1)]** のままにします。 |
    | パフォーマンス | 既定値 **[標準]** のままにします。 |
    | レプリケーション | 既定値 **[ローカル冗長ストレージ (LRS)]** のままにします。

1. **[OK]** を選択します。

1. **[Review + create]\(レビュー + 作成\)** を選択します。 **[確認および作成]** ページが表示され、Azure によって構成が検証されます。

1. **検証に成功した**ことを確認したら、**[作成]** を選択します。

    VM の作成には数分かかります。 Azure で VM の作成が完了するまで次に進まないでください。 **[デプロイが進行中です]** ページにデプロイの詳細が表示されます。

1. VM の準備が完了したら、**[リソースに移動]** を選択します。

## <a name="turn-on-ip-forwarding"></a>IP 転送を有効にする

*myVmNva* の IP 転送を有効にします。 Azure からネットワーク トラフィックが *myVmNva* に送信され、そのトラフィックが別の IP アドレス宛ての場合、IP 転送でトラフィックが正しい場所に送信されます。

1. **[myVmNva]** で、**[設定]** の下にある **[ネットワーク]** を選択します。

1. **[myvmnva123]** を選択します。 これは、Azure によって VM のために作成されたネットワーク インターフェイスです。 お客様に固有の数字の文字列が割り当てられます。

    ![VM ネットワーク](./media/tutorial-create-route-table-portal/virtual-machine-networking.png)

1. **[設定]** の下にある **[IP 構成]** を選択します。

1. **[myvmnva123 - IP 構成]** の **[IP 転送]** で、**[有効]**、**[保存]** の順に選択します。

    ![IP 転送を有効にする](./media/tutorial-create-route-table-portal/enable-ip-forwarding.png)

## <a name="create-public-and-private-virtual-machines"></a>パブリックおよびプライベートの仮想マシンを作成する

仮想ネットワーク内にパブリック VM とプライベート VM を作成します。 後の手順でこれらを使用して、Azure によって *Public* サブネット トラフィックが NVA を介して *Private* サブネットにルーティングされることを確認します。

[NVA の作成](#create-an-nva)手順の 1 から 12 を実行します。 ほとんどは同じ設定を使用します。 次の値は、変更する必要があります。

| Setting | 値 |
| ------- | ----- |
| **Public VM** | |
| 基本 |  |
| 仮想マシン名 | 「*myVmPublic*」と入力します。 |
| ネットワーク | |
| サブネット | **[Public (10.0.0.0/24)]** を選択します。 |
| パブリック IP アドレス | 既定値のままにします。 |
| パブリック受信ポート | **[選択したポートを許可する]** を選択します。 |
| 受信ポートの選択 | **[HTTP]** と **[RDP]** を選択します。 |
| 管理 | |
| 診断ストレージ アカウント | 既定値 **[mynvastorageaccount]** のままにします。 |
| **Private VM** | |
| 基本 |  |
| 仮想マシン名 | 「*myVmPrivate*」と入力します。 |
| ネットワーク | |
| サブネット | **[Private (10.0.1.0/24)]** を選択します。 |
| パブリック IP アドレス | 既定値のままにします。 |
| パブリック受信ポート | **[選択したポートを許可する]** を選択します。 |
| 受信ポートの選択 | **[HTTP]** と **[RDP]** を選択します。 |
| 管理 | |
| 診断ストレージ アカウント | 既定値 **[mynvastorageaccount]** のままにします。 |

Azure で *myVmPublic* VM が作成されている場合に *myVmPrivate* VM を作成できます。 Azure で両方の VM の作成が完了するまで、以降の手順に進まないでください。

## <a name="route-traffic-through-an-nva"></a>NVA を経由するトラフィックのルーティング

### <a name="sign-in-to-myvmprivate-over-remote-desktop"></a>リモート デスクトップ経由で myVmPrivate にサインインします。

1. ポータルの検索バーで、「*myVmPrivate*」と入力します。

1. 検索結果に **[myVmPrivate]** VM が表示されたら、それを選択します。

1. **[接続]** を選択して、*myVmPrivate* VM へのリモート デスクトップ接続を作成します。

1. **[仮想マシンへの接続]** で、**[RDP ファイルのダウンロード]** を選択します。 リモート デスクトップ プロトコル (*.rdp*) ファイルが作成され、お使いのコンピューターにダウンロードされます。

1. ダウンロードされた *.rdp* ファイルを開きます。

    1. メッセージが表示されたら、**[Connect]** を選択します。

    1. Private VM の作成時に指定したユーザー名とパスワードを入力します。

    1. 必要に応じて **[その他]** > **[別のアカウントを使用する]** を選択して、Private VM の資格情報を使用します。

1. **[OK]** を選択します。

    サインイン処理中に証明書の警告が表示される場合があります。

1. **[はい]** を選択して VM に接続します。

### <a name="enable-icmp-through-the-windows-firewall"></a>ICMP が Windows ファイアウォールを通過できるようにする

後の手順では、ルーティングのテストにトレース ルート ツールを使用します。 トレース ルートはインターネット制御メッセージ プロトコル (ICMP) を使用していますが、Windows ファイアウォールでは既定で拒否されます。 ICMP が Windows ファイアウォールを通過できるようにします。

1. *myVmPrivate* のリモート デスクトップで、PowerShell を開きます。

1. 次のコマンドを入力します。

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
    ```

    このチュートリアルでは、トレース ルートを使用してルーティングをテストしています。 運用環境では、Windows ファイアウォール経由の ICMP はお勧めしません。

### <a name="turn-on-ip-forwarding-within-myvmnva"></a>myVmNva 内で IP 転送を有効にする

Azure を使用して VM のネットワーク インターフェイスの [IP 転送を有効にしました](#turn-on-ip-forwarding)。 VM のオペレーティング システムでも、ネットワーク トラフィックを転送する必要があります。 これらのコマンドを使用して、*myVmNva* VM のオペレーティング システムの IP 転送を有効にします。

1. *myVmPrivate* VM でコマンド プロンプトを使用して、*myVmNva* VM へのリモート デスクトップ接続を開きます。

    ```cmd
    mstsc /v:myvmnva
    ```

1. *myVmNva* 上の PowerShell から、このコマンドを入力して IP 転送を有効にします。

    ```powershell
    Set-ItemProperty -Path HKLM:\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters -Name IpEnableRouter -Value 1
    ```

1. *myVmNva* VM を再起動します。 タスク バーから**スタート ボタン** > **電源ボタン**、**[その他 (計画済)]** > **[続行]** の順に選択します。

    これで、リモート デスクトップ セッションも切断されます。

1. *myVmNva* VM が再起動したら、*myVmPublic* VM へのリモート デスクトップ セッションを作成します。 *myVmPrivate* VM に接続したまま、コマンド プロンプトを開き、次のコマンドを実行します。

    ```cmd
    mstsc /v:myVmPublic
    ```
1. *myVmPublic* のリモート デスクトップで、PowerShell を開きます。

1. 次のコマンドを入力して、ICMP が Windows ファイアウォールを通過できるようにします。

    ```powershell
    New-NetFirewallRule –DisplayName “Allow ICMPv4-In” –Protocol ICMPv4
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

    最初のホップが 10.0.2.4 であることがわかります。 これは NVA のプライベート IP アドレスです。 2 番目のホップ先は、*myVmPrivate* VM のプライベート IP アドレスである10.0.1.4 です。 以前の手順で、このルートを *myRouteTablePublic* ルート テーブルに追加し、それを *Public* サブネットに関連付けました。 その結果、Azure によってトラフィックが *Private* サブネットに直接送信されず、NVA 経由で送信されました。

1. *myVmPublic* VM へのリモート デスクトップ セッションを閉じます。*myVmPrivate* VM にはまだ接続されたままです。

1. *myVmPrivate* VM のコマンド プロンプトから、次のコマンドを入力します。

    ```cmd
    tracert myVmPublic
    ```

    これで、*myVmPrivate* VM から *myVmPublic* VM へのネットワーク トラフィックのルーティングをテストします。 応答は次の例のようになります。

    ```cmd
    Tracing route to myVmPublic.vpgub4nqnocezhjgurw44dnxrc.bx.internal.cloudapp.net [10.0.0.4]
    over a maximum of 30 hops:

    1     1 ms     1 ms     1 ms  10.0.0.4

    Trace complete.
    ```

    Azure によって *myVmPrivate* VM から *myVmPublic* VM にトラフィックが直接ルーティングされていることがわかります。 既定では、サブネット間でトラフィックが直接ルーティングされます。

1. *myVmPrivate* VM へのリモート デスクトップ セッションを閉じます。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループとそれに含まれるすべてのリソースが不要になったら削除します。

1. ポータルの検索バーに、「*myResourceGroup*」と入力します。

1. 検索結果に **[myResourceGroup]** が表示されたら、それを選択します。

1. **[リソース グループの削除]** を選択します。

1. **[TYPE THE RESOURCE GROUP NAME:]\(リソース グループ名を入力してください:\)** に「*myResourceGroup*」と入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、ルート テーブルを作成し、それをサブネットに関連付けました。 トラフィックをパブリック サブネットからプライベート サブネットにルーティングする単純な NVA を作成しました。 その手順を理解したので、[Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking) の別の事前構成された NVA をデプロイすることができます。 役に立つ数多くのネットワーク機能を備えたものがあります。 ルーティングの詳細については、[ルーティングの概要](virtual-networks-udr-overview.md)と[ルート テーブルの管理](manage-route-table.md)に関する記事をご覧ください。

仮想ネットワーク内では多数の Azure リソースをデプロイできますが、Azure では、一部の PaaS サービスのリソースを仮想ネットワークにデプロイできません。 一部の Azure PaaS サービスのリソースへのアクセスを制限することはできます。 ただし、この制限は仮想ネットワーク サブネットからのトラフィックに限定する必要があります。 Azure PaaS リソースへのネットワーク アクセスを制限する方法を学習するには、次のチュートリアルに進んでください。

> [!div class="nextstepaction"]
> [PaaS リソースへのネットワーク アクセスを制限する](tutorial-restrict-network-access-to-resources.md)
