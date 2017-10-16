---
title: "アプリケーション ゲートウェイの作成 - Azure CLI 2.0 | Microsoft Docs"
description: "Resource Manager で Azure CLI 2.0 を使用して、アプリケーション ゲートウェイを作成する方法について説明します。"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 3f43feaa8866725c5e6d8aa8cb03cd1a945de453
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-by-using-the-azure-cli-20"></a>Azure CLI 2.0 を使用してアプリケーション ゲートウェイを作成する

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure クラシック PowerShell](application-gateway-create-gateway.md)
> * [Azure Resource Manager テンプレート](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)

Azure Application Gateway は、アプリケーション配信コントローラー (ADC) をサービスとして提供する専用仮想アプライアンスで、さまざまなレイヤー 7 負荷分散機能をアプリケーションで利用できるようにします。

## <a name="cli-versions"></a>CLI のバージョン

次のコマンド ライン インターフェイス (CLI) バージョンのいずれかを使用して、アプリケーション ゲートウェイを作成できます。

* [Azure CLI 1.0](application-gateway-create-gateway-cli-nodejs.md): クラシック デプロイメント モデルと Azure Resource Manager デプロイメント モデル用の Azure CLI
* [Azure CLI 2.0](application-gateway-create-gateway-cli.md): Resource Manager デプロイメント モデル用の次世代 CLI

## <a name="prerequisite-install-the-azure-cli-20"></a>前提条件: Azure CLI 2.0 のインストール

