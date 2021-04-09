---
title: セッションの動作を構成する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C でセッションの動作を構成する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/04/2021
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
zone_pivot_groups: b2c-policy-type
ms.openlocfilehash: 3a3cdb93ee4cbf4a2e15540b9daf78b6c231d393
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579741"
---
# <a name="configure-session-behavior-in-azure-active-directory-b2c"></a>Azure Active Directory B2C でセッションの動作を構成する

[!INCLUDE [active-directory-b2c-choose-user-flow-or-custom-policy](../../includes/active-directory-b2c-choose-user-flow-or-custom-policy.md)]

シングル サインオン (SSO) によって、ユーザーが Azure Active Directory B2C (Azure AD B2C) のアプリケーションにサインインするときのセキュリティと利便性が向上します。 この記事では、Azure AD B2C で使用されるシングル サインオンの方法について説明します。ポリシーを構成するときに最適な SSO 方法を選択するのに役立ちます。

シングル サインオンを使用すると、ユーザーは 1 つのアカウントで 1 回サインインするだけで、複数のアプリケーションにアクセスできます。 アプリケーションは、Web、モバイル、シングル ページのどのアプリケーションでもよく、プラットフォームまたはドメイン名には関係ありません。

ユーザーが初めてアプリケーションにサインインしたときに、Azure AD B2C によって Cookie ベースのセッションが保持されます。 それ以降の認証要求では、Azure AD B2C によって Cookie ベースのセッションが読み取られて検証され、アクセス トークンが発行されます。ユーザーにサインインを求めるメッセージが再び表示されることはありません。 Cookie ベースのセッションが有効期限切れまたは無効になった場合、ユーザーはもう一度サインインするように求められます。  

## <a name="prerequisites"></a>前提条件

[!INCLUDE [active-directory-b2c-customization-prerequisites](../../includes/active-directory-b2c-customization-prerequisites.md)]

## <a name="azure-ad-b2c-session-overview"></a>Azure AD B2C セッションの概要

Azure AD B2C との統合には、次の 3 種類の SSO セッションが含まれます。

- **Azure AD B2C** - Azure AD B2C によって管理されるセッション
- **フェデレーション ID プロバイダー** - ID プロバイダー (Facebook、Salesforce、Microsoft アカウントなど) によって管理されるセッション
- **アプリケーション** - Web アプリケーション、モバイル アプリケーション、またはシングル ページ アプリケーションによって管理されるセッション

![SSO セッション](media/session-behavior/sso-session-types.png)

### <a name="azure-ad-b2c-session"></a>Azure AD B2C のセッション 

ユーザーがローカル アカウントまたはソーシャル アカウントでの認証に成功すると、Azure AD B2C によって Cookie ベースのセッションがユーザーのブラウザーに保存されます。 Cookie は、Azure AD B2C テナントのドメイン名 (`https://contoso.b2clogin.com` など) の下に格納されます。

ユーザーが最初にフェデレーション アカウントを使用してサインインした後、セッション時間枠 (Time to Live (TTL)) の間に同じアプリまたは別のアプリにサインインすると、Azure AD B2C によってフェデレーション ID プロバイダーから新しいアクセス トークンの取得が試みられます。 フェデレーション ID プロバイダー セッションが有効期限切れまたは無効になった場合、ユーザーはフェデレーション ID プロバイダーから資格情報の入力を求められます。 セッションがまだアクティブになっている場合は (または、ユーザーがフェデレーション アカウントではなくローカル アカウントを使用してサインインしている場合)、Azure AD B2C によってユーザーが承認され、それ以上のプロンプトは表示されません。

セッションの TTL や、Azure AD B2C によりポリシーやアプリケーション間でセッションが共有される方法など、セッションの動作を構成できます。

### <a name="federated-identity-provider-session"></a>フェデレーション ID プロバイダー セッション

