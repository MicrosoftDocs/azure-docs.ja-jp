---
title: SSL 終了でアプリケーション ゲートウェイを構成する - Azure Portal | Microsoft Docs
description: Azure Portal を使用して、アプリケーション ゲートウェイを構成し、SSL 終了の証明書を追加する方法について説明します。
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
tags: azure-resource-manager
ms.service: application-gateway
ms.topic: article
ms.date: 5/15/2018
ms.author: victorh
ms.openlocfilehash: 92db27aa486936d53c2e2e1c92db7d728b7d99c5
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091836"
---
# <a name="configure-an-application-gateway-with-ssl-termination-using-the-azure-portal"></a>Azure Portal を使用して SSL 終了でアプリケーション ゲートウェイを構成する

Azure Portal を使用して、バックエンド サーバーに仮想マシンを使用する SSL 終了の証明書で、[アプリケーション ゲートウェイ](overview.md)を構成することができます。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * 自己署名証明書の作成
> * 証明書でのアプリケーション ゲートウェイの作成
> * バックエンド サーバーとして使用される仮想マシンの作成

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="log-in-to-azure"></a>Azure にログインする

Azure Portal ([https://portal.azure.com](https://portal.azure.com)) にログインする

## <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

このセクションでは、アプリケーション ゲートウェイのリスナーを作成するときに、[New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/new-selfsignedcertificate) を使用して、Azure Portal にアップロードする自己署名証明書を作成します。

ローカル コンピューターでは、管理者として Windows PowerShell ウィンドウを開きます。 次のコマンドを実行して、証明書を作成します。

```powershell
New-SelfSignedCertificate \
  -certstorelocation cert:\localmachine\my \
  -dnsname www.contoso.com
```

次のような応答が表示されます。

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com

Use [Export-PfxCertificate](https://docs.microsoft.com/powershell/module/pkiclient/export-pfxcertificate) with the Thumbprint that was returned to export a pfx file from the certificate:
```

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate \
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 \
  -FilePath c:\appgwcert.pfx \
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

作成したリソース間の通信には仮想ネットワークが必要です。 この例では 2 つのサブネットが作成されます。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。

1. Azure Portal の左上にある **[新規]** をクリックします。
2. **[ネットワーク]** を選択し、注目のリストで **[Application Gateway]** を選択します。
3. アプリケーション ゲートウェイの名前として「*myAppGateway*」を、新しいリソース グループとして「*myResourceGroupAG*」を入力します。
4. 他の設定は既定値をそのまま使用し、**[OK]** をクリックします。
5. **[仮想ネットワークの選択]**、**[新規作成]** の順にクリックし、次の仮想ネットワークの値を入力します。

   - *myVNet* - 仮想ネットワークの名前です。
   - *10.0.0.0/16* - 仮想ネットワークのアドレス空間です。
   - *myAGSubnet* - サブネットの名前です。
   - *10.0.0.0/24* - サブネットのアドレス空間です。

     ![Create virtual network](./media/create-ssl-portal/application-gateway-vnet.png)

6. **[OK]** をクリックして、仮想ネットワークとサブネットを作成します。
7. **[パブリック IP アドレスの選択]**、**[新規作成]** の順にクリックし、パブリック IP アドレスの名前を入力します。 この例では、パブリック IP アドレスの名前は *myAGPublicIPAddress* にします。 他の設定は既定値をそのまま使用し、**[OK]** をクリックします。
8. リスナーのプロトコルとして **[HTTPS]** をクリックし、ポートが **443** として定義されていることを確認します。
9. フォルダー アイコンをクリックし、前に作成したアップロードする *appgwcert.pfx* 証明書に移動します。
10. 証明書の名前として「*mycert1*」を、パスワードとして「*Azure123456!*」 を入力し、**[OK]** をクリックします。

    ![新しいアプリケーション ゲートウェイの作成](./media/create-ssl-portal/application-gateway-create.png)

11. 概要ページで設定を確認し、**[OK]** をクリックして、ネットワーク リソースとアプリケーション ゲートウェイを作成します。 アプリケーション ゲートウェイの作成には数分かかる場合があります。デプロイが正常に終了するのを待ち、その後で次のセクションに進みます。

### <a name="add-a-subnet"></a>サブネットの追加

1. 左側のメニューで **[すべてのリソース]** をクリックし、リソースの一覧で **[myVNet]** をクリックします。
2. **[サブネット]**、**[サブネット]** の順にクリックします。

    ![サブネットの作成](./media/create-ssl-portal/application-gateway-subnet.png)

3. サブネットの名前として「*myBackendSubnet*」を入力し、**[OK]** をクリックします。

## <a name="create-backend-servers"></a>バックエンド サーバーの作成

この例では、アプリケーション ゲートウェイのバックエンド サーバーとして使用する 2 つの仮想マシンを作成します。 また、IIS を仮想マシンにインストールして、アプリケーション ゲートウェイが正常に作成されたことを確認します。

### <a name="create-a-virtual-machine"></a>仮想マシンの作成

1. **[新規]** をクリックします。
2. **[コンピューティング]** をクリックし、注目のリストで **[Windows Server 2016 Datacenter]** を選択します。
3. 次の仮想マシンの値を入力します。

    - *myVM* - 仮想マシンの名前です。
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

    ![カスタム拡張機能のインストール](./media/create-ssl-portal/application-gateway-extension.png)

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

3. 2 番目の仮想マシンを作成し、終了したばかりの手順を使用して、IIS をインストールします。 その名前および Set-AzVMExtension の VMName として「*myVM2*」を入力します。

### <a name="add-backend-servers"></a>バックエンド サーバーの追加

1. **[すべてのリソース]**、**[myAppGateway]** の順にクリックします。
1. **[バックエンド プール]** をクリックします。 既定のプールがアプリケーション ゲートウェイで自動的に作成されます。 **[appGatewayBackendPool]** をクリックします。
1. **[ターゲットの追加]** をクリックして、作成した各仮想マシンをバックエンド プールに追加します。

    ![バックエンド サーバーの追加](./media/create-ssl-portal/application-gateway-backend.png)

1. **[Save]** をクリックします。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

1. **[すべてのリソース]**、**[myAGPublicIPAddress]** の順にクリックします。

    ![アプリケーション ゲートウェイのパブリック IP アドレスの記録](./media/create-ssl-portal/application-gateway-ag-address.png)

2. パブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 自己署名証明書を使用した場合、セキュリティ警告を受け入れるには、[詳細] を選択し、Web ページに進みます。

    ![セキュリティに関する警告](./media/create-ssl-portal/application-gateway-secure.png)

    セキュリティで保護された IIS Web サイトは、次の例のように表示されます。

    ![アプリケーション ゲートウェイでのベース URL のテスト](./media/create-ssl-portal/application-gateway-iistest.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 自己署名証明書の作成
> * 証明書でのアプリケーション ゲートウェイの作成
> * バックエンド サーバーとして使用される仮想マシンの作成

アプリケーション ゲートウェイとその関連リソースの詳細を確認するには、ハウツー記事に進みます。
