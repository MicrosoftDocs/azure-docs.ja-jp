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
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
ms.openlocfilehash: 450ebc621758363c5ea9ab6d631cd6c7df38794b
ms.sourcegitcommit: f8c592ebaad4a5fc45710dadc0e5c4480d122d6f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2019
ms.locfileid: "58619724"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management でクライアント証明書認証を使用して API を保護する方法

API Management には、クライアント証明書を使用して API (つまりクライアントから API Management) へのアクセスを保護する機能が備わっています。 現時点では、クライアント証明書の拇印が目的の値であるかを確認できます。 API Management にアップロードした既存の証明書に対する拇印を確認することもできます。  

クライアント証明書を使用して API のバックエンド サービスへのアクセス (つまり API Management からバックエンド) を保護する方法については、[クライアント証明書認証を使用して、バックエンド サービスを保護する方法](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)に関するページを参照してください。

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="checking-the-expiration-date"></a>有効期限の確認

以下のポリシーを構成して、証明書が有効期限切れかどうかを確認することができます。

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.NotAfter < DateTime.Now)" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-issuer-and-subject"></a>発行者とサブジェクトの確認

以下のポリシーを構成して、クライアント証明書の発行者とサブジェクトを確認できます。

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Issuer != "trusted-issuer" || context.Request.Certificate.SubjectName.Name != "expected-subject-name")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-the-thumbprint"></a>拇印の確認

以下のポリシーを構成して、クライアント証明書の拇印を確認できます。

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint")" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>
```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management にアップロードされた証明書に対する拇印の確認

以下の例では、API Management にアップロードされた証明書に対して、クライアント証明書の拇印を確認する方法を示しています。 

```xml
<choose>
    <when condition="@(context.Request.Certificate == null || !context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="403" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>次のステップ

*  [クライアント証明書認証を使用してバックエンド サービスを保護する方法](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)
*  [証明書のアップロード方法](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates)