この記事の手順を実行するには、[macOS、Linux、Windows 用の Azure CLI をインストールする](https://docs.microsoft.com/en-us/cli/azure/install-az-cli2)必要があります。

> [!NOTE]
> アプリケーション ゲートウェイを作成するには、Azure アカウントが必要です。 お持ちでない場合は、 [無料試用版](../active-directory/sign-up-organization.md)にサインアップしてください。

## <a name="scenario"></a>シナリオ

このシナリオでは、Azure Portal を使用してアプリケーション ゲートウェイを作成する方法について説明します。

このシナリオで、以下の作業を行います。

* 2 つのインスタンスを持つ中規模のアプリケーション ゲートウェイを作成します。
* 予約済み CIDR ブロック 10.0.0.0/16 を持つ、AdatumAppGatewayVNET という名前の仮想ネットワークを作成します。
* CIDR ブロックとして 10.0.0.0/28 を使用する Appgatewaysubnet という名前のサブネットを作成します。

> [!NOTE]
> カスタムの正常性プローブ、バックエンド プール アドレス、追加規則など、アプリケーション ゲートウェイの追加の構成は、初めてデプロイしている間ではなく、アプリケーション ゲートウェイが作成された後で行われます。

## <a name="before-you-begin"></a>開始する前に

アプリケーション ゲートウェイには、専用のサブネットが必要です。 仮想ネットワークを作成している場合は、複数のサブネットのための十分なアドレス空間を残しておいてください。 アプリケーション ゲートウェイをサブネットにデプロイした後、そのサブネットには、追加のアプリケーション ゲートウェイのみをデプロイできます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

**Microsoft Azure コマンド プロンプト**を開き、サインインします。

```azurecli-interactive
az login -u "username"
```

> [!NOTE]
> aka.ms/devicelogin でのコード入力が必要なデバイス ログインのスイッチを用いずに、`az login` を使用することもできます。

上記のコマンドを入力した後、コードが表示されます。 ブラウザーで https://aka.ms/devicelogin に移動して、サインイン プロセスを続行します。

![デバイスのログインを表示するコマンド プロンプト][1]

ブラウザーで、受け取ったコードを入力します。 これにより、サインイン ページにリダイレクトされます。

![ブラウザーにコードを入力][2]

コードを入力してサインインし、ブラウザーを閉じて続行します。

![正常にサインイン][3]

## <a name="create-the-resource-group"></a>リソース グループの作成

アプリケーション ゲートウェイを作成する前に、そのアプリケーション ゲートウェイの追加先となるリソース グループを作成します。 次のコマンドを使用します。

```azurecli-interactive
az group create --name myresourcegroup --location "eastus"
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

ご利用のバックエンド サーバーの IP アドレスのバックエンド IP アドレスを使用します。 バックエンド サーバーに該当する仮想ネットワーク内のプライベート IP、パブリック IP、または完全修飾ドメイン名を指定してください。 次の例では、HTTP 設定、ポート、および規則に関する追加の構成を持つアプリケーション ゲートウェイを作成します。

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet" \
--subnet-address-prefix "10.0.0.0/28" \
--servers 10.0.0.4 10.0.0.5 \
--capacity 2 \
--sku Standard_Small \
--http-settings-cookie-based-affinity Enabled \
--http-settings-protocol Http \
--frontend-port 80 \
--routing-rule-type Basic \
--http-settings-port 80 \
--public-ip-address "pip2" \
--public-ip-address-allocation "dynamic" \

```

前の例では、アプリケーション ゲートウェイの作成中は必要でない複数のプロパティが示されています。 次のコード例は、必要な情報を持つアプリケーション ゲートウェイを作成します。

```azurecli-interactive
az network application-gateway create \
--name "AdatumAppGateway" \
--location "eastus" \
--resource-group "myresourcegroup" \
--vnet-name "AdatumAppGatewayVNET" \
--vnet-address-prefix "10.0.0.0/16" \
--subnet "Appgatewaysubnet \
--subnet-address-prefix "10.0.0.0/28" \
--servers "10.0.0.5"  \
--public-ip-address pip
```
 
> [!NOTE]
> 作成中に使用するパラメーターのリストを表示するには、`az network application-gateway create --help` コマンドを実行します。

この例では、リスナー、バックエンド プール、バックエンド HTTP 設定、規則の既定の設定を持つ基本的なアプリケーション ゲートウェイを作成しています。 プロビジョニングが成功したら、独自のデプロイに合わせて、これらの設定を変更することができます。

前の手順でバックエンド プールに対して Web アプリケーションを定義した場合、ここで負荷分散が開始されます。

## <a name="get-the-application-gateway-dns-name"></a>アプリケーション ゲートウェイの DNS 名の取得
ゲートウェイを作成したら、次は通信用にフロントエンドを構成します。 パブリック IP を使用している場合、アプリケーション ゲートウェイには、動的に割り当てられたフレンドリではない DNS 名が必要です。 ユーザーがアプリケーション ゲートウェイを確実にヒットできるように、CNAME レコードを使用して、アプリケーション ゲートウェイのパブリック エンドポイントを参照します。 詳細については、「[Azure DNS を使用して Azure サービス用のカスタム ドメイン設定を提供する](../dns/dns-custom-domain.md)」をご覧ください。

別名を構成するには、アプリケーション ゲートウェイに接続されている PublicIPAddress 要素を使用して、アプリケーション ゲートウェイの詳細とそれに関連付けられている IP/DNS 名を取得します。 アプリケーション ゲートウェイの DNS 名を使用して、2 つの Web アプリケーションがこの DNS 名を指すように CNAME レコードを作成します。 アプリケーション ゲートウェイの再起動時に VIP が変更される可能性があるため、A レコードは使用しません。


```azurecli-interactive
az network public-ip show --name "pip" --resource-group "AdatumAppGatewayRG"
```

```
{
  "dnsSettings": {
    "domainNameLabel": null,
    "fqdn": "8c786058-96d4-4f3e-bb41-660860ceae4c.cloudapp.net",
    "reverseFqdn": null
  },
  "etag": "W/\"3b0ac031-01f0-4860-b572-e3c25e0c57ad\"",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/publicIPAddresses/pip2",
  "idleTimeoutInMinutes": 4,
  "ipAddress": "40.121.167.250",
  "ipConfiguration": {
    "etag": null,
    "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/AdatumAppGatewayRG/providers/Microsoft.Network/applicationGateways/AdatumAppGateway2/frontendIPConfigurations/appGatewayFrontendIP",
    "name": null,
    "privateIpAddress": null,
    "privateIpAllocationMethod": null,
    "provisioningState": null,
    "publicIpAddress": null,
    "resourceGroup": "AdatumAppGatewayRG",
    "subnet": null
  },
  "location": "eastus",
  "name": "pip2",
  "provisioningState": "Succeeded",
  "publicIpAddressVersion": "IPv4",
  "publicIpAllocationMethod": "Dynamic",
  "resourceGroup": "AdatumAppGatewayRG",
  "resourceGuid": "3c30d310-c543-4e9d-9c72-bbacd7fe9b05",
  "tags": {
    "cli[2] owner[administrator]": ""
  },
  "type": "Microsoft.Network/publicIPAddresses"
}
```

## <a name="delete-all-resources"></a>すべてのリソースの削除

この記事で作成したすべてのリソースを削除するには、次のコマンドを実行します。

```azurecli-interactive
az group delete --name AdatumAppGatewayRG
```
 
## <a name="next-steps"></a>次のステップ

カスタムの正常性プローブを作成する方法については、「[ポータルを使用して Application Gateway 用カスタム プローブを作成する](application-gateway-create-probe-portal.md)」をご覧ください。

SSL オフロードを構成してコストがかかる SSL 暗号化解除をご利用の Web サーバーから切り離す方法については、「[Azure リソース マネージャーを使用した SSL オフロード用の Application Gateway の構成](application-gateway-ssl-arm.md)」をご覧ください。

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png
