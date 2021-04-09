---
title: チュートリアル:ポータルを使用した URL パスベースのルーティング規則 - Azure Application Gateway
description: このチュートリアルでは、Azure portal を使用して、アプリケーション ゲートウェイと仮想マシン スケール セットの URL パスベースのルーティング規則を作成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: tutorial
ms.date: 02/23/2021
ms.author: victorh
ms.openlocfilehash: b0ab3cbd2891ef1677c0d4ba7a00821d67714b6d
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/03/2021
ms.locfileid: "101708953"
---
# <a name="tutorial-create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>チュートリアル:Azure Portal を使用してパス ベースのルーティング規則のあるアプリケーション ゲートウェイを作成する

[アプリケーション ゲートウェイ](./overview.md)を作成するときに、Azure Portal を使用して [URL パス ベースのルーティング規則](./url-route-overview.md)を構成できます。 このチュートリアルでは、仮想マシンを使用してバックエンド プールを作成します。 その後、Web トラフィックがプール内の適切なサーバーに確実に到着するようにルーティング規則を作成します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * アプリケーション ゲートウェイの作成
> * バックエンド サーバー用の仮想マシンの作成
> * バックエンド サーバーでのバックエンド プールの作成
> * バックエンド リスナーの作成
> * パス ベースのルーティング規則の作成

![URL ルーティングの例](./media/application-gateway-create-url-route-portal/scenario.png)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="create-virtual-machines"></a>仮想マシンを作成する

この例では、アプリケーション ゲートウェイのバックエンド サーバーとして使用する 3 つの仮想マシンを作成します。 また、IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが想定どおりに動作することを確認します。

1. Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。
1. Azure portal で、 **[リソースの作成]** を選択します。
2. [人気順] の一覧で **[Windows Server 2016 Datacenter]** を選択します。
3. 次の仮想マシンの値を入力します。

    - **[サブスクリプション]** - ご使用のサブスクリプションを選択します。
    - **[リソース グループ]** : **[新規作成]** を選択し、「*myResourceGroupAG*」と入力します。
    - **[仮想マシン名]** : *myVM1*
    - **[リージョン]** : *(米国) 米国東部*
    - **[ユーザー名]** : ユーザー名を入力します
    - **[パスワード]** : パスワードを入力します


4. **[Next:Disks]\(次へ: ディスク\)** を選択します。
5. **[次へ:ネットワーク]** を選択します。
6. **[仮想ネットワーク]** で、 **[新規作成]** を選択し、次の仮想ネットワークの値を入力します。

   - *myVNet* - 仮想ネットワークの名前です。
   - *10.0.0.0/16* - 仮想ネットワークのアドレス空間です。
   - *myBackendSubnet* - 最初のサブネットの名前です。
   - *10.0.1.0/24* - サブネットのアドレス空間です。
   - *myAGSubnet* - 2 つ目のサブネットの名前です。
   - *10.0.0.0/24* - サブネットのアドレス空間です。
7. **[OK]** を選択します。

8. **[サブネット]** で、そのサブネットに対して **myBackendSubnet** が選択されていることを確認してから、 **[次へ: 管理]** を選択します。
9. **[無効]** を選択して、ブート診断を無効にします。
10. **[Review + Create]\(確認と作成\)** を選択し、概要ページの設定を確認して、 **[作成]** を選択します。
11. さらに 2 つの仮想マシン *myVM2* と *myVM3* を作成し、それらを *MyVNnet* 仮想ネットワークと *myBackendSubnet* サブネットに配置します。

### <a name="install-iis"></a>IIS のインストール

