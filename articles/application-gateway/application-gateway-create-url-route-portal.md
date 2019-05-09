---
title: URL パス ベースのルーティング規則のあるアプリケーション ゲートウェイを作成する - Azure Portal
description: Azure Portal を使用して、アプリケーション ゲートウェイと仮想マシン スケール セットの URL パス ベースのルーティング規則を作成する方法について説明します。
services: application-gateway
author: vhorne
manager: jpconnock
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 3/26/2018
ms.author: victorh
ms.openlocfilehash: 10bc4e4c440e5495afd820f588270b7990108b68
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2019
ms.locfileid: "58259263"
---
# <a name="create-an-application-gateway-with-path-based-routing-rules-using-the-azure-portal"></a>Azure Portal を使用してパス ベースのルーティング規則のあるアプリケーション ゲートウェイを作成する

[アプリケーション ゲートウェイ](application-gateway-introduction.md)を作成するときに、Azure Portal を使用して [URL パス ベースのルーティング規則](application-gateway-url-route-overview.md)を構成できます。 このチュートリアルでは、仮想マシンを使用してバックエンド プールを作成します。 その後、Web トラフィックがプール内の適切なサーバーに確実に到着するようにルーティング規則を作成します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * アプリケーション ゲートウェイの作成
> * バックエンド サーバー用の仮想マシンの作成
> * バックエンド サーバーでのバックエンド プールの作成
> * バックエンド リスナーの作成
> * パス ベースのルーティング規則の作成

![URL ルーティングの例](./media/application-gateway-create-url-route-portal/scenario.png)

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインする

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

作成したリソース間の通信には仮想ネットワークが必要です。 この例では 2 つのサブネットが作成されます。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。

1. Azure Portal の左上にある **[新規]** をクリックします。
2. **[ネットワーク]** を選択し、注目のリストで **[Application Gateway]** を選択します。
3. 次のアプリケーション ゲートウェイの値を入力します。

   - *myAppGateway* - アプリケーション ゲートウェイの名前です。
   - *myResourceGroupAG* - 新しいリソース グループの名前です。

     ![新しいアプリケーション ゲートウェイの作成](./media/application-gateway-create-url-route-portal/application-gateway-create.png)

4. 他の設定は既定値をそのまま使用し、**[OK]** をクリックします。
5. **[仮想ネットワークの選択]**、**[新規作成]** の順にクリックし、次の仮想ネットワークの値を入力します。

   - *myVNet* - 仮想ネットワークの名前です。
   - *10.0.0.0/16* - 仮想ネットワークのアドレス空間です。
   - *myAGSubnet* - サブネットの名前です。
   - *10.0.0.0/24* - サブネットのアドレス空間です。

     ![Create virtual network](./media/application-gateway-create-url-route-portal/application-gateway-vnet.png)

6. **[OK]** をクリックして、仮想ネットワークとサブネットを作成します。
7. **[パブリック IP アドレスの選択]**、**[新規作成]** の順にクリックし、パブリック IP アドレスの名前を入力します。 この例では、パブリック IP アドレスの名前は *myAGPublicIPAddress* にします。 他の設定は既定値をそのまま使用し、**[OK]** をクリックします。
8. リスナーの構成は既定値をそのまま使用し、Web アプリケーション ファイアウォールは無効のままにして、**[OK]** をクリックします。
9. 概要ページで設定を確認し、**[OK]** をクリックして、ネットワーク リソースとアプリケーション ゲートウェイを作成します。 アプリケーション ゲートウェイの作成には数分かかる場合があります。デプロイが正常に終了するのを待ち、その後で次のセクションに進みます。

### <a name="add-a-subnet"></a>サブネットの追加

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myVNet]** をクリックします。
2. **[サブネット]**、**[サブネット]** の順にクリックします。

    ![サブネットの作成](./media/application-gateway-create-url-route-portal/application-gateway-subnet.png)

3. サブネットの名前として「*myBackendSubnet*」を入力し、**[OK]** をクリックします。

## <a name="create-virtual-machines"></a>仮想マシンを作成する

この例では、アプリケーション ゲートウェイのバックエンド サーバーとして使用する 3 つの仮想マシンを作成します。 また、IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが正常に作成されたことを確認します。

1. **[新規]** をクリックします。
2. **[コンピューティング]** をクリックし、注目のリストで **[Windows Server 2016 Datacenter]** を選択します。
3. 次の仮想マシンの値を入力します。

    - *myVM1* - 仮想マシンの名前です。
    - *azureuser* - 管理者のユーザー名です。
    - *Azure123456!* パスワードです。
    - **[既存のものを使用]**、*[myResourceGroupAG]* の順に選択します。

