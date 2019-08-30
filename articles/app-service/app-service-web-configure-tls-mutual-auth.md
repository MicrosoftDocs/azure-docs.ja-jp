---
title: TLS 相互認証の構成 - Azure App Service
description: TLS でクライアント証明書認証を使用するようにアプリを構成する方法について説明します。
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/22/2019
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: c4e97a96687e5fa1d934ab8c0317b52cb753f72c
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/28/2019
ms.locfileid: "70088166"
---
# <a name="configure-tls-mutual-authentication-for-azure-app-service"></a>Azure App Service に対する TLS 相互認証の構成

さまざまな種類の認証を有効にすることで、Azure App Service アプリへのアクセスを制限できます。 その方法の 1 つとして、クライアント要求が TLS/SSL を経由するときにクライアント証明書を要求し、その証明書を検証することが挙げられます。 このメカニズムは TLS 相互認証またはクライアント証明書認証と呼ばれます。 この記事では、クライアント証明書認証を使用するようにアプリを設定する方法について説明します。

> [!NOTE]
> HTTPS ではなく HTTP 経由でサイトにアクセスする場合は、クライアント証明書を受信しません。 したがって、アプリケーションにクライアント証明書が必要な場合は、HTTP 経由でのアプリケーションへの要求を許可しないでください。
>

## <a name="enable-client-certificates"></a>クライアント証明書を有効にする

