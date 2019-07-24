---
title: Azure Active Directory B2C の "サインインしたままにする (KMSI)" | Microsoft Docs
description: Azure Active Directory B2C で "サインインしたままにする (KMSI)" を設定する方法を説明します。
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: a8ad5c3091c3c78aa31dbf38eb6b3032e4dc7662
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/07/2019
ms.locfileid: "55870964"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Azure Active Directory B2C で "サインインしたままにする (KMSI)" を有効にする

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure Active Directory (Azure AD) B2C では、Web アプリケーションとネイティブ アプリケーションに対して "サインインしたままにする (KMSI)" 機能を有効にできます。 この機能では、ユーザー名とパスワードの再入力を求めることなく、戻ってきたユーザー にアプリケーションへのアクセスを許可します。 このアクセスは、ユーザーがサインアウトすると失効します。 

公共のコンピューターではこのオプションを有効にしないでください。 

![[サインインしたままにする] の有効化](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>前提条件

ローカル アカウントのサインアップとサインインを許可するように構成されている Azure AD B2C テナント。 テナントがない場合は、「[チュートリアル: Azure Active Directory B2C テナントを作成する](tutorial-create-tenant.md)」の手順に従って作成できます。

## <a name="add-a-content-definition-element"></a>コンテンツ定義要素を追加する 

拡張ファイルの **BuildingBlocks** 要素の下に、**ContentDefinitions** 要素を追加します。 

1. **ContentDefinitions** 要素の下に、識別子を `api.signuporsigninwithkmsi` に設定した **ContentDefinition** 要素を追加します。
2. **ContentDefinition** 要素の下に、**LoadUri**、**RecoveryUri**、**DataUri** の各要素を追加します。 **DataUri** 要素の `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` の値は、サインイン ページに KMSI チェック ボックスを表示する、マシンが理解できる識別子です。 この値は変更できません。 

    ```XML
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsigninwithkmsi">
          <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
          <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
          <Metadata>
            <Item Key="DisplayName">Signin and Signup</Item>
          </Metadata>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>                       
    ```

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>ローカル アカウントのサインイン クレーム プロバイダーを追加する  

ポリシーの拡張ファイル内にある **ClaimsProvider** 要素を使用して、ローカル アカウント サインインをクレーム プロバイダーとして定義できます。

1. 作業ディレクトリから *TrustFrameworkExtensions.xml* ファイルを開きます。 
2. **ClaimsProviders** 要素を見つけます。 存在しない場合は、ルート要素の下に追加します。 [スターター パック](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)には、ローカル アカウント サインイン クレーム プロバイダーが含まれます。 
3. 次の例に示すように **DisplayName** と **TechnicalProfile** を設定した **ClaimsProvider** 要素を追加します。

    ```XML
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Local Account SignIn</DisplayName>
        <TechnicalProfiles>
          <TechnicalProfile Id="login-NonInteractive">
            <Metadata>
              <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
              <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
            </Metadata>
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>アプリケーション識別子をカスタム ポリシーに追加する

アプリケーション識別子を *TrustFrameworkExtensions.xml* ファイルに追加します。

1. *TrustFrameworkExtensions.xml* ファイルで、識別子が `login-NonInteractive` の **TechnicalProfile** 要素と、識別子が `login-NonInteractive-PasswordChange` の **TechnicalProfile** 要素を探し、`IdentityExperienceFrameworkAppId` のすべての値を Identity Experience Framework アプリケーションのアプリケーション識別しに置き換えます ([概要](active-directory-b2c-get-started-custom.md)に関するページの説明をご覧ください)。

    ```
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. `ProxyIdentityExperienceFrameworkAppId` のすべての値を、[概要](active-directory-b2c-get-started-custom.md)で説明されている Proxy Identity Experience Framework アプリケーションのアプリケーション識別子に置き換えます。
3. 拡張ファイルを保存します。

## <a name="create-a-kmsi-enabled-user-journey"></a>KMSI が有効なユーザー体験を作成する

ローカル アカウントに対するサインイン クレーム プロバイダーをユーザー体験に追加します。 

1. ポリシーの基本ファイルを開きます。 たとえば、*TrustFrameworkBase.xml* などです。
2. **UserJourneys** 要素を探し、識別子 `SignUpOrSignIn` を使用している **UserJourney** 要素の内容全体をコピーします。
3. 拡張ファイルを開きます。 たとえば *TrustFrameworkExtensions.xml* を開き、**UserJourneys** 要素を探します。 要素が存在しない場合は追加します。
4. コピーした **UserJourney** 要素全体を、**UserJourneys** 要素の子として貼り付けます。
5. 新しいユーザー体験の識別子の値を変更します。 たとえば、「 `SignUpOrSignInWithKmsi` 」のように入力します。
6. 最後に、最初のオーケストレーション手順で、**ContentDefinitionReferenceId** の値を `api.signuporsigninwithkmsi` に変更します。 この値を設定すると、ユーザー体験のチェック ボックスが有効になります。 
7. 拡張ファイルを保存してアップロードし、すべての検証が正常に行われることを確認します。

    ```XML
    <UserJourneys>
      <UserJourney Id="SignUpOrSignInWithKmsi">
        <OrchestrationSteps>
          <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
            <ClaimsProviderSelections>
              <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
            </ClaimsProviderSelections>
            <ClaimsExchanges>
              <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="2" Type="ClaimsExchange">
            <Preconditions>
              <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
                <Value>objectId</Value>
                <Action>SkipThisOrchestrationStep</Action>
              </Precondition>
            </Preconditions>
            <ClaimsExchanges>
              <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <!-- This step reads any user attributes that we may not have received when in the token. -->
          <OrchestrationStep Order="3" Type="ClaimsExchange">
            <ClaimsExchanges>
              <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
            </ClaimsExchanges>
          </OrchestrationStep>
          <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
        </OrchestrationSteps>
        <ClientDefinition ReferenceId="DefaultWeb" />
      </UserJourney>
    </UserJourneys>
    ```

## <a name="create-a-relying-party-file"></a>証明書利用者ファイルを作成する

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。

1. 作業ディレクトリに *SignUpOrSignIn.xml* ファイルのコピーを作成して、名前を変更します。 たとえば、*SignUpOrSignInWithKmsi.xml* などとします。
2. 新しいファイルを開き、**TrustFrameworkPolicy** の **PolicyId** 属性を一意の値で更新します。 これがポリシーの名前になります。 たとえば、「 `SignUpOrSignInWithKmsi` 」のように入力します。
3. **DefaultUserJourney** 要素の **ReferenceId** 属性を、作成した新しいユーザー体験の識別子と一致するように変更します。 たとえば、「 `SignUpOrSignInWithKmsi` 」のように入力します。

    KMSI は、最初の子要素として **SingleSignOn**、**SessionExpiryType**、および **SessionExpiryInSeconds** を持つ **UserJourneyBehaviors** 要素を使用して構成されます。 **KeepAliveInDays** 属性は、ユーザーがサインインしている期間を制御します。 次の例では、KMSI セッションは、ユーザーがサイレント認証を実行する頻度に関係なく `7` 日後に自動的に期限が切れます。 **KeepAliveInDays** の値を `0` に設定すると、KMSI 機能がオフになります。 この値の既定値は `0` です。 **SessionExpiryType** の値が `Rolling` である場合は、ユーザーがサイレント認証を実行するたびに KMSI セッションが `7` 日延長されます。  `Rolling` を選択する場合は、日数を最小限にしておく必要があります。 

    **SessionExpiryInSeconds** の値は、SSO セッションの有効期限の時間を表します。 この値は、KMSI のセッションが期限切れかどうかを確認するために、Azure AD B2C によって内部的に使用されます。 **KeepAliveInDays** の値は、Web ブラウザーでの SSO Cookie の Expires/Max-Age の値を決定します。 **SessionExpiryInSeconds** とは異なり、**KeepAliveInDays** はブラウザーを閉じるときに Cookie がクリアされるのを防ぐために使用されます。 ユーザーは、SSO セッション Cookie が存在し (**KeepAliveInDays** によって制御)、有効期限 (**SessionExpiryInSeconds** によって制御) が切れていない場合にのみ、自動的にサインインできます。 
    
    ユーザーがサインアップおよびサインイン ページで **[サインインしたままにする]** を有効にしていない場合は、**SessionExpiryInSeconds** で指定された時間が経過するか、ブラウザーを閉じると、セッションの有効期限が切れます。 ユーザーが **[サインインしたままにする]** を有効にしている場合は、**KeepAliveInDays** の値が **SessionExpiryInSeconds** の値より優先され、セッションの有効期限が示されます。 ユーザーがブラウザーを閉じて再度開いた場合でも、**KeepAliveInDays** の時間内であれば、引き続き自動的にサインインできます。 次の例に示すように、**KeepAliveInDays** の値は比較的長い期間 (7 日間) に設定できますが、**SessionExpiryInSeconds** の値は短期間 (1200 秒) に設定することをお勧めします。

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
        <SessionExpiryType>Absolute</SessionExpiryType>
        <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
      </UserJourneyBehaviors>
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="displayName" />
          <OutputClaim ClaimTypeReferenceId="givenName" />
          <OutputClaim ClaimTypeReferenceId="surname" />
          <OutputClaim ClaimTypeReferenceId="email" />
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

4. 変更内容を保存し、ファイルをアップロードします。
5. アップロードしたカスタムのポリシーをテストするには、Azure portal でポリシー ページに移動し、**[今すぐ実行]** を選択します。

[こちら](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in)でサンプル ポリシーを見つけることができます。








