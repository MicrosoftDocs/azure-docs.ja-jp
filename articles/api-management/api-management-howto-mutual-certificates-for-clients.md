---
title: "API Management でのクライアント証明書認証を使用した API の保護 - Azure API Management | Microsoft Docs"
description: "クライアント証明書を使用して API へのアクセスを保護する方法の詳細"
services: api-management
documentationcenter: 
author: miaojiang
manager: erikre
editor: 
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/01/2017
ms.author: apimpm
translationtype: Human Translation
ms.sourcegitcommit: 06f274fe3febd4c3d6d3da90b361c3137ec795b9
ms.openlocfilehash: e6514465db0d01b248bdb9e5113450e2bd3d2346
ms.lasthandoff: 02/23/2017

---

# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management でクライアント証明書認証を使用して API を保護する方法

API Management には、クライアント証明書を使用して API (つまりクライアントから API Management) へのアクセスを保護する機能が備わっています。 現時点では、クライアント証明書の拇印が目的の値であるかを確認できます。 API Management にアップロードした既存の証明書に対する拇印を確認することもできます。  

クライアント証明書を使用して API のバックエンド サービスへのアクセス (つまり API Management からバックエンド) を保護する方法については、[クライアント証明書認証を使用して、バックエンド サービスを保護する方法](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)に関するページを参照してください。

## <a name="checking-a-thumbprint-against-a-desired-value"></a>目的の値に対する拇印の確認

以下のポリシーを構成して、クライアント証明書の拇印を確認できます。

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Request.Certificate.Thumbprint != "desired-thumbprint-to-validate")" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="checking-a-thumbprint-against-certificates-uploaded-to-api-management"></a>API Management にアップロードされた証明書に対する拇印の確認

以下の例では、API Management にアップロードされた証明書に対して、クライアント証明書の拇印を確認する方法を示しています。 

```
<choose>
    <when condition="@(context.Request.Certificate == null || context.Deployment.Certificates.Any(c => c.Value.Thumbprint == context.Request.Certificate.Thumbprint))" >
        <return-response>
            <set-status code="401" reason="Invalid client certificate" />
        </return-response>
    </when>
</choose>

```

## <a name="next-step"></a>次のステップ

*  [クライアント証明書認証を使用してバックエンド サービスを保護する方法](https://docs.microsoft.com/en-us/azure/api-management/api-management-howto-mutual-certificates)
*  [証明書のアップロード方法](https://docs.microsoft.com/azure/api-management/api-management-howto-mutual-certificates#a-namestep1-aupload-a-client-certificate)