クライアント証明書を要求するようにアプリを設定するには、アプリの `clientCertEnabled` 設定を `true` に設定する必要があります。 この設定を行うには、[Cloud Shell](https://shell.azure.com) で次のコマンドを実行します。

```azurecli-interactive
az webapp update --set clientCertEnabled=true --name <app_name> --resource-group <group_name>
```

## <a name="access-client-certificate"></a>クライアント証明書にアクセスする

App Service では、要求の SSL 終了がフロントエンドのロード バランサー側で行われます。 [クライアント証明書を有効にした](#enable-client-certificates)状態で要求をアプリ コードに転送すると、App Service によって `X-ARR-ClientCert` 要求ヘッダーにクライアント証明書が挿入されます。 App Service がこのクライアント証明書に対して行うのは、この証明書をアプリに転送する処理だけです。 クライアント証明書の検証はアプリ コードが行います。

ASP.NET の場合は、**HttpRequest.ClientCertificate** プロパティを通じてクライアント証明書を使用できます。

他のアプリケーション スタック (Node.js や PHP など) の場合は、`X-ARR-ClientCert` 要求ヘッダー内の base64 エンコード値を通じて、アプリでクライアント証明書を使用できます。

## <a name="aspnet-sample"></a>ASP.NET のサンプル

```csharp
    using System;
    using System.Collections.Specialized;
    using System.Security.Cryptography.X509Certificates;
    using System.Web;

    namespace ClientCertificateUsageSample
    {
        public partial class cert : System.Web.UI.Page
        {
            public string certHeader = "";
            public string errorString = "";
            private X509Certificate2 certificate = null;
            public string certThumbprint = "";
            public string certSubject = "";
            public string certIssuer = "";
            public string certSignatureAlg = "";
            public string certIssueDate = "";
            public string certExpiryDate = "";
            public bool isValidCert = false;

            //
            // Read the certificate from the header into an X509Certificate2 object
            // Display properties of the certificate on the page
            //
            protected void Page_Load(object sender, EventArgs e)
            {
                NameValueCollection headers = base.Request.Headers;
                certHeader = headers["X-ARR-ClientCert"];
                if (!String.IsNullOrEmpty(certHeader))
                {
                    try
                    {
                        byte[] clientCertBytes = Convert.FromBase64String(certHeader);
                        certificate = new X509Certificate2(clientCertBytes);
                        certSubject = certificate.Subject;
                        certIssuer = certificate.Issuer;
                        certThumbprint = certificate.Thumbprint;
                        certSignatureAlg = certificate.SignatureAlgorithm.FriendlyName;
                        certIssueDate = certificate.NotBefore.ToShortDateString() + " " + certificate.NotBefore.ToShortTimeString();
                        certExpiryDate = certificate.NotAfter.ToShortDateString() + " " + certificate.NotAfter.ToShortTimeString();
                    }
                    catch (Exception ex)
                    {
                        errorString = ex.ToString();
                    }
                    finally 
                    {
                        isValidCert = IsValidClientCertificate();
                        if (!isValidCert) Response.StatusCode = 403;
                        else Response.StatusCode = 200;
                    }
                }
                else
                {
                    certHeader = "";
                }
            }

            //
            // This is a SAMPLE verification routine. Depending on your application logic and security requirements, 
            // you should modify this method
            //
            private bool IsValidClientCertificate()
            {
                // In this example we will only accept the certificate as a valid certificate if all the conditions below are met:
                // 1. The certificate is not expired and is active for the current time on server.
                // 2. The subject name of the certificate has the common name nildevecc
                // 3. The issuer name of the certificate has the common name nildevecc and organization name Microsoft Corp
                // 4. The thumbprint of the certificate is 30757A2E831977D8BD9C8496E4C99AB26CB9622B
                //
                // This example does NOT test that this certificate is chained to a Trusted Root Authority (or revoked) on the server 
                // and it allows for self signed certificates
                //

                if (certificate == null || !String.IsNullOrEmpty(errorString)) return false;

                // 1. Check time validity of certificate
                if (DateTime.Compare(DateTime.Now, certificate.NotBefore) < 0 || DateTime.Compare(DateTime.Now, certificate.NotAfter) > 0) return false;

                // 2. Check subject name of certificate
                bool foundSubject = false;
                string[] certSubjectData = certificate.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certSubjectData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundSubject = true;
                        break;
                    }
                }
                if (!foundSubject) return false;

                // 3. Check issuer name of certificate
                bool foundIssuerCN = false, foundIssuerO = false;
                string[] certIssuerData = certificate.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
                foreach (string s in certIssuerData)
                {
                    if (String.Compare(s.Trim(), "CN=nildevecc") == 0)
                    {
                        foundIssuerCN = true;
                        if (foundIssuerO) break;
                    }

                    if (String.Compare(s.Trim(), "O=Microsoft Corp") == 0)
                    {
                        foundIssuerO = true;
                        if (foundIssuerCN) break;
                    }
                }

                if (!foundIssuerCN || !foundIssuerO) return false;

                // 4. Check thumprint of certificate
                if (String.Compare(certificate.Thumbprint.Trim().ToUpper(), "30757A2E831977D8BD9C8496E4C99AB26CB9622B") != 0) return false;

                return true;
            }
        }
    }
```

## <a name="nodejs-sample"></a>Node.js のサンプル

次の Node.js サンプル コードは、`X-ARR-ClientCert` ヘッダーを取得し、[node-forge](https://github.com/digitalbazaar/forge) を使用して base64 エンコード PEM 文字列を証明書オブジェクトに変換して検証します。

```javascript
import { NextFunction, Request, Response } from 'express';
import { pki, md, asn1 } from 'node-forge';

export class AuthorizationHandler {
    public static authorizeClientCertificate(req: Request, res: Response, next: NextFunction): void {
        try {
            // Get header
            const header = req.get('X-ARR-ClientCert');
            if (!header) throw new Error('UNAUTHORIZED');

            // Convert from PEM to pki.CERT
            const pem = `-----BEGIN CERTIFICATE-----${header}-----END CERTIFICATE-----`;
            const incomingCert: pki.Certificate = pki.certificateFromPem(pem);

            // Validate certificate thumbprint
            const fingerPrint = md.sha1.create().update(asn1.toDer(pki.certificateToAsn1(incomingCert)).getBytes()).digest().toHex();
            if (fingerPrint.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate time validity
            const currentDate = new Date();
            if (currentDate < incomingCert.validity.notBefore || currentDate > incomingCert.validity.notAfter) throw new Error('UNAUTHORIZED');

            // Validate issuer
            if (incomingCert.issuer.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            // Validate subject
            if (incomingCert.subject.hash.toLowerCase() !== 'abcdef1234567890abcdef1234567890abcdef12') throw new Error('UNAUTHORIZED');

            next();
        } catch (e) {
            if (e instanceof Error && e.message === 'UNAUTHORIZED') {
                res.status(401).send();
            } else {
                next(e);
            }
        }
    }
}
```