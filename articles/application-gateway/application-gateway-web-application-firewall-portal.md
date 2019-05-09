---
title: チュートリアル - Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する - Azure Portal | Microsoft Docs
description: Azure Portal を使用して Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する方法について説明します。
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/25/2019
ms.author: victorh
ms.openlocfilehash: 1284ddec4cd9cea3ea53c20d437550405dd614d9
ms.sourcegitcommit: 9f4eb5a3758f8a1a6a58c33c2806fa2986f702cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58905870"
---
# <a name="create-an-application-gateway-with-a-web-application-firewall-using-the-azure-portal"></a>Azure Portal を使用して Web アプリケーション ファイアウォールのあるアプリケーション ゲートウェイを作成する

> [!div class="op_single_selector"]
>
> - [Azure ポータル](application-gateway-web-application-firewall-portal.md)
> - [PowerShell](tutorial-restrict-web-traffic-powershell.md)
> - [Azure CLI](tutorial-restrict-web-traffic-cli.md)
>
> 

このチュートリアルでは、Azure portal を使用して、[Web アプリケーション ファイアウォール](application-gateway-web-application-firewall-overview.md) (WAF) のある[アプリケーション ゲートウェイ](application-gateway-introduction.md)を作成する方法を示します。 WAF は、[OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) ルールを使用してアプリケーションを保護します。 こうしたルールには、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの攻撃に対する保護が含まれます。 アプリケーション ゲートウェイを作成してから、それをテストして正しく動作していることを確認します。 Azure Application Gateway では、ポートにリスナーを割り当て、ルールを作成し、バックエンド プールにリソースを追加することによって、お客様のアプリケーション Web トラフィックを特定のリソースに転送します。 わかりやすくするために、この記事では、パブリック フロントエンド IP、このアプリケーション ゲートウェイで単一サイトをホストするための基本リスナー、バックエンド プールに使用される 2 つの仮想マシン、および基本要求ルーティング規則を使用する簡単な設定を使用します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * WAF 対応のアプリケーション ゲートウェイの作成
> * バックエンド サーバーとして使用される仮想マシンの作成
> * ストレージ アカウントの作成と診断の構成

![Web アプリケーション ファイアウォールの例](./media/application-gateway-web-application-firewall-portal/scenario-waf.png)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Azure へのサインイン

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にサインインします。

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

お客様が作成するリソースの間で Azure による通信が行われるには、仮想ネットワークが必要です。 新しい仮想ネットワークを作成することも、既存の仮想ネットワークを使用することもできます。 この例では、新しい仮想ネットワークを作成します。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。 Application Gateway インスタンスは、個別のサブネットに作成されます。 この例では 2 つのサブネットを作成します。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。

