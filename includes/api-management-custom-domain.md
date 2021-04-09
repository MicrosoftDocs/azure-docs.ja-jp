---
author: vladvino
ms.service: api-management
ms.topic: include
ms.date: 11/09/2018
ms.author: vlvinogr
ms.openlocfilehash: 1858317d40efa59b188ce894534be93a1f11b287
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96028106"
---
## <a name="how-apim-proxy-server-responds-with-ssl-certificates-in-the-tls-handshake"></a>APIM プロキシ サーバーが SSL 証明書を使用して TLS ハンドシェイクで応答する方法

### <a name="clients-calling-with-sni-header"></a>SNI ヘッダーを使用して呼び出すクライアント
プロキシ用に 1 つ以上のカスタム ドメインが設定されている場合、APIM は、既定のドメイン (apim-service-name.azure-api.net など) だけでなく、そのカスタム ドメイン (contoso.com など) からの HTTPS 要求にも応答できます。 APIM は、Server Name Indication (SNI) ヘッダーの情報に基づいて、適切なサーバー証明書を使用して応答します。

### <a name="clients-calling-without-sni-header"></a>SNI ヘッダーを使用せずに呼び出すクライアント
[SNI](https://tools.ietf.org/html/rfc6066#section-3) ヘッダーを送信しないクライアントを使用している場合、APIM は次のロジックに基づいて応答を作成します。

* このサービスにプロキシ用のカスタム ドメインが 1 つだけ設定されている場合、そのプロキシ カスタム ドメインに発行された証明書が既定の証明書になります。
* このサービスにプロキシ用のカスタム ドメインが複数設定されている場合 (**Developer** および **Premium** レベルでサポート)、お客様は既定の証明書を指定できます。 既定の証明書を設定するには、[defaultSslBinding](/rest/api/apimanagement/2019-12-01/apimanagementservice/createorupdate#hostnameconfiguration) プロパティを true に設定する ("defaultSslBinding":"true") 必要があります。 お客様がこのプロパティを設定していない場合、既定の証明書は、*.azure-api.net でホストされる既定のプロキシ ドメインに発行された証明書になります。

## <a name="support-for-putpost-request-with-large-payload"></a>ペイロードの大きい PUT/POST 要求のサポート

HTTPS でクライアント側の証明書を使用する場合、APIM プロキシ サーバーでペイロードの大きな要求 (40 KB を超えるペイロードなど) がサポートされます。 サーバーの要求がフリーズしないように、プロキシ ホスト名に ["negotiateClientCertificate": "true"](/rest/api/apimanagement/2019-12-01/ApiManagementService/CreateOrUpdate#hostnameconfiguration) プロパティを設定できます。 このプロパティが true に設定されている場合、HTTP 要求の交換前の SSL/TLS 接続時に、クライアント証明書が要求されます。 この設定は **プロキシ ホスト名** レベルで適用されるため、すべての接続要求でクライアント証明書が求められます。 プロキシ用のカスタム ドメインを最大 20 個設定して (**Premium** レベルでのみサポート)、この制限を回避できます。