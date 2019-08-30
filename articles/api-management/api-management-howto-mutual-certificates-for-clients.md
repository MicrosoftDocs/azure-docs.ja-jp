---
title: API Management でのクライアント証明書認証を使用した API の保護 - Azure API Management | Microsoft Docs
description: クライアント証明書を使用して API へのアクセスを保護する方法の詳細
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 05/30/2019
ms.author: apimpm
ms.openlocfilehash: 263f8495b9dbb0a1c5b3c54301b4b4deab425e31
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70072364"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management でクライアント証明書認証を使用して API を保護する方法

API Management には、クライアント証明書を使用して API (つまりクライアントから API Management) へのアクセスを保護する機能が備わっています。 受信証明書を検証し、ポリシー式を使用してその証明書のプロパティを目的の値に対して確認することができます。

クライアント証明書を使用して API のバックエンド サービスへの (つまり、API Management からバックエンドへの) アクセスをセキュリティで保護する方法については、[クライアント証明書認証を使用してバックエンド サービスをセキュリティで保護する方法](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)に関するページを参照してください。

> [!IMPORTANT]
> Consumption レベルでクライアント証明書を受信して確認するには、下に示すように、まず [カスタム ドメイン] ブレードにある [Request client certificate] (クライアント証明書を要求する) の設定を有効にする必要があります。

![[Request client certificate] (クライアント証明書を要求する)](./media/api-management-howto-mutual-certificates-for-clients/request-client-certificate.png)

## <a name="checking-the-issuer-and-subject"></a>発行者とサブジェクトの確認

以下のポリシーを構成して、クライアント証明書の発行者とサブジェクトを確認できます。

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 証明書失効リストの確認を無効にするには、`context.Request.Certificate.Verify()` の代わりに `context.Request.Certificate.VerifyNoRevocation()` を使用します。
> クライアント証明書が自己署名済みである場合、`context.Request.Certificate.Verify()` と `context.Request.Certificate.VerifyNoRevocation()` が機能するには、ルート (または中間) の CA 証明書を API Management に[アップロード](api-management-howto-ca-certificates.md)する必要があります。

## <a name="checking-the-thumbprint"></a>拇印の確認

以下のポリシーを構成して、クライアント証明書の拇印を確認できます。

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

> [!NOTE]
> 証明書失効リストの確認を無効にするには、`context.Request.Certificate.Verify()` の代わりに `context.Request.Certificate.VerifyNoRevocation()` を使用します。
> クライアント証明書が自己署名済みである場合、`context.Request.Certificate.Verify()` と `context.Request.Certificate.VerifyNoRevocation()` が機能するには、ルート (または中間) の CA 証明書を API Management に[アップロード](api-management-howto-ca-certificates.md)する必要があります。

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management にアップロードされた証明書に対する拇印の確認

以下の例では、API Management にアップロードされた証明書に対して、クライアント証明書の拇印を確認する方法を示しています。

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify()  || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

> [!NOTE]
> 証明書失効リストの確認を無効にするには、`context.Request.Certificate.Verify()` の代わりに `context.Request.Certificate.VerifyNoRevocation()` を使用します。
> クライアント証明書が自己署名済みである場合、`context.Request.Certificate.Verify()` と `context.Request.Certificate.VerifyNoRevocation()` が機能するには、ルート (または中間) の CA 証明書を API Management に[アップロード](api-management-howto-ca-certificates.md)する必要があります。

> [!TIP]
> この[記事](https://techcommunity.microsoft.com/t5/Networking-Blog/HTTPS-Client-Certificate-Request-freezes-when-the-Server-is/ba-p/339672)で説明されているクライアント証明書のデッドロックに関する問題が、要求が凍結する、要求がタイムアウト後に `403 Forbidden` 状態コードになる、`context.Request.Certificate` が `null` である、などのいくつかの形で現れる場合があります。 この問題は通常、コンテンツの長さが約 60KB 以上ある `POST` および `PUT` 要求に影響を与えます。
> この問題が発生しないようにするには、下に示すように、[カスタム ドメイン] ブレードにある目的のホスト名の [Negotiate client certificate] (クライアント証明書をネゴシエートする) の設定を有効にします。 この機能は、Consumption レベルでは使用できません。

![[Negotiate client certificate] (クライアント証明書をネゴシエートする)](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

## <a name="next-steps"></a>次の手順

-   [クライアント証明書認証を使用してバックエンド サービスを保護する方法](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
-   [証明書のアップロード方法](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