ソーシャルまたはエンタープライズ ID プロバイダーにより、独自のセッションが管理されます。 Cookie は、ID プロバイダーのドメイン名 (`https://login.salesforce.com` など) の下に格納されます。 Azure AD B2C では、フェデレーション ID プロバイダーのセッションは制御されません。 代わりに、セッションの動作はフェデレーション ID プロバイダーによって決定されます。 

以下のシナリオについて考えてみます。

1. ユーザーが Facebook にサインインして、フィードを確認します。
2. その後、ユーザーがアプリケーションを開き、サインイン プロセスを開始します。 アプリケーションでは、ユーザーを Azure AD B2C にリダイレクトして、サインイン プロセスを完了します。
3. Azure AD B2C のサインアップ ページまたはサインイン ページで、ユーザーは Facebook アカウントでのサインインを選択します。 ユーザーは Facebook にリダイレクトされます。 Facebook にアクティブなセッションがある場合、ユーザーは資格情報の入力を求められることはなく、Facebook のトークンを使用して Azure AD B2C に直ちにリダイレクトされます。

### <a name="application-session"></a>アプリケーション セッション

Web アプリケーション、モバイル アプリケーション、またはシングル ページ アプリケーションは、OAuth アクセス、ID トークン、または SAML トークンによって保護できます。 ユーザーがアプリの保護されたリソースにアクセスしようとすると、アプリケーション側にアクティブなセッションがあるかどうかが、アプリによって確認されます。 アプリのセッションがない場合、またはセッションの有効期限が切れている場合、アプリによりユーザーは Azure AD B2C のサインイン ページに移動されます。

アプリケーションのセッションは、アプリケーション ドメイン名 (`https://contoso.com` など) の下に格納されている Cookie ベースのセッションでもかまいません。 モバイル アプリケーションでは、セッションの格納方法が異なる場合がありますが、同様のアプローチが使用されます。

## <a name="configure-azure-ad-b2c-session-behavior"></a>Azure AD B2C セッションの動作を構成する

次のような Azure AD B2C セッションの動作を構成できます。

- **[Web アプリのセッションの有効期間 (分)]** - 認証が成功した後で、ユーザーのブラウザーに Azure AD B2C セッション Cookie が格納されている時間の長さです。 セッションの有効期間は最大 24 時間まで設定できます。

- **[Web アプリのセッション タイムアウト]** - セッションの有効期間の設定または "サインインしたままにする" (KMSI) 設定によってセッションがどのように拡張されるかが示されます。
  - **[ローリング]** - ユーザーが Cookie ベースの認証 (既定) を実行するたびにセッションが延長されることを示します。
  - **[絶対]** - 指定した時間が経過した後、再認証がユーザーに強制されることを示します。

- **[シングル サインオン構成]** - Azure AD B2C のセッションは、次のスコープで構成できます。
  - **[テナント]** - この設定が既定値です。 この設定を使用すると、B2C テナント内の複数のアプリケーションとユーザー フローで同じユーザー セッションを共有できます。 たとえば、ユーザーがあるアプリケーションにサインインすると、そのユーザーは別のアプリケーションにもシームレスにサインインしてアクセスできます。
  - **[アプリケーション]** - この設定により、ユーザー セッションをあるアプリケーションに対して、他のアプリケーションとは独立に排他的に維持できます。 たとえば、ユーザーが Contoso Groceries に既にサインインしているかどうかに関係なく、Contoso Pharmacy にサインインできるようにする場合は、この設定を使用できます。
  - **[ポリシー]** - この設定により、ユーザー セッションをあるポリシーに対して、そのユーザー フローを使用するアプリケーションとは独立に排他的に維持できます。 たとえば、あるユーザーが既にサインインし、多要素認証 (MFA) の手順を完了している場合は、ユーザー フローに関連付けられたセッションが期限切れになっていない限り、そのユーザーは複数のアプリケーションのよりセキュリティの高い部分にアクセスできます。
  - **[抑制]** - この設定は、ユーザー フローを実行するたびに、ユーザーにユーザー体験全体を強制的に実行させます。
