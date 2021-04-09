---
title: API Management でクライアント証明書認証を使用して API を保護する
titleSuffix: Azure API Management
description: クライアント証明書を使用して API へのアクセスを保護する方法について説明します。 ポリシー式を使用して、受信証明書を検証できます。
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: apimpm
ms.openlocfilehash: 553b4527796db3e5d0f430afd6c5e614626187e5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "99988887"
---
# <a name="how-to-secure-apis-using-client-certificate-authentication-in-api-management"></a>API Management でクライアント証明書認証を使用して API を保護する方法

API Management には、クライアント証明書を使用して API (つまりクライアントから API Management) へのアクセスを保護する機能が備わっています。 受信証明書を検証し、ポリシー式を使用してその証明書のプロパティを目的の値に対して確認することができます。

クライアント証明書を使用して API のバックエンド サービスへの (つまり、API Management からバックエンドへの) アクセスをセキュリティで保護する方法については、[クライアント証明書認証を使用してバックエンド サービスをセキュリティで保護する方法](./api-management-howto-mutual-certificates.md)に関するページを参照してください。

> [!IMPORTANT]
> Developer、Basic、Standard、または Premium レベルで HTTP/2 経由でクライアント証明書を受信して確認するには、次に示すように、[カスタム ドメイン] ブレードで [クライアント証明書のネゴシエート] 設定を有効にする必要があります。

![[Negotiate client certificate] (クライアント証明書をネゴシエートする)](./media/api-management-howto-mutual-certificates-for-clients/negotiate-client-certificate.png)

> [!IMPORTANT]
> Consumption レベルでクライアント証明書を受信して確認するには、下に示すように、[カスタム ドメイン] ブレードにある [Request client certificate] (クライアント証明書を要求する) の設定を有効にする必要があります。

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
    <when condition="@(context.Request.Certificate == null || !context.Request.Certificate.Verify() || context.Request.Certificate.Thumbprint != "DESIRED-THUMBPRINT-IN-UPPER-CASE")" >
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
> この問題が発生しないようにするには、このドキュメントの最初の画像で示すように、[カスタム ドメイン] ブレードにある目的のホスト名の [クライアント証明書のネゴシエート] の設定を有効にします。 この機能は、Consumption レベルでは使用できません。

## <a name="certificate-validation-in-self-hosted-gateway"></a>セルフホステッド ゲートウェイでの証明書の検証

既定の API Management [セルフホステッド ゲートウェイ](self-hosted-gateway-overview.md) イメージでは、API Management インスタンスにアップロードされた [CA ルート証明書](api-management-howto-ca-certificates.md)を使用したサーバーおよびクライアント証明書の検証をサポートしていません。 カスタム証明書をセルフホステッド ゲートウェイに提示するクライアントでは、ゲートウェイでの証明書失効リスト (CRL) 検証に時間がかかる場合があるため、応答が遅くなることがあります。 

ゲートウェイを実行する際の回避策として、API Management インスタンスの代わりに localhost アドレス (127.0.0.1) を指すように PKI IP アドレスを構成することができます。 これにより、ゲートウェイがクライアント証明書を検証しようとしたときに CRL 検証が直ちに失敗します。 ゲートウェイを構成するには、コンテナーの `/etc/hosts` ファイルで localhost に解決するように API Management インスタンスの DNS エントリを追加します。 ゲートウェイのデプロイ中に、このエントリを追加できます。
 
* Docker デプロイの場合 - `--add-host <hostname>:127.0.0.1` パラメーターを `docker run` コマンドに追加します。 詳細については、[コンテナーの hosts ファイルにエントリを追加する方法](https://docs.docker.com/engine/reference/commandline/run/#add-entries-to-container-hosts-file---add-host)に関するページを参照してください。
 
* Kubernetes デプロイの場合 - `hostAliases` 指定を `myGateway.yaml` 構成ファイルに追加します。 詳細については、[ホスト エイリアスを使用してポッドの /etc/hosts にエントリを追加する方法](https://kubernetes.io/docs/concepts/services-networking/add-entries-to-pod-etc-hosts-with-host-aliases/)に関するページを参照してください。




## <a name="next-steps"></a>次の手順

-   [クライアント証明書認証を使用してバックエンド サービスを保護する方法](./api-management-howto-mutual-certificates.md)
-   [証明書のアップロード方法](./api-management-howto-mutual-certificates.md)
