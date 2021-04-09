---
title: Azure AD B2C でパスワードのリセット フローを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C でパスワードのリセット フローを設定する方法を説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: fa34e8ea71c307b75a3f345861f8ed99d131b3fd
ms.sourcegitcommit: f6193c2c6ce3b4db379c3f474fdbb40c6585553b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/08/2021
ms.locfileid: "102447930"
---
# <a name="set-up-a-password-reset-flow-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でパスワードのリセット フローを設定する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

## <a name="password-reset-flow"></a>パスワードのリセット フロー

[サインアップとサインイン](add-sign-up-and-sign-in-policy.md)の体験では、ユーザーは **[パスワードを忘れた場合]** リンクを使用して自分のパスワードをリセットできます。 パスワードのリセット フローでは、次の手順を実行します。

1. ユーザーはサインアップおよびサインイン ページで、 **[パスワードを忘れた場合]** リンクをクリックします。 Azure AD B2C によってパスワードのリセット フローが開始されます。 
2. ユーザーは、時間限定のワン タイム パスコードを入力して、電子メール アドレスを確認します。
3. ユーザーは次に、新しいパスワードを入力できます。

![パスワードのリセット フロー](./media/add-password-reset-policy/password-reset-flow.png)

パスワードのリセット フローは、サインイン用のパスワードが指定されている[電子メール アドレス](identity-provider-local.md#email-sign-in)または[ユーザー名](identity-provider-local.md#username-sign-in)を使用する Azure AD B2C のローカル アカウントに適用されます。

ランダムなパスワードを使用してユーザーを Azure AD B2C に移行した後には、一般に、最初のサインイン時にユーザーに電子メール アドレスを確認させ、パスワードをリセットさせることを行います。 管理者がユーザーのパスワードを変更した後に、ユーザーにパスワードのリセットを強制することも一般的です。この機能を有効にするには、[パスワードの強制的なリセット](force-password-reset.md)に関するページを参照してください。

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="self-service-password-reset-recommended"></a>セルフサービス パスワード リセット (推奨)

新しいパスワード リセット エクスペリエンスが、サインアップやサインインのポリシーの一部になりました。 ユーザーが **[パスワードを忘れた場合]** リンクを選択すると、すぐにパスワードを忘れた場合のエクスペリエンスが表示されます。 アプリケーションでは、[AADB2C90118 エラー コード](#password-reset-policy-legacy)を処理する必要がなくなりました。また、パスワード リセット用の別個のポリシーは必要ありません。

::: zone pivot="b2c-user-flow"

セルフサービス パスワード リセットのエクスペリエンスは、**サインイン (推奨)** または **サインアップとサインイン (推奨)** のユーザー フローで構成できます。 そのようなユーザー フローがない場合は、[サインインとサインアップ](add-sign-up-and-sign-in-policy.md)のユーザー フローを作成します。 

サインアップまたはサインインのユーザーフローでセルフサービス パスワード リセットを有効にするには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. カスタマイズする (種類が **[推奨]** である) サインアップまたはサインインのユーザー フローを選択します。
1. 左側のメニューの **[設定]** で、 **[プロパティ]** を選択します。
1. **[パスワードの複雑さ]** で、 **[セルフサービス パスワード リセット]** を選択します。
1. **[保存]** を選択します。
1. 左側のメニューの **[カスタマイズ]** で、 **[ページ レイアウト]** を選択します。
1. **[ページ レイアウトのバージョン]** で、 **[2.1.2 - 現行]** 以上を選択します。
1. **[保存]** を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

以降のセクションでは、セルフサービス パスワード エクスペリエンスをカスタム ポリシーに追加する方法について説明します。 このサンプルは、[カスタム ポリシー スターター パック](./custom-policy-get-started.md)に含まれるポリシー ファイルに基づいています。 

> [!TIP]
> "パスワード リセットによるサインアップまたはサインイン" ポリシーの完全なサンプルは、[GitHub](https://github.com/azure-ad-b2c/samples/tree/master/policies/embedded-password-reset) に用意されています。

### <a name="indicate-a-user-selected-the-forgot-your-password-link"></a>ユーザーが [パスワードを忘れた場合] リンクを選択したことを示す

ユーザーが **[パスワードを忘れた場合]** リンクを選択したことをポリシーに示すため、ブール値の要求を定義します。 この要求は、ユーザー体験を、パスワードを忘れた場合の技術プロファイルに移動させるために使用されます。 この要求をトークンに対して発行し、ユーザーがパスワードを忘れた場合のフローを介してサインインしたことが、アプリケーションで認識されるようにすることもできます。

要求は、[要求スキーマ](claimsschema.md)内で宣言します。 お使いのポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。

1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ClaimsSchema](claimsschema.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. 次の要求を **ClaimsSchema** 要素に追加します。 

```XML
<!-- 
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="isForgotPassword">
      <DisplayName>isForgotPassword</DisplayName>
      <DataType>boolean</DataType>
      <AdminHelpText>Whether the user has selected Forgot your Password</AdminHelpText>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="upgrade-the-page-layout-version"></a>ページ レイアウト バージョンをアップグレードする

[ページ レイアウト バージョン](contentdefinitions.md#migrating-to-page-layout) `2.1.2` は、サインアップまたはサインインの体験内でセルフサービス パスワード リセットのフローを有効にするために必要です。

1. [BuildingBlocks](buildingblocks.md) 要素を検索します。 要素が存在しない場合は追加します。
1. [ContentDefinitions](contentdefinitions.md) 要素を見つけます。 要素が存在しない場合は追加します。
1. 下に示すように、ID として **api.signuporsignin** を使用して、**ContentDefinition** 要素内の **DataURI** 要素を変更します。

```xml
<!-- 
<BuildingBlocks>
  <ContentDefinitions> -->
    <ContentDefinition Id="api.signuporsignin">
      <DataUri>urn:com:microsoft:aad:b2c:elements:contract:unifiedssp:2.1.2</DataUri>
    </ContentDefinition>
  <!-- 
  </ContentDefinitions>
</BuildingBlocks> -->
```

`isForgotPassword` 要求を開始するために、要求変換の技術プロファイルが使用されます。 この技術プロファイルは後で参照されます。 これが呼び出されると、`isForgotPassword` 要求の値が `true` に設定されます。 `ClaimsProviders` 要素を見つけます。 要素が存在しない場合は追加します。 次に、下のクレーム プロバイダーを追加します。  

```xml
<!-- 
<ClaimsProviders> -->
  <ClaimsProvider>
    <DisplayName>Local Account</DisplayName>
    <TechnicalProfiles>
      <TechnicalProfile Id="ForgotPassword">
        <DisplayName>Forgot your password?</DisplayName>
        <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.ClaimsTransformationProtocolProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null"/>
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="true" AlwaysUseDefaultValue="true"/>
        </OutputClaims>
      </TechnicalProfile>
      <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
        <Metadata>
          <Item Key="setting.forgotPasswordLinkOverride">ForgotPasswordExchange</Item>
        </Metadata>
      </TechnicalProfile>
    </TechnicalProfiles>
  </ClaimsProvider>
<!-- 
</ClaimsProviders> -->
```

`SelfAsserted-LocalAccountSignin-Email` 技術プロファイル `setting.forgotPasswordLinkOverride` で、ユーザー体験で実行されるパスワード リセット要求の交換を定義します。 

### <a name="add-the-password-reset-sub-journey"></a>パスワード リセットのサブ体験を追加する

これで、ユーザーがサインインやサインアップを行ってパスワード リセットを実行するための機能が体験に含められました。 ユーザー体験をより適切にまとめるために、[サブ体験](subjourneys.md)を使用してパスワードのリセット フローを処理できます。

サブ体験は、ユーザー体験から呼び出されて、ユーザーにパスワード リセット エクスペリエンスを提供するための特定の手順を実行します。 サブ経験が完了したらそのサブ経験を開始したオーケストレーション ステップに制御が返されるように、`Call` 型のサブ体験を使用します。

`SubJourneys` 要素を見つけます。 この要素が存在しない場合は、`User Journeys` 要素の後に追加します。 次に、下記のサブ体験を追加します。

```xml
<!--
<SubJourneys>-->
  <SubJourney Id="PasswordReset" Type="Call">
    <OrchestrationSteps>
      <!-- Validate user's email address. -->
      <OrchestrationStep Order="1" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="PasswordResetUsingEmailAddressExchange" TechnicalProfileReferenceId="LocalAccountDiscoveryUsingEmailAddress" />
        </ClaimsExchanges>
      </OrchestrationStep>

      <!-- Collect and persist a new password. -->
      <OrchestrationStep Order="2" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="NewCredentials" TechnicalProfileReferenceId="LocalAccountWritePasswordUsingObjectId" />
        </ClaimsExchanges>
      </OrchestrationStep>
    </OrchestrationSteps>
  </SubJourney>
<!--
</SubJourneys>-->
```

### <a name="prepare-your-user-journey"></a>ユーザー体験を準備する

パスワードを忘れた場合のサブ体験への **[パスワードを忘れた場合]** リンクに接続する必要があります。 これを行うには、**CombinedSignInAndSignUp** ステップの **ClaimsProviderSelection** 要素内で、パスワードを忘れた場合のサブ体験の ID を参照します。

**CombinedSignInAndSignUp** ステップを含む独自のカスタム ユーザー体験を用意していない場合は、次の手順に従って、既存のサインアップまたはサインインのユーザー体験を複製します。 そうでない場合は、次の手順に進みます。

1. スターター パックから *TrustFrameworkBase.xml* ファイルを開きます。
2. `Id="SignUpOrSignIn"` を含む **UserJourney** 要素を見つけ、その内容全体をコピーします。
3. *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を見つけます。 要素が存在しない場合は追加します。
4. 手順 2. でコピーした **UserJourney** 要素の内容全体を貼り付けて、**UserJourneys** 要素の子要素を作成します。
5. ユーザー体験の ID の名前を変更します。 たとえば、「 `Id="CustomSignUpSignIn"` 」のように入力します。

### <a name="connect-the-forgot-password-link-to-the-forgot-password-sub-journey"></a>パスワードを忘れた場合のサブ体験への、パスワードを忘れた場合のリンクに接続する 

ユーザー体験内では、パスワードを忘れた場合のサブ体験を **ClaimsProviderSelection** として表現できます。 この要素を追加して、 **[パスワードを忘れた場合]** リンクを、パスワードを忘れた場合のサブ体験に接続させます。

1. ユーザー体験内で、`Type="CombinedSignInAndSignUp"` または `Type="ClaimsProviderSelection"` を含むオーケストレーション ステップ要素を見つけます。 これは通常、最初のオーケストレーション ステップです。 **ClaimsProviderSelections** 要素には、ユーザーがサインインに使用できる ID プロバイダーの一覧が含まれています。 次の行を追加します。
    
    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ForgotPasswordExchange" />
    ```

1. 次のオーケストレーション ステップで、**ClaimsExchange** 要素を追加します。 次の行を追加します。

    ```xml
    <ClaimsExchange Id="ForgotPasswordExchange" TechnicalProfileReferenceId="ForgotPassword" />
    ```
    
1. 現在のステップと次のステップの間に、次のオーケストレーション ステップを追加します。 追加した新しいオーケストレーション ステップによって、`isForgotPassword` 要求が存在するかどうかが確認されます。 要求が存在する場合は、[パスワード リセットのサブ体験](#add-the-password-reset-sub-journey)が呼び出されます。 

    ```xml
    <OrchestrationStep Order="3" Type="InvokeSubJourney">
      <Preconditions>
        <Precondition Type="ClaimsExist" ExecuteActionsIf="false">
          <Value>isForgotPassword</Value>
          <Action>SkipThisOrchestrationStep</Action>
        </Precondition>
      </Preconditions>
      <JourneyList>
        <Candidate SubJourneyReferenceId="PasswordReset" />
      </JourneyList>
    </OrchestrationStep>
    ```
    
1. 新しいオーケストレーション ステップを追加した後、ステップの番号には、1 から N の整数がスキップされずに順番に付け替えられます。

### <a name="set-the-user-journey-to-be-executed"></a>実行するユーザー体験を設定する

これでユーザー体験を変更または作成したので、 **[証明書利用者]** セクションで、Azure AD B2C がこのカスタム ポリシーに対して実行する体験を指定します。 [RelyingParty](relyingparty.md) 要素内で、**DefaultUserJourney** 要素を見つけます。 **ClaimsProviderSelections** を追加したユーザー体験の ID と一致するように **DefaultUserJourney ReferenceId** を更新します。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="CustomSignUpSignIn" />
  ...
</RelyingParty>
```

### <a name="indicate-the-forgot-password-flow-to-your-app"></a>パスワードを忘れた場合のフローをアプリに示す

アプリケーションで、パスワードを忘れた場合のユーザー フローを介してユーザーがサインインしたかどうかを検出する必要がある場合があります。 **isForgotPassword** 要求には、これを示すブール値が含まれています。これは、アプリケーションに送信されるトークンで発行できます。 必要に応じて、 **[証明書利用者]** セクションで出力要求に `isForgotPassword` を追加します。 アプリケーションで `isForgotPassword` 要求を確認すると、ユーザーがパスワードをリセットしているかどうかを確認できます。

```xml
<RelyingParty>
  <OutputClaims>
    ...
    <OutputClaim ClaimTypeReferenceId="isForgotPassword" DefaultValue="false" />
  </OutputClaims>
</RelyingParty>
```


### <a name="upload-the-custom-policy"></a>カスタム ポリシーをアップロードする

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[ポリシー]** で **[Identity Experience Framework]** を選択します。
1. **[カスタム ポリシーのアップロード]** を選択し、変更した 2 つのポリシー ファイルを次の順序でアップロードします。
   1. 拡張機能ポリシー (例: `TrustFrameworkExtensions.xml`)。
   2. 証明書利用者ポリシー (例: `SignUpSignIn.xml`)。

::: zone-end

### <a name="test-the-password-reset-flow"></a>パスワード リセット フローをテストする

1. テストする (種類が [推奨] である) サインアップまたはサインインのユーザー フローを選択します。
1. **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** を選択します。
1. サインアップまたはサインインのページで、 **[パスワードを忘れた場合]** を選択します。
1. 前に作成したアカウントの電子メール アドレスを確認してから、 **[続行]** を選択します。
1. これで、ユーザーのパスワードを変更できるようになりました。 パスワードを変更し、 **[続行]** を選択します。 トークンが `https://jwt.ms` に返され、表示されます。
1. 返されたトークンの `isForgotPassword` 要求値を調べます。 存在していて true に設定されている場合は、ユーザーがパスワードをリセットしたことを示しています。

## <a name="password-reset-policy-legacy"></a>パスワード リセット ポリシー (レガシ)

[セルフサービス パスワード リセット](#self-service-password-reset-recommended)のエクスペリエンスが有効になっていない場合、このリンクをクリックしても、パスワードのリセット ユーザー フローは自動的にトリガーされません。 代わりに、エラー コード `AADB2C90118` がアプリケーションに返されます。 アプリケーションでは、認証ライブラリを再初期化して Azure AD B2C のパスワードのリセット ユーザー フローを認証することで、このエラー コードを処理する必要があります。

次の図で説明します。

1. ユーザーがアプリケーションから、[サインイン] をクリックします。 アプリが承認要求を開始し、サインインを完了するために ユーザーを Azure AD B2C に移動させます。 承認要求では、**B2C_1_signup_signin** などのサインアップまたはサインインのポリシー名を指定します。
1. ユーザーは **[パスワードを忘れた場合]** リンクを選択します。 Azure AD B2C によって、アプリケーションに AADB2C90118 エラー コードが返されます。
1. アプリケーションはエラー コードを処理し、新しい承認要求を開始します。 承認要求では、**B2C_1_pwd_reset** などのパスワード リセット ポリシーの名前を指定します。

![レガシ パスワード リセットのユーザー フロー](./media/add-password-reset-policy/password-reset-flow-legacy.png)

例を確認するには、[簡単な ASP.NET サンプル](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-DotNet-SUSI)を参照してください。これに、ユーザー フローのリンクの実例が示されています。

::: zone pivot="b2c-user-flow"

### <a name="create-a-password-reset-user-flow"></a>パスワードのリセット ユーザー フローを作成する

アプリケーションのユーザーが自分のパスワードをリセットできるようにするには、パスワードのリセット ユーザー フローを作成します。

1. Azure AD B2C テナントの概要メニューで、 **[ユーザー フロー]** を選択し、 **[新しいユーザー フロー]** を選択します。
1. **[ユーザー フローを作成する]** ページで、 **[パスワード リセット]** ユーザー フローを選択します。 
1. **[バージョンの選択]** で **[Recommended]\(推奨\)** を選択して、 **[作成]** を選択します。
1. ユーザー フローの **[名前]** を入力します。 たとえば、「*passwordreset1*」と入力します。
1. **[ID プロバイダー]** で、 **[Reset password using email address]\(メール アドレスを使用してパスワードをリセットする\)** を有効にします。
1. **[アプリケーション要求]** で **[詳細を表示]** を選択し、アプリケーションに送り返される承認トークンで返される要求を選択します。 たとえば、 **[User's Object ID] (ユーザーのオブジェクト ID)** を選択します。
1. **[OK]** を選択します。
1. **[作成]** を選択して、ユーザー フローを追加します。 *B2C_1* というプレフィックスが自動的に名前に追加されます。

### <a name="test-the-user-flow"></a>ユーザー フローをテストする

1. 作成したユーザー フローを選択してその概要ページを開いてから、 **[ユーザー フローの実行]** を選択します。
1. **[アプリケーション]** で、以前に登録した *webapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローの実行]** をクリックし、前に作成したアカウントの電子メール アドレスを確認したら、 **[続行]** を選択します。
1. ここで、ユーザーのパスワードを変更できます。 パスワードを変更し、 **[続行]** を選択します。 トークンが `https://jwt.ms` に返され、表示されます。

::: zone-end

::: zone pivot="b2c-custom-policy"

### <a name="create-a-password-reset-policy"></a>パスワードのリセット ポリシーを作成する

カスタム ポリシーは、ユーザー体験を定義するために Azure AD B2C テナントにアップロードされる XML ファイルのセットです。 サインアップとサインイン、パスワードのリセット、プロファイル編集ポリシーなど、いくつかの事前に構築されたポリシーを含むスターター パックが用意されています。 詳細については、「[Azure AD B2C でのカスタム ポリシーの概要](custom-policy-get-started.md)」を参照してください。

::: zone-end

## <a name="next-steps"></a>次のステップ

[パスワードの強制リセット](force-password-reset.md)を設定します。
