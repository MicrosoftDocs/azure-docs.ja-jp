---
title: Web アプリケーション ファイアウォールを有効にする - Azure CLI
description: Azure CLI を使用して、アプリケーション ゲートウェイ上の Web アプリケーション ファイアウォールで Web トラフィックを制限する方法について説明します。
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.date: 08/31/2020
ms.author: victorh
ms.topic: how-to
ms.custom: devx-track-azurecli
ms.openlocfilehash: 967d4d4a49809c2b5fa7a344286469bb67eec6cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102217603"
---
# <a name="enable-web-application-firewall-using-the-azure-cli"></a>Azure CLI を使用して Web アプリケーション ファイアウォールを有効にする

[Web アプリケーション ファイアウォール](ag-overview.md) (WAF) でアプリケーション ゲートウェイ上のトラフィックを制限できます。 WAF は、[OWASP](https://www.owasp.org/index.php/Category:OWASP_ModSecurity_Core_Rule_Set_Project) ルールを使用してアプリケーションを保護します。 こうしたルールには、SQL インジェクション、クロスサイト スクリプティング攻撃、セッション ハイジャックなどの攻撃に対する保護が含まれます。

この記事では、次の方法について説明します。

 * ネットワークのセットアップ
 * WAF 対応のアプリケーション ゲートウェイの作成
 * 仮想マシン スケール セットを作成する
 * ストレージ アカウントの作成と診断の構成

![Web アプリケーション ファイアウォールの例](../media/tutorial-restrict-web-traffic-cli/scenario-waf.png)

好みに応じて、[Azure PowerShell](tutorial-restrict-web-traffic-powershell.md) を使ってこの手順を実行することもできます。

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../../includes/azure-cli-prepare-your-environment.md)]

- この記事では、Azure CLI のバージョン 2.0.4 以降が必要です。 Azure Cloud Shell を使用している場合は、最新バージョンが既にインストールされています。

## <a name="create-a-resource-group"></a>リソース グループを作成する

