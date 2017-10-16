---
title: "URL ルーティング ルールを使用してアプリケーション ゲートウェイを作成する - Azure CLI 2.0 | Microsoft Docs"
description: "このページでは、URL ルーティング ルールを使用してアプリケーション ゲートウェイを作成し、構成する方法について説明します。"
documentationcenter: na
services: application-gateway
author: davidmu1
manager: timlt
editor: tysonn
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/26/2017
ms.author: davidmu
ms.openlocfilehash: 10d01d5d80e2d111d6b39598eed3612f80162b23
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-by-using-path-based-routing-with-azure-cli-20"></a>Azure CLI 2.0 でパスベースのルーティングを使用してアプリケーション ゲートウェイを作成する

> [!div class="op_single_selector"]
> * [Azure ポータル](application-gateway-create-url-route-portal.md)
> * [Azure Resource Manager の PowerShell](application-gateway-create-url-route-arm-ps.md)
> * [Azure CLI 2.0](application-gateway-create-url-route-cli.md)

URL パスベースのルーティングを使用すると、HTTP 要求の URL パスに基づいてルートを関連付けできます。 アプリケーション ゲートウェイに記載されている URL に対して構成されたバックエンド サーバー プールへのルートがあるかどうかを調べて、定義されたプールにネットワーク トラフィックを送信します。 URL パスベースのルーティングの一般的な用途は、さまざまな種類のコンテンツに対する要求をさまざまなバックエンド サーバー プールに負荷分散することです。

Azure Application Gateway には、基本ルールと URL パスベース ルールという 2 種類のルールがあります。 基本ルールの種類は、バックエンド プールにラウンド ロビン サービスを提供します。 パスベースのルールでは、適切なバックエンド プールを選択する際に、ラウンド ロビンによる分散だけでなく要求 URL のパス パターンも使用します。

## <a name="scenario"></a>シナリオ

次の例では、アプリケーション ゲートウェイは、2 つのバックエンド サーバー プール (既定のサーバー プールとイメージ サーバー プール) を使用して contoso.com のトラフィックを処理します。

http://contoso.com/image* に対する要求は、イメージ サーバー プール (**imagesBackendPool**) にルーティングされます。 パス パターンが一致しない場合、アプリケーション ゲートウェイは既定のサーバー プール (**appGatewayBackendPool**) を選択します。

![URL ルート](./media/application-gateway-create-url-route-cli/scenario.png)

## <a name="sign-in-to-azure"></a>Azure へのサインイン

**Microsoft Azure コマンド プロンプト**を開き、サインインします。

```azurecli
az login -u "username"
```

> [!NOTE]
> aka.ms/devicelogin でのコード入力が必要なデバイス ログインのスイッチを用いずに、`az login` を使用することもできます。

上記のコマンドを入力すると、コードが表示されます。 ブラウザーで https://aka.ms/devicelogin に移動して、サインイン プロセスを続行します。

![デバイスのログインを表示するコマンド プロンプト][1]

ブラウザーで、受け取ったコードを入力します。 これにより、サインイン ページにリダイレクトされます。

![ブラウザーにコードを入力][2]

コードを入力してサインインし、ブラウザーを閉じて続行します。

![正常にサインイン][3]

## <a name="add-a-path-based-rule-to-an-existing-application-gateway"></a>パスベース ルールを既存のアプリケーション ゲートウェイに追加する

次の手順では、パスベース ルールを既存のアプリケーション ゲートウェイに追加する方法を説明します。
### <a name="create-a-new-back-end-pool"></a>新しいバックエンド プールを作成する

バックエンド プールでネットワーク トラフィックを負荷分散するように、アプリケーション ゲートウェイの設定 **imagesBackendPool** を構成します。 この例では、新しいバックエンド プールに対して別のバックエンド プール設定を構成します。 各バックエンド プールは、独自の設定にすることができます。 パスベース ルールでは、適切なバックエンド プール メンバーにトラフィックをルーティングするために、バックエンド HTTP 設定を使用します。 これによって、バックエンド プール メンバーにトラフィックを送信する際に使用されるプロトコルとポートが決定されます。 バックエンド HTTP 設定は、Cookie ベースのセッションを使用するかどうかも決定します。  有効にした場合、Cookie ベースのセッション アフィニティは、各パケットに対する以前の要求と同じバックエンドにトラフィックを送信します。

```azurecli-interactive
az network application-gateway address-pool create \
--gateway-name AdatumAppGateway \
--name imagesBackendPool  \
--resource-group myresourcegroup \
--servers 10.0.0.6 10.0.0.7
```

### <a name="create-a-new-front-end-port-for-an-application-gateway"></a>アプリケーション ゲートウェイに新しいフロントエンド ポートを作成する

フロントエンド ポート構成オブジェクトはリスナーで使用されます。これを使用して、アプリケーション ゲートウェイのリスナーがトラフィックをリッスンするポートを定義します。

```azurecli-interactive
az network application-gateway frontend-port create --port 82 --gateway-name AdatumAppGateway --resource-group myresourcegroup --name port82
```

### <a name="create-a-new-listener"></a>新しいリスナーを作成する

この手順では、着信ネットワーク トラフィックを受信するために使用するポートとパブリック IP アドレスについて、リスナーを構成します。 次の例では、先ほど構成したフロントエンド IP 構成、フロントエンド ポート構成、プロトコル (http または https、大文字と小文字は区別されます) を使用して、リスナーを構成します。 この例のリスナーは、このシナリオで先ほど作成したパブリック IP アドレスを使って、ポート 82 で HTTP トラフィックをリッスンします。

```azurecli-interactive
az network application-gateway http-listener create --name imageListener --frontend-ip appGatewayFrontendIP  --frontend-port port82 --resource-group myresourcegroup --gateway-name AdatumAppGateway
```

### <a name="create-the-url-path-map"></a>URL パス マップを作成する

この手順では、アプリケーション ゲートウェイが使用する相対 URL パスを構成します。これにより、パスと着信トラフィックを処理するために割り当てられたバックエンド プールとの間のマッピングが定義されます。

> [!IMPORTANT]
> 各パスは "/" で始まる必要があり、アスタリスクを配置できるのは末尾だけです。 有効な例としては、/xyz、/xyz*、または /xyz/* があります。 パス照合に提供する文字列では、最初の "?" または "#" の後にテキストを含めません (これらの文字は許可されません)。 

次の例では、パス /images/* に対し、トラフィックをバックエンド **imagesBackendPool** にルーティングするルールを作成します。 このルールにより、URL の各セットのトラフィックがバックエンドに確実にルーティングされます。 たとえば、http://adatum.com/images/figure1.jpg は **imagesBackendPool** にルーティングされます。 パスがあらかじめ定義されているパス ルールのいずれとも一致しない場合、ルール パス マップ構成では、既定のバックエンド アドレス プールも構成します。 たとえば、http://adatum.com/shoppingcart/test.html は **pool1** にルーティングされます。このプールが、不一致のトラフィックの既定のプールとして定義されているためです。

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
