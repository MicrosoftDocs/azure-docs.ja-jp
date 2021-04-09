---
title: チュートリアル:ポータルを使用して作成する - Web アプリケーション ファイアウォール
description: このチュートリアルでは、Azure portal を使用して Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する方法について説明します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: tutorial
ms.date: 09/16/2020
ms.author: victorh
ms.openlocfilehash: b9733eeb0d9941f6e23dcc9c0fa4dba60f4e4d30
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94561031"
---
# <a name="tutorial-create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>チュートリアル:Azure portal を使用して Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する

このチュートリアルでは、Azure portal を使用して、Web アプリケーション ファイアウォール (WAF) のあるアプリケーション ゲートウェイを作成する方法を示します。 WAF は、[OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) ルールを使用してアプリケーションを保護します。 こうしたルールには、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの攻撃に対する保護が含まれます。 アプリケーション ゲートウェイを作成してから、それをテストして正しく動作していることを確認します。 Azure Application Gateway では、ポートにリスナーを割り当て、ルールを作成し、バックエンド プールにリソースを追加することによって、お客様のアプリケーション Web トラフィックを特定のリソースに転送します。 わかりやすくするために、このチュートリアルでは、パブリック フロントエンド IP、このアプリケーション ゲートウェイで単一サイトをホストするための基本リスナー、バックエンド プールに使用される 2 つの仮想マシン、および基本要求ルーティング規則を使用する簡単な設定を使用します。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * WAF 対応のアプリケーション ゲートウェイの作成
> * バックエンド サーバーとして使用される仮想マシンの作成
> * ストレージ アカウントの作成と診断の構成
> * アプリケーション ゲートウェイのテスト

