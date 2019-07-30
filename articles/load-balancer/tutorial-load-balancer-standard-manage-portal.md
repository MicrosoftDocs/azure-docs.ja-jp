---
title: チュートリアル:VM へのインターネット トラフィックの負荷分散 - Azure portal
titlesuffix: Azure Load Balancer
description: このチュートリアルでは、Azure Portal を使用して Standard Load Balancer を作成して管理する方法について説明します。
services: load-balancer
documentationcenter: na
author: asudbring
manager: twooley
Customer intent: I want to create and Standard Load Balancer so that I can load balance internet traffic to VMs and add and remove VMs from the load-balanced set.
ms.service: load-balancer
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/11/2019
ms.author: allensu
ms.custom: seodec18
ms.openlocfilehash: 22d0e56a77036c551b6006f43997c92fcce07499
ms.sourcegitcommit: 9a699d7408023d3736961745c753ca3cec708f23
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/16/2019
ms.locfileid: "68273349"
---
# <a name="tutorial-load-balance-internet-traffic-to-vms-using-the-azure-portal"></a>チュートリアル:Azure portal を使用してインターネット トラフィックを VM に負荷分散する

負荷分散では、着信要求を複数の仮想マシンに分散させることで、より高いレベルの可用性とスケールを実現します。 このチュートリアルでは、インターネット トラフィックを VM に分散して高可用性を提供する、Azure Standard Load Balancer のさまざまなコンポーネントについて説明します。 学習内容は次のとおりです。


> [!div class="checklist"]
> * Azure Load Balancer を作成する
> * Load Balancer のリソースを作成する
> * 仮想マシンを作成し、IIS サーバーをインストールする
> * 動作中の Load Balancer を表示する
> * Load Balancer に対して VM を追加または削除する

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。 

## <a name="sign-in-to-the-azure-portal"></a>Azure portal にサインインします

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-a-standard-load-balancer"></a>Standard Load Balancer を作成する

このセクションでは、仮想マシンの負荷分散に役立つ Standard Load Balancer を作成します。 Standard Load Balancer では、Standard パブリック IP アドレスだけがサポートされています。 Standard Load Balancer を作成するときに、Standard Load Balancer のフロントエンド (既定では *LoadBalancerFrontend* という名前) として構成される新しい Standard パブリック IP アドレスも作成する必要があります。 

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[ロード バランサー]** の順にクリックします。
2. **[ロード バランサーの作成]** ページの **[基本]** タブで、次の情報を入力するか選択し、それ以外の設定では既定値をそのまま使用して、 **[確認と作成]** を選択します。

    | Setting                 | 値                                              |
    | ---                     | ---                                                |
    | Subscription               | サブスクリプションを選択します。    |    
    | Resource group         | **[新規作成]** を選択して、テキスト ボックスに「*myResourceGroupSLB*」と入力します。|
    | EnableAdfsAuthentication                   | *myLoadBalancer*                                   |
    | リージョン         | **[西ヨーロッパ]** を選択します。                                        |
    | Type          | **[パブリック]** を選択します。                                        |
    | SKU           | **[Standard]** を選択します。                          |
    | パブリック IP アドレス | **[新規作成]** を選択します。 |
    | パブリック IP アドレス名              | テキスト ボックスに「*myPublicIP*」と入力します。   |
    |可用性ゾーン| **[ゾーン冗長]** を選択します。    |

3. **[確認と作成]** タブで、 **[作成]** をクリックします。

   ![Standard Load Balancer を作成する](./media/quickstart-load-balancer-standard-public-portal/create-standard-load-balancer.png)

## <a name="create-load-balancer-resources"></a>Load Balancer のリソースを作成する

このセクションでは、バックエンド アドレス プールの Load Balancer の設定と正常性プローブを構成し、バランサーの規則を指定します。

### <a name="create-a-backend-address-pool"></a>バックエンド アドレス プールの作成

トラフィックを VM に分散するには、バックエンド アドレス プールに、Load Balancer に接続される仮想 NIC の IP アドレスを含めます。 バックエンド アドレス プール *myBackendPool* を作成し、インターネット トラフィックを負荷分散するために仮想マシンを含めます。

1. 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択してから、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[バックエンド プール]** をクリックし、 **[追加]** をクリックします。
3. **[バックエンド プールの追加]** ページ上で、バックエンド プールの名前として「*myBackendPool*」と入力し、 **[追加]** を選択します。

### <a name="create-a-health-probe"></a>正常性プローブの作成