- **[サインインしたままにする] (KMSI)**  - 永続的な Cookie を使用することにより、セッションの有効期間が延長されます。 この機能が有効になっており、これをユーザーが選択した場合は、ユーザーがブラウザーを閉じて再び開いても、セッションはアクティブのままです。 セッションは、ユーザーがサインアウトしたときにのみ取り消されます。サインインしたままにする機能 (KMSI 機能) は、ローカル アカウントでのサインインにのみ適用されます。 KMSI 機能は、セッションの有効期間よりも優先されます。

::: zone pivot="b2c-user-flow"

セッションの動作を構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. あらかじめ作成しておいたユーザー フローを開きます。
1. **[プロパティ]** を選択します。
1. **[Web アプリのセッションの有効期間 (分)]** 、 **[Web アプリのセッション タイムアウト]** 、 **[シングル サインオン構成]** 、 **[ログアウト要求に ID トークンが必要]** を必要に応じて構成します。
1. **[保存]** をクリックします。

::: zone-end

::: zone pivot="b2c-custom-policy"

セッションの動作と SSO の構成を変更するには、[RelyingParty](relyingparty.md) 要素の内部に **UserJourneyBehaviors** 要素を追加します。  **UserJourneyBehaviors** 要素は、**DefaultUserJourney** の直後にする必要があります。 **UserJourneyBehavors** 要素は、次の例のようになります。

