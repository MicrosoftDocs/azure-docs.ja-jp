---
title: ポータルでの HTTP から HTTPS へのリダイレクト - Azure Application Gateway
description: Azure portal を使用して、HTTP から HTTPS にトラフィックがリダイレクトされるアプリケーション ゲートウェイを作成する方法について説明します。
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 11/13/2019
ms.author: victorh
ms.openlocfilehash: 67153fa750fee765dcaa1072eec87a2f6169b918
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "93397282"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-portal"></a>Azure portal を使用して HTTP から HTTPS にリダイレクトするアプリケーション ゲートウェイを作成する

Azure portal で TLS 終端の証明書を使用して[アプリケーション ゲートウェイ](overview.md)を作成できます。 アプリケーション ゲートウェイで HTTP トラフィックを HTTPS ポートにリダイレクトするために、ルーティング規則が使用されます。 また、この例では、2 つの仮想マシン インスタンスが含まれるアプリケーション ゲートウェイのバックエンド プールのために[仮想マシン スケール セット](../virtual-machine-scale-sets/overview.md)を作成します。

この記事では、次のことについて説明します。

* 自己署名証明書の作成
* ネットワークの設定
* 証明書でのアプリケーション ゲートウェイの作成
* リスナーとリダイレクト規則の追加
* 既定のバックエンド プールでの仮想マシン スケール セットの作成

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

このチュートリアルでは、証明書を作成して IIS をインストールするために、Azure PowerShell モジュール バージョン 1.0.0 以降が必要になります。 バージョンを確認するには、`Get-Module -ListAvailable Az` を実行します。 アップグレードする必要がある場合は、[Azure PowerShell モジュールのインストール](/powershell/azure/install-az-ps)に関するページを参照してください。 このチュートリアルでコマンドを実行するには、`Login-AzAccount` を実行して Azure との接続を作成することも必要です。

## <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

実際の運用では、信頼できるプロバイダーによって署名された有効な証明書をインポートする必要があります。 このチュートリアルでは、[New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) を使用して、自己署名証明書を作成します。 [Export-PfxCertificate](/powershell/module/pkiclient/export-pfxcertificate) と返されたサムプリントを使用して、pfx ファイルを証明書からエクスポートできます。

```powershell
New-SelfSignedCertificate `
  -certstorelocation cert:\localmachine\my `
  -dnsname www.contoso.com
```

次のような結果が表示されます。

```
PSParentPath: Microsoft.PowerShell.Security\Certificate::LocalMachine\my

Thumbprint                                Subject
----------                                -------
E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630  CN=www.contoso.com
```

サムプリントを使用して、pfx ファイルを作成します。

```powershell
$pwd = ConvertTo-SecureString -String "Azure123456!" -Force -AsPlainText
Export-PfxCertificate `
  -cert cert:\localMachine\my\E1E81C23B3AD33F9B4D1717B20AB65DBB91AC630 `
  -FilePath c:\appgwcert.pfx `
  -Password $pwd
