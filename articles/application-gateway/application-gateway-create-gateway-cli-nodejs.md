---
title: "Azure Application Gateway の作成 - Azure CLI 1.0 | Microsoft Docs"
description: "Resource Manager で Azure CLI 1.0 を使用して、アプリケーション ゲートウェイを作成する方法について説明します。"
services: application-gateway
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: c2f6516e-3805-49ac-826e-776b909a9104
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/23/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: f550ec9a8d254378d165f0c842459fd50ade7945
ms.openlocfilehash: 9ea999ea483543beda8d258f58dc8fba479aa603
ms.lasthandoff: 03/30/2017


---
# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Azure CLI を使用してアプリケーション ゲートウェイを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-gateway-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-gateway-arm.md)
> * [Azure Classic PowerShell (Azure クラシック PowerShell)](application-gateway-create-gateway.md)
> * [Azure Resource Manager テンプレート](application-gateway-create-gateway-arm-template.md)
> * [Azure CLI 1.0](application-gateway-create-gateway-cli.md)
> * [Azure CLI 2.0](application-gateway-create-gateway-cli.md)
> 
> 

Azure Application Gateway はレイヤー&7; のロード バランサーです。 クラウドでもオンプレミスでも、異なるサーバー間のフェールオーバーと HTTP 要求のパフォーマンス ルーティングを提供します。 Application Gateway は、HTTP 負荷分散、Cookie ベースのセッション アフィニティ、Secure Sockets Layer (SSL) オフロード、カスタムの正常性プローブ、マルチサイトのサポートなどのアプリケーション配信機能を備えています。

## <a name="prerequisite-install-the-azure-cli"></a>前提条件: Azure CLI のインストール

この記事の手順を実行するには、[Mac、Linux、Windows 用の Azure コマンド ライン インターフェイス (Azure CLI) をインストール](../xplat-cli-install.md)し、[Azure にログオン](../xplat-cli-connect.md)する必要があります。 

> [!NOTE]
> Azure アカウントをお持ちでない場合は、取得する必要があります。 [ここで無料試用版](../active-directory/sign-up-organization.md)にサインアップしてください。

## <a name="scenario"></a>シナリオ

このシナリオでは、Azure ポータルを使用してアプリケーション ゲートウェイを作成する方法について説明します。

このシナリオで、以下の作業を行います。

* 2 つのインスタンスを持つ中規模のアプリケーション ゲートウェイを作成します。
* 予約済み CIDR ブロック 10.0.0.0/16 を持つ、AdatumAppGatewayVNET という名前の仮想ネットワークを作成します。
* CIDR ブロックとして 10.0.0.0/28 を使用する Appgatewaysubnet という名前のサブネットを作成します。
* SSL オフロード用の証明書を構成します。

![Scenario example][scenario]

> [!NOTE]
> カスタムの正常性プローブ、バックエンド プール アドレス、追加規則など、アプリケーション ゲートウェイの追加の構成は、初めてデプロイしている間ではなく、アプリケーション ゲートウェイが構成された後で構成されます。

## <a name="before-you-begin"></a>開始する前に

Azure Application Gateway には、専用のサブネットが必要です。 仮想ネットワークを作成する場合は、複数のサブネットを持つことができるように十分なアドレス空間を残しておいてください。 アプリケーション ゲートウェイをサブネットにデプロイすると、追加のアプリケーション ゲートウェイのみをそのサブネットにデプロイすることができます。

## <a name="log-in-to-azure"></a>Azure へのログイン

**Microsoft Azure コマンド プロンプト**を開き、ログインします。 

```azurecli
azure login
```

上記の例に従って入力すると、コードが表示されます。 ブラウザーで https://aka.ms/devicelogin に移動して、ログイン プロセスを続行します。

![cmd showing device login][1]

ブラウザーで、受け取ったコードを入力します。 サインイン ページにリダイレクトされます。

![browser to enter code][2]

コードを入力してサインインした後、ブラウザーを閉じてこのシナリオに基づいて操作を続行します。

![successfully signed in][3]

## <a name="switch-to-resource-manager-mode"></a>Resource Manager モードへの切り替え

```azurecli
azure config mode arm
```

## <a name="create-the-resource-group"></a>リソース グループの作成

アプリケーション ゲートウェイを作成する前に、そのアプリケーション ゲートウェイの追加先となるリソース グループを作成します。 コマンドを次に示します。

```azurecli
azure group create -n AdatumAppGatewayRG -l eastus
```

## <a name="create-a-virtual-network"></a>仮想ネットワークの作成

リソース グループを作成したら、アプリケーション ゲートウェイに使用する仮想ネットワークを作成します。  次の例では、前回のシナリオで定義した 10.0.0.0/16 をアドレス空間として使用しています。

```azurecli
azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus
```

## <a name="create-a-subnet"></a>サブネットの作成

仮想ネットワークを作成したら、アプリケーション ゲートウェイ用のサブネットを追加します。  アプリケーション ゲートウェイと同じ仮想ネットワークで Web アプリをホストし、アプリケーション ゲートウェイと一緒に使用する予定がある場合は、別のサブネットを確保できるだけの余裕を持たせてください。

```azurecli
azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 
```

## <a name="create-the-application-gateway"></a>アプリケーション ゲートウェイの作成

仮想ネットワークとサブネットを作成すれば、アプリケーション ゲートウェイの前提条件は満たされたことになります。 以下の手順では、それに加えて、あらかじめエクスポートしておいた .pfx 証明書とそのパスワードが必要となります。バックエンド用の IP アドレスは、ご使用のバックエンド サーバーの IP アドレスです。 バックエンド サーバーに該当する仮想ネットワーク内のプライベート IP、パブリック IP、または完全修飾ドメイン名を指定してください。

```azurecli
azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard
```

> [!NOTE]
> 作成時に指定できるパラメーターのリストを表示するには、**azure network application-gateway create --help** コマンドを実行します。

この例では、リスナー、バックエンド プール、バックエンド http 設定、規則の既定の設定を持つ基本的なアプリケーション ゲートウェイを作成しています。 さらに、SSL オフロードの構成も行われます。 プロビジョニングが成功したら、独自のデプロイに合わせて、これらの設定を変更することができます。
前の手順でバックエンド プールに対して既に Web アプリケーションを定義している場合、アプリケーション ゲートウェイを作成すると負荷分散が開始されます。

## <a name="next-steps"></a>次のステップ

[カスタムの正常性プローブの作成](application-gateway-create-probe-portal.md)

[SSL オフロードの構成](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png