Load Balancer でアプリの状態を監視するには、正常性プローブを使用します。 正常性プローブは、Load Balancer のローテーションに含める VM を、正常性チェックへの応答に基づいて動的に追加したり削除したりする働きをします。 正常性プローブ *myHealthProbe* を作成し、VM の正常性を監視します。

1. 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択してから、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[正常性プローブ]** をクリックし、 **[追加]** をクリックします。
3. 正常性プローブの作成では、以下の値を使用します。
     
    | Setting | 値 |
    | ------- | ----- |
    | EnableAdfsAuthentication | 「*myHealthProbe*」と入力します。 |
    | Protocol | **[HTTP]** を選択します。 |
    | Port | 「*80*」と入力します。|
    | Interval | プローブの試行の**間隔**を示す秒数として、「*15*」を入力します。 |
    | 異常のしきい値 | **異常しきい値**またはプローブの連続する失敗の回数として *[2]* を選択します。この回数を超えると、VM は異常と見なされます。|
    | 正常性プローブ | *[myHealthProbe]* を選択します。 |
    
4. **[OK]** を選択します。

### <a name="create-a-load-balancer-rule"></a>ロード バランサー規則を作成する

ロード バランサー規則の目的は、一連の VM に対するトラフィックの分散方法を定義することです。 着信トラフィック用のフロントエンド IP 構成と、トラフィックを受信するためのバックエンド IP プールを、必要な発信元ポートと宛先ポートと共に定義します。 Load Balancer の規則 *myLoadBalancerRuleWeb* を作成して、フロントエンド *FrontendLoadBalancer* のポート 80 をリッスンし、同じポート 80 を使用して、負荷分散されたネットワーク トラフィックをバックエンド アドレス プール *myBackEndPool* に送信します。

1. 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択してから、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[負荷分散規則]** をクリックし、 **[追加]** をクリックします。
3. 負荷分散規則の構成には、以下の値を使用します。

    | Setting | 値 |
    | ------- | ----- |
    | EnableAdfsAuthentication | 「*myHTTPRule*」と入力します。 |
    | Protocol | **[TCP]** を選択します。 |
    | Port | 「*80*」と入力します。|
    | バックエンド ポート | 「*80*」と入力します。 |
    | バックエンド プール | *[myBackendPool]* を選択します。|
    | 正常性プローブ | *[myHealthProbe]* を選択します。 |
    
4. 残りの部分は既定値のままにし、 **[OK]** を選択します。

## <a name="create-backend-servers"></a>バックエンド サーバーの作成

このセクションでは、仮想ネットワークを作成し、Load Balancer のバックエンド プール用に 3 台の仮想マシンを作成して、Load Balancer をテストするために仮想マシンに IIS をインストールします。

### <a name="create-a-virtual-network"></a>仮想ネットワークの作成

1. 画面の左上で、 **[リソースの作成]**  >  **[ネットワーキング]**  >  **[仮想ネットワーク]** の順に選択します。
2. **[仮想ネットワークの作成]** に次の情報を入力または選択します。

    | Setting | 値 |
    | ------- | ----- |
    | EnableAdfsAuthentication | 「*myVNet*」と入力します。 |
    | アドレス空間 | 「*10.1.0.0/16*」を入力します。 |
    | Subscription | サブスクリプションを選択します。|
    | Resource group | 既存のリソース *[myResourceGroupSLB]* を選択します。 |
    | Location | **[西ヨーロッパ]** を選択します。|
    | サブネット - 名前 | 「*myBackendSubnet*」と入力します。 |
    | サブネット アドレス範囲 | 「*10.1.0.0/24*」と入力します。 |
    
3. 残りの部分は既定値のままにし、 **[作成]** を選択します。

### <a name="create-virtual-machines"></a>仮想マシンを作成する

Standard Load Balancer は、バックエンド プール内の Standard IP アドレスを使用する VM のみをサポートします。 このセクションでは、先ほど作成した Standard Load Balancer のバックエンド プールに追加される 3 つの異なるゾーン (*ゾーン 1*、*ゾーン 2*、および*ゾーン 3*) 内に、Standard パブリック IP アドレスを持つ 3 台の VM (*myVM1*、*myVM2*、および *myVM3*) を作成します。

1. ポータルの左上で、 **[リソースの作成]**  >  **[Compute]**  >  **[Windows Server 2016 Datacenter]** の順に選択します。 
   