リソース グループとは、Azure リソースのデプロイと管理に使用する論理コンテナーです。 [az group create](/cli/azure/group#az-group-create) を使用して *myResourceGroupAG* という名前の Azure リソース グループを作成します。

```azurecli-interactive
az group create --name myResourceGroupAG --location eastus
```

## <a name="create-network-resources"></a>ネットワーク リソースを作成する

仮想ネットワークとサブネットは、アプリケーション ゲートウェイとその関連リソースにネットワーク接続を提供するために使用されます。 "*myVNet*" という名前の仮想ネットワークと "*myAGSubnet*" という名前のサブネットを作成します。 次に、"*myAGPublicIPAddress*" という名前のパブリック IP アドレスを作成します。

```azurecli-interactive
az network vnet create \
  --name myVNet \
  --resource-group myResourceGroupAG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name myBackendSubnet \
  --subnet-prefix 10.0.1.0/24

az network vnet subnet create \
  --name myAGSubnet \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --address-prefix 10.0.2.0/24

az network public-ip create \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --allocation-method Static \
  --sku Standard
```

## <a name="create-an-application-gateway-with-a-waf"></a>WAF を含んだアプリケーション ゲートウェイの作成

[az network application-gateway create](/cli/azure/network/application-gateway) を使用して、*myAppGateway* という名前のアプリケーション ゲートウェイを作成することができます。 Azure CLI を使用してアプリケーション ゲートウェイを作成するときは、容量、SKU、HTTP 設定などの構成情報を指定します。 アプリケーション ゲートウェイは、"*myAGSubnet*" と "*myAGPublicIPAddress*" に割り当てられます。

```azurecli-interactive
az network application-gateway create \
  --name myAppGateway \
  --location eastus \
  --resource-group myResourceGroupAG \
  --vnet-name myVNet \
  --subnet myAGSubnet \
  --capacity 2 \
  --sku WAF_v2 \
  --http-settings-cookie-based-affinity Disabled \
  --frontend-port 80 \
  --http-settings-port 80 \
  --http-settings-protocol Http \
  --public-ip-address myAGPublicIPAddress

az network application-gateway waf-config set \
  --enabled true \
  --gateway-name myAppGateway \
  --resource-group myResourceGroupAG \
  --firewall-mode Detection \
  --rule-set-version 3.0
```

アプリケーション ゲートウェイの作成には数分かかる場合があります。 アプリケーション ゲートウェイを作成すると、新たに次の機能が確認できます。

- *appGatewayBackendPool* - アプリケーション ゲートウェイには、少なくとも 1 つのバックエンド アドレス プールが必要です。
- *appGatewayBackendHttpSettings* - 通信に使用するポート 80 と HTTP プロトコルを指定します。
- *appGatewayHttpListener* - *appGatewayBackendPool* に関連付けられている既定のリスナー。
- *appGatewayFrontendIP* -*myAGPublicIPAddress* を *appGatewayHttpListener* に割り当てます。
- *rule1* - *appGatewayHttpListener* に関連付けられている既定のルーティング規則。

## <a name="create-a-virtual-machine-scale-set"></a>仮想マシン スケール セットを作成する

この例では、アプリケーション ゲートウェイのバックエンド プールに 2 つのサーバーを提供する仮想マシン スケール セットを作成します。 スケール セット内の仮想マシンは、*myBackendSubnet* サブネットに関連付けます。 スケール セットを作成するには、[az vmss create](/cli/azure/vmss#az-vmss-create) を使用できます。

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

```azurecli-interactive
az vmss extension set \
  --publisher Microsoft.Azure.Extensions \
  --version 2.0 \
  --name CustomScript \
  --resource-group myResourceGroupAG \
  --vmss-name myvmss \
  --settings '{ "fileUris": ["https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/application-gateway/iis/install_nginx.sh"],"commandToExecute": "./install_nginx.sh" }'
```

## <a name="create-a-storage-account-and-configure-diagnostics"></a>ストレージ アカウントの作成と診断の構成

この記事では、アプリケーション ゲートウェイは、検出および防止の目的で、ストレージ アカウントを使用してデータを格納します。 Azure Monitor ログまたはイベント ハブを使用して、データを記録することもできます。 

### <a name="create-a-storage-account"></a>ストレージ アカウントの作成

*myagstore1* という名前のストレージ アカウントを [az storage account create](/cli/azure/storage/account#az-storage-account-create) で作成します。

```azurecli-interactive
az storage account create \
  --name myagstore1 \
  --resource-group myResourceGroupAG \
  --location eastus \
  --sku Standard_LRS \
  --encryption-services blob
```

### <a name="configure-diagnostics"></a>診断の構成

ApplicationGatewayAccessLog、ApplicationGatewayPerformanceLog、および ApplicationGatewayFirewallLog ログにデータが記録されるように診断を構成します。 `<subscriptionId>` を実際のサブスクリプション ID に置き換えてから、[az monitor diagnostic-settings create](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) で診断を構成します。

```azurecli-interactive
appgwid=$(az network application-gateway show --name myAppGateway --resource-group myResourceGroupAG --query id -o tsv)

storeid=$(az storage account show --name myagstore1 --resource-group myResourceGroupAG --query id -o tsv)

az monitor diagnostic-settings create --name appgwdiag --resource $appgwid \
  --logs '[ { "category": "ApplicationGatewayAccessLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayPerformanceLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } }, { "category": "ApplicationGatewayFirewallLog", "enabled": true, "retentionPolicy": { "days": 30, "enabled": true } } ]' \
  --storage-account $storeid
```

## <a name="test-the-application-gateway"></a>アプリケーション ゲートウェイのテスト

アプリケーション ゲートウェイのパブリック IP アドレスを取得するには、[az network public-ip show](/cli/azure/network/public-ip#az-network-public-ip-show) を使用します。 そのパブリック IP アドレスをコピーし、ブラウザーのアドレス バーに貼り付けます。

```azurecli-interactive
az network public-ip show \
  --resource-group myResourceGroupAG \
  --name myAGPublicIPAddress \
  --query [ipAddress] \
  --output tsv
```

![アプリケーション ゲートウェイでのベース URL のテスト](../media/tutorial-restrict-web-traffic-cli/application-gateway-nginxtest.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

必要がなくなったら、リソース グループ、アプリケーション ゲートウェイ、およびすべての関連リソースを削除します。

```azurecli-interactive
az group delete --name myResourceGroupAG 
```

## <a name="next-steps"></a>次のステップ

[Web アプリケーション ファイアウォール規則のカスタマイズ](application-gateway-customize-waf-rules-portal.md)
