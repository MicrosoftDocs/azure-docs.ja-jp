---
title: チュートリアル:内部ロード バランサーの作成 - Azure portal
titlesuffix: Azure Load Balancer
description: このチュートリアルでは、Azure portal を使用して、内部 Basic ロード バランサーを作成する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: As an IT administrator, I want to create a load balancer that load balances incoming internal traffic to virtual machines within a specific zone in a region.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/27/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 09a73b5d97e46797dd880f64383fdf03fd591f3d
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273492"
---
# <a name="tutorial-balance-internal-traffic-load-with-a-basic-load-balancer-in-the-azure-portal"></a>チュートリアル:Azure portal の Basic ロードバランサーを使用して内部トラフィックの負荷を分散する

負荷分散では、着信要求を仮想マシン (VM) に分散させることで、より高いレベルの可用性とスケールを実現します。 Azure portal を使用して、Basic ロード バランサーを作成し、内部トラフィックを VM 間で分散させることができます。 このチュートリアルでは、Basic 価格レベルで、内部ロード バランサー、バックエンド サーバー、およびネットワーク リソースを作成および構成する方法について説明します。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

必要に応じて、ポータルではなく [Azure CLI](load-balancer-get-started-ilb-arm-cli.md) または [Azure PowerShell](load-balancer-get-started-ilb-arm-ps.md) を使用してこのような手順を実行できます。