1. **[仮想マシンの作成]** の **[Basic]** タブに次の値を入力するか選択します。
   - **[サブスクリプション]**  >  **[リソース グループ]** : **[myResourceGroupSLB]** を選択します。
   - **[インスタンスの詳細]**  >  **[仮想マシン名]** : 「*myVM1*」と入力します。
   - **[インスタンスの詳細]**  >  **[リージョン]** > **[西ヨーロッパ]** を選択します。
   - **[インスタンスの詳細]**  >  **[可用性オプション]** > **[可用性ゾーン]** を選択します。 
   - **[インスタンスの詳細]**  >  **[可用性ゾーン]** > **[1]** を選択します。
  
1. **[ネットワーク]** タブまたは **[次へ: ディスク]** を選択してから **[次へ: ネットワーク]** を選択します。 
   
   - 以下が選択されていることを確認します。
       - **[仮想ネットワーク]** : **myVNet**
       - **[サブネット]** : **myBackendSubnet**
       - **[パブリック IP]** > **[新規作成]** を選択し、 **[パブリック IP アドレスの作成]** ウィンドウで **[SKU]** に **[Standard]** を選択し、 **[可用性ゾーン]** に **[ゾーン冗長]** を選択します。
      
   - 新しいネットワーク セキュリティ グループ (NSG) を作成し、ファイアウォールの種類を選択するには、 **[ネットワーク セキュリティ グループ]** で **[Advanced]\(高度\)** を選択します。 
       1. **ネットワーク セキュリティ グループの構成**フィールドで、 **[新規作成]** を選択します。 
       1. 「*myNetworkSecurityGroup*」と入力し、 **[OK]** を選択します。

   - VM を Load Balancer のバックエンド プールの一部にするには、次の手順を実行します。
        - **[負荷分散]** 内の **[この仮想マシンを既存の負荷分散ソリューションの後ろに配置しますか?]** で **[はい]** を選択します。
        - **[Load balancing settings]\(負荷分散の設定\)** 内の **[負荷分散のオプション]** で **[Azure Load Balancer]** を選択します。
        - **[ロード バランサーを選択します]** で *[myLoadBalancer]* を選択します。 
1. **[管理]** タブまたは **[次へ]**  >  **[管理]** を選択します。 **[監視]** で **[ブート診断]** を **[オフ]** に設定します。 
1. **[Review + create]\(レビュー + 作成\)** を選択します。   
1. 設定を確認し、 **[作成]** を選択します。
1. 手順に従ってさらに 2 台の VM ( *myVM2* および *myVM3*) を作成します。Standard SKU パブリック IP アドレスは、それぞれ **[可用性ゾーン]** の **2** および **3 に設定し、** その他の設定はすべて *myVM1* と同じにします。  

### <a name="create-network-security-group-rule"></a>ネットワーク セキュリティ グループ規則を作成する

このセクションでは、HTTP を使用する受信接続を許可するためのネットワーク セキュリティ グループの規則を作成します。

1. 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択してから、リソースの一覧で **[myResourceGroupSLB]** リソース グループにある **[myNetworkSecurityGroup]** をクリックします。
2. **[設定]** で **[受信セキュリティ規則]** をクリックし、 **[追加]** をクリックします。
3. *myHTTPRule* という名前の受信セキュリティ規則のために以下の値を入力し、ポート 80 を使用する受信 HTTP 接続を許可します。
    - "*サービス タグ*" - **ソース**。
    - "*インターネット*" - **ソース サービス タグ**
    - *80* - **宛先ポート範囲**
    - *TCP* - **プロトコル**
    - "*許可*" - **アクション**
    - *100* - **優先度**
    - *myHTTPRule* - 名前
    - "*HTTP を許可する*" - 説明
4. **[追加]** を選択します。

### <a name="install-iis-on-vms"></a>VM に IIS をインストールする

1. 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択してから、リソースの一覧で *[myResourceGroupSLB]* リソース グループにある **[myVM1]** をクリックします。
2. **[概要]** ページで **[接続]** をクリックして、RDP で VM に接続します。
3. **[Connect to virtual machine]\(仮想マシンへの接続\)** ポップアップ ウィンドウで、 **[RDP ファイルのダウンロード]** を選択して、ダウンロードされた RDP ファイルを開きます。
4. **[リモート デスクトップ接続]** ウィンドウで **[接続]** をクリックします。
5. VM の作成中に指定した資格情報を使用して、この VM にログインします。 これで、仮想マシン *myVM1* とのリモート デスクトップ セッションが起動されます。
6. サーバーのデスクトップで、 **[Windows 管理ツール]** > **[Windows PowerShell]** の順に移動します。
7. PowerShell ウィンドウで、以下のコマンドを実行して IIS サーバーをインストールし、既定の iisstart.htm ファイルを削除して、VM の名前を表示する新しい iisstart.htm ファイルを追加します。

   ```azurepowershell-interactive
    
    # install IIS server role
    Install-WindowsFeature -name Web-Server -IncludeManagementTools
    
    # remove default htm file
     remove-item  C:\inetpub\wwwroot\iisstart.htm
    
    # Add a new htm file that displays server name
     Add-Content -Path "C:\inetpub\wwwroot\iisstart.htm" -Value $("Hello World from " + $env:computername)
   ```