Azure portal の左側のメニューにある **[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。

**[ネットワーク]** を選択し、**おすすめ**のリストで **[Application Gateway]** を選択します。

### <a name="basics-page"></a>基本ページ

1. **[基本]** ページで、次のアプリケーション ゲートウェイの設定に以下の値を入力します。
   - **[名前]**:アプリケーション ゲートウェイの名前として「*myAppGateway*」と入力します。
   - **[リソース グループ]**:リソース グループには、**myResourceGroupAG** を選択します。 存在しない場合は、**[新規作成]** を選択して作成します。
   - アプリケーション ゲートウェイのレベルとして *[WAF]* を選択します。![新しいアプリケーション ゲートウェイの作成](./media/application-gateway-web-application-firewall-portal/application-gateway-create.png)

他の設定は既定値をそのまま使用し、**[OK]** をクリックします。

### <a name="settings-page"></a>[設定] ページ

1. **[設定]** ページの **[サブネット構成]** で、**[仮想ネットワークの選択]** を選択します。 <br>
2. **[仮想ネットワークの選択]** ページで、**[新規作成]** を選択してから、次の仮想ネットワークの設定に値を入力します。
   - **[名前]**:仮想ネットワークの名前として「*myVNet*」と入力します。
   - **[アドレス空間]**: 仮想ネットワークのアドレス空間として「*10.0.0.0/16*」と入力します。
   - **[サブネット名]**: サブネットの名前として「*myAGSubnet*」と入力します。<br>アプリケーション ゲートウェイ サブネットには、アプリケーション ゲートウェイのみを含めることができます。 その他のリソースは許可されません。
   - **[サブネットのアドレス範囲]**: サブネットのアドレス範囲として「*10.0.0.0/24*」と入力します。![仮想ネットワークの作成](./media/application-gateway-web-application-firewall-portal/application-gateway-vnet.png)
3. **[OK]** をクリックして、仮想ネットワークとサブネットを作成します。
4. **[フロントエンド IP 構成の削除]** を選択します。 **[フロントエンド IP 構成]** で、**[IP アドレスの種類]** が **[パブリック]** に設定されていることを確認します。 **[パブリック IP アドレス]** で、**[新規作成]** が選択されていることを確認します｡ <br>ユース ケースに従って、フロントエンド IP を [パブリック] または [プライベート] に設定できます。 この例では、パブリック フロントエンド IP を選択します。 
5. パブリック IP アドレス名として「*myAGPublicIPAddress*」と入力します。 
6. 他の設定は既定値をそのまま使用し、**[OK]** を選択します。<br>この記事ではわかりやすくするために既定値を選択しますが、ユース ケースに応じて他の設定でカスタム値を構成することができます 

### <a name="summary-page"></a>概要ページ

**[概要]** ページで設定を確認し、**[OK]** を選択して、仮想ネットワーク、パブリック IP アドレス、アプリケーション ゲートウェイを作成します。 Azure によるアプリケーション ゲートウェイの作成には数分かかる場合があります。 次のセクションに進む前に、デプロイが正常に完了するまで待機します。

## <a name="add-backend-pool"></a>バックエンド プールを追加する

バックエンドプールは、要求を処理するバックエンド サーバーに要求をルーティングするために使用されます。 バックエンド プールは、NIC、仮想マシン スケール セット、パブリック IP、内部 IP、完全修飾ドメイン名 (FQDN)、および Azure App Service などのマルチテナント バックエンドで構成できます。 バックエンド プールにバックエンド ターゲットを追加する必要があります。

この例では、ターゲットのバックエンドとして仮想マシンを使用します。 既存の仮想マシンを使用することも、新しい仮想マシンを作成することもできます。 この例では、Azure によってアプリケーション ゲートウェイのバックエンド サーバーとして使用される 2 つの仮想マシンを作成します。 そのためには、以下の手順を実行します。

1. 新しいサブネット *myBackendSubnet* を作成します。ここに、新しい VM が作成されます。 
2. 2 つの新しい VM、*myVM* と *myVM2* を作成し、バックエンド サーバーとして使用します。
3. IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが正常に作成されたことを確認します。
4. バックエンド サーバーをバックエンド プールに追加します。

### <a name="add-a-subnet"></a>サブネットの追加

以下の作業に従って、お客様が作成した仮想ネットワークにサブネットを追加します。

1. Azure portal の左側のメニューにある **[すべてのリソース]** を選択し、検索ボックスに「*myVNet*」と入力して、検索結果から **[myVNet]** を選択します。

2. 左側のメニューから **[サブネット]** を選択し、**+ [サブネット]** を選択します。 

   ![サブネットの作成](./media/application-gateway-create-gateway-portal/application-gateway-subnet.png)

3. **[サブネットの追加]** ページで、サブネットの**名前**として「*myBackendSubnet*」と入力し、**[OK]** を選択します。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. Azure portal で、**[リソースの作成]** を選択します。 **[新規作成]** ウィンドウが表示されます。
2. **[コンピューティング]** を選択し、**おすすめ**のリストで **[Windows Server 2016 Datacenter]** を選択します。 **[仮想マシンの作成]** ページが表示されます。<br>Application Gateway は、バックエンド プールで使用されているあらゆる種類の仮想マシンにトラフィックをルートできます。 この例では、Windows Server 2016 Datacenter を使用します。
3. **[基本]** タブで、次の仮想マシンの設定に以下の値を入力します。
   - **[リソース グループ]**:リソース グループ名には、**myResourceGroupAG** を選択します。
   - **[仮想マシン名]**: 仮想マシンの名前として「*myVM*」と入力します。
   - **[ユーザー名]**: 管理者のユーザー名として「*azureuser*」と入力します。
   - **Password**:管理者パスワードとして「*Azure123456!*」と入力します 。
4. 他の既定値をそのまま使用し、**[Next: ディスク]** を選択します。  
5. **[ディスク]** タブの既定値をそのまま使用し、**[Next: Networking]\(次へ : ネットワーク\)** を選択します。
6. **[ネットワーク]** タブで、**[仮想ネットワーク]** に **myVNet** が選択されていること、および **[サブネット]** が **myBackendSubnet** に設定されていることを確認します。 他の既定値をそのまま使用し、**[Next: 管理]** を選択します。<br>Application Gateway は仮想ネットワークの外部にあるインスタンスと通信できますが、IP 接続があることを確認する必要があります。 
7. **[管理]** タブで、**[ブート診断]** を **[オフ]** に設定します。 他の既定値をそのまま使用し、**[確認および作成]** を選択します。
8. **[確認および作成]** タブで、設定を確認し、検証エラーを修正してから、**[作成]** を選択します。
9. 仮想マシンの作成が完了するのを待って先に進みます。

### <a name="install-iis-for-testing"></a>テスト用の IIS のインストール

この例では、Azure がアプリケーションゲートウェイを正常に作成したことを確認する目的でのみ、仮想マシンに IIS をインストールします。 

1. [Azure PowerShell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell)を開きます。 そのためには、Azure portal の上部のナビゲーション バーで **[Cloud Shell]** を選択して、ドロップダウン リストで **[PowerShell]** を選択します。 

   ![カスタム拡張機能のインストール](./media/application-gateway-create-gateway-portal/application-gateway-extension.png)

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

2. 左側のメニューで **[バックエンド プール]** を選択します。 アプリケーション ゲートウェイを作成したときに、既定のプール **appGatewayBackendPool** が Azure によって自動的に作成されました。 

3. **[appGatewayBackendPool]** を選択します。

4. **[ターゲット]** の下のドロップダウン リストで **[仮想マシン]** を選択します。

5. **[仮想マシン]** と **[ネットワーク インターフェイス]** で、**myVM** 仮想マシンおよび **myVM2** 仮想マシンとそれらの関連するネットワーク インターフェイスをドロップダウン リストから選択します。

   ![バックエンド サーバーの追加](./media/application-gateway-create-gateway-portal/application-gateway-backend.png)

6. **[保存]** を選択します。

## <a name="create-a-storage-account-and-configure-diagnostics"></a>ストレージ アカウントの作成と診断の構成

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

このチュートリアルでは、アプリケーション ゲートウェイは、検出および防止の目的で、ストレージ アカウントを使用してデータを格納します。 Azure Monitor ログまたはイベント ハブを使用して、データを記録することもできます。

1. Azure Portal の左上にある **[新規]** をクリックします。
2. **[ストレージ]**、**[ストレージ アカウント - Blob、File、Table、Queue]** の順に選択します。
3. ストレージ アカウントの名前を入力し、リソース グループでは **[既存のものを使用]** を選択して、**[myResourceGroupAG]** を選択します。 この例では、ストレージ アカウント名は *myagstore1* です。 他の設定は既定値をそのまま使用し、**[作成]** をクリックします。

### <a name="configure-diagnostics"></a>診断の構成

ApplicationGatewayAccessLog、ApplicationGatewayPerformanceLog、および ApplicationGatewayFirewallLog ログにデータが記録されるように診断を構成します。

1. 左側のメニューで、**[すべてのリソース]** をクリックし、*[myAppGateway]* を選択します。
2. [監視] の **[診断ログ]** をクリックします。
3. **[診断設定の追加]** をクリックします。
4. 診断設定の名前として「*myDiagnosticsSettings*」と入力します。
5. **[ストレージ アカウントへのアーカイブ]** を選択し、**[構成]** をクリックして、前に作成した *myagstore1* ストレージ アカウントを選択します。
6. 収集および保持するためのアプリケーション ゲートウェイ ログを選択します。
7. **[Save]** をクリックします。

    ![診断の構成](./media/application-gateway-web-application-firewall-portal/application-gateway-diagnostics.png)

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

IIS はアプリケーション ゲートウェイを作成するのに必要ではありませんが、このクイック スタートでは、Azure によってアプリケーション ゲートウェイが正常に作成されたかどうかを確認するためにインストールしました。 IIS を使用してアプリケーション ゲートウェイをテストします。

1. **[概要]** ページでアプリケーション ゲートウェイのパブリック IP アドレスを見つけます。![アプリケーション ゲートウェイのパブリック IP アドレスの記録](./media/application-gateway-create-gateway-portal/application-gateway-record-ag-address.png)または、**[すべてのリソース]** を選択して、検索ボックスに *myAGPublicIPAddress* と入力し、検索結果でそれを選択することができます。 Azure によって、**[概要]** ページにパブリック IP アドレスが表示されます。
2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。
3. 応答を確認します。 応答が有効であれば、アプリケーション ゲートウェイが正常に作成され、バックエンドと正常に接続できることが保証されます。![アプリケーション ゲートウェイのテスト](./media/application-gateway-create-gateway-portal/application-gateway-iistest.png)

## <a name="clean-up-resources"></a>リソースのクリーンアップ

アプリケーション ゲートウェイと共に作成したリソースが不要になったら、リソース グループを削除します。 リソース グループを削除することで、アプリケーション ゲートウェイとそのすべての関連リソースも削除します。 

リソース グループを削除するには:

1. Azure portal の左側のメニューにある **[リソース グループ]** を選択します。
2. **[リソース グループ]** ページで、リストの **myResourceGroupAG** を探して選択します。
3. **[リソース グループ] ページ**で、**[リソース グループの削除]** を選択します。
4. **[TYPE THE RESOURCE GROUP NAME]\(リソース グループ名を入力してください\)** に「*myResourceGroupAG*」と入力し、**[削除]** を選択します。

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * WAF 対応のアプリケーション ゲートウェイの作成
> * バックエンド サーバーとして使用される仮想マシンの作成
> * ストレージ アカウントの作成と診断の構成

アプリケーション ゲートウェイとその関連リソースの詳細を確認するには、ハウツー記事に進みます。
