---
title: チュートリアル:ゾーン内のロード バランサー VM - Azure portal
titlesuffix: Azure Load Balancer
description: このチュートリアルでは、Azure portal を使用して、ゾーン フロントエンドを使った Standard Load Balancer を作成し、可用性ゾーン内の VM の負荷を分散する方法について説明します
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internet traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 0ec9fae1ce4ef976d5f50e1d8d8412354706c5f8
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273397"
---
# <a name="tutorial-load-balance-vms-within-an-availability-zone-with-standard-load-balancer-by-using-the-azure-portal"></a>チュートリアル:Azure portal を使用した Standard Load Balancer による可用性ゾーン内での VM の負荷分散

このチュートリアルでは、Azure portal を使用して、パブリック IP 標準アドレスを使ったゾーン フロントエンドでパブリック [Azure Standard Load Balancer インスタンス](https://aka.ms/azureloadbalancerstandard)を作成します。 このシナリオでは、フロントエンド インスタンスとバックエンド インスタンスに特定のゾーンを指定して、特定のゾーンに合わせてデータ パスとリソースを調整することができます。 次の機能を実行する方法を説明します。

> [!div class="checklist"]
> * ゾーンのフロントエンドを使用して Standard Load Balancer インスタンスを作成する。
> * 着信トラフィック規則を定義するネットワーク セキュリティ グループを作成する。
> * ゾーン仮想マシン (VM) を作成してロード バランサーにアタッチする。
> * ロード バランサーの正常性プローブを作成する。
> * ロード バランサーのトラフィック ルールを作成する。
> * 基本的なインターネット インフォメーション サービス (IIS) サイトを作成する。
> * 動作中のロード バランサーを表示する。

可用性ゾーンと Standard Load Balancer の使用方法について詳しくは、「[Standard Load Balancer と可用性ゾーン](load-balancer-standard-availability-zones.md)」をご覧ください。

必要に応じて、[Azure CLI](load-balancer-standard-public-zonal-cli.md) を使用してこのチュートリアルを完了することもできます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-public-standard-load-balancer-instance"></a>パブリック Standard Load Balancer インスタンスを作成する

Standard Load Balancer では、標準パブリック IP アドレスだけがサポートされています。 ロード バランサーの作成時に新しいパブリック IP を作成すると、自動的に Standard SKU バージョンとして構成されます。 自動的にゾーン冗長にもなります。

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[Load Balancer]** の順に選択します。
2. **[ロード バランサーの作成]** ページの **[基本]** タブで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[確認と作成]** を選択します。

    | Setting                 | 値                                              |
    | ---                     | ---                                                |
    | Subscription               | サブスクリプションを選択します。    |    
    | Resource group         | **[新規作成]** を選択して、テキスト ボックスに「*MyResourceGroupZLB*」と入力します。|
    | EnableAdfsAuthentication                   | *myLoadBalancer*                                   |
    | リージョン         | **[西ヨーロッパ]** を選択します。                                        |
    | Type          | **[パブリック]** を選択します。                                        |
    | SKU           | **[Standard]** を選択します。                          |
    | パブリック IP アドレス | **[新規作成]** を選択します。 |
    | パブリック IP アドレス名              | テキスト ボックスに「*myPublicIP*」と入力します。   |
    |可用性ゾーン| **[1]** を選択します。    |
3. **[確認と作成]** タブで、 **[作成]** をクリックします。   

   ## <a name="create-backend-servers"></a>バックエンド サーバーの作成

このセクションでは、仮想ネットワークを作成します。 また、リージョンの同じゾーン (つまり、ゾーン 1) に 2 つの仮想マシンを作成して、ロード バランサーのバックエンド プールに追加します。 次に、ゾーン冗長ロード バランサーをテストするために仮想マシンに IIS をインストールします。 1 つの VM が失敗した場合、同じゾーンの VM の正常性プローブが失敗します。 同じゾーン内の他の VM によって処理されるトラフィックは続行されます。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成
1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に選択します。  次の仮想ネットワークの値を入力します。
    - **myVnet** - 仮想ネットワークの名前。
    - **myResourceGroupZLB** - 既存のリソース グループの名前。
    - **myBackendSubnet** - サブネット名。
2. **[作成]** を選択して、仮想ネットワークを作成します。

    ![仮想ネットワークの作成](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-network.png)

## <a name="create-a-network-security-group"></a>ネットワーク セキュリティ グループの作成

1. 画面の左上で、 **[リソースの作成]** を選択します。 [検索] ボックスに「**ネットワーク セキュリティ グループ**」と入力します。 ネットワーク セキュリティ グループ ページで、 **[作成]** を選択します。
2. **[ネットワーク セキュリティ グループの作成]** ページで次の値を入力します。
   - **myNetworkSecurityGroup** - ネットワーク セキュリティ グループの名前。
   - **myResourceGroupLBAZ** - 既存のリソース グループの名前。
   
     ![ネットワーク セキュリティ グループの作成](./media/tutorial-load-balancer-standard-zonal-portal/create-network-security-group.png)

### <a name="create-nsg-rules"></a>NSG ルールを作成する

このセクションでは、Azure portal を使用して HTTP と Microsoft リモート デスクトップ プロトコル (RDP) を使用する受信接続を許可するための NSG 規則を作成します。

1. Azure portal の左側のメニューで **[すべてのリソース]** を選択します。 **myNetworkSecurityGroup** を検索して選択します。 これは **myResourceGroupZLB** リソース グループにあります。
2. **[設定]** で **[受信セキュリティ規則]** を選びます。 その後、 **[追加]** を選択します。
3. **myHTTPRule** という名前の受信セキュリティ規則のために以下の値を入力し、ポート 80 を使用する受信 HTTP 接続を許可します。
    - **サービス タグ** - **ソース**。
    - **インターネット** - **ソース サービス タグ**。
    - **80** - **宛先ポート範囲**。
    - **vTCP** - **プロトコル**。
    - **許可** - **アクション**。
    - **100** - **優先度**。
    - **myHTTPRule** - **名前**。
    - **HTTP を許可する** - **説明**。
4. **[OK]** を選択します。
 
   ![NSG ルールを作成する](./media/load-balancer-standard-public-availability-zones-portal/8-load-balancer-nsg-rules.png)

5. 手順 2. から 4. を繰り返して、**myRDPRule** という別の規則を作成します。 この規則はポート 3389 を使用する受信 RDP 接続に対応し、値は次のとおりです。
    - **サービス タグ** - **ソース**。
    - **インターネット** - **ソース サービス タグ**。
    - **3389** - **宛先ポート範囲**。
    - **TCP** - **プロトコル**。
    - **許可** - **アクション**。
    - **200** - **優先度**。
    - **myRDPRule** - **名前**。
    - **RDP を許可する** - **説明**。

      ![RDP 規則を作成する](./media/tutorial-load-balancer-standard-zonal-portal/create-rdp-rule.png)

### <a name="create-virtual-machines"></a>仮想マシンを作成する

1. 画面の左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[Windows Server 2016 Datacenter]** の順に選択します。 次の仮想マシンの値を入力します。
    - **myVM1** - 仮想マシンの名前。        
    - **azureuser** - 管理者のユーザー名。    
    - **myResourceGroupLB** - **リソース グループ**。 **[既存のものを使用]** 、 **[myResourceGroupZLB]** の順に選択します。
2. **[OK]** を選択します。
3. 仮想マシンのサイズとして **[DS1_V2]** を選択します。 **[選択]** を選択します。
4. VM の設定に以下の値を入力します。
    - **ゾーン 1** - VM を配置する可用性ゾーン。
    -  **myVNet** - 仮想ネットワークとしてこれが選択されていることを確認します。
    - **myVM1PIP** - 作成する標準パブリック IP アドレス。 **[新規作成]** を選択します。 名前の種類として **[myVM1PIP]** を選択します。 **[ゾーン]** には **[1]** を選択します。 IP アドレスの SKU は、既定では Standard です。
    - **myBackendSubnet** - サブネットとして選択されていることを確認します。
    - **myNetworkSecurityGroup** - 既存のネットワーク セキュリティ グループのファイアウォールの名前。
5. **[無効]** を選択して、ブート診断を無効にします。
6. **[OK]** を選択します。 [概要] ページで設定を確認します。 **[作成]** を選択します。
7. 手順 1. から 6. を繰り返して、**myVM2** という 2 番目の VM をゾーン 1 に作成します。 **myVnet** を仮想ネットワークにします。 **myVM2PIP** を標準パブリック IP アドレスにします。 **myBackendSubnet** をサブネットにします。 **myNetworkSecurityGroup** をネットワーク セキュリティ グループにします。

    ![仮想マシンを作成する](./media/tutorial-load-balancer-standard-zonal-portal/create-virtual-machine.png) 

### <a name="install-iis-on-vms"></a>VM に IIS をインストールする

1. 左側のメニューで **[すべてのリソース]** を選択します。 リソースの一覧から **[myVM1]** を選択します。 これは **myResourceGroupZLB** リソース グループにあります。
2. **[概要]** ページで **[接続]** を選択して、RDP を使用して VM に接続します。
3. VM の作成時に指定したユーザー名とパスワードを使用して VM にサインインします。 VM の作成時に入力した資格情報を指定するには、 **[その他]** を選択する必要があります。 次に、 **[別のアカウントを使う]** を選択します。 **[OK]** を選択します。 サインイン処理中に証明書の警告が表示される場合があります。 **[はい]** を選択して、接続処理を続行します。
4. サーバーのデスクトップで、 **[Windows 管理ツール]**  >  **[Windows PowerShell]** の順に移動します。
6. **PowerShell** ウィンドウで、次のコマンドを実行して IIS サーバーをインストールします。 また、これらのコマンドは、既定の iisstart.htm ファイルを削除して、VM の名前を表示する新しい iisstart.htm ファイルを追加します。

   ```azurepowershell-interactive
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from" + $env:computername)
   ```
7. **myVM1** で RDP セッションを閉じます。
8. 手順 1. から 7. を繰り返して、**myVM2** に IIS をインストールします。

## <a name="create-load-balancer-resources"></a>ロード バランサーのリソースを作成する

このセクションでは、バックエンド アドレス プールと正常性プローブのロード バランサー設定を構成します。 ロード バランサーとネットワーク アドレス変換ルールも指定します。


### <a name="create-a-backend-address-pool"></a>バックエンド アドレス プールの作成

トラフィックを VM に分散するために、バックエンド アドレス プールには、ロード バランサーに接続される仮想ネットワーク インターフェイス カードの IP アドレスが含まれます。 バックエンド アドレス プール **myBackendPool** を作成し、**VM1** および **VM2** を含めます。

1. 左側のメニューで **[すべてのリソース]** を選択します。 リソースの一覧から **[myLoadBalancer]** を選択します。
2. **[設定]** で、 **[バックエンド プール]** を選択します。 その後、 **[追加]** を選択します。
3. **[バックエンド プールの追加]** ページで、以下のアクションを実行します。
    - バックエンド プールの名前として、[名前] に「**myBackEndPool**」と入力します。
    - **[仮想ネットワーク]** の場合は、ドロップダウン メニューの **[myVNet]** を選択します。 
    - **[仮想マシン]** および **[IP アドレス]** には、**myVM1** および **myVM2** と、これらに該当するパブリック IP アドレスを追加します。
4. **[追加]** を選択します。
5. ロード バランサーのバックエンド プールの設定に、**myVM1** と **myVM2** の両方の VM が表示されていることを確認します。
 
    ![バックエンド プールの作成](./media/tutorial-load-balancer-standard-zonal-portal/create-backend-pool.png) 

### <a name="create-a-health-probe"></a>正常性プローブの作成

ロード バランサーでアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 正常性プローブ **myHealthProbe** を作成し、VM の正常性を監視します。

1. 左側のメニューで **[すべてのリソース]** を選択します。 リソースの一覧から **[myLoadBalancer]** を選択します。
2. **[設定]** で、 **[正常性プローブ]** を選択します。 その後、 **[追加]** を選択します。
3. 正常性プローブの作成では、以下の値を使用します。
    - **myHealthProbe** - 正常性プローブの名前。
    - **HTTP** - プロトコルの種類。
    - **80** - ポート番号。
    - **15** - プローブの試行の**間隔**を示す秒数。
    - **2** - **異常しきい値**またはプローブの連続する失敗の回数。この回数を超えると、VM は異常と見なされます。
4. **[OK]** を選択します。

   ![正常性プローブの追加](./media/load-balancer-standard-public-availability-zones-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則は、VM に対するトラフィックの分散方法を定義します。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 フロント エンド **FrontendLoadBalancer** のポート 80 をリッスンするために、**myLoadBalancerRuleWeb** というロード バランサー規則を作成します。 この規則は、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール **myBackEndPool** に送信し、この送信にもポート 80 が使用されます。 

1. 左側のメニューで **[すべてのリソース]** を選択します。 リソースの一覧から **[myLoadBalancer]** を選択します。
2. **[設定]** で、 **[負荷分散規則]** を選択します。 その後、 **[追加]** を選択します。
3. 負荷分散規則の構成には、以下の値を使用します。
    - **myHTTPRule** - 負荷分散規則の名前。
    - **TCP** - プロトコルの種類。
    - **80** - ポート番号。
    - **80** - バックエンド ポート。
    - **myBackendPool** - バックエンド プールの名前。
    - **myHealthProbe** - 正常性プローブの名前。
4. **[OK]** を選択します。
    
    ![負荷分散規則の追加](./media/tutorial-load-balancer-standard-zonal-portal/load-balancing-rule.png)

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする
1. **[概要]** 画面で、ロード バランサーのパブリック IP アドレスを見つけます。 **[すべてのリソース]** を選択します。 次に、 **[myPublicIP]** を選択します。 

2. パブリック IP アドレスをコピーします。 次に、それをブラウザーのアドレス バーに貼り付けます。 Web サーバー ページの名前を含む既定のページが、ブラウザーに表示されます。

      ![IIS Web サーバー](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)
3. 動作中のロード バランサーを確認するには、表示されている VM を強制的に停止します。 ブラウザーを更新して、ブラウザーに表示される他のサーバー名を参照します。

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、ロード バランサー、および関連するすべてのリソースは、不要になったら削除します。 ロード バランサーを含むリソース グループを選択ます。 次に、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

- [Standard Load Balancer](load-balancer-standard-overview.md) の詳細を確認する。
- [可用性ゾーン間で VM の負荷分散を行う](tutorial-load-balancer-standard-public-zone-redundant-portal.md)。