6. *myVM1* で RDP セッションを閉じます。
7. 手順 1. から 6. を繰り返して、IIS と更新済み iisstart.htm ファイルを *myVM2* と *myVM3* にインストールします。

## <a name="test-the-load-balancer"></a>Load Balancer をテストする
1. **[概要]** 画面で、ロード バランサーのパブリック IP アドレスを見つけます。 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択してから、 **[myPublicIP]** をクリックします。

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 IIS Web サーバーの既定のページがブラウザーに表示されます。

      ![IIS Web サーバー](./media/tutorial-load-balancer-standard-zonal-portal/load-balancer-test.png)

アプリを実行している 3 つの VM の間で、Load Balancer がトラフィックを負荷分散していることを確認するには、Web ブラウザーを強制的に最新の情報に更新します。

## <a name="remove-or-add-vms-from-the-backend-pool"></a>バックエンド プールの VM を追加または削除する
アプリを実行している VM には、OS の更新プログラムをインストールするなどメンテナンスが必要になることもあります。 また、アプリのトラフィックが増大すれば、それに対処するために、新たに VM を追加しなければなりません。 このセクションでは、Load Balancer に対して、VM (*myVM1*) を追加したり、削除したりする方法について説明します。

### <a name="remove-vm-from-a-backend-pool"></a>バックエンド プールから VM を削除する
バックエンド プールから *myVM1* を削除するには、次の手順を実行します。

1. 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択してから、リソースの一覧で **[myLoadBalancer]** をクリックします。
2. **[設定]** で **[バックエンド プール]** をクリックし、バックエンド プールの一覧で **myBackendPool** をクリックします。
3. **[myBackendPool]** ページ上で、*VM1* を削除するために、 *[myVM1]* が表示されている行の末尾にある削除アイコンを選択して、 **[保存]** をクリックします。

*myVM1* がバックエンド アドレス プールに存在しなくなると、ソフトウェア更新プログラムのインストールなど、*myVM1* に対するメンテナンス タスクを実行できます。 *VM1* がなくなると、負荷は *myVM2* と *myVM3* の間で分散されるようになります。 

### <a name="add-vm-to-a-backend-pool"></a>バックエンド プールに VM を追加する
バックエンド プールに *myVM1* を再び追加するには、次の手順を実行します。

1. 左側のメニューで **[すべてのサービス]** を選択し、 **[すべてのリソース]** を選択してから、リソースの一覧で **[myVM1]** を選択します。
2. **[VM1]** ページの **[設定]** の下で **[ネットワーク]** を選択します。
3. **[ネットワーク]** ページで **[負荷分散]** タブを選択し、 **[負荷分散の追加]** を選択します。
4. **[負荷分散の追加]** ページで、次の操作を実行します。
   1. **[負荷分散のオプション ]** で、 **[Azure Load Balancer]** を選択します。
   2. **[ロード バランサーを選択します]** で、 *[myLoadBalancer]* を選択します。
   3. **[バックエンド プールの選択]** で、 *[myBackendPool]* を選択します。 

## <a name="clean-up-resources"></a>リソースのクリーンアップ

リソース グループ、Load Balancer、および関連するすべてのリソースは、不要になったら削除します。 これを行うには、Load Balancer を含む *[myResouceGroupSLB]* リソース グループを選択して、 **[削除]** を選択します。

## <a name="next-steps"></a>次の手順

このチュートリアルでは、Standard Load Balancer の作成、VM のアタッチ、Load Balancer のトラフィック規則と正常性プローブの構成を行ってから、Load Balancer をテストしました。 また、負荷分散セットから VM を削除し、バックエンド アドレス プールに VM を追加して戻しました。 Azure Load Balancer についてさらに学習するには、Azure Load Balancer のチュートリアルに進みます。

> [!div class="nextstepaction"]
> [Azure Load Balancer のチュートリアル](tutorial-load-balancer-standard-public-zone-redundant-portal.md)