このチュートリアルを使用してこの手順を実行するには、[https://portal.azure.com](https://portal.azure.com) の Azure portal にサインインします。

## <a name="create-a-vnet-back-end-servers-and-a-test-vm"></a>VNet、バックエンド サーバー、およびテスト VM を作成する

まず仮想ネットワーク (VNet) を作成します。 VNet では、Basic ロードバランサーのバックエンド プールに使用する 2 つの VM と、ロード バランサーのテストに使用する 3 つ目の VM を作成します。 

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. ポータルの画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に選択します。
   
1. **[仮想ネットワークの作成]** ウィンドウで、以下の値を入力または選択します。
   
   - **[名前]** :「*MyVNet*」と入力します。
   - **ResourceGroup**: **[新規作成]** を選択してから「*MyResourceGroupLB*」と入力し、 **[OK]** を選択します。 
   - **[サブネット]**  >  **[名前]** :「*MyBackendSubnet*」と入力します。
   
1. **作成** を選択します。

   ![仮想ネットワークの作成](./media/tutorial-load-balancer-basic-internal-portal/2-load-balancer-virtual-network.png)

### <a name="create-virtual-machines"></a>仮想マシンを作成する

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[Windows Server 2016 Datacenter]** の順に選択します。 
   
1. **[仮想マシンの作成]** の **[Basic]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : ドロップダウンから **[MyResourceGroupLB]** を選択します。
   - **[インスタンスの詳細]**  >  **[仮想マシン名]** : 「*MyVM1*」と入力します。
   - **[インスタンスの詳細]**  >  **[可用性オプション]** : 
     1. ドロップダウン メニューから **[可用性セット]** を選択します。 
     2. **[新規作成]** を選択し、「*MyAvailabilitySet*」と入力して **[OK]** を選択します。
   
1. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。 
   
   以下が選択されていることを確認します。
   - **[仮想ネットワーク]** :**MyVNet**
   - **[サブネット]** : **MyBackendSubnet**
   
   **[ネットワーク セキュリティ グループ]** :
   1. **[Advanced] \(詳細設定)** を選択します。 
   1. ドロップダウン メニュー **[ネットワーク セキュリティ グループの構成]** から **[なし]** を選択します。 
   
1. **[管理]** タブまたは **[次へ]**  >  **[管理]** を選択します。 **[監視]** で **[ブート診断]** を **[オフ]** に設定します。
   
1. **[Review + create]\(レビュー + 作成\)** を選択します。
   
1. 設定を確認し、 **[作成]** を選択します。 

1. 手順に従って *MyVM2* という名前の 2 つ目の VM を作成します。他のすべての設定は MyVM1 と同じです。 

1. この手順に従って *MyTestVM* という 3 つ目の VM を作成します。 

## <a name="create-a-basic-load-balancer"></a>Basic Load Balancer を作成する

ポータルを使用して Basic 内部ロード バランサーを作成します。 作成する名前と IP アドレスは、ロード バランサーのフロント エンドとして自動的に構成されます。

1. ポータルの左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[Load Balancer]** の順に選択します。
   
2. **[ロード バランサーの作成]** ページの **[基本]** タブで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[確認と作成]** を選択します。

    | Setting                 | 値                                              |
    | ---                     | ---                                                |
    | Subscription               | サブスクリプションを選択します。    |    
    | Resource group         | **[新規作成]** を選択して、テキスト ボックスに「*MyResourceGroupLB*」と入力します。|
    | EnableAdfsAuthentication                   | *myLoadBalancer*                                   |
    | リージョン         | **[西ヨーロッパ]** を選択します。                                        |
    | Type          | **[内部]** を選択します。                                        |
    | SKU           | **[Basic]** を選択します。                          |
    | 仮想ネットワーク           | *[MyVNet]* を選択します。                          |    
    | IP アドレスの割り当て              | **[静的]** を選択します。   |
    | プライベート IP アドレス|仮想ネットワークとサブネットのアドレス空間内にあるアドレスを入力します (例: *10.3.0.7*)。  |

3. **[確認と作成]** タブで、 **[作成]** をクリックします。 
   

## <a name="create-basic-load-balancer-resources"></a>Basic ロード バランサーのリソースを作成する

このセクションでは、バックエンド アドレス プールと正常性プローブのロード バランサー設定を構成し、ロード バランサーの規則を指定します。

### <a name="create-a-back-end-address-pool"></a>バックエンド アドレス プールを作成する

トラフィックを複数の VM に分散させるために、バックエンド アドレス プールを使用します。 バックエンド アドレス プールに、ロード バランサーに接続される仮想ネットワーク インターフェイス (NIC) の IP アドレスを含めます。 

**VM1 と VM2 を含むバックエンド アドレス プールを作成するには:**

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[MyLoadBalancer]** を選択します。
   
1. **[設定]** で、 **[バックエンド プール]** 、 **[追加]** の順に選択します。
   
1. **[バックエンド プールの追加]** ページで、以下の値を入力または選択します。
   
   - **[名前]** :「*MyBackendPool*」と入力します。
   - **[関連付け先]** :ドロップダウン メニューから **[可用性セット]** を選択します。
   - **[可用性セット]** : **[MyAvailabilitySet]** を選択します。
   
1. **[ターゲット ネットワーク IP 構成の追加]** を選択します。 
   1. **[MyVM1]** と **[MyVM2]** をバックエンド プールに追加します。
   2. 各マシンを追加したら、ドロップダウンメニューから **[ネットワーク IP 構成]** を選択します。 
   
   >[!NOTE]
   >**[MyTestVM]** はプールに追加しないでください。 
   
1. **[OK]** を選択します。
   
   ![バックエンド アドレス プールの追加](./media/tutorial-load-balancer-basic-internal-portal/3-load-balancer-backend-02.png)
   
1. **[バックエンド プール]** ページで、 **[MyBackendPool]** を展開し、 **[VM1]** と **[VM2]** の両方が表示されていることを確認します。

### <a name="create-a-health-probe"></a>正常性プローブの作成

ロード バランサーで VM の状態を監視するには、正常性プローブを使用します。 正常性プローブは、ロード バランサーのローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 

**正常性プローブを作成して VM の正常性を監視するには:**

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[MyLoadBalancer]** を選択します。
   
1. **[設定]** で、 **[正常性プローブ]** 、 **[追加]** の順に選択します。
   
1. **[正常性プローブの追加]** ページで、以下の値を入力または選択します。
   
   - **[名前]** :「*MyHealthProbe*」と入力します。
   - **[プロトコル]** : ドロップダウンから **[HTTP]** を選択します。 
   - **[ポート]** : 「*80*」と入力します。 
   - **[パス]** : 既定の URI の */* をそのまま使用します。 この値は任意の別の URI に置き換えることができます。 
   - **[間隔]** : 「*15*」と入力します。 [間隔] は、プローブの試行の間隔を示す秒数です。
   - **[異常のしきい値]** : 「*2*」と入力します。 この値は、プローブの連続する失敗回数です (この回数を超えると、VM は異常と見なされます)。
   
1. **[OK]** を選択します。
   
   ![プローブの追加](./media/tutorial-load-balancer-basic-internal-portal/4-load-balancer-probes.png)

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則の作成

ロード バランサー規則は、VM に対するトラフィックの分散方法を定義します。 この規則で、着信トラフィック用のフロントエンド IP 構成、トラフィックを受信するためのバックエンド IP プール、必要な発信元ポートと宛先ポートを定義します。 

**MyLoadBalancerRule** という名前のロード バランサー規則は、フロントエンド **LoadBalancerFrontEnd** のポート 80 をリッスンしています。 この規則は、ネットワーク トラフィックをバックエンド アドレス プール **MyBackendPool** に、またポート 80 上で送信します。 

**ロード バランサー規則を作成するには:**

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧から **[MyLoadBalancer]** を選択します。
   
1. **[設定]** で、 **[負荷分散規則]** 、 **[追加]** の順に選択します。
   
1. **[負荷分散規則の追加]** ページで、(まだ指定されていない場合は) 次の値を入力または選択します。
   
   - **[名前]** :「*MyLoadBalancerRule*」と入力します。
   - **[フロントエンド IP アドレス]** :指定されていない場合は「*LoadBalancerFrontEnd*」と入力します。
   - **[プロトコル]** : **[TCP]** を選択します。
   - **[ポート]** : 「*80*」と入力します。
   - **[バックエンド ポート]** :「*80*」と入力します。
   - **[バックエンド プール]** : **[MyBackendPool]** を選択します。
   - **[正常性プローブ]** : **[MyHealthProbe]** を選択します。 
   
1. **[OK]** を選択します。
   
   ![ロード バランサー規則の追加](./media/tutorial-load-balancer-basic-internal-portal/5-load-balancing-rules.png)

## <a name="test-the-load-balancer"></a>ロード バランサーをテストする

バックエンド サーバーにインターネット インフォメーション サービス (IIS) をインストールしてから、MyTestVM を使用し、プライベート IP アドレスを使用してロード バランサーをテストします。 各バックエンド VM は、既定の IIS Web ページの異なるバージョンを提供するため、ロード バランサーが 2 つの VM 間で要求を分散していることがわかります。

ポータルでは、**MyLoadBalancer** の **[概要]** ページの **[プライベート IP アドレス]** にその IP アドレスがあります。 アドレスにカーソルを置き、 **[コピー]** アイコンを選択してコピーします。 この例では **10.3.0.7** です。 

### <a name="connect-to-the-vms-with-rdp"></a>RDP を使用して VM に接続する

まず、リモート デスクトップ (RDP) を使用して 3 つの VM すべてに接続します。 

>[!NOTE]
>既定で、VM では、リモート デスクトップ アクセスを許可する **RDP** (リモート デスクトップ) ポートが開かれています。 

**リモート デスクトップ (RDP) から VM に接続するには:**

1. ポータルの左側のメニューで **[すべてのリソース]** を選択します。 リソースの一覧の **MyResourceGroupLB** リソース グループから各 VM を選択します。
   
1. **[概要]** ページで **[接続]** を選択してから **[RDP ファイルのダウンロード]** を選択します。 
   
1. ダウンロードした RDP ファイルを開き、 **[接続]** を選択します。
   
1. [Windows セキュリティ] ウィンドウで、 **[その他]** 、 **[別のアカウントを使用する]** の順に選択します。 
   
   ユーザー名とパスワードを入力し、 **[OK]** を選択します。
   
1. 証明書のプロンプトが表示されたら **[はい]** を選択します。 
   
   VM デスクトップが新しいウィンドウに開きます。 

### <a name="install-iis-and-replace-the-default-iis-page-on-the-back-end-vms"></a>バックエンド VM に IIS をインストールし、既定の IIS ページを置き換えます。

各バックエンド サーバー上で、PowerShell を使用して IIS をインストールし、既定の IIS Web ページをカスタマイズしたページに置き換えます。

>[!NOTE]
>また、 **[サーバー マネージャー]** の **[役割と機能の追加ウィザード]** を使用して IIS をインストールすることもできます。 

**IIS をインストールし、既定の Web ページを PowerShell で更新するには:**

1. MyVM1 と MyVM2 上で、 **[スタート]** メニューから **Windows PowerShell** を起動します。 

2. 次のコマンドを実行して IIS をインストールし、既定の IIS Web ページを置き換えます。
   
   ```powershell-interactive
    # Install IIS
      Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # Remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    #Add custom htm file
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
    ```
1. **[切断]** を選択して、MyVM1 と MyVM2 との RDP 接続を閉じます。 VM をシャット ダウンしないでください。

### <a name="test-the-load-balancer"></a>ロード バランサーをテストする

1. MyTestVM で **Internet Explorer** を開き、構成のプロンプトが表示されたら **[OK]** を選択します。 
   
1. ロード バランサーのプライベート IP アドレス (*10.3.0.7*) をブラウザーのアドレス バーに貼り付けるか入力します。 
   
   カスタマイズされた IIS Web サーバーの既定のページがブラウザーに表示されます。 **Hello World from MyVM1** または **Hello World from MyVM2** というメッセージが表示されます。
   
1. ブラウザーを更新して、ロード バランサーによって VM 間でトラフィックが分散されていることを確認します。 場合によっては、試行の間にブラウザーのキャッシュをクリアする必要があります。

   ロード バランサーによって要求が各バックエンド VM に分散されている場合、**MyVM1** ページが表示されるときと、**MyVM2** ページが表示されるときがあります。 

   ![新しい IIS の既定のページ](./media/tutorial-load-balancer-basic-internal-portal/9-load-balancer-test.png) 
   
## <a name="clean-up-resources"></a>リソースのクリーンアップ

不要になったときにロード バランサーと関連するすべてのリソースを削除するには、**MyResourceGroupLB** リソース グループを開き、 **[リソース グループの削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Basic レベルの内部ロード バランサーを作成しました。 ネットワーク リソース、バックエンド サーバー、正常性プローブ、およびロードバランサーの規則を作成および構成しました。 バックエンド VM に IIS をインストールし、テスト VM を使用してブラウザーでロード バランサーをテストしました。 

次は、可用性ゾーン間で VM の負荷分散を行う方法を学びます。

> [!div class="nextstepaction"]
> [可用性ゾーン間での VM の負荷分散](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