1. 対話型シェルを開いて、**PowerShell** に設定されていることを確認します。

    ![カスタム拡張機能のインストール](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. 次のコマンドを実行して、IIS を仮想マシンにインストールします。 

    ```azurepowershell
         $publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1");  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }

        Set-AzVMExtension `
         -ResourceGroupName myResourceGroupAG `
         -Location eastus `
         -ExtensionName IIS `
         -VMName myVM1 `
         -Publisher Microsoft.Compute `
         -ExtensionType CustomScriptExtension `
         -TypeHandlerVersion 1.4 `
         -Settings $publicSettings
    ```

3. 終了したばかりの手順を使用して、他の仮想マシンに IIS をインストールします。 Set-AzVMExtension の VMName 値には、*myVM2* と *myVM3* を使用します。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

1. Azure portal の左側のメニューにある **[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。

2. **[ネットワーク]** を選択し、**おすすめ** のリストで **[Application Gateway]** を選択します。

### <a name="basics-tab"></a>[基本] タブ

1. **[基本]** タブで、次のアプリケーション ゲートウェイの設定に以下の値を入力します。

   - **[サブスクリプション]**:サブスクリプションを選択します。
   - **[リソース グループ]** :リソース グループには、**myResourceGroupAG** を選択します。
   - **[アプリケーション ゲートウェイ名]** : アプリケーション ゲートウェイの名前として「*myAppGateway*」と入力します。
   - **[リージョン]** : **[(米国) 米国東部]** を選択します。

        ![新しいアプリケーション ゲートウェイの作成:基本](./media/application-gateway-create-gateway-portal/application-gateway-create-basics.png)

2.  **[仮想ネットワークの構成]** で、仮想ネットワークの名前として **myVNet** を選択します。
3. サブネットには、**myAGSubnet** を選択します。
3. 他の設定は既定値をそのまま使用し、 **[次へ:フロントエンド]** を選択します。

### <a name="frontends-tab"></a>[フロントエンド] タブ

1. **[フロントエンド]** タブで、 **[フロントエンド IP アドレスの種類]** が **[パブリック]** に設定されていることを確認します。

   > [!NOTE]
   > Application Gateway v2 SKU では、 **[パブリック]** フロントエンド IP 構成のみを選択できます。 プライベート フロントエンド IP 構成は、この v2 SKU では現在有効になっていません。

2. **[パブリック IP アドレス]** として **[新規追加]** を選択し、パブリック IP アドレス名として「*myAGPublicIPAddress*」と入力し、 **[OK]** を選択します。 
3. **バックエンド** を選択します。

### <a name="backends-tab"></a>[バックエンド] タブ

バックエンド プールは、要求を処理するバックエンド サーバーに要求をルーティングするために使用されます。 バックエンド プールは、NIC、仮想マシン スケール セット、パブリック IP、内部 IP、完全修飾ドメイン名 (FQDN)、および Azure App Service などのマルチテナント バックエンドで構成できます。

1. **[バックエンド]** タブで、 **[バックエンド プールの追加]** を選択します。

2. 開いた **[バックエンド プールの追加]** ウィンドウで、次の値を入力して空のバックエンド プールを作成します。

    - **Name**:バックエンド プールの名前として「*myBackendPool*」と入力します。
3. **[ターゲットの種類]** の下のドロップダウン リストで **[仮想マシン]** を選択します。

5. **[ターゲット]** で、 **myVM1** のネットワーク インターフェイスを選択します。
6. **[追加]** を選択します。
7. 手順を繰り返し、*myVM2* をターゲットとして指定した *Images* バックエンド プールと、*myVM3* をターゲットとして指定した *Video* バックエンド プールを追加します。
8. **[追加]** を選択してバックエンド プールの構成を保存し、 **[バックエンド]** タブに戻ります。

4. **[バックエンド]** タブで、 **[次へ:構成]** を選択します。

### <a name="configuration-tab"></a>[構成] タブ

**[構成]** タブで、ルーティング規則を使用して作成したフロントエンドとバックエンド プールを接続します。

1. **[ルーティング規則]** 列で **[ルーティング規則の追加]** を選択します。

2. 開いた **[ルーティング規則の追加]** ウィンドウで、 **[規則名]** に「*myRoutingRule*」と入力します。

3. ルーティング規則にはリスナーが必要です。 **[ルーティング規則の追加]** ウィンドウ内の **[リスナー]** タブで、リスナーの次の値を入力します。

    - **[リスナー名]** :リスナーの名前として「*myListener*」と入力します。
    - **[フロントエンド IP]** : **[パブリック]** を選択して、フロントエンド用に作成したパブリック IP を選択します。
    - **ポート**:「*8080*」と入力します。
  
        **[リスナー]** タブで他の設定の既定値をそのまま使用し、 **[バックエンド ターゲット]** タブを選択して、ルーティング規則の残りの部分を構成します。

4. **[バックエンド ターゲット]** タブで、 **[バックエンド ターゲット]** の **[myBackendPool]** を選択します。

5. **[HTTP 設定]** には **[新規追加]** を選択して、新しい HTTP 設定を作成します。 HTTP 設定によって、ルーティング規則の動作が決まります。 

6. 開いた **[HTTP 設定の追加]** ウィンドウで、 **[HTTP 設定名]** として「*myHTTPSetting*」と入力します。 **[HTTP 設定の追加]** ウィンドウで他の設定の既定値をそのまま使用し、 **[追加]** を選択して **[ルーティング規則の追加]** ウィンドウに戻ります。
7. **[パスベースのルーティング]** で、 **[パスベースの規則を作成する複数のターゲットを追加します]** を選択します。
8. **[パス]** に、「 */images/* \*」と入力します。
9. **[ターゲット名]** に、「*Images*」と入力します。
10. **[HTTP 設定]** で、 **[myHTTPSetting]** を選択します。
11. **[バックエンド ターゲット]** で、 **[Images]** を選択します。
12. **[追加]** を選択して、パスの規則を保存し、 **[ルーティング規則の追加]** タブに戻ります。
13. 手順を繰り返し、Video 用の別の規則を追加します。
14. **[追加]** を選択して、ルーティング規則を追加し、 **[構成]** タブに戻ります。
15. **タグ**、**次へ:確認と作成** をクリックします。

> [!NOTE]
> 既定のケースを処理するために、カスタム */* * パス規則を追加する必要はありません。 これは、既定のバックエンド プールによって自動的に処理されます。

### <a name="review--create-tab"></a>[確認と作成] タブ

**[確認と作成 ]** タブの設定を確認し、 **[作成]** を選択して、仮想ネットワーク、パブリック IP アドレス、およびアプリケーションゲートウェイを作成します。 Azure によるアプリケーション ゲートウェイの作成には数分かかる場合があります。 次のセクションに進む前に、デプロイが正常に完了するまで待機します。


## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. **[すべてのリソース]** を選択し、**myAppGateway** を選択します。

    ![アプリケーション ゲートウェイのパブリック IP アドレスの記録](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 http:\///52.188.72.175:8080 などです。

    ![アプリケーション ゲートウェイでのベース URL のテスト](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

   ポート 8080 のリスナーは、この要求を既定のバックエンド プールにルーティングします。

3. URL を *http://&lt;ip-address&gt;:8080/images/test.htm* に変更します。&lt;ip-address&gt; は、使用している IP アドレスに置き換えてください。次の例のようになります。

    ![アプリケーション ゲートウェイでのイメージ URL のテスト](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

   ポート 8080 のリスナーは、この要求を *Images* バックエンド プールにルーティングします。

4. URL を *http://&lt;ip-address&gt;:8080/video/test.htm* に変更します。&lt;ip-address&gt; は、使用している IP アドレスに置き換えてください。次の例のようになります。

    ![アプリケーション ゲートウェイでのビデオ URL のテスト](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

   ポート 8080 のリスナーは、この要求を *Video* バックエンド プールにルーティングします。

## <a name="clean-up-resources"></a>リソースをクリーンアップする

リソース グループおよび関連するすべてのリソースは、不要になったら削除します。 リソース グループを選択し、 **[リソース グループの削除]** を選択してください。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [Azure Application Gateway でエンド ツー エンド TLS を有効にする](./ssl-overview.md)