---
title: Azure Active Directory B2C による TypingDNA
titleSuffix: Azure AD B2C
description: Azure AD B2C 認証を TypingDNA と統合して、ユーザーの入力パターンに基づく ID 検証と文章校正を支援する方法について説明します。また、多要素認証を強制する ID 検証ソリューションを提供し、決済サービス指令 2 (PSD2) の SCA 要件への準拠を支援します。
author: gargi-sinha
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 06/25/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 68617d86fda940c5d3752f2389088a8c729aebec
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97108350"
---
# <a name="tutorial-for-configuring-typingdna-with-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して TypingDNA を構成するためのチュートリアル

このチュートリアルでは、Azure Active Directory B2C のサンプル オンライン決済アプリを TypingDNA アプリと統合する方法について説明します。 Azure AD B2C をお使いのお客様は、TypingDNA アプリを使用すると、キーストローク ダイナミクスと強力な顧客認証を介して、[決済サービス指令 2](https://www.typingdna.com/use-cases/sca-strong-customer-authentication) (PSD2) のトランザクション要件に準拠することができます。 TypingDNA の詳細については[こちら](https://www.typingdna.com/)を参照してください。

 Azure AD B2C では、TypingDNA のテクノロジを使用して、ユーザーの入力の特性をキャプチャし、各認証について熟知するために記録および分析しています。 これにより、認証のリスクに関連する保護レイヤーが追加され、リスク レベルが評価されます。 Azure AD B2C から他のメカニズムを呼び出すと、Azure AD MFA の呼び出し、メール検証の強制、またはシナリオに適したその他のカスタム ロジックによって、ユーザーの本人確認の信頼性を高めることができます。

>[!NOTE]
> このサンプル ポリシーは [SocialAndLocalAccountsWithMfa](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccountsWithMfa) スターター パックに基づいています。

## <a name="scenario-description"></a>シナリオの説明

![TypingDNA アーキテクチャ図のスクリーンショット](./media/partner-typingdna/typingdna-architecture-diagram.png)

### <a name="sign-up"></a>サインアップ

1. Azure AD B2C ページでは、TypingDNA の JavaScript ライブラリを使用してユーザーの入力パターンが記録されます。 たとえば、ユーザー名とパスワードは最初の登録のサインアップ時に記録され、その後、確認のためにサインインのたびに記録されます。

2. ユーザーがページを送信すると、TypingDNA ライブラリによってユーザーの入力の特性が計算されます。 その後、Azure AD B2C によってレンダリングされた非表示のテキスト フィールドに情報が挿入されます。 このフィールドは CSS を使用して非表示にされています。  

    このサンプルには、JavaScript と CSS の変更を含む [HTML ファイルが含まれており](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignUp.cshtml)、`api.selfasserted.tdnasignin` および `api.selfasserted.tdnasignup` のコンテンツ定義から参照されます。 HTML ファイルをホストするには、「[ページ コンテンツのホスト](./customize-ui-with-html.md#hosting-the-page-content)」を参照してください。

3. Azure AD B2C では、ユーザーが資格情報を送信したときに、要求バッグ内に入力パターンが含まれるようになりました。 このデータを TypingDNA REST API エンドポイントに渡すには、(ご自分の) API を呼び出す必要があります。 この API は[サンプル (typingDNA-API-Interface)](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) に含まれています。
4. 次に、中間層 API から入力パターン データが TypingDNA REST API に渡されます。 サインアップ時に、[check user endpoint](https://api.typingdna.com/index.html#api-API_Services-GetUser) が呼び出されてユーザーが存在しないことが確認され、次に [save pattern](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) エンドポイントが呼び出されてユーザーの最初の入力パターンが保存されます。

> [!NOTE]
> TypingDNA REST API エンドポイントへのすべての呼び出しでは、UserId が送信されます。 これは常にハッシュ値です。 Azure AD B2C では、`HashObjectIdWithEmail` 要求変換が使用され、ランダムなソルトとシークレットを使ってメールがハッシュされます。  

REST API の呼び出しは `LocalAccountSignUpWithLogonEmail-TDNA` 内の `validationTechnicalProfiles` でモデル化されています。

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="AAD-UserWriteUsingLogonEmail-TDNA" />
    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="true"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser"/>

</ValidationTechnicalProfiles>

```

### <a name="sign-in"></a>サインイン

その後のサインインでは、ユーザーの入力パターンは、[カスタムの HTML](https://github.com/azure-ad-b2c/partner-integrations/blob/master/samples/TypingDNA/source-code/selfAssertedSignIn.cshtml) を使用したサインアップ時と同じ方法で計算されます。 入力プロファイルが Azure AD B2C 要求バッグ内に入ると、Azure AD B2C から、TypingDNA の REST API エンドポイントを呼び出す API が呼び出されます。 [check user](https://api.typingdna.com/index.html#api-API_Services-GetUser) エンドポイントが呼び出され、ユーザーが存在することが確認されます。 次に、[verify pattern](https://api.typingdna.com/index.html#api-API_Services-verifyTypingPattern) エンドポイントが呼び出され、`net_score` が返されます。 この `net_score` は、サインアップ時に入力パターンが元とどれだけ近いかを示しています。

この入力パターンは、`SelfAsserted-LocalAccountSignin-Email-TDNA` 内の `validationTechnicalProfiles` でモデル化されています。

```xml

<ValidationTechnicalProfiles>

    <ValidationTechnicalProfile ReferenceId="login-NonInteractive"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-CheckUser" ContinueOnError="false"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-VerifyUser"/>

    <ValidationTechnicalProfile ReferenceId="REST-TDNA-SaveUser">

        <Preconditions>

            <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>saveTypingPattern</Value>

            <Value>False</Value>

            <Action>SkipThisValidationTechnicalProfile</Action>

            </Precondition>

        </Preconditions>

    </ValidationTechnicalProfile>

</ValidationTechnicalProfiles>

```

 `net_score` が高い入力パターンをユーザーが取得した場合、TypingDNA の [save typing pattern](https://api.typingdna.com/index.html#api-API_Services-saveUserPattern) エンドポイントを使用してこれを保存できます。  

TypingDNA の save typing pattern エンドポイントを Azure AD B2C から (API 経由で) 呼び出す場合は、お客様の API から要求 `saveTypingPattern` を返す必要があります。

リポジトリの例には、次のプロパティで構成された API (TypingDNA-API-Interface) が含まれています。

- トレーニング モード - ユーザーが保存したパターンが 2 つ未満の場合は、常に MFA のプロンプトを表示します。

- ユーザーが 2 個から 5 個のパターンを保存していて、`net_score` が 50 未満の場合は、MFA のプロンプトを表示します。

- ユーザーが 5 個以上のパターンを保存していて、`net_score` が 65 未満の場合は、MFA のプロンプトを表示します。

これらのしきい値は、ユース ケースに合わせて調整する必要があります。

- API によって `net_score` が評価された後は、ブール要求が B2C (`promptMFA`) に返されます。

- `promptMFA` 要求は、Azure AD MFA を条件付きで実行するために前提条件内で使用されます。

```xml

<OrchestrationStep Order="3" Type="ClaimsExchange">

    <Preconditions>

        <Precondition Type="ClaimsExist" ExecuteActionsIf="true">

            <Value>isActiveMFASession</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

        <Precondition Type="ClaimEquals" ExecuteActionsIf="true">

            <Value>promptMFA</Value>

            <Value>False</Value>

            <Action>SkipThisOrchestrationStep</Action>

        </Precondition>

    </Preconditions>

    <ClaimsExchanges>

        <ClaimsExchange Id="PhoneFactor-Verify" TechnicalProfileReferenceId="PhoneFactor-InputOrVerify" />

    </ClaimsExchanges>

</OrchestrationStep>

```

## <a name="onboard-with-typingdna"></a>TypingDNA によるオンボード

1. [ここ](https://www.typingdna.com/)で TypingDNA にサインアップします
2. TypingDNA ダッシュボードにログインし、**API キー** と **API シークレット** を取得します。 これは、後で API インターフェイスの設定で必要になります

## <a name="integrate-typingdna-with-azure-ad-b2c"></a>TypingDNA を Azure AD B2C と統合する

1. 選択したホスティング プロバイダーで [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) をホストする
2. [TypingDNA-API-Interface](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) ソリューションの `apiKey` および `apiSecret` のすべてのインスタンスを TypingDNA ダッシュボードの資格情報に置き換えます
3. [ここ](./customize-ui-with-html.md#3-configure-cors)の CORS の要件に従って、選択したプロバイダーで HTML ファイルをホストします
4. `TrustFrameworkExtensions.xml` ファイルの `api.selfasserted.tdnasignup` および `api.selfasserted.tdnasignin` コンテンツ定義の LoadURI 要素を、ホストされている HTML ファイルの URI にそれぞれ置き換えます。
5. **Azure portal** の Azure AD ブレードの ID エクスペリエンス フレームワークの下に B2C ポリシー キーを作成します。 `Generate` オプションを使用して、このキーに `tdnaHashedId` という名前を付けます。
6. ポリシー ファイルの TenantId を置き換えます
7. すべての TypingDNA REST API 技術プロファイル (REST-TDNA-VerifyUser、REST-TDNA-SaveUser、REST-TDNA-CheckUser) の ServiceURL を [TypingDNA-API-Interface API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/source-code/TypingDNA-API-Interface) のエンドポイントに置き換えます。
8. [ポリシー ファイル](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/TypingDNA/policy)をテナントにアップロードします。

## <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. B2C テナントを開き、ID エクスペリエンス フレームワークを選択します。
2. 以前に作成した **ユーザー フロー** を選択します。
3. **[ユーザー フローを実行します]** を選択します。

    a. **[アプリケーション]** : 登録済みのアプリを選択します (サンプルは JWT)

    b. **[応答 URL]** : リダイレクト URL を選択します

    c. **[ユーザー フローを実行します]** を選択します。
  
4. サインアップ フローを実行し、アカウントを作成します
5. サインアウトする
6. サインイン フローを実行します
7. 結果の JWT 結果には、TypingDNA の結果が表示されます

## <a name="live-version"></a>ライブ バージョン

• このテスト バージョンでは MFA が無効になっていますが、認証後に要求 `promptMFA` によって MFA のプロンプトが表示されたかどうかの結果を確認できます。

• サインアップは[こちら](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SU_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)、サインインは[こちら](https://b2cprod.b2clogin.com/b2cprod.onmicrosoft.com/oauth2/v2.0/authorize?p=B2C_1A_SI_TDNA&client_id=51d907f8-db14-4460-a1fd-27eaeb2a74da&nonce=defaultNonce&redirect_uri=https://jwt.ms/&scope=openid&response_type=id_token&prompt=login)を使用してください

## <a name="next-steps"></a>次のステップ

追加情報については、次の記事を参照してください。

- [AAD B2C のカスタム ポリシー](./custom-policy-overview.md)

- [AAD B2C のカスタム ポリシーの概要](./custom-policy-get-started.md?tabs=applications)