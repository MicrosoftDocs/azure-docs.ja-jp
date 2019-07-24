---
title: REST API 要求交換を Azure Active Directory B2C ユーザー体験に統合する | Microsoft Docs
description: REST API 要求交換をユーザー入力の検証として Azure AD B2C ユーザー体験に統合します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/30/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: bc2e41fd5da4737ea1efe329b70964535daff54a
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58105966"
---
# <a name="integrate-rest-api-claims-exchanges-in-your-azure-ad-b2c-user-journey-as-validation-of-user-input"></a>REST API 要求交換をユーザー入力の検証として Azure AD B2C ユーザー体験に統合する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory B2C (Azure AD B2C) の基盤となる Identity Experience Framework を使用すると、ユーザー体験における RESTful API と統合することができます。 このチュートリアルでは、Azure AD B2C と .NET Framework RESTful サービス (Web API) がどのようなやり取りをするかを学習します。

## <a name="introduction"></a>はじめに
Azure AD B2C を使用すると、自分の RESTful サービスを呼び出すことで、独自のビジネス ロジックをユーザー体験に追加できます。 Identity Experience Framework は、*入力要求*コレクションでデータを RESTful サービスに送信し、*出力要求*コレクションで RESTful から返されたデータを受信します。 RESTful サービスの統合により、次の操作を実行できます。

* **ユーザー入力データの検証:** このアクションは、不適切なデータが Azure AD に保持されるのを防ぎます。 ユーザーが入力した値が有効でない場合、ユーザーに入力を指示するエラー メッセージがご利用の RESTful サービスによって返されます。 たとえば、ユーザーによって入力されたメール アドレスが顧客データベースに存在するかどうかを確認できます。
* **入力要求を上書きする**:たとえば、ユーザーが名をすべて小文字または大文字で入力する場合に、名の最初の文字だけを大文字にするように書式設定できます。
* **企業の基幹業務アプリケーションとさらに緊密に統合することでユーザー データを拡充する**:RESTful サービスでは、ユーザーのメール アドレスを受け取り、顧客のデータベースを照会し、ユーザーのロイヤルティ番号を Azure AD B2C に返すことができます。 返された要求は、ユーザーの Azure AD アカウントに保存するか、次の*オーケストレーション手順*で評価するか、アクセス トークンに含めることができます。
* **カスタム ビジネス ロジックの実行:** プッシュ通知の送信、企業データベースの更新、ユーザーの移行プロセスの実行、アクセス許可の管理、データベースの監査、およびその他のアクションを実行できます。

次の方法で、RESTful サービスとの統合を設計できます。

* **検証技術プロファイル:** RESTful サービスへの呼び出しは、指定した技術プロファイルの検証技術プロファイル内で発生します。 検証技術プロファイルは、ユーザー体験を進める前に、ユーザーが入力したデータを検証します。 検証技術プロファイルでは、次を行うことができます。
   * 入力要求を送信する。
   * 入力要求を検証し、カスタム エラー メッセージをスローする。
   * 出力要求を返送する。

* **要求の交換**:この設計は検証技術プロファイルに似ていますが、オーケストレーション手順で発生します。 この定義は次に制限されます。
   * 入力要求を送信する。
   * 出力要求を返送する。

## <a name="restful-walkthrough"></a>RESTful のチュートリアル
このチュートリアルでは、ユーザー入力を検証し、ユーザーのロイヤルティ番号を提供する .NET Framework Web API を開発します。 たとえば、アプリケーションでは、ロイヤルティ番号に基づいて、*プラチナ会員のメリット*へのアクセス権を付与できます。

概要:
* RESTful サービスを開発する (.NET Framework Web API)。
* ユーザー体験で RESTful サービスを使用する。
* 入力要求を送信し、コード内でそれらを読み取る。
* ユーザーの名を検証する。
* ロイヤルティ番号を返送する。
* JSON Web トークン (JWT) にロイヤルティ番号を追加する。