```xml
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
::: zone-end

## <a name="enable-keep-me-signed-in-kmsi"></a>"サインインしたままにする (KMSI)" を有効にする

KMSI 機能は、Azure AD B2C ディレクトリでローカル アカウントを持つ Web 利用ユーザーとネイティブ アプリケーションに対して有効にできます。 この機能を有効にし、ユーザーがサインインしたままにすることを選択すると、ブラウザーを閉じた後もセッションのアクティブ状態が続きます。 その後でブラウザーを再度開いても、ユーザー名とパスワードの再入力は求められません。 このアクセスは、ユーザーがサインアウトすると失効します。

![[サインインしたままにする] チェックボックスを示すサインアップ サインイン ページの例](./media/session-behavior/keep-me-signed-in.png)


::: zone pivot="b2c-user-flow"

KMSI は、個々のユーザー フロー レベルで構成できます。 ユーザー フローに対して KMSI を有効にする前に、次の点を考慮してください。

- KMSI は、**推奨** バージョンのサインアップ/サインイン (SUSI)、サインイン、およびプロファイル編集ユーザー フローに対してのみサポートされています。 現在 **Standard** または **レガシー プレビュー (v2)** バージョンのユーザー フローを使用しており、この状況で KMSI を有効にするには、これらのユーザー フローの **推奨** バージョンを新たに作成する必要があります。
- KMSI は、パスワード リセット フローまたはサインアップ ユーザー フローではサポートされていません。
- テナント内のすべてのアプリケーションに対して KMSI を有効にする場合は、テナント内のすべてのユーザー フローに対して KMSI を有効にすることをお勧めします。 セッション中に複数のポリシーがユーザーに提示され、KMSI が有効になっていないユーザーが、セッションから KMSI Cookie を削除する可能性があるためです。
- 公共のコンピューターで KMSI を有効にすることはできません。

### <a name="configure-kmsi-for-a-user-flow"></a>ユーザー フロー用に KMSI を構成する

ユーザー フローで KMSI を有効にするには、次の手順を実行します。

1. [Azure portal](https://portal.azure.com) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部のメニューにある  **[ディレクトリ + サブスクリプション]**  フィルターを選択し、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
3. Azure portal の左上隅にある  **[すべてのサービス]**  を選択してから、 **[Azure AD B2C]** を検索して選択します。
4.  **[ユーザー フロー (ポリシー)]** を選択します。
5. あらかじめ作成しておいたユーザー フローを開きます。
6.  **[プロパティ]** を選択します。

7.  **[セッションの動作]** で **[[セッションにサインインしたままにする] を有効にする]** を選択します。 **[セッションにサインインしたままにする (日数)]** の横に、1 から 90 までの値を入力して、セッションを開いたままにする日数を指定します。


   ![[セッションにサインインしたままにする] を有効にする](media/session-behavior/enable-keep-me-signed-in.png)

::: zone-end

::: zone pivot="b2c-custom-policy"

公共のコンピューターではこのオプションを有効にしないでください。

### <a name="configure-the-page-identifier"></a>ページ識別子を設定する

KMSI を有効にするには、コンテンツ定義の `DataUri` 要素を [ページ識別子](contentdefinitions.md#datauri) `unifiedssp` に設定し、[ページ バージョン](page-layout.md)を *1.1.0* 以上に設定します。

1. ポリシーの拡張ファイルを開きます。 たとえば、<em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em>です。 この拡張ファイルは、カスタム ポリシー スターター パックに含まれているポリシー ファイルの 1 つであり、[カスタム ポリシーの概要](custom-policy-get-started.md)に関するページの前提条件の中で、取得済みになっている必要があります。
1. **BuildingBlocks** 要素を検索します。 要素が存在しない場合は追加します。
1. **ContentDefinitions** 要素をポリシーの **BuildingBlocks** 要素に追加します。

    カスタム ポリシーは次のコード スニペットのようになります。

    ```xml
    <BuildingBlocks>
      <ContentDefinitions>
        <ContentDefinition Id="api.signuporsignin">
          <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        </ContentDefinition>
      </ContentDefinitions>
    </BuildingBlocks>
    ```

### <a name="add-the-metadata-to-the-self-asserted-technical-profile"></a>メタデータをセルフアサート技術プロファイルに追加する

サインアップおよびサインイン ページに KMSI チェック ボックスを追加するには、`setting.enableRememberMe` メタデータを [true] に設定します。 拡張ファイルで SelfAsserted-LocalAccountSignin-Email 技術プロファイルを上書きします。

1. ClaimsProviders 要素を見つけます。 要素が存在しない場合は追加します。
1. ClaimsProviders 要素に次のクレーム プロバイダーを追加します。

```xml
<ClaimsProvider>
  <DisplayName>Local Account</DisplayName>
  <TechnicalProfiles>
    <TechnicalProfile Id="SelfAsserted-LocalAccountSignin-Email">
      <Metadata>
        <Item Key="setting.enableRememberMe">True</Item>
      </Metadata>
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

1. 拡張ファイルを保存します。

### <a name="configure-a-relying-party-file"></a>証明書利用者ファイルを設定する

作成したユーザー体験を開始する証明書利用者 (RP) ファイルを更新します。 keepAliveInDays パラメーターを使用すると、サインインしたままにする (KMSI) セッションの Cookie を保持する期間を構成できます。 たとえば、値を 30 に設定した場合、KMSI セッションの Cookie は 30 日間保持されます。 値の範囲は 1 日から 90 日です。

1. カスタム ポリシー ファイルを開きます。 たとえば、*SignUpOrSignin.xml* などです。
1. まだ存在しない場合は、子ノード `<UserJourneyBehaviors>` を `<RelyingParty>` ノードに追加します。 `<DefaultUserJourney ReferenceId="User journey Id" />` の直後に配置する必要があります。たとえば、`<DefaultUserJourney ReferenceId="SignUpOrSignIn" />` のようにします。
1. 次のノードを `<UserJourneyBehaviors>` 要素の子として追加します。

    ```xml
    <UserJourneyBehaviors>
      <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
      <SessionExpiryType>Absolute</SessionExpiryType>
      <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
    </UserJourneyBehaviors>
    ```