4. Click **OK**.
5. 仮想マシンのサイズとして **[DS1_V2]** を選択し、**[選択]** をクリックします。
6. 仮想ネットワークに対して **[myVNet]** が選択されていること、およびサブネットが **myBackendSubnet** であることを確認します。 
7. **[無効]** をクリックして、ブート診断を無効にします。
8. **[OK]** をクリックし、概要ページの設定を確認して、**[作成]** をクリックします。

### <a name="install-iis"></a>IIS のインストール

1. 対話型シェルを開いて、**PowerShell** に設定されていることを確認します。

    ![カスタム拡張機能のインストール](./media/application-gateway-create-url-route-portal/application-gateway-extension.png)

2. 次のコマンドを実行して、IIS を仮想マシンにインストールします。 

    ```azurepowershell-interactive
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

3. さらに 2 つの仮想マシンを作成し、終了したばかりの手順を使用して、IIS をインストールします。 名前および Set-AzVMExtension の VMName の値として、*myVM2* と *myVM3* という名前を入力します。

## <a name="create-backend-pools-with-the-virtual-machines"></a>仮想マシンでのバックエンド プールの作成

1. **[すべてのリソース]**、**[myAppGateway]** の順にクリックします。
2. **[バックエンド プール]** をクリックします。 既定のプールがアプリケーション ゲートウェイで自動的に作成されます。 **[appGatewayBackendPool]** をクリックします。
3. **[ターゲットの追加]** をクリックして、*myVM1* を appGatewayBackendPool に追加します。

    ![バックエンド サーバーの追加](./media/application-gateway-create-url-route-portal/application-gateway-backend.png)

4. **[Save]** をクリックします。
5. **[バックエンド プール]**、**[追加]** の順にクリックします。
6. *imagesBackendPool* という名前を入力し、**[ターゲットの追加]** を使用して *myVM2* を追加します。
7. Click **OK**.
8. **[追加]** を再度クリックして、*videoBackendPool* という名前の別のバックエンド プールを追加し、*myVM3* を追加します。

## <a name="create-a-backend-listener"></a>バックエンド リスナーの作成

1. **[リスナー]**、**[基本]** の順にクリックします。
2. 名前として「*myBackendListener*」を、フロントエンド ポートの名前として「*myFrontendPort*」を、リスナーのポートとして「*8080*」を入力します。
3. Click **OK**.

## <a name="create-a-path-based-routing-rule"></a>パス ベースのルーティング規則の作成

1. **[ルール]**、**[パス ベース]** の順にクリックします。
2. 名前として「*rule2*」を入力します。
3. 最初のパスの名前として「*イメージ*」と入力します。 パスには「*/images/*\*」と入力します。 バックエンド プールとして **[imagesBackendPool]** を選択します。
4. 2 番目のパスの名前として「*ビデオ*」と入力します。 パスには「*/video/*\*」と入力します。 バックエンド プールとして **[videoBackendPool]** を選択します。

    ![パス ベース ルールの作成](./media/application-gateway-create-url-route-portal/application-gateway-route-rule.png)

5. Click **OK**.

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. **[すべてのリソース]**、**[myAGPublicIPAddress]** の順にクリックします。

    ![アプリケーション ゲートウェイのパブリック IP アドレスの記録](./media/application-gateway-create-url-route-portal/application-gateway-record-ag-address.png)

2. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 http:\//40.121.222.19 などです。

    ![アプリケーション ゲートウェイでのベース URL のテスト](./media/application-gateway-create-url-route-portal/application-gateway-iistest.png)

3. URL を http://&lt;ip-address&gt;:8080/images/test.htm に変更します。&lt;ip-address&gt; は使用している IP アドレスに置き換えてください。次の例のように表示されます。

    ![アプリケーション ゲートウェイでのイメージ URL のテスト](./media/application-gateway-create-url-route-portal/application-gateway-iistest-images.png)

4. URL を http://&lt;ip-address&gt;:8080/video/test.htm に変更します。&lt;ip-address&gt; は使用している IP アドレスに置き換えてください。次の例のように表示されます。

    ![アプリケーション ゲートウェイでのビデオ URL のテスト](./media/application-gateway-create-url-route-portal/application-gateway-iistest-video.png)

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです

> [!div class="checklist"]
> * アプリケーション ゲートウェイの作成
> * バックエンド サーバー用の仮想マシンの作成
> * バックエンド サーバーでのバックエンド プールの作成
> * バックエンド リスナーの作成
> * パス ベースのルーティング規則の作成

アプリケーション ゲートウェイとその関連リソースの詳細を確認するには、ハウツー記事に進みます。