## <a name="prerequisites"></a>前提条件
[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

## <a name="step-1-create-an-aspnet-web-api"></a>手順 1:ASP.NET Web API を作成する

1. Visual Studio で、**[ファイル]** > **[新規作成]** > **[プロジェクト]** の順に選択して、プロジェクトを作成します。

2. **[新しいプロジェクト]** ウィンドウで、**[Visual C#]** > **[Web]** > **[ASP.NET Web アプリケーション (.NET Framework)]** の順に選択します。

3. **[名前]** ボックスに、アプリケーションの名前 (*Contoso.AADB2C.API* など) を入力して、**[OK]** を選択します。

    ![新しい Visual Studio プロジェクトの作成](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-create-project.png)

4. **[新しい ASP.NET Web アプリケーション]** ウィンドウで、**[Web API]** または **[Azure API App]** テンプレートを選択します。

    ![Web API テンプレートの選択](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-select-web-api.png)

5. 認証が **[認証なし]** に設定されていることを確認します。

6. **[OK]** を選択してプロジェクトを作成します。

## <a name="step-2-prepare-the-rest-api-endpoint"></a>手順 2:REST API エンドポイントを準備する

### <a name="step-21-add-data-models"></a>手順 2.1:データ モデルを追加する
モデルは、RESTful サービスの入力要求と出力要求のデータを表します。 入力要求モデルを JSON 文字列から C# オブジェクト (使用しているモデル) に逆シリアル化することで、ご利用のコードによって入力データが読み取られます。 ASP.NET Web API では、出力要求モデルが JSON に自動的に逆シリアル化された後、シリアル化されたデータが、HTTP 応答メッセージの本文に書き込まれます。

次の手順を実行して、入力要求を表すモデルを作成します。

1. ソリューション エクスプローラーが開いていない場合は、**[表示]** > **[ソリューション エクスプローラー]** を選択します。
2. ソリューション エクスプローラーで、**Models** フォルダーを右クリックし、**[追加]**、**[クラス]** の順に選択します。

    ![Add model](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-model.png)

3. クラス `InputClaimsModel` に名前を付けて、次のプロパティを `InputClaimsModel` クラスに追加します。

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class InputClaimsModel
        {
            public string email { get; set; }
            public string firstName { get; set; }
            public string lastName { get; set; }
        }
    }
    ```

4. 新しいモデル `OutputClaimsModel` を作成し、次のプロパティを `OutputClaimsModel` クラスに追加します。

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class OutputClaimsModel
        {
            public string loyaltyNumber { get; set; }
        }
    }
    ```

5. 入力の検証エラー メッセージをスローするために使用する、もう 1 つのモデル `B2CResponseContent` を作成します。 次のプロパティを `B2CResponseContent` クラスに追加し、欠落している参照に入力して、ファイルを保存します。

    ```csharp
    namespace Contoso.AADB2C.API.Models
    {
        public class B2CResponseContent
        {
            public string version { get; set; }
            public int status { get; set; }
            public string userMessage { get; set; }

            public B2CResponseContent(string message, HttpStatusCode status)
            {
                this.userMessage = message;
                this.status = (int)status;
                this.version = Assembly.GetExecutingAssembly().GetName().Version.ToString();
            }
        }
    }
    ```

### <a name="step-22-add-a-controller"></a>手順 2.2:コントローラーを追加する
Web API では、_コントローラー_ は、HTTP 要求を処理するオブジェクトです。 コントローラーは、出力要求を返すか、名前が有効でない場合は HTTP 競合のエラー メッセージをスローします。

1. ソリューション エクスプローラーで、**Controllers** フォルダーを右クリックし、**[追加]**、**[コントローラー]** の順に選択します。

    ![新しいコントロールの追加](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-1.png)

2. **[スキャフォールディングを追加]** ウィンドウで **[Web API コントローラー - 空]** を選択し、**[追加]** を選択します。

    ![[Web API 2 コントローラー - 空] の選択](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-2.png)

3. **[コントローラーの追加]** ウィンドウで、コントローラーに **IdentityController** という名前を付けて、**[追加]** を選択します。

    ![コントローラーの名前の入力](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-add-controller-3.png)

    *IdentityController.cs* という名前のファイルが *[コントローラー]* フォルダーに作成されます。

4. *IdentityController.cs* ファイルがまだ開いていない場合は、それをダブルクリックして、ファイル内のコードを次のコードに置き換えます。

    ```csharp
    using Contoso.AADB2C.API.Models;
    using Newtonsoft.Json;
    using System;
    using System.NET;
    using System.Web.Http;

    namespace Contoso.AADB2C.API.Controllers
    {
        public class IdentityController: ApiController
        {
            [HttpPost]
            public IHttpActionResult SignUp()
            {
                // If no data came in, then return
                if (this.Request.Content == null) throw new Exception();

                // Read the input claims from the request body
                string input = Request.Content.ReadAsStringAsync().Result;

                // Check the input content value
                if (string.IsNullOrEmpty(input))
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Request content is empty", HttpStatusCode.Conflict));
                }

                // Convert the input string into an InputClaimsModel object
                InputClaimsModel inputClaims = JsonConvert.DeserializeObject(input, typeof(InputClaimsModel)) as InputClaimsModel;

                if (inputClaims == null)
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Can not deserialize input claims", HttpStatusCode.Conflict));
                }

                // Run an input validation
                if (inputClaims.firstName.ToLower() == "test")
                {
                    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Test name is not valid, please provide a valid name", HttpStatusCode.Conflict));
                }

                // Create an output claims object and set the loyalty number with a random value
                OutputClaimsModel outputClaims = new OutputClaimsModel();
                outputClaims.loyaltyNumber = new Random().Next(100, 1000).ToString();

                // Return the output claim(s)
                return Ok(outputClaims);
            }
        }
    }
    ```

## <a name="step-3-publish-the-project-to-azure"></a>手順 3:Azure にプロジェクトを発行する
1. ソリューション エクスプローラーで **Contoso.AADB2C.API** プロジェクトを右クリックしてから、**[発行]** を選択します。

    ![Microsoft Azure App Service への発行](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-1.png)

2. **[発行]** ウィンドウで、**[Microsoft Azure App Service]** を選択し、**[発行]** を選択します。

    ![新しい Microsoft Azure App Service の作成](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-2.png)

    **[App Service の作成]** ウィンドウが開きます。 このウィンドウでは、Azure で ASP.NET Web アプリを実行するために必要なすべての Azure リソースを作成します。

    > [!NOTE]
    >発行方法の詳細については、「[Azure に ASP.NET Web アプリを作成する](https://docs.microsoft.com/azure/app-service-web/app-service-web-get-started-dotnet)」を参照してください。

3. **[Web アプリ名]** ボックスに、一意のアプリ名 (有効な文字は a-z、0-9、およびハイフン (-)) を入力します。 Web アプリの URL は http://<app_name>.azurewebsites.NET です。*app_name* は、Web アプリの名前です。 自動的に生成される名前をそのまま使用してもかまいません。この名前は一意になっています。

    ![App Service プロパティの入力](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-publish-to-azure-3.png)

4. Azure リソースの作成を開始するには、**[作成]** を選択します。  
    ASP.NET Web アプリが作成されると、ウィザードはそれを Azure に発行してから、既定のブラウザーでアプリを起動します。

6. Web アプリの URL をコピーします。

## <a name="step-4-add-the-new-loyaltynumber-claim-to-the-schema-of-your-trustframeworkextensionsxml-file"></a>手順 4:新しい要求 `loyaltyNumber` を TrustFrameworkExtensions.xml ファイルのスキーマに追加する
要求 `loyaltyNumber` は、スキーマではまだ定義されていません。 要素 `<BuildingBlocks>` 内に定義を追加します。これは *TrustFrameworkExtensions.xml* ファイルの先頭で見つけることができます。

```xml
<BuildingBlocks>
    <ClaimsSchema>
        <ClaimType Id="loyaltyNumber">
            <DisplayName>loyaltyNumber</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Customer loyalty number</UserHelpText>
        </ClaimType>
    </ClaimsSchema>
</BuildingBlocks>
```

## <a name="step-5-add-a-claims-provider"></a>手順 5:クレーム プロバイダーを追加する
クレーム プロバイダーには必ず、1 つ以上の技術プロファイルが必要で、そのプロファイルによって、クレーム プロバイダーとの通信に必要なエンドポイントとプロトコルが決まります。

クレーム プロバイダーが、複数の技術プロファイルを持つことができる理由はさまざまです。 たとえば、クレーム プロバイダーが複数のプロトコルをサポートしていたり、エンドポイントがさまざまな機能を持てたり、リリースにさまざまな保証レベルの要求を含めることができる、などの理由で、技術プロファイルが複数定義される可能性があります。 あるユーザー体験で機密性の高い要求をリリースできても、別のユーザー体験ではリリースできないこともあります。

XML スニペットには、次の 2 つの技術プロファイルを持つクレーム プロバイダー ノードが含まれています。

* **TechnicalProfile Id="REST-API-SignUp"**:RESTful サービスを定義します。
  * `Proprietary` は、RESTful ベースのプロバイダーのプロトコルとして記述されています。
  * `InputClaims` は、Azure AD B2C から REST サービスに送信される要求を定義します。

    この例では、要求 `givenName` のコンテンツは `firstName` として REST サービスに送信され、要求 `surname` のコンテンツは `lastName` として REST サービスに送信され、`email` はそのまま送信されます。 `OutputClaims` 要素は、RESTful サービスから Azure AD B2C に戻る間に取得する要求を定義します。

* **TechnicalProfile Id="LocalAccountSignUpWithLogonEmail"**:検証技術プロファイルを、(基本ポリシーで定義されている) 既存の技術プロファイルに追加します。 サインアップ中、検証技術プロファイルは、上記の技術プロファイルを呼び出します。 RESTful サービスで HTTP エラー 409 (競合エラー) が返された場合、そのエラー メッセージがユーザーに表示されます。

`<ClaimsProviders>` ノードを見つけて、次の XML スニペットを `<ClaimsProviders>` ノードの下に追加します。

```xml
<ClaimsProvider>
  <DisplayName>REST APIs</DisplayName>
  <TechnicalProfiles>
    
    <!-- Custom Restful service -->
    <TechnicalProfile Id="REST-API-SignUp">
      <DisplayName>Validate user's input data and return loyaltyNumber claim</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.RestfulProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ServiceUrl">https://your-app-name.azurewebsites.NET/api/identity/signup</Item>
        <Item Key="AuthenticationType">None</Item>
        <Item Key="SendClaimsIn">Body</Item>
        <Item Key="AllowInsecureAuthInProduction">true</Item>
      </Metadata>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="email" />
        <InputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="firstName" />
        <InputClaim ClaimTypeReferenceId="surname" PartnerClaimType="lastName" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
    </TechnicalProfile>

    <!-- Change LocalAccountSignUpWithLogonEmail technical profile to support your validation technical profile -->
    <TechnicalProfile Id="LocalAccountSignUpWithLogonEmail">
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" PartnerClaimType="loyaltyNumber" />
      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="REST-API-SignUp" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

## <a name="step-6-add-the-loyaltynumber-claim-to-your-relying-party-policy-file-so-the-claim-is-sent-to-your-application"></a>手順 6:要求 `loyaltyNumber` がアプリケーションに送信されるように、その要求を証明書利用者ポリシー ファイル に追加する
*SignUpOrSignIn.xml* 証明書利用者 (RP) ファイルを編集し、TechnicalProfile Id="PolicyProfile" 要素を修正して次を追加します。`<OutputClaim ClaimTypeReferenceId="loyaltyNumber" />`

新しい要求を追加した後の証明書利用者コードは次のようになります。

```xml
<RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="loyaltyNumber" DefaultValue="" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
    </RelyingParty>
</TrustFrameworkPolicy>
```

## <a name="step-7-upload-the-policy-to-your-tenant"></a>手順 7:ポリシーをテナントにアップロードする

1. [Azure Portal](https://portal.azure.com) で、[Azure AD B2C テナントのコンテキスト](active-directory-b2c-navigate-to-b2c-context.md)に切り替えてから、**[Azure AD B2C]** を開きます。

2. **[Identity Experience Framework]** を選択します。

3. **[All Policies]\(すべてのポリシー\)** を開きます。

4. **[ポリシーのアップロード]** を選択します。

5. **[ポリシーが存在する場合は上書きする]** チェック ボックスをオンにします。

6. TrustFrameworkExtensions.xml ファイルをアップロードし、検証に合格することを確認します。

7. SignUpOrSignIn.xml ファイルを使用して前の手順を繰り返します。

## <a name="step-8-test-the-custom-policy-by-using-run-now"></a>ステップ 8:[今すぐ実行] を使用してカスタム ポリシーをテストする
1. **[Azure AD B2C の設定]** を選択してから、**[Identity Experience Framework]** に移動します。

    > [!NOTE]
    > **[今すぐ実行]** を使用するには、テナントに少なくとも 1 つのアプリケーションが事前登録されている必要があります。 アプリケーションを登録する方法については、 Azure AD B2C の[概要](active-directory-b2c-get-started.md)に関する記事または[アプリケーションの登録](active-directory-b2c-app-registration.md)に関する記事を参照してください。

2. アップロードした証明書利用者 (RP) カスタム ポリシーである **B2C_1A_signup_signin** を開いてから、**[今すぐ実行]** を選択します。

    ![[B2C_1A_signup_signin] ウィンドウ](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-run.png)

3. **[名]** ボックスに「**Test**」と入力して、プロセスをテストします。  
    ウィンドウの上部に Azure AD B2C によってエラー メッセージが表示されます。

    ![ポリシーのテスト](media/aadb2c-ief-rest-api-netfw/aadb2c-ief-rest-api-netfw-test.png)

4. **[名]** ボックスに ("Test" 以外の) 名前を入力します。  
    Azure AD B2C でユーザーがサインアップされ、アプリケーションに loyaltyNumber が送信されます。 この JWT の番号に注意してください。

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
* [カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)チュートリアルの完了後に、独自のカスタム ポリシー ファイルを使用してシナリオを構築することをお勧めします。 参照用に[サンプルのポリシー ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw)が提供されています。
* 完全なコードは、[参照用の Visual Studio ソリューションのサンプル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/)からダウンロードできます。

## <a name="next-steps"></a>次のステップ
* [基本認証 (ユーザー名とパスワード) を使用して RESTful API をセキュリティで保護する](active-directory-b2c-custom-rest-api-netfw-secure-basic.md)
* [クライアント証明書を使用して RESTful API をセキュリティで保護する](active-directory-b2c-custom-rest-api-netfw-secure-cert.md)