次の例に示すように、KeepAliveInDays の値は比較的長い期間 (30 日間) に設定できますが、SessionExpiryInSeconds の値は短期間 (1200 秒) に設定することをお勧めします。

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <UserJourneyBehaviors>
    <SingleSignOn Scope="Tenant" KeepAliveInDays="30" />
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
      <OutputClaim ClaimTypeReferenceId="identityProvider" />
      <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
```

::: zone-end

## <a name="sign-out"></a>サインアウト

ユーザーをアプリケーションからサインアウトさせるときは、アプリケーションの Cookie を消去する、あるいはユーザーとのセッションを終了するだけでは十分ではありません。 サインアウトさせるには、ユーザーを Azure AD B2C にリダイレクトする必要があります。そうしないと、ユーザーは資格情報を再入力しなくても、アプリケーションに対する再認証を行うことができる場合があります。

サインアウト要求時に、Azure AD B2C では次のことが行われます。

1. Azure AD B2C の Cookie ベースのセッションが無効にされます。
::: zone pivot="b2c-user-flow"
2. フェデレーション ID プロバイダーからのサインアウトが試みられます
::: zone-end
::: zone pivot="b2c-custom-policy"
3. フェデレーション ID プロバイダーからのサインアウトが試みられます。
   - OpenID Connect - ID プロバイダーの既知の構成エンドポイントで `end_session_endpoint` の場所が指定されている場合。 サインアウト要求で `id_token_hint` パラメーターが渡されません。 フェデレーション ID プロバイダーでこのパラメーターが必要な場合、サインアウト要求は失敗します。
   - OAuth2 - [ID プロバイダーのメタデータ](oauth2-technical-profile.md#metadata)に `end_session_endpoint` の場所が含まれている場合。
   - SAML - [ID プロバイダーのメタデータ](identity-provider-generic-saml.md)に `SingleLogoutService` の場所が含まれている場合。
4. 必要に応じて、他のアプリケーションからのサインアウトが行われます。 詳しくは、「[シングル サインアウト](#single-sign-out)」セクションをご覧ください。

> [!NOTE]
> ID プロバイダーの技術プロファイル メタデータ `SingleLogoutEnabled` を `false` に設定することによって、フェデレーション ID プロバイダーからのサインアウトを無効にできます。
::: zone-end

サインアウトにより、Azure AD B2C でのユーザーのシングル サインオン状態はクリアされますが、ユーザーはソーシャル ID プロバイダーのセッションからサインアウトされない場合があります。 ユーザーは、その後のサインインで同じ ID プロバイダーを選択した場合、資格情報を入力しなくても再び認証される可能性があります。 ユーザーがアプリケーションからサインアウトしようとする場合、そのユーザーは必ずしも自分の Facebook アカウントからサインアウトしようとしているとは限りません。 ただし、ローカル アカウントを使用している場合、ユーザーのセッションは正常に終了します。

::: zone pivot="b2c-custom-policy"

### <a name="single-sign-out"></a>シングル サインアウト 

Azure AD B2C のサインアウト エンドポイントにユーザーをリダイレクトすると (OAuth2 と SAML プロトコルの両方)、Azure AD B2C によってユーザーのセッションがブラウザーからクリアされます。 ただし、ユーザーは認証に Azure AD B2C を使用する他のアプリケーションにサインインしたままになることがあります。 それらのアプリケーションでユーザーを同時にサインアウトできるように、Azure AD B2C では、ユーザーが現在サインインしているすべてのアプリケーションの登録済み `LogoutUrl` に、HTTP GET 要求が送信されます。

アプリケーションは、ユーザーを識別するすべてのセッションを消去し、`200` 応答を返すことで、この要求に応答する必要があります。 アプリケーションでシングル サインアウトをサポートする場合は、アプリケーションのコードで `LogoutUrl` を実装する必要があります。 

シングル サインアウトをサポートするには、JWT と SAML の両方のトークン発行者技術プロファイルで次を指定する必要があります。

- プロトコル名 (`<Protocol Name="OpenIdConnect" />` など)
- セッション技術プロファイルへの参照 (`UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />` など)。

次の例は、シングル サインアウトでの JWT と SAML のトークン発行者を示しています。

```xml
<ClaimsProvider>
  <DisplayName>Local Account SignIn</DisplayName>
  <TechnicalProfiles>
    <!-- JWT Token Issuer -->
    <TechnicalProfile Id="JwtIssuer">
      <DisplayName>JWT token Issuer</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputTokenFormat>JWT</OutputTokenFormat>
      ...    
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-OAuth-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for OIDC based tokens -->
    <TechnicalProfile Id="SM-OAuth-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.OAuthSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>

    <!--SAML token issuer-->
    <TechnicalProfile Id="Saml2AssertionIssuer">
      <DisplayName>SAML token issuer</DisplayName>
      <Protocol Name="SAML2" />
      <OutputTokenFormat>SAML2</OutputTokenFormat>
      ...
      <UseTechnicalProfileForSessionManagement ReferenceId="SM-Saml-issuer" />
    </TechnicalProfile>

    <!-- Session management technical profile for SAML based tokens -->
    <TechnicalProfile Id="SM-Saml-issuer">
      <DisplayName>Session Management Provider</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.SSO.SamlSSOSessionProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
    </TechnicalProfile>
  </TechnicalProfiles>
