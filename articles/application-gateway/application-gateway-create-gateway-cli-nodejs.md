---
title: Azure Application Gateway を作成する - Azure クラシック CLI
description: Resource Manager で Azure クラシック CLI を使用して Application Gateway を作成する方法について説明します
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 4/15/2019
ms.author: victorh
ms.openlocfilehash: 7107f45253c4f13b3378489726bf5034e104fa30
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/16/2019
ms.locfileid: "59608473"
---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Azure CLI を使用してアプリケーション ゲートウェイを作成する

Azure Application Gateway はレイヤー 7 のロード バランサーです。 クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。 Application Gateway には、次のアプリケーション配信機能があります: HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポート。

## <a name="prerequisite-install-the-azure-cli"></a>前提条件:Azure CLI のインストール

この記事の手順を実行するには、[Azure CLI をインストール](../xplat-cli-install.md)して、[Azure にサインイン](/cli/azure/authenticate-azure-cli)する必要があります。 

> [!NOTE]
> Azure アカウントをお持ちでない場合は、取得する必要があります。 [ここで無料試用版](../active-directory/fundamentals/sign-up-organization.md)にサインアップしてください。

## <a name="scenario"></a>シナリオ

このシナリオでは、Azure Portal を使用してアプリケーション ゲートウェイを作成する方法について説明します。

このシナリオで、以下の作業を行います。

* 2 つのインスタンスを持つ中規模のアプリケーション ゲートウェイを作成します。
* 予約済み CIDR ブロック 10.0.0.0/16 を持つ、ContosoVNET という名前の仮想ネットワークを作成します。
* CIDR ブロックとして 10.0.0.0/28 を使用する subnet01 という名前のサブネットを作成します。

> [!NOTE]
> カスタムの正常性プローブ、バックエンド プール アドレス、追加規則など、アプリケーション ゲートウェイの追加の構成は、初めてデプロイしている間ではなく、アプリケーション ゲートウェイが構成された後で構成されます。

## <a name="before-you-begin"></a>開始する前に

Azure Application Gateway には、専用のサブネットが必要です。 仮想ネットワークを作成する場合は、複数のサブネットを持つことができるように十分なアドレス空間を残しておいてください。 アプリケーション ゲートウェイをサブネットにデプロイすると、追加のアプリケーション ゲートウェイのみをそのサブネットにデプロイすることができます。

## <a name="sign-in-to-azure"></a>Azure へのサインイン

**Microsoft Azure コマンド プロンプト**を開き、サインインします。

```azurecli-interactive
az login
```

上記の例に従って入力すると、コードが表示されます。 ブラウザーで https://aka.ms/devicelogin に移動して、サインオン プロセスを続けます。

![cmd showing device login][1]

ブラウザーで、受け取ったコードを入力します。 サインイン ページにリダイレクトされます。

![browser to enter code][2]

コードを入力してサインインした後、ブラウザーを閉じてこのシナリオに基づいて操作を続行します。

![successfully signed in][3]

## <a name="switch-to-resource-manager-mode"></a>Resource Manager モードへの切り替え

```azurecli-interactive
azure config mode arm
```

## <a name="create-the-resource-group"></a>リソース グループの作成

アプリケーション ゲートウェイを作成する前に、そのアプリケーション ゲートウェイの追加先となるリソース グループを作成します。 コマンドを次に示します。

```azurecli-interactive
azure group create \
--name ContosoRG \
--location eastus
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

リソース グループを作成したら、アプリケーション ゲートウェイに使用する仮想ネットワークを作成します。  次の例では、前回のシナリオで定義した 10.0.0.0/16 をアドレス空間として使用しています。

```azurecli-interactive
azure network vnet create \
--name ContosoVNET \
--address-prefixes 10.0.0.0/16 \
--resource-group ContosoRG \
--location eastus
```

## <a name="create-a-subnet"></a>サブネットの作成

仮想ネットワークを作成したら、アプリケーション ゲートウェイ用のサブネットを追加します。  アプリケーション ゲートウェイと同じ仮想ネットワークで Web アプリをホストし、アプリケーション ゲートウェイと一緒に使用する予定がある場合は、別のサブネットを確保できるだけの余裕を持たせてください。

```azurecli-interactive
azure network vnet subnet create \
--resource-group ContosoRG \
--name subnet01 \
--vnet-name ContosoVNET \
--address-prefix 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

仮想ネットワークとサブネットを作成すれば、アプリケーション ゲートウェイの前提条件は満たされたことになります。 さらに、以下の手順では、前にエクスポートした .pfx 証明書と証明書のパスワードが必要です。バックエンドに使用される IP アドレスは、バックエンド サーバーの IP アドレスです。 バックエンド サーバーに該当する仮想ネットワーク内のプライベート IP、パブリック IP、または完全修飾ドメイン名を指定してください。

```azurecli-interactive
azure network application-gateway create \
--name AdatumAppGateway \
--location eastus \
--resource-group ContosoRG \
--vnet-name ContosoVNET \
--subnet-name subnet01 \
--servers 134.170.185.46,134.170.188.221,134.170.185.50 \
--capacity 2 \
--sku-tier Standard \
--routing-rule-type Basic \
--frontend-port 80 \
--http-settings-cookie-based-affinity Enabled \
--http-settings-port 80 \
--http-settings-protocol http \
--frontend-port http \
--sku-name Standard_Medium
```

> [!NOTE]
> 作成時に指定できるパラメーターのリストを表示するには、**azure network application-gateway create --help** コマンドを実行します。

この例では、リスナー、バックエンド プール、バックエンド http 設定、規則の既定の設定を持つ基本的なアプリケーション ゲートウェイを作成しています。 プロビジョニングが成功したら、独自のデプロイに合わせて、これらの設定を変更することができます。
前の手順でバックエンド プールに対して既に Web アプリケーションを定義している場合、アプリケーション ゲートウェイを作成すると負荷分散が開始されます。

## <a name="next-steps"></a>次の手順

 [カスタムの正常性プローブの作成](application-gateway-create-probe-portal.md)

 [SSL オフロードの構成](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli-nodejs/scenario.png
[1]: ./media/application-gateway-create-gateway-cli-nodejs/figure1.png
[2]: ./media/application-gateway-create-gateway-cli-nodejs/figure2.png
[3]: ./media/application-gateway-create-gateway-cli-nodejs/figure3.png
