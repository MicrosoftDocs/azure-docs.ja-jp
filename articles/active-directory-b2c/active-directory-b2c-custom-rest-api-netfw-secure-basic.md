---
title: Azure Active Directory B2C の HTTP 基本認証を使用して RESTful サービスをセキュリティ保護する | Microsoft Docs
description: HTTP 基本認証を使用して Azure AD B2C でのカスタム REST API 要求交換をセキュリティで保護する。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 08aebf698a7a00729a0e37b57cb15938853e4185
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2019
ms.locfileid: "67501621"
---
# <a name="secure-your-restful-services-by-using-http-basic-authentication"></a>HTTP 基本認証を使用して RESTful サービスをセキュリティで保護する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

[関連する Azure AD B2C の記事](active-directory-b2c-custom-rest-api-netfw.md)では、Azure Active Directory B2C (Azure AD B2C) のユーザー体験を認証なしで統合する RESTful サービス (Web API) を作成しました。

この記事では、RESTful サービスに HTTP 基本認証を追加して、B2C を含む検証済みのユーザーのみが API にアクセスできるようにします。 HTTP 基本認証では、ユーザーの資格情報 (アプリ ID とアプリ シークレット) をカスタム ポリシーに設定します。

詳細については、「[Basic authentication in ASP.NET web API](https://docs.microsoft.com/aspnet/web-api/overview/security/basic-authentication)」(ASP.NET Web API における基本認証) を参照してください。

## <a name="prerequisites"></a>前提条件

[REST API 要求交換の Azure AD B2C ユーザー体験への統合](active-directory-b2c-custom-rest-api-netfw.md)に関する記事の手順を完了してください。

## <a name="step-1-add-authentication-support"></a>手順 1:認証サポートの追加

### <a name="step-11-add-application-settings-to-your-projects-webconfig-file"></a>手順 1.1: アプリケーション設定をプロジェクトの web.config ファイルに追加する

1. 作成済みの Visual Studio プロジェクトを開きます。

2. 次のアプリケーション設定を web.config ファイルの `appSettings` 要素の下に追加します。

    ```XML
    <add key="WebApp:ClientId" value="B2CServiceUserAccount" />
    <add key="WebApp:ClientSecret" value="your secret" />
    ```

3. パスワードを作成し、`WebApp:ClientSecret` 値を設定します。

    複雑なパスワードを生成するには、次の PowerShell コードを実行します。 任意の値を使用できます。

    ```powershell
    $bytes = New-Object Byte[] 32
    $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
    $rand.GetBytes($bytes)
    $rand.Dispose()
    [System.Convert]::ToBase64String($bytes)
    ```

### <a name="step-12-install-owin-libraries"></a>手順 1.2: OWIN ライブラリをインストールする

まず、Visual Studio Package Manager Console を使用して、OWIN ミドルウェア NuGet パッケージをプロジェクトに追加します。

```powershell
PM> Install-Package Microsoft.Owin
PM> Install-Package Owin
PM> Install-Package Microsoft.Owin.Host.SystemWeb
```

### <a name="step-13-add-an-authentication-middleware-class"></a>手順 1.3: 認証ミドルウェア クラスを追加する

`ClientAuthMiddleware.cs` クラスを *[App_Start]* フォルダーの下に追加します。 そのためには、次の手順を実行します。

1. *[App_Start]* フォルダー右クリックし、 **[追加]** を選択し、次に **[クラス]** を選択します。

   ![App_Start フォルダーに ClientAuthMiddleware.cs クラスを追加する](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth1.png)

2. **[名前]** ボックスに「**ClientAuthMiddleware.cs**」と入力します。

   ![新しい C# クラスを作成する](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup-auth2.png)

3. *App_Start\ClientAuthMiddleware.cs* ファイルを開き、ファイルの内容を次のコードに置き換えます。

    ```csharp
    
    using Microsoft.Owin;
    using System;
    using System.Collections.Generic;
    using System.Configuration;
    using System.Linq;
    using System.Security.Principal;
    using System.Text;
    using System.Threading.Tasks;
    using System.Web;
    
    namespace Contoso.AADB2C.API
    {
        /// <summary>
        /// Class to create a custom owin middleware to check for client authentication
        /// </summary>
        public class ClientAuthMiddleware
        {
            private static readonly string ClientID = ConfigurationManager.AppSettings["WebApp:ClientId"];
            private static readonly string ClientSecret = ConfigurationManager.AppSettings["WebApp:ClientSecret"];
    
            /// <summary>
            /// Gets or sets the next owin middleware
            /// </summary>
            private Func<IDictionary<string, object>, Task> Next { get; set; }
    
            /// <summary>
            /// Initializes a new instance of the <see cref="ClientAuthMiddleware"/> class.
            /// </summary>
            /// <param name="next"></param>
            public ClientAuthMiddleware(Func<IDictionary<string, object>, Task> next)
            {
                this.Next = next;
            }
    
            /// <summary>
            /// Invoke client authentication middleware during each request.
            /// </summary>
            /// <param name="environment">Owin environment</param>
            /// <returns></returns>
            public Task Invoke(IDictionary<string, object> environment)
            {
                // Get wrapper class for the environment
                var context = new OwinContext(environment);
    
                // Check whether the authorization header is available. This contains the credentials.
                var authzValue = context.Request.Headers.Get("Authorization");
                if (string.IsNullOrEmpty(authzValue) || !authzValue.StartsWith("Basic ", StringComparison.OrdinalIgnoreCase))
                {
                    // Process next middleware
                    return Next(environment);
                }
    
                // Get credentials
                var creds = authzValue.Substring("Basic ".Length).Trim();
                string clientId;
                string clientSecret;
    
                if (RetrieveCreds(creds, out clientId, out clientSecret))
                {
                    // Set transaction authenticated as client
                    context.Request.User = new GenericPrincipal(new GenericIdentity(clientId, "client"), new string[] { "client" });
                }
    
                return Next(environment);
            }
    
            /// <summary>
            /// Retrieve credentials from header
            /// </summary>
            /// <param name="credentials">Authorization header</param>
            /// <param name="clientId">Client identifier</param>
            /// <param name="clientSecret">Client secret</param>
            /// <returns>True if valid credentials were presented</returns>
            private bool RetrieveCreds(string credentials, out string clientId, out string clientSecret)
            {
                string pair;
                clientId = clientSecret = string.Empty;
    
                try
                {
                    pair = Encoding.UTF8.GetString(Convert.FromBase64String(credentials));
                }
                catch (FormatException)
                {
                    return false;
                }
                catch (ArgumentException)
                {
                    return false;
                }
    
                var ix = pair.IndexOf(':');
                if (ix == -1)
                {
                    return false;
                }
    
                clientId = pair.Substring(0, ix);
                clientSecret = pair.Substring(ix + 1);
    
                // Return whether credentials are valid
                return (string.Compare(clientId, ClientAuthMiddleware.ClientID) == 0 &&
                    string.Compare(clientSecret, ClientAuthMiddleware.ClientSecret) == 0);
            }
        }
    }
    ```

### <a name="step-14-add-an-owin-startup-class"></a>手順 1.4: OWIN Startup クラスを追加する

`Startup.cs` という名前の OWIN Startup クラスを API に追加します。 そのためには、次の手順を実行します。
1. プロジェクトを右クリックし、 **[追加]**  >  **[新しい項目]** の順に選択して、**OWIN** を検索します。

   ![OWIN Startup クラスを追加する](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-OWIN-startup.png)

2. *Startup.cs* ファイルを開き、ファイルの内容を次のコードに置き換えます。

    ```csharp
    using Microsoft.Owin;
    using Owin;
    
    [assembly: OwinStartup(typeof(Contoso.AADB2C.API.Startup))]
    namespace Contoso.AADB2C.API
    {
        public class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                app.Use<ClientAuthMiddleware>();
            }
        }
    }
    ```

### <a name="step-15-protect-the-identity-api-class"></a>手順 1.5: ID API クラスを保護する

Controllers\IdentityController.cs を開き、`[Authorize]` タグをコントローラー クラスに追加します。 このタグは、コントローラーへのアクセスを承認要件を満たすユーザーに制限します。

![コントローラーに [承認] タグを追加する](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-authorize.png)

## <a name="step-2-publish-to-azure"></a>手順 2:Azure に発行する

プロジェクトを発行するには、ソリューション エクスプローラーで **Contoso.AADB2C.API** プロジェクトを右クリックし、 **[発行]** を選択します。

## <a name="step-3-add-the-restful-services-app-id-and-app-secret-to-azure-ad-b2c"></a>手順 3:RESTful サービス アプリのアプリ ID とアプリ シークレットを Azure AD B2C に追加する

RESTful サービスがクライアント ID (ユーザー名) とシークレットで保護された後、Azure AD B2C テナントに資格情報を格納する必要があります。 RESTful サービスを呼び出すときに、カスタム ポリシーによって資格情報が提供されます。

### <a name="step-31-add-a-restful-services-client-id"></a>手順 3.1: RESTful サービスのクライアント ID を追加する

1. Azure AD B2C テナントで、 **[B2C Settings]\(B2C 設定\)**  >  **[Identity Experience Framework]** の順に選択します。


2. **[ポリシー キー]** を選択して、テナント内で使用できるキーを表示します。

3. **[追加]** を選択します。

4. **[オプション]** には **[手動]** を選択します。

5. **[名前]** には「**B2cRestClientId**」と入力します。  
    プレフィックス *B2C_1A_* が自動的に追加される場合があります。

6. **[シークレット]** ボックスに、前に定義したアプリ ID を入力します。

7. **[キー使用法]** には **[署名]** を選択します。

8. **作成** を選択します。

9. `B2C_1A_B2cRestClientId` キーを作成したことを確認します。

### <a name="step-32-add-a-restful-services-client-secret"></a>手順 3.2: RESTful サービスのクライアント シークレットを追加する

1. Azure AD B2C テナントで、 **[B2C Settings]\(B2C 設定\)**  >  **[Identity Experience Framework]** の順に選択します。

2. **[ポリシー キー]** を選択して、テナント内で利用できるキーを表示します。

3. **[追加]** を選択します。

4. **[オプション]** には **[手動]** を選択します。

5. **[名前]** には「**B2cRestClientSecret**」と入力します。  
    プレフィックス *B2C_1A_* が自動的に追加される場合があります。

6. **[シークレット]** ボックスに、前に定義したアプリ シークレットを入力します。

7. **[キー使用法]** には **[署名]** を選択します。

8. **作成** を選択します。

9. `B2C_1A_B2cRestClientSecret` キーを作成したことを確認します。

## <a name="step-4-change-the-technical-profile-to-support-basic-authentication-in-your-extension-policy"></a>手順 4:拡張ポリシーで基本認証をサポートするように技術プロファイルを変更する

1. 作業ディレクトリで、拡張ポリシー ファイル (TrustFrameworkExtensions.xml) を開きます。

2. `Id="REST-API-SignUp"` を含む `<TechnicalProfile>` ノードを探します。

3. `<Metadata>` 要素を見つけます。

4. 次のように、*AuthenticationType* を *Basic* に変更します。

    ```xml
    <Item Key="AuthenticationType">Basic</Item>
    ```

5. `<Metadata>` 要素の終了直後に、次の XML スニペットを追加します。

    ```xml
    <CryptographicKeys>
        <Key Id="BasicAuthenticationUsername" StorageReferenceId="B2C_1A_B2cRestClientId" />
        <Key Id="BasicAuthenticationPassword" StorageReferenceId="B2C_1A_B2cRestClientSecret" />
    </CryptographicKeys>
    ```

    スニペットを追加すると、技術プロファイルは次の XML コードのようになります。
    
    ![基本認証の XML 要素を追加する](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-secure-basic-add-1.png)

## <a name="step-5-upload-the-policy-to-your-tenant"></a>手順 5:ポリシーをテナントにアップロードする

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替えてから、 **[Azure AD B2C]** を開きます。

2. **[Identity Experience Framework]** を選択します。

3. **[All Policies]\(すべてのポリシー\)** を開きます。

4. **[ポリシーのアップロード]** を選択します。

5. **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。

6. *TrustFrameworkExtensions.xml* ファイルをアップロードし、検証に合格したことを確認します。

## <a name="step-6-test-the-custom-policy-by-using-run-now"></a>手順 6:[今すぐ実行] を使用してカスタム ポリシーをテストする

1. **[Azure AD B2C の設定]** を開き、 **[Identity Experience Framework]** を選択します。

    >[!NOTE]
    >[今すぐ実行] を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。

2. アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開いてから、 **[今すぐ実行]** を選択します。

3. **[名]** ボックスに「**Test**」と入力して、プロセスをテストします。  
    ウィンドウの上部に Azure AD B2C によってエラー メッセージが表示されます。

    ![ID API をテストする](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. **[名]** ボックスに ("Test" 以外の) 名前を入力します。  
    Azure AD B2C でユーザーがサインアップされ、アプリケーションにロイヤルティ番号が送信されます。 次の例の番号に注意してください。

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

## <a name="optional-download-the-complete-policy-files-and-code"></a>完全なポリシー ファイルとコードをダウンロードする (省略可能)

* [カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)チュートリアルの完了後に、独自のカスタム ポリシー ファイルを使用してシナリオを構築することをお勧めします。 参照用に[サンプルのポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)が提供されています。
* 完全なコードは、[参照用の Visual Studio ソリューションのサンプル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-basic)からダウンロードできます。

## <a name="next-steps"></a>次の手順

* [クライアント証明書を使用して RESTful API をセキュリティで保護する](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