![Web アプリケーション ファイアウォールの例](../media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

<!---If you prefer, you can complete this tutorial using [Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) or [Azure CLI](tutorial-restrict-web-traffic-cli.md).--->

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

リソースの間で Azure による通信が行われるには、仮想ネットワークが必要です。 新しい仮想ネットワークを作成することも、既存の仮想ネットワークを使用することもできます。 この例では、新しい仮想ネットワークを作成します。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。 Application Gateway インスタンスは、個別のサブネットに作成されます。 この例では 2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。

Azure portal の左側のメニューにある **[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。

**[ネットワーク]** を選択し、**おすすめ** のリストで **[Application Gateway]** を選択します。

### <a name="basics-tab"></a>[基本] タブ

1. **[基本]** タブで、次のアプリケーション ゲートウェイの設定に以下の値を入力します。

   - **[リソース グループ]** :リソース グループには、**myResourceGroupAG** を選択します。 存在しない場合は、 **[新規作成]** を選択して作成します。
   - **[アプリケーション ゲートウェイ名]** :アプリケーション ゲートウェイの名前として「*myAppGateway*」と入力します。
   - **[レベル]** : **[WAF V2]** を選択します。

     ![新しいアプリケーション ゲートウェイの作成:基本](../media/application-gateway-web-application-firewall-portal/application-gateway-create-basics.png)

2.  お客様が作成するリソースの間で Azure による通信が行われるには、仮想ネットワークが必要です。 新しい仮想ネットワークを作成することも、既存の仮想ネットワークを使用することもできます。 この例では、アプリケーション ゲートウェイの作成と同時に新しい仮想ネットワークを作成します。 Application Gateway インスタンスは、個別のサブネットに作成されます。 この例では 2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。

    **[仮想ネットワークの構成]** で、 **[新規作成]** を選択して新しい仮想ネットワークを作成します。 **[仮想ネットワークの作成]** ウィンドウが開いたら、次の値を入力して、仮想ネットワークと 2 つのサブネットを作成します。

    - **Name**:仮想ネットワークの名前として「*myVNet*」と入力します。

    - **[サブネット名]** (Application Gateway サブネット): **[サブネット]** グリッドには、 *[既定]* という名前のサブネットが表示されます。 このサブネットの名前を *myAGSubnet* に変更します。<br>アプリケーション ゲートウェイ サブネットには、アプリケーション ゲートウェイのみを含めることができます。 その他のリソースは許可されません。

    - **[サブネット名]** (バックエンド サーバー サブネット): **[サブネット]** グリッドの 2 行目で、 **[サブネット名]** 列に「*myBackendSubnet*」と入力します。

    - **[アドレス範囲]** (バックエンド サーバー サブネット): **[サブネット]** グリッドの 2 行目に、*myAGSubnet* のアドレス範囲と重複しないアドレス範囲を入力します。 たとえば、*myAGSubnet* のアドレス範囲が 10.0.0.0/24 の場合は、*myBackendSubnet* のアドレス範囲として「*10.0.1.0/24*」と入力します。

    **[OK]** を選択して **[仮想ネットワークの作成]** ウィンドウを閉じ、仮想ネットワークの設定を保存します。

     ![新しいアプリケーションゲートウェイの作成: 仮想ネットワーク](../media/application-gateway-web-application-firewall-portal/application-gateway-create-vnet.png)
    
3. **[基本]** タブで、その他の設定の既定値をそのまま使用し、 **[次へ:フロントエンド]** を選択します。

### <a name="frontends-tab"></a>[フロントエンド] タブ

1. **[フロントエンド]** タブで、 **[フロントエンド IP アドレスの種類]** が **[パブリック]** に設定されていることを確認します。 <br>ユース ケースに従って、フロントエンド IP を [パブリック] または [プライベート] に設定できます。 この例では、パブリック フロントエンド IP を選択します。
   > [!NOTE]
   > Application Gateway v2 SKU では、 **[パブリック]** フロントエンド IP 構成のみを選択できます。 プライベート フロントエンド IP 構成は、この v2 SKU では現在有効になっていません。

2. **[パブリック IP アドレス]** として **[新規作成]** を選択し、パブリック IP アドレス名として「*myAGPublicIPAddress*」と入力し、 **[OK]** を選択します。 

     ![新しいアプリケーション ゲートウェイの作成: フロントエンド](../media/application-gateway-web-application-firewall-portal/application-gateway-create-frontends.png)

3. **バックエンド** を選択します。

### <a name="backends-tab"></a>[バックエンド] タブ

バックエンド プールは、要求を処理するバックエンド サーバーに要求をルーティングするために使用されます。 バックエンド プールは、NIC、仮想マシン スケール セット、パブリック IP、内部 IP、完全修飾ドメイン名 (FQDN)、および Azure App Service などのマルチテナント バックエンドで構成できます。 この例では、アプリケーション ゲートウェイを使用して空のバックエンド プールを作成し、バックエンド ターゲットをバックエンド プールに追加します。

1. **[バックエンド]** タブで、 **[+ バックエンド プールの追加]** を選択します。

2. 開いた **[バックエンド プールの追加]** ウィンドウで、次の値を入力して空のバックエンド プールを作成します。

    - **Name**:バックエンド プールの名前として「*myBackendPool*」と入力します。
    - **[ターゲットを持たないバックエンド プールを追加します]** : **[はい]** を選択して、ターゲットを持たないバックエンド プールを作成します。 アプリケーション ゲートウェイを作成した後で、バックエンド ターゲットを追加します。

3. **[バックエンド プールの追加]** ウィンドウで、 **[追加]** を選択してバックエンド プールの構成を保存し、 **[バックエンド]** タブに戻ります。

     ![新しいアプリケーション ゲートウェイの作成: バックエンド](../media/application-gateway-web-application-firewall-portal/application-gateway-create-backends.png)

4. **[バックエンド]** タブで、 **[次へ:構成]** を選択します。

### <a name="configuration-tab"></a>[構成] タブ

**[構成]** タブで、ルーティング規則を使用して作成したフロントエンドとバックエンド プールを接続します。

1. **[ルーティング規則]** 列で **[規則を追加する]** を選択します。

2. 開いた **[ルーティング規則の追加]** ウィンドウで、 **[規則名]** に「*myRoutingRule*」と入力します。

3. ルーティング規則にはリスナーが必要です。 **[ルーティング規則の追加]** ウィンドウ内の **[リスナー]** タブで、リスナーの次の値を入力します。

    - **[リスナー名]** :リスナーの名前として「*myListener*」と入力します。
    - **[フロントエンド IP]** : **[パブリック]** を選択して、フロントエンド用に作成したパブリック IP を選択します。
  
      **[リスナー]** タブで他の設定の既定値をそのまま使用し、 **[バックエンド ターゲット]** タブを選択して、ルーティング規則の残りの部分を構成します。

   ![新しいアプリケーション ゲートウェイの作成: リスナー](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-listener.png)

4. **[バックエンド ターゲット]** タブで、 **[バックエンド ターゲット]** の **[myBackendPool]** を選択します。

5. **[HTTP 設定]** には **[新規作成]** を選択して新しい HTTP 設定を作成します。 HTTP 設定によって、ルーティング規則の動作が決まります。 開いた **[HTTP 設定の追加]** ウィンドウで、 **[HTTP 設定名]** として「*myHTTPSetting*」と入力します。 **[HTTP 設定の追加]** ウィンドウで他の設定の既定値をそのまま使用し、 **[追加]** を選択して **[ルーティング規則の追加]** ウィンドウに戻ります。 

     ![新しいアプリケーション ゲートウェイの作成:HTTP 設定](../media/application-gateway-web-application-firewall-portal/application-gateway-create-httpsetting.png)

6. **[ルーティング規則の追加]** ウィンドウで **[追加]** を選択してルーティング規則を保存し、 **[構成]** タブに戻ります。

     ![新しいアプリケーションゲートウェイの作成: ルーティング規則](../media/application-gateway-web-application-firewall-portal/application-gateway-create-rule-backends.png)

7. **タグ**、**次へ:確認と作成** をクリックします。

### <a name="review--create-tab"></a>[確認と作成] タブ

**[確認と作成 ]** タブの設定を確認し、 **[作成]** を選択して、仮想ネットワーク、パブリック IP アドレス、およびアプリケーションゲートウェイを作成します。 Azure によるアプリケーション ゲートウェイの作成には数分かかる場合があります。 

次のセクションに進む前に、デプロイが正常に完了するまで待機します。

## <a name="add-backend-targets"></a>バックエンド ターゲットの追加

この例では、ターゲットのバックエンドとして仮想マシンを使用します。 既存の仮想マシンを使用することも、新しい仮想マシンを作成することもできます。 Azure がアプリケーション ゲートウェイのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。

これを行うには、次を実行します。

1. バックエンド サーバーとして使用される 2 つの新しい VM (*myVM* と *myVM2*) を作成します。
2. IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが正常に作成されたことを確認します。
3. バックエンド サーバーをバックエンド プールに追加します。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. Azure portal で、 **[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。
2. **[人気順]** の一覧で **[Windows Server 2016 Datacenter]** を選択します。 **[仮想マシンの作成]** ページが表示されます。<br>Application Gateway は、バックエンド プールで使用されているあらゆる種類の仮想マシンにトラフィックをルートできます。 この例では、Windows Server 2016 Datacenter を使用します。
3. **[基本]** タブで、次の仮想マシンの設定に以下の値を入力します。

    - **[リソース グループ]** :リソース グループ名には、**myResourceGroupAG** を選択します。
    - **[仮想マシン名]** : 仮想マシンの名前として「*myVM*」と入力します。
    - **[ユーザー名]** : 管理者のユーザー名として「*azureuser*」と入力します。
    - **パスワード**:管理者パスワードとして「*Azure123456!* 」と入力します 。
4. 他の既定値をそのまま使用し、 **[Next: ディスク]** を選択します。  
5. **[ディスク]** タブの既定値をそのまま使用し、 **[Next: Networking]\(次へ : ネットワーク\)** を選択します。
6. **[ネットワーク]** タブで、 **[仮想ネットワーク]** に **myVNet** が選択されていること、および **[サブネット]** が **myBackendSubnet** に設定されていることを確認します。 他の既定値をそのまま使用し、 **[Next: 管理]** を選択します。<br>Application Gateway は、それが存在している仮想ネットワークの外部にあるインスタンスと通信できますが、IP 接続があることを確認する必要があります。
7. **[管理]** タブで、 **[ブート診断]** を **[オフ]** に設定します。 他の既定値をそのまま使用し、 **[確認および作成]** を選択します。
8. **[確認および作成]** タブで、設定を確認し、検証エラーを修正してから、 **[作成]** を選択します。
9. 仮想マシンの作成が完了するのを待って先に進みます。

### <a name="install-iis-for-testing"></a>テスト用の IIS のインストール

この例では、Azure がアプリケーション ゲートウェイを正常に作成したことを確認するためにのみ、仮想マシンに IIS をインストールします。

1. [Azure PowerShell](../../cloud-shell/quickstart-powershell.md)を開きます。 そのためには、Azure portal の上部のナビゲーション バーで **[Cloud Shell]** を選択して、ドロップダウン リストで **[PowerShell]** を選択します。 

    ![カスタム拡張機能のインストール](../media/application-gateway-web-application-firewall-portal/application-gateway-extension.png)

2. 次のコマンドを実行して、IIS を仮想マシンにインストールします。 

    ```azurepowershell-interactive
    Set-AzVMExtension `
      -ResourceGroupName myResourceGroupAG `
      -ExtensionName IIS `
      -VMName myVM `
      -Publisher Microsoft.Compute `
      -ExtensionType CustomScriptExtension `
      -TypeHandlerVersion 1.4 `
      -SettingString '{"commandToExecute":"powershell Add-WindowsFeature Web-Server; powershell Add-Content -Path \"C:\\inetpub\\wwwroot\\Default.htm\" -Value $($env:computername)"}' `
      -Location EastUS
    ```

3. 2 番目の仮想マシンを作成し、お客様が先ほど完了した手順を使用して IIS をインストールします。 仮想マシンの名前と、**Set-AzVMExtension** コマンドレットの **VMName** 設定には、*myVM2* を使用します。

### <a name="add-backend-servers-to-backend-pool"></a>バックエンド プールヘのバックエンド サーバーの追加

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

2. 左側のメニューで **[バックエンド プール]** を選択します。

3. **[myBackendPool]** を選択します。

4. **[ターゲット]** の下のドロップダウン リストで **[仮想マシン]** を選択します。

5. **[仮想マシン]** と **[ネットワーク インターフェイス]** で、**myVM** 仮想マシンおよび **myVM2** 仮想マシンとそれらの関連するネットワーク インターフェイスをドロップダウン リストから選択します。

    ![バックエンド サーバーの追加](../media/application-gateway-web-application-firewall-portal/application-gateway-backend.png)

6. **[保存]** を選択します。

7. デプロイが完了するまで待ってから次の手順に進んでください。

## <a name="create-a-storage-account-and-configure-diagnostics"></a>ストレージ アカウントの作成と診断の構成

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

この記事では、アプリケーション ゲートウェイは、検出および防止の目的で、ストレージ アカウントを使用してデータを格納します。 Azure Monitor ログまたはイベント ハブを使用して、データを記録することもできます。

1. Azure portal の左上隅にある **[リソースの作成]** を選択します。
1. **[ストレージ]** を選択し、**[ストレージ アカウント]** を選択します。
1. "*リソース グループ*" には、**myResourceGroupAG** を選択します。
1. ストレージ アカウントの名前として、「*myagstore1*」と入力します。
1. 他の設定は既定値をそのまま使用し、**[確認および作成]** を選択します。
1. 設定を確認し、 **[作成]** を選択します。

### <a name="configure-diagnostics"></a>診断の構成

ApplicationGatewayAccessLog、ApplicationGatewayPerformanceLog、および ApplicationGatewayFirewallLog ログにデータが記録されるように診断を構成します。

1. 左側のメニューで、**[すべてのリソース]** を選択し、*[myAppGateway]* を選択します。
2. [監視] で **[診断設定]** を選択します。
3. **[診断設定の追加]** を選択します。
4. 診断設定の名前として「*myDiagnosticsSettings*」と入力します。
5. **[ストレージ アカウントへのアーカイブ]** を選択し、**[構成]** を選択して、前に作成した *myagstore1* ストレージ アカウントを選択します。その後、**[OK]** を選択します。
6. 収集および保持するためのアプリケーション ゲートウェイ ログを選択します。
7. **[保存]** を選択します。

    ![診断の構成](../media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="create-and-link-a-web-application-firewall-policy"></a>Web アプリケーション ファイアウォールのポリシーを作成してリンクする

WAF のカスタマイズと設定はすべて、WAF ポリシーと呼ばれる別のオブジェクトに含まれています。 このポリシーは Application Gateway に関連付けられている必要があります。 WAF ポリシーを作成するには、[WAF ポリシーの作成](create-waf-policy-ag.md)に関するページを参照してください。 作成されたら、**[関連付けられたアプリケーション ゲートウェイ]** タブで WAF ポリシーから自分の WAF (または個別リスナー) にポリシーを関連付けることができます。 

![関連付けられたアプリケーション ゲートウェイ](../media/application-gateway-web-application-firewall-portal/associated-application-gateways.png)

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

IIS はアプリケーション ゲートウェイを作成するのに必要ではありませんが、Azure によってアプリケーション ゲートウェイが正常に作成されたかどうかを確認するためにインストールしました。 IIS を使用してアプリケーション ゲートウェイをテストします。

1. **[概要]** ページで、アプリケーション ゲートウェイのパブリック IP アドレスを見つけます。![アプリケーション ゲートウェイのパブリック IP アドレスの記録](../media/application-gateway-web-application-firewall-portal/application-gateway-record-ag-address.png) 

   または、**[すべてのリソース]** を選択し、検索ボックスに「*myAGPublicIPAddress*」と入力してから、検索結果でそれを選択できます。 Azure によって、 **[概要]** ページにパブリック IP アドレスが表示されます。
1. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。
1. 応答を確認します。 応答が有効であれば、アプリケーション ゲートウェイが正常に作成され、バックエンドと正常に接続できることが保証されます。

   ![アプリケーション ゲートウェイのテスト](../media/application-gateway-web-application-firewall-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、リソース グループを削除します。 リソース グループを削除することで、アプリケーション ゲートウェイとそのすべての関連リソースも削除します。 

リソース グループを削除するには:

1. Azure portal の左側のメニューにある **[リソース グループ]** を選択します。
2. **[リソース グループ]** ページで、リストの **myResourceGroupAG** を探して選択します。
3. **[リソース グループ] ページ** で、 **[リソース グループの削除]** を選択します。
4. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「*myResourceGroupAG*」と入力し、 **[削除]** を選択します。

## <a name="next-steps"></a>次のステップ

> [!div class="nextstepaction"]
> [Web アプリケーション ファイアウォールの詳細](../overview.md)