```

## <a name="create-an-application-gateway"></a>アプリケーション ゲートウェイの作成

作成したリソース間の通信には仮想ネットワークが必要です。 この例では 2 つのサブネットが作成されます。1 つはアプリケーション ゲートウェイ用で、もう 1 つはバックエンド サーバー用です。 仮想ネットワークは、アプリケーション ゲートウェイを作成するときに同時に作成できます。

1. Azure Portal [https://portal.azure.com](https://portal.azure.com) にサインインします。
2. Azure Portal の左上隅にある **[リソースの作成]** をクリックします。
3. **[ネットワーク]** を選択し、注目のリストで **[Application Gateway]** を選択します。
4. 次のアプリケーション ゲートウェイの値を入力します。

   - *myAppGateway* - アプリケーション ゲートウェイの名前です。
   - *myResourceGroupAG* - 新しいリソース グループの名前です。

     ![新しいアプリケーション ゲートウェイの作成](./media/create-url-route-portal/application-gateway-create.png)

5. 他の設定は既定値をそのまま使用し、 **[OK]** をクリックします。
6. **[仮想ネットワークの選択]** 、 **[新規作成]** の順にクリックし、次の仮想ネットワークの値を入力します。

   - *myVNet* - 仮想ネットワークの名前です。
   - *10.0.0.0/16* - 仮想ネットワークのアドレス空間です。
   - *myAGSubnet* - サブネットの名前です。
   - *10.0.0.0/24* - サブネットのアドレス空間です。

     ![Create virtual network](./media/create-url-route-portal/application-gateway-vnet.png)

7. **[OK]** をクリックして、仮想ネットワークとサブネットを作成します。
8. **[フロントエンド IP 構成]** で、 **[IP アドレスの種類]** は **[パブリック]** であり、 **[新規作成]** が確実に選択されているようにします。 名前として「*myAGPublicIPAddress*」と入力します。 他の設定は既定値をそのまま使用し、 **[OK]** をクリックします。
9. **[リスナー構成]** で、 **[HTTPS]** 、 **[ファイルの選択]** の順に選び、*c:\appgwcert.pfx* ファイルに移動して **[開く]** を選択します。
10. 証明書の名前として「*appgwcert*」と入力し、パスワードとして「*Azure123456!* 」と 入力します。
11. Web アプリケーション ファイアウォールは無効のままにし、 **[OK]** を選択します。
12. 概要ページで設定を確認し、 **[OK]** を選択して、ネットワーク リソースとアプリケーション ゲートウェイを作成します。 アプリケーション ゲートウェイの作成には数分かかる場合があります。デプロイが正常に終了するのを待ち、その後で次のセクションに進みます。

### <a name="add-a-subnet"></a>サブネットの追加

1. 左側のメニューで **[すべてのリソース]** を選択し、リソースの一覧で **[myVNet]** を選びます。
2. **[サブネット]** を選択してから、 **[サブネット]** をクリックします。

    ![サブネットの作成](./media/create-url-route-portal/application-gateway-subnet.png)

3. サブネットの名前として「*myBackendSubnet*」と入力します。
4. アドレス範囲として「*10.0.2.0/24*」と入力してから、 **[OK]** を選択します。

## <a name="add-a-listener-and-redirection-rule"></a>リスナーとリダイレクト規則の追加

### <a name="add-the-listener"></a>リスナーを追加する

最初に、ポート 80 用に *myListener* という名前のリスナーを追加します。

1. **myResourceGroupAG** リソース グループを開き、 **[myAppGateway]** を選択します。
2. **[リスナー]** 、 **[+ Basic]\(+ 基本\)** の順に選択します。
3. 名前として「*MyListener*」と入力します。
4. 新しいフロントエンド ポート名については「*httpPort*」、ポートは「*80*」と入力します。
5. プロトコルが確実に **[HTTP]** に設定されている場合は、 **[OK]** を選択します。

### <a name="add-a-routing-rule-with-a-redirection-configuration"></a>リダイレクト構成と共にルーティング規則を追加する

1. **[myAppGateway]** で、 **[規則]** 、 **[+Request routing rule]\(+基本ルーティング規則\)** の順に選択します。
2. **[規則名]** には、「*Rule2*」と入力します。
3. リスナーに対して **[MyListener]** が確実に選択されているようにします。
4. **[Backend targets]\(バックエンド ターゲット\)** タブをクリックし、 **[ターゲットの種類]** は *[リダイレクト]* を選択します。
5. **[リダイレクトの種類]** では、 **[永続]** を選択します。
6. **[リダイレクト ターゲット]** では、 **[リスナー]** を選択します。
7. **[対象リスナー]** が **[appGatewayHttpListener]** に確実に設定されているようにします。
8. **[クエリ文字列を含める]** と **[パスを含める]** は *[はい]* を選択します。
9. **[追加]** を選択します。

## <a name="create-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを作成する

この例では、アプリケーション ゲートウェイのバックエンド プールにサーバーを提供する仮想マシン スケール セットを作成します。

1. ポータルの左上隅にある **[+リソースの作成]** を選択します。
2. **[コンピューティング]** を選択します。
3. 検索ボックスに、「*スケール セット*」と入力し、Enter キーを押します。
4. **[仮想マシン スケール セット]** 、 **[作成]** の順に選択します。
5. **[仮想マシン スケール セットの名前]** に、「*myvmss*」と入力します。
6. オペレーティング システムのディスク イメージとして、** **[Windows Server 2016 Datacenter]** が確実に選択されているようにします。
7. **[リソース グループ]** では、 **[myResourceGroupAG]** を選択します。
8. **[ユーザー名]** では、「*azureuser*」と入力します。
9. **[パスワード]** では、「*Azure123456!* 」と入力し、 パスワードを確認します。
10. **[インスタンス数]** では、値が確実に **2** であるようにします。
11. **[インスタンス サイズ]** では、 **[D2s_v3]** を選択します。
12. **[ネットワーク]** で、 **[負荷分散オプションの選択]** が **[Application Gateway]** に確実に設定されているようにします。
13. **[アプリケーション ゲートウェイ]** が確実に **[myAppGateway]** に設定されているようにします。
14. **[サブネット]** が **[myBackendSubnet]** に確実に設定されているようにします。
15. **［作成］** を選択します

### <a name="associate-the-scale-set-with-the-proper-backend-pool"></a>スケール セットを適切なバックエンド プールに関連付ける

仮想マシン スケール セットのポータル UI でスケール セット用の新しいバックエンド プールが作成されますが、それを既存の appGatewayBackendPool に関連付ける必要があります。

1. **myResourceGroupAg** リソース グループを開きます。
2. **[myAppGateway]** を選択します。
3. **[バックエンド プール]** を選択します。
4. **[myAppGatewaymyvmss]** を選択します。
5. **[Remove all targets from backend pool]\(バックエンド プールからすべてのターゲットを削除\)** を選択します。
6. **[保存]** を選択します。
7. このプロセスが完了したら、 **[myAppGatewaymyvmss]** バックエンド プール、 **[削除]** 、 **[OK]** の順に選択して確定します。
8. **[appGatewayBackendPool]** を選択します。
9. **[ターゲット]** で、 **[VMSS]** を選択します。
10. **[VMSS]** で、 **[myvmss]** を選択します。
11. **[ネットワーク インターフェイスの構成]** で、 **[myvmssNic]** を選択します。
12. **[保存]** を選択します。

### <a name="upgrade-the-scale-set"></a>スケール セットをアップグレードする

最後に、これらの変更でスケール セットをアップグレードする必要があります。

1. **[myvmss]** スケール セットを選択します。
2. **[設定]** で、 **[インスタンス]** を選択します。
3. 両方のインスタンスを選択し、 **[アップグレード]** を選びます。
4. **[はい]** を選択して確定します。
5. これが完了したら、**myAppGateway** に戻り、 **[バックエンド プール]** を選択します。 これで、**appGatewayBackendPool** のターゲットは 2 個になり、**myAppGatewaymyvmss** のターゲットは 0 個になるはずです。
6. **[myAppGatewaymyvmss]** 、 **[削除]** の順に選択します。
7. **[OK]** を選択して確定します。

### <a name="install-iis"></a>IIS のインストール

スケール セットに IIS をインストールする簡単な方法は、PowerShell を使用することです。 ポータルで Cloud Shell アイコンをクリックし、確実に **[PowerShell]** が選択されているようにします。

次のコードを PowerShell ウィンドウに貼り付け、Enter キーを押します。

```azurepowershell
$publicSettings = @{ "fileUris" = (,"https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/appgatewayurl.ps1"); 
  "commandToExecute" = "powershell -ExecutionPolicy Unrestricted -File appgatewayurl.ps1" }