</ClaimsProvider>
```

::: zone-end

### <a name="secure-your-logout-redirect"></a>ログアウトのリダイレクトをセキュリティで保護する

ログアウト後、ユーザーは、アプリケーションに対して指定されている応答 URL に関係なく、`post_logout_redirect_uri` パラメーターに指定された URI にリダイレクトされます。 ただし、有効な `id_token_hint` が渡され、 **[ログアウト要求に ID トークンが必要]** が有効になっている場合、Azure AD B2C では、リダイレクトの実行前に、`post_logout_redirect_uri` の値がいずれかのアプリケーションの構成済みのリダイレクト URI と一致するかどうかが検証されます。 一致する応答 URL がアプリケーションで構成されていない場合は、エラー メッセージが表示され、ユーザーはリダイレクトされません。 

::: zone pivot="b2c-user-flow"

ログアウト要求で ID トークンを求めるようにするには、以下の手順に従います。

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[ユーザー フロー]** を選択します。
1. あらかじめ作成しておいたユーザー フローを開きます。
1. **[プロパティ]** を選択します。
1. **[ログアウト要求に ID トークンが必要]** を有効にします。
1. **[Azure AD B2C]** に戻ります。
1. **[アプリの登録]** を選択してから、お客様のアプリケーションを選択します。
1. **[認証]** を選択します。
1. **[ログアウト URL]** テキスト ボックスに、ログアウト後のリダイレクト URI を入力し、 **[保存]** を選択します。

::: zone-end

::: zone pivot="b2c-custom-policy"

ログアウト要求で ID トークンを要求するには、[RelyingParty](relyingparty.md) 要素内に **UserJourneyBehaviors** 要素を追加します。 次に、**SingleSignOn** 要素の **EnforceIdTokenHintOnLogout** を `true` に設定します。 **UserJourneyBehavors** 要素は、次の例のようになります。

```xml
<UserJourneyBehaviors>
  <SingleSignOn Scope="Tenant" EnforceIdTokenHintOnLogout="true"/>
</UserJourneyBehaviors>
```

::: zone-end

アプリケーション ログアウト URL を構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用の Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択してから、アプリケーションを選択します。
1. **[認証]** を選択します。
1. **[ログアウト URL]** テキスト ボックスに、ログアウト後のリダイレクト URI を入力し、 **[保存]** を選択します。

## <a name="next-steps"></a>次のステップ

- [Azure AD B2C でトークンを構成する](configure-tokens.md)方法を確認します。
