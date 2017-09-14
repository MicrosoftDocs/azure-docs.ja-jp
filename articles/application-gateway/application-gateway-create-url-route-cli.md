---
title: "URL ルーティング ルールを使用して Application Gateway を作成する - Azure CLI 2.0 | Microsoft Docs"
description: "このページでは、URL ルーティング ルールを使用して Azure Application Gateway を作成し、構成する方法について説明します。"
documentationcenter: na
services: application-gateway
author: georgewallace
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: gwallace
ms.translationtype: HT
ms.sourcegitcommit: 8b857b4a629618d84f66da28d46f79c2b74171df
ms.openlocfilehash: 958049830d6753ec26635f18f8f8b2fabdec0733
ms.contentlocale: ja-jp
ms.lasthandoff: 08/04/2017

---
# <a name="create-an-application-gateway-using-path-based-routing-with-azure-cli-20"></a>Azure CLI 2.0 でパスベースのルーティングを使用して Application Gateway を作成する

> [!div class="op_single_selector"]
> * [Azure Portal](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

URL パスベースのルーティングを使用すると、HTTP 要求の URL パスに基づいてルートの関連付けを行うことができます。 Application Gateway に示されている URL に対して構成されたバックエンド プールへのルートがあるかどうかを調べて、定義されたバックエンド プールにネットワーク トラフィックを送信します。 URL ベースのルーティングの一般的な用途は、さまざまな種類のコンテンツに対する要求をさまざまなバックエンド サーバー プールに負荷分散することです。

URL ベースのルーティングでは、新しいルールの種類が Application Gateway に導入されています。 Application Gateway には、basic と PathBasedRouting という 2 つのルールの種類があります。 basic は、バックエンド プールに対してラウンド ロビン サービスを提供します。一方、PathBasedRouting はラウンド ロビン サービスに加えて、バックエンド プールを選択する際に要求 URL のパス パターンも考慮に入れます。

## <a name="scenario"></a>シナリオ

次の例で、Application Gateway は、2 つのバックエンド サーバー プール (既定のサーバー プールとイメージ サーバー プール) を使用して contoso.com のトラフィックを処理します。

http://contoso.com/image* への要求は、イメージ サーバー プール (imagesBackendPool) にルーティングされます。パス パターンが一致しない場合は、既定のサーバー プール (appGatewayBackendPool) が選択されます。

![url ルート](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="log-in-to-azure"></a>Azure へのログイン

**Microsoft Azure コマンド プロンプト**を開き、ログインします。 

```azurecli
az login -u "username"
```

> [!NOTE]
> aka.ms/devicelogin でのコード入力が必要なデバイス ログインのスイッチを用いずに、`az login` を使用することもできます。

上記の例に従って入力すると、コードが表示されます。 ブラウザーで https://aka.ms/devicelogin に移動して、ログイン プロセスを続行します。

![cmd showing device login][1]

ブラウザーで、受け取ったコードを入力します。 サインイン ページにリダイレクトされます。

![browser to enter code][2]

コードを入力してサインインした後、ブラウザーを閉じてこのシナリオに基づいて操作を続行します。

![successfully signed in][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>パスベース ルールを既存のアプリケーション ゲートウェイに追加する

パス ルールが定義されたアプリケーション ゲートウェイを作成します

### <a name="create-a-new-back-end-pool"></a>新しいバックエンド プールを作成する

バックエンド プールでネットワーク トラフィックを負荷分散するように、アプリケーション ゲートウェイの設定 **imagesBackendPool** を構成します。 この例では、新しいバックエンド プールに対して別のバックエンド プール設定を構成します。 各バックエンド プールは、独自のバックエンド プール設定を持つことができます。  バックエンド HTTP 設定は、適切なバックエンド プール メンバーにトラフィックをルーティングするための規則で使用されます。 これは、バックエンド プール メンバーにトラフィックを送信する際に使用されるプロトコルとポートを決定します。 Cookie ベースのセッションを使用するかどうかも、この設定で決定します。  有効にした場合、Cookie ベースのセッション アフィニティは、各パケットに対する以前の要求と同じバックエンドにトラフィックを送信します。

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port"></a>新しいフロントエンド ポートを作成する

Application Gateway のフロント エンド ポートを構成します。 フロントエンド ポート構成オブジェクトはリスナーで使用されます。これを使用して、アプリケーション ゲートウェイのリスナーがトラフィックをリッスンするポートを定義します。

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>新しいリスナーを作成する

リスナーを構成します。 この手順では、着信ネットワーク トラフィックを受信するために使用するポートとパブリック IP アドレスについて、リスナーを構成します。 次の例では、先ほど構成したフロントエンド IP 構成、フロントエンド ポート構成、プロトコル (http または https) を使用して、リスナーを構成します。 この例のリスナーは、先ほど作成したパブリック IP アドレスを使ってポート 82 で HTTP トラフィックをリッスンします。

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>URL パス マップを作成する

バックエンド プールの URL ルール パスを構成します。 この手順では、Application Gateway が使用する相対パスを構成します。これにより、URL パスと、着信トラフィックを処理するために割り当てられるバックエンド プールとの間のマッピングが定義されます。

> [!IMPORTANT]
> 各パスは "/" で始まる必要があり、"\*" を配置できるのは末尾だけです。 有効な例としては、/xyz、/xyz*、または /xyz/* があります。 パス照合に提供する文字列では、最初の "?" または "#" の後にテキストを含めません (これらの文字は許可されません)。 

次の例では、"/images/*" パスに対し、トラフィックを "imagesBackendPool" バックエンドにルーティングする 1 つのルールを作成します。 このルールにより、URL の各セットのトラフィックがバックエンドに確実にルーティングされます。 たとえば、http://adatum.com/images/figure1.jpg は "imagesBackendPool" にルーティングされます。 パスがあらかじめ定義されているパス ルールのいずれとも一致しない場合、ルール パス マップ構成では、既定のバックエンド アドレス プールも構成します。 たとえば、http://adatum.com/shoppingcart/test.html は pool1 にルーティングされます。このプールが、不一致のトラフィックの既定のプールとして定義されているためです。

```azurecli-interactive
az network application-gateway url-path-map create \
--gateway-name AdatumAppGateway \
--name imagespathmap \
--paths /images/* \
--resource-group myresourcegroup2 \
--address-pool imagesBackendPool \
--default-address-pool appGatewayBackendPool \
--default-http-settings appGatewayBackendHttpSettings \
--http-settings appGatewayBackendHttpSettings \
--rule-name images
```

## <a name="next-steps"></a>次のステップ

Secure Sockets Layer (SSL) オフロードの詳細については、[SSL オフロード用のアプリケーション ゲートウェイの構成](application-gateway-ssl-cli.md)に関するページを参照してください。


[scenario]: ./media/application-gateway-create-url-route-cli/scenario.png
[1]: ./media/application-gateway-create-url-route-cli/figure1.png
[2]: ./media/application-gateway-create-url-route-cli/figure2.png
[3]: ./media/application-gateway-create-url-route-cli/figure3.png