$vmss = Get-AzVmss -ResourceGroupName myResourceGroupAG -VMScaleSetName myvmss
Add-AzVmssExtension -VirtualMachineScaleSet $vmss `
  -Name "customScript" `
  -Publisher "Microsoft.Compute" `
  -Type "CustomScriptExtension" `
  -TypeHandlerVersion 1.8 `
  -Setting $publicSettings
Update-AzVmss `
  -ResourceGroupName myResourceGroupAG `
  -Name myvmss `
  -VirtualMachineScaleSet $vmss
```

### <a name="upgrade-the-scale-set"></a>スケール セットをアップグレードする

IIS でインスタンスを変更した後、この変更でスケール セットをもう一度アップグレードする必要があります。

1. **[myvmss]** スケール セットを選択します。
2. **[設定]** で、 **[インスタンス]** を選択します。
3. 両方のインスタンスを選択し、 **[アップグレード]** を選びます。
4. **[はい]** を選択して確定します。

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

アプリケーションのパブリック IP アドレスは、アプリケーション ゲートウェイの [概要] ページから取得できます。

1. **[myAppGateway]** を選択します。
2. **[概要]** ページで、 **[フロントエンド パブリック IP アドレス]** の IP アドレスを書き留めます。

3. そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。 たとえば、 http://52.170.203.149 のように指定します。

   ![セキュリティに関する警告](./media/redirect-http-to-https-powershell/application-gateway-secure.png)

4. 自己署名証明書を使用した場合、セキュリティ警告を受け入れるには、そのまま **[詳細]** を選択し **[Web ページへ移動]** を選択します。 セキュリティで保護された IIS Web サイトは、次の例のように表示されます。

   ![アプリケーション ゲートウェイでのベース URL のテスト](./media/redirect-http-to-https-powershell/application-gateway-iistest.png)

## <a name="next-steps"></a>次のステップ

[内部リダイレクトを行うアプリケーション ゲートウェイを作成する](redirect-internal-site-powershell.md)方法を学習します。