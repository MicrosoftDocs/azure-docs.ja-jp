---
title: Azure Active Directory B2C のクライアント証明書を使用して RESTful サービスをセキュリティで保護する | Microsoft Docs
description: クライアント証明書を使用して Azure AD B2C でのカスタム REST API 要求交換をセキュリティで保護する
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 502b769e1a25874bfa5a9e8f082361605a52ccf6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165678"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>クライアント証明書を使用して RESTful サービスをセキュリティで保護する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

関連する記事では、Azure Active Directory B2C (Azure AD B2C) と対話する [RESTful サービスを作成](active-directory-b2c-custom-rest-api-netfw.md)します。

この記事では、クライアント証明書を使用して Azure Web アプリ (RESTful API) へのアクセスを制限する方法について説明します。 このメカニズムは TLS 相互認証または*クライアント証明書認証*と呼ばれ、 適切な証明書を持つサービス (Azure AD B2C など) のみが、RESTful サービスにアクセスできます。

>[!NOTE]
>また、[HTTP 基本認証](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)を使用して、RESTful サービスをセキュリティで保護することもできます。 ただし、HTTP 基本認証は、クライアント証明書より安全性が低いと見なされます。 この記事で説明するように、RESTful サービスは、クライアント証明書認証を使用してセキュリティで保護することをお勧めします。

この記事では、次の方法について詳しく説明します。
* クライアント証明書認証を使用するように Web アプリを設定する。
* 証明書を Azure AD B2C ポリシー キーにアップロードする。
* クライアント証明書を使用するようにカスタム ポリシーを構成する。

## <a name="prerequisites"></a>前提条件
* [REST API 要求交換を統合](active-directory-b2c-custom-rest-api-netfw.md)する記事の手順を完了している。
* 有効な証明書 (秘密キーを備えた .pfx ファイル) がある。

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>手順 1:Web アプリのクライアント証明書認証を構成する
クライアント証明書を要求するように **Azure App Service** を設定するには、Web アプリの `clientCertEnabled` サイト設定を *true* に設定します。 この変更を行うには、Azure portal で Web アプリのページを開きます。 左側のナビゲーションの **[設定]** で **[SSL 設定]** を選択します。 **[クライアント証明書]** セクションで、**[着信クライアント証明書]** オプションをオンにします。

>[!NOTE]
>Azure App Service プランが Standard 以上であることを確認してください。 詳細については、[Azure App Service プランの詳細な概要](https://docs.microsoft.com/azure/app-service/overview-hosting-plans)に関する記事をご覧ください。

>[!NOTE]
>**clientCertEnabled** プロパティを設定する方法の詳細については、[Web アプリの TLS 相互認証を構成する](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth)方法に関する記事をご覧ください。

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>手順 2:証明書を Azure AD B2C ポリシー キーにアップロードする
`clientCertEnabled` を *true* に設定すると、RESTful API との通信にクライアント証明書が求められます。 クライアント証明書を取得し、Azure AD B2C テナントにアップロードして保存するには、次の手順を実行する必要があります。 
1. Azure AD B2C テナントで、**[B2C Settings]\(B2C 設定\)** > **[Identity Experience Framework]** の順に選択します。

2. テナント内で使用できるキーを表示するには、**[ポリシー キー]** を選択します。

3. **[追加]** を選択します。  
    **[キーの作成]** ウィンドウが開きます。

4. **[オプション]** ボックスで、**[アップロード]** を選択します。

5. **[名前]** ボックスに「**B2cRestClientCertificate**」と入力します。  
    プレフィックス *B2C_1A_* が自動的に追加されます。

6. **[ファイルのアップロード]** ボックスで、秘密キーを備えた証明書の .pfx ファイルを選択します。

7. **[パスワード]** ボックスに、証明書のパスワードを入力します。

    ![ポリシー キーのアップロード](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. **作成**を選択します。

8. テナント内で利用できるキーを表示して、作成したキー `B2C_1A_B2cRestClientCertificate` を確認するには、**[ポリシー キー]** を選択します。

## <a name="step-3-change-the-technical-profile"></a>手順 3:技術プロファイルを変更する
カスタム ポリシーでクライアント証明書認証をサポートするには、次の手順を実行して技術プロファイルを変更します。

1. 作業ディレクトリで、*TrustFrameworkExtensions.xml* 拡張ポリシー ファイルを開きます。

2. `Id="REST-API-SignUp"` を含む `<TechnicalProfile>` ノードを探します。

3. `<Metadata>` 要素を見つけます。

4. 次のように、*AuthenticationType* を *ClientCertificate* に変更します。

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. `<Metadata>` 要素の終了直後に、次の XML スニペットを追加します。 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    スニペットを追加すると、技術プロファイルは次の XML コードのようになります。

    ![ClientCertificate 認証 XML 要素の設定](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>手順 4:ポリシーをテナントにアップロードする

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替えてから、**[Azure AD B2C]** を選択します。

2. **[Identity Experience Framework]** を選択します。

3. **[すべてのポリシー]** を選択します。

4. **[ポリシーのアップロード]** を選択します。

5. **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。

6. *TrustFrameworkExtensions.xml* ファイルをアップロードし、検証に合格したことを確認します。

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>手順 5:[今すぐ実行] を使用してカスタム ポリシーをテストする
1. **[Azure AD B2C の設定]** を開き、**[Identity Experience Framework]** を選択します。

    >[!NOTE]
    >[今すぐ実行] を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。

2. アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開いてから、**[今すぐ実行]** を選択します。

3. **[名]** ボックスに「**Test**」と入力して、プロセスをテストします。  
    ウィンドウの上部に Azure AD B2C によってエラー メッセージが表示されます。    

    ![ID API をテストする](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. **[名]** ボックスに ("Test" 以外の) 名前を入力します。  
    Azure AD B2C でユーザーがサインアップされ、アプリケーションにロイヤルティ番号が送信されます。 この JWT の例の番号に注意してください。

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >"*この名前は有効ではありません。有効な名前を入力してください*" という内容のエラー メッセージが表示された場合は、クライアント証明書を提示するときに、Azure AD B2C が RESTful サービスの呼び出しに成功したことを意味します。 次は証明書を検証します。

## <a name="step-6-add-certificate-validation"></a>手順 6:証明書の検証を追加する
Azure AD B2C によって RESTful サービスに送信されたクライアント証明書は、Azure App Service プラットフォームによる検証が行われていません (証明書の有無の確認を除く)。 証明書の検証は、Web アプリが実行する必要があります。 

このセクションでは、認証するために証明書のプロパティを検証するサンプル ASP.NET コードを追加します。

> [!NOTE]
>クライアント証明書認証に対して Azure App Service を構成する方法の詳細については、[Web アプリの TLS 相互認証を構成する](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth)方法に関する記事をご覧ください。

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 アプリケーション設定をプロジェクトの web.config ファイルに追加する
以前に作成した Visual Studio プロジェクトの *web.config* ファイルで、次のアプリケーション設定を `appSettings` 要素の後に追加します。

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

証明書の**サブジェクト名**、**発行者名**、および**証明書の拇印**の値を、ご自分の証明書の値に置き換えます。

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 IsValidClientCertificate 関数を追加する
*Controllers\IdentityController.cs* ファイルを開き、次の関数を `Identity` コントローラー クラスに追加します。 

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }
    
    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
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
```

上記のサンプル コードでは、次の条件がすべて満たされた場合にのみ、証明書を有効として受け入れます。
* 証明書が期限切れではなく、サーバー上の現在の時刻でアクティブである。
* 証明書の `Subject` 名が、`ClientCertificate:Subject` アプリケーション設定値と同じである。
* 証明書の `Issuer` 名が、`ClientCertificate:Issuer` アプリケーション設定値と同じである。
* 証明書の `thumbprint` が、`ClientCertificate:Thumbprint` アプリケーション設定値と同じである。

>[!IMPORTANT]
>サービスの機密性によっては、検証をさらに追加しなければならない場合があります。 たとえば、信頼されたルート証明機関に証明書がチェーンされているかどうかをテストしたり、発行者組織名を検証したりする必要がある場合があります。

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 IsValidClientCertificate 関数を呼び出す
*Controllers\IdentityController.cs* ファイルを開き、`SignUp()` 関数の先頭に、次のコード スニペットを追加します。 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

スニペットを追加すると、`Identity` コントローラーは次のコードのようになります。

![証明書の検証コードの追加](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>手順 7:プロジェクトを Azure に発行してテストする
1. **ソリューション エクスプローラー**で **Contoso.AADB2C.API** プロジェクトを右クリックしてから、**[発行]** を選択します。

2. "手順 6". を繰り返し、カスタム ポリシーを証明書の検証で再度テストします。 ポリシーを実行し、検証を追加した後、すべてが正しく動作するかどうかを確認します。

3. *web.config* ファイルで、`ClientCertificate:Subject` の値を**無効**に変更します。 ポリシーを再実行すると、エラー メッセージが表示されます。

4. 値を**有効**に戻して、ポリシーで REST API を呼び出すことができるかどうかを確認します。

この手順をトラブルシューティングする必要がある場合は、[Application Insights を使用したログの収集](active-directory-b2c-troubleshoot-custom.md)に関する記事をご覧ください。

## <a name="optional-download-the-complete-policy-files-and-code"></a>完全なポリシー ファイルとコードをダウンロードする (省略可能)
* [カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)チュートリアルの完了後に、独自のカスタム ポリシー ファイルを使用してシナリオを構築することをお勧めします。 参照用に[サンプルのポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)が提供されています。
* 完全なコードは、[参照用のVisual Studio ソリューションのサンプル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API)からダウンロードできます。 
