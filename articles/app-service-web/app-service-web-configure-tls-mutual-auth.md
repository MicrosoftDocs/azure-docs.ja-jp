---
title: "Web アプリの TLS 相互認証を構成する方法"
description: "Web アプリを TLS でクライアント証明書認証を使用するように構成する方法について説明します。"
services: app-service
documentationcenter: 
author: naziml
manager: erikre
editor: jimbe
ms.assetid: cd1d15d3-2d9e-4502-9f11-a306dac4453a
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2016
ms.author: naziml
translationtype: Human Translation
ms.sourcegitcommit: 6d749e5182fbab04adc32521303095dab199d129
ms.openlocfilehash: db69852cffd1ff331ac4a640b04ea4360d00bf75
ms.lasthandoff: 03/22/2017


---
# <a name="how-to-configure-tls-mutual-authentication-for-web-app"></a>Web アプリの TLS 相互認証を構成する方法
## <a name="overview"></a>概要
さまざまな種類の認証を有効にすることで、Azure Web アプリへのアクセスを制限できます。 これを行う 1 つの方法は、要求が TLS と SSL を経由するときに、クライアント証明書を使用して認証することです。 このメカニズムは、TLS 相互認証またはクライアント証明書認証と呼ばれます。この記事では、Web アプリをクライアント証明書認証を使用するように設定する方法について詳しく説明します。

> **注:** HTTPS ではなく HTTP 経由でサイトにアクセスする場合は、クライアント証明書を受信しません。 したがって、アプリケーションにクライアント証明書が必要な場合は、HTTP 経由でのアプリケーションへの要求を許可しないでください。
> 
> 

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="configure-web-app-for-client-certificate-authentication"></a>Web アプリのクライアント証明書認証を構成する
Web アプリを、クライアント証明書を要求するように設定するには、Web アプリ用の clientCertEnabled サイト設定を追加し、true に設定する必要があります。 現時点では、この設定は、ポータルでの管理エクスペリエンスを通して実行することはできません。これを実現するには REST API を使用する必要があります。

[ARMClient ツール](https://github.com/projectkudu/ARMClient) を使用して、REST API 呼び出しを簡単に作成できます。 このツールを使用してログインした後、次のコマンドを発行する必要があります。

    ARMClient PUT subscriptions/{Subscription Id}/resourcegroups/{Resource Group Name}/providers/Microsoft.Web/sites/{Website Name}?api-version=2015-04-01 @enableclientcert.json -verbose

{} 内のすべての Web アプリの情報に置き換え、enableclientcert.json という名前の次の JSON コンテンツを持つファイルを作成します。

    {
        "location": "My Web App Location",
        "properties": {
            "clientCertEnabled": true
        }
    }

"location" の値を、Web アプリが配置される場所に変更します。例: 米国中北部、米国西部など。

https://resources.azure.com を使用して、`clientCertEnabled` プロパティを `true` に切り替えることもできます。

> **注:** PowerShell から ARMClient を実行する場合、JSON ファイルの @ 記号をアクサン グラーブ (`) でエスケープする必要があります。
> 
> 

## <a name="accessing-the-client-certificate-from-your-web-app"></a>Web アプリからクライアント証明書にアクセスする
ASP.NET を使用し、クライアント証明書認証を使用するようにアプリを構成する場合、証明書は **HttpRequest.ClientCertificate** プロパティを通じて利用可能になります。 他のアプリケーション スタックの場合は、"X-ARR-ClientCert" 要求ヘッダー内の base64 エンコード値を通して、アプリでクライアント証明書を使用できます。 アプリケーションは、この値から証明書を作成した後、アプリケーションの認証と承認の目的でそれを使用できます。

## <a name="special-considerations-for-certificate-validation"></a>証明書の検証に関する特別な考慮事項
アプリケーションに送信されるクライアント証明書は、Azure Web Apps プラットフォームによる検証を受けません。 この証明書の検証は、Web アプリが実行する必要があります。 認証するために証明書のプロパティを検証するサンプル ASP.NET コードを次に示します。

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

                // If you also want to test if the certificate chains to a Trusted Root Authority you can uncomment the code below
                //
                //X509Chain certChain = new X509Chain();
                //certChain.Build(certificate);
                //bool isValidCertChain = true;
                //foreach (X509ChainElement chElement in certChain.ChainElements)
                //{
                //    if (!chElement.Certificate.Verify())
                //    {
                //        isValidCertChain = false;
                //        break;
                //    }
                //}
                //if (!isValidCertChain) return false;

                return true;
            }
        }
    }

