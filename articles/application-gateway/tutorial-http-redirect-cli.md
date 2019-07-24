---
title: 証明書を使用してアプリケーション ゲートウェイを作成する - Azure CLI | Microsoft Docs
description: Azure CLI を使用して、アプリケーション ゲートウェイを作成し、SSL 終了の証明書を追加する方法について説明します。
services: application-gateway
author: vhorne
manager: jpconnock
editor: tysonn
ms.service: application-gateway
ms.topic: article
ms.workload: infrastructure-services
ms.date: 7/14/2018
ms.author: victorh
ms.openlocfilehash: fb7d84264b8de8833d7dec2a8b22dc23995c17bb
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/06/2019
ms.locfileid: "55767157"
---
# <a name="create-an-application-gateway-with-http-to-https-redirection-using-the-azure-cli"></a>Azure CLI を使用して HTTP から HTTPS へのリダイレクトと共にアプリケーション ゲートウェイを作成する

Azure CLI を使用して、SSL 終了の証明書を使って[アプリケーション ゲートウェイ](application-gateway-introduction.md)を作成できます。 アプリケーション ゲートウェイで HTTP トラフィックを HTTPS ポートにリダイレクトするために、ルーティング規則が使用されます。 また、この例では、2 つの仮想マシン インスタンスが含まれるアプリケーション ゲートウェイのバックエンド プールのために[仮想マシン スケール セット](../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md)を作成します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * 自己署名証明書の作成
> * ネットワークの設定
> * 証明書でのアプリケーション ゲートウェイの作成
> * リスナーとリダイレクト規則の追加
> * 既定のバックエンド プールでの仮想マシン スケール セットの作成

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

CLI をローカルにインストールして使用する場合、このクイック スタートを実施するには、Azure CLI バージョン 2.0.4 以降を実行している必要があります。 バージョンを確認するには、`az --version` を実行します。 インストールまたはアップグレードする必要がある場合は、[Azure CLI のインストール](/cli/azure/install-azure-cli)に関するページを参照してください。

## <a name="create-a-self-signed-certificate"></a>自己署名証明書の作成

実際の運用では、信頼できるプロバイダーによって署名された有効な証明書をインポートする必要があります。 このチュートリアルでは、openssl コマンドを使用して、自己署名証明書と pfx ファイルを作成します。

```azurecli-interactive
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out appgwcert.crt
```

証明書に対して意味のある値を入力します。 既定値をそのまま使用することもできます。

```azurecli-interactive
openssl pkcs12 -export -out appgwcert.pfx -inkey privateKey.key -in appgwcert.crt
```

証明書のパスワードを入力します。 この例では、*Azure123456!* が 使用されています。

## <a name="create-a-resource-group"></a>リソース グループの作成

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 [az group create](/cli/azure/group) を使用してリソース グループを作成します。

次の例では、*myResourceGroupAG* という名前のリソース グループを *eastus* に作成します。

```azurecli-interactive 
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

[az network vnet create](/cli/azure/network/vnet) を使用して、*myVNet* という名前の仮想ネットワークと *myAGSubnet* という名前のサブネットを作成します。 次に、[az network vnet subnet create](/cli/azure/network/vnet/subnet) を使用して、バックエンド サーバーに必要な *myBackendSubnet* という名前のサブネットを追加できます。 [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create) を使用して *myAGPublicIPAddress* という名前のパブリック IP アドレスを作成します。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myAGSubnet \
  --subnet-prefix 10.0.1.0/24
az network vnet subnet create \
  --name myBackendSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24
az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

[az network application-gateway create](/cli/azure/network/application-gateway) を使用して、*myAppGateway* という名前のアプリケーション ゲートウェイを作成することができます。 Azure CLI でアプリケーション ゲートウェイを作成するときは、キャパシティ、SKU、HTTP 設定などの構成情報を指定します。 

このアプリケーション ゲートウェイを、先ほど作成した *myAGSubnet* と *myAGPublicIPAddress* に割り当てます。 この例では、アプリケーション ゲートウェイを作成するときに、作成した証明書とそのパスワードを関連付けます。 

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGsubnet \
  --capacity 2 \
  --sku Standard_Medium \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 443 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress \
  --cert-file appgwcert.pfx \
  --cert-password "Azure123456!"

```

 アプリケーション ゲートウェイの作成には数分かかる場合があります。 アプリケーション ゲートウェイを作成すると、新たに次の機能が確認できます。

- *appGatewayBackendPool* - アプリケーション ゲートウェイには、少なくとも 1 つのバックエンド アドレス プールが必要です。
- *appGatewayBackendHttpSettings* - 通信に使用するポート 80 と HTTP プロトコルを指定します。
- *appGatewayHttpListener* - *appGatewayBackendPool* に関連付けられている既定のリスナー。
- *appGatewayFrontendIP* -*myAGPublicIPAddress* を *appGatewayHttpListener* に割り当てます。
- *rule1* - *appGatewayHttpListener* に関連付けられている既定のルーティング規則。

## <a name="add-a-listener-and-redirection-rule"></a>リスナーとリダイレクト規則の追加

### <a name="add-the-http-port"></a>HTTP ポートの追加

[az network application-gateway frontend-port create](/cli/azure/network/application-gateway/frontend-port) を使用して、HTTP ポートをアプリケーション ゲートウェイに追加できます。

```azurecli-interactive
az network application-gateway frontend-port create \
  --port 80 \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --name httpPort
```

### <a name="add-the-http-listener"></a>HTTP リスナーの追加

[az network application-gateway http-listener create](/cli/azure/network/application-gateway/http-listener#az-network-application-gateway-http-listener-create) を使用して、*myListener* という名前のリスナーをアプリケーション ゲートウェイに追加できます。

```azurecli-interactive
az network application-gateway http-listener create \
  --name myListener \
  --frontend-ip appGatewayFrontendIP \
  --frontend-port httpPort \
  --resource-group myResourceGroupAG \
  --gateway-name myAppGateway
```

### <a name="add-the-redirection-configuration"></a>リダイレクト構成の追加

[az network application-gateway redirect-config create](/cli/azure/network/application-gateway/redirect-config) を使用して、HTTP から HTTPS へのリダイレクト構成をアプリケーション ゲートウェイに追加します。

```azurecli-interactive
az network application-gateway redirect-config create \
  --name httpToHttps \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --type Permanent \
  --target-listener appGatewayHttpListener \
  --include-path true \
  --include-query-string true
```

### <a name="add-the-routing-rule"></a>ルーティング規則の追加

[az network application-gateway rule create](/cli/azure/network/application-gateway/rule) を使用して、*rule2* という名前のルーティング規則をリダイレクト構成と共にアプリケーション ゲートウェイに追加します。

```azurecli-interactive
az network application-gateway rule create \
  --gateway-name myAppGateway \
  --name rule2 \
  --resource-group myResourceGroupAG \
  --http-listener myListener \
  --rule-type Basic \
  --redirect-config httpToHttps
```

## <a name="create-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを作成する

この例では、アプリケーション ゲートウェイのバックエンド プールにサーバーを提供する、*myvmss* という名前の仮想マシン スケール セットを作成します。 スケール セット内の仮想マシンは、*myBackendSubnet* と *appGatewayBackendPool* に関連付けられています。 スケール セットを作成するには、[az vmss create](/cli/azure/vmss#az-vmss-create) を使用できます。

```azurecli-interactive
az vmss create \
  --name myvmss \
  --resource-group myResourceGroupAG \
  --image UbuntuLTS \
  --admin-username azureuser \
  --admin-password Azure123456! \
  --instance-count 2 \
  --vnet-name myVNet \
  --subnet myBackendSubnet \
  --vm-sku Standard_DS2 \
  --upgrade-policy-mode Automatic \
  --app-gateway myAppGateway \
  --backend-pool-name appGatewayBackendPool
```

### <a name="install-nginx"></a>NGINX のインストール

シェル ウィンドウでこのコマンドを実行します。

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],
  "commandToExecute": "./install_nginx.sh" }'
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

アプリケーション ゲートウェイのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip) を使用できます。 そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。

```azurepowershell-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![セキュリティに関する警告](./media/tutorial-http-redirect-cli/application-gateway-secure.png)

自己署名証明書を使用した場合、セキュリティ警告を受け入れるには、そのまま **[詳細]** を選択し **[Web ページへ移動]** を選択します。 セキュリティで保護された NGINX サイトは、次の例のように表示されます。

![アプリケーション ゲートウェイでのベース URL のテスト](./media/tutorial-http-redirect-cli/application-gateway-nginxtest.png)

## <a name="next-steps"></a>次の手順

このチュートリアルでは、以下の内容を学習しました。

> [!div class="checklist"]
> * 自己署名証明書の作成
> * ネットワークの設定
> * 証明書でのアプリケーション ゲートウェイの作成
> * リスナーとリダイレクト規則の追加
> * 既定のバックエンド プールでの仮想マシン スケール セットの作成

> [!div class="nextstepaction"]
> [アプリケーション ゲートウェイでできることについてさらに学習する](application-gateway-introduction.md)
