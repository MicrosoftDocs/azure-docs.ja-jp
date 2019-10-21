---
title: Azure Active Directory B2C でカスタム ポリシーに独自の属性を追加する | Microsoft Docs
description: 拡張プロパティおよびカスタム属性を使用し、ユーザー インターフェイスにそれらを含めるチュートリアル。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 82a796a3252a4de6eacabcad45c61c864e963fe0
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71066165"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C:カスタム プロファイル編集ポリシーでカスタム属性を使用する

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure Active Directory B2C (Azure AD B2C) ディレクトリにカスタム属性を作成します。 この新しい属性は、プロファイル編集ユーザー体験でのカスタム要求として使用します。

## <a name="prerequisites"></a>前提条件

「[Azure Active Directory B2C: カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」に記載の手順に従ってください。

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>カスタム属性を使用すると、カスタム ポリシーにより Azure AD B2C の顧客に関する情報が収集されます。
Azure AD B2C ディレクトリには、組み込みの属性セットが付属します。 たとえば、**Given Name**、**Surname**、**City**、**Postal Code**、**userPrincipalName** などがあります。 多くの場合は、次に示したような独自の属性を作成する必要があります。
* 顧客向けアプリケーションは、**LoyaltyNumber** のような属性に対して維持される必要があります。
* ID プロバイダーは、**uniqueUserGUID** のような、保存する必要がある一意のユーザー識別子を保持しています。
* カスタム ユーザー体験は、**migrationStatus** のような、ユーザーの状態に対して維持される必要があります。

Azure AD B2C では、各ユーザー アカウントで保存される属性セットを拡張できます。 また、 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)を使用してこれらの属性を読み書きすることもできます。

拡張プロパティは、ディレクトリ内のユーザー オブジェクトのスキーマを拡張します。 用語の*拡張機能プロパティ*、*カスタム属性*、および*カスタム要求*では、この記事のコンテキストと同じ内容を参照します。 名前は、アプリケーション、オブジェクト、ポリシーなどのコンテキストによって異なります。

拡張プロパティは、ユーザーに関するデータを保持できる場合でも、アプリケーション オブジェクトにしか登録できません。 プロパティは、アプリケーションに関連付けられます。 アプリケーション オブジェクトは、拡張プロパティを登録するための書き込みアクセス権を持っている必要があります。 1 つのオブジェクトに対して、100 個の拡張プロパティ (すべての型とすべてのアプリケーションでの合計) を書き込むことができます。 拡張プロパティは、対象のディレクトリ タイプに追加され、Azure AD B2C ディレクトリ テナント内ですぐに利用できる状態になります。
アプリケーションを削除すると、このような拡張プロパティも、それらに含まれている、すべてのユーザーに関するデータと共に削除されます。 拡張プロパティは、アプリケーションによって削除されると、対象ディレクトリ オブジェクトで削除され、その値も削除されます。

拡張プロパティは、テナント内の登録されたアプリケーションのコンテキストにのみ存在します。 そのアプリケーションのオブジェクト ID は、それを使用する **TechnicalProfile** に含まれている必要があります。

>[!NOTE]
>Azure AD B2C ディレクトリには、通常、`b2c-extensions-app` という名前の Web アプリが含まれています。 このアプリケーションは、主に、Azure Portal で作成されたカスタム要求用の B2C 組み込みポリシーによって使用されます。 このアプリケーションを使用して拡張プロパティを B2C カスタム ポリシーに登録することは、上級ユーザーのみにお勧めします。
手順については、この記事の「**次のステップ**」のセクションをご覧ください。

## <a name="create-a-new-application-to-store-the-extension-properties"></a>拡張プロパティを格納するための新しいアプリケーションを作成する

1. ブラウズ セッションを開いて [Azure Portal](https://portal.azure.com) に移動します。 構成する B2C ディレクトリの管理者資格情報を使用してサインインします。
2. 左側のナビゲーション メニューで、 **[Azure Active Directory]** を選択します。 **[その他のサービス]** をクリックしないと表示されない場合があります。
3. **[アプリの登録]** を選択します。 **[新しいアプリケーションの登録]** を選択します。
4. 次のエントリを指定します。
    * Web アプリケーションの名前: **WebApp-GraphAPI-DirectoryExtensions**。
    * アプリケーションの種類: **Web アプリ/API**。
    * サインオン URL: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**。
5. **作成** を選択します。
6. 新しく作成された Web アプリケーションを選択します。
7. **［設定］**  >  **［必要なアクセス許可］** の順に選択します。
8. API **Windows Azure Active Directory** を選択します。
9. [アプリケーションのアクセス許可] の **[ディレクトリ データの読み取りと書き込み]** チェック ボックスをオンにします。 次に、 **[保存]** を選択します。
10. **[アクセス許可の付与]** を選択し、確認のために **[はい]** をクリックします。
11. 次の識別子をクリップボードにコピーして保存します。
    * **アプリケーション ID**。 例: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **オブジェクト ID**。 例: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>カスタム ポリシーを変更して **ApplicationObjectId** を追加する

「[Azure Active Directory B2C: カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)」の手順に従った場合は、**TrustFrameworkBase.xml**、**TrustFrameworkExtensions.xml**、**SignUpOrSignin.xml**、**ProfileEdit.xml**、**PasswordReset.xml** という名前の[サンプル ファイル](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip)をダウンロードして変更しました。 この手順では、これらのファイルにさらに変更を加えます。

* 次の例のように、**TrustFrameworkBase.xml** ファイルを開いて `Metadata` セクションを追加します。 `ApplicationObjectId` の値には以前に記録したオブジェクト ID、`ClientId` の値には以前に記録したアプリケーション ID を挿入します。

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> **TechnicalProfile** が新しく作成された拡張プロパティに初めて書き込むときに、1 回限りのエラーが発生することがあります。 拡張機能プロパティは、初めて使われるときに作成されます。

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>ユーザー体験で新しい拡張機能プロパティまたはカスタム属性を使用する

1. **ProfileEdit.xml** ファイルを開きます。
2. カスタム要求 `loyaltyId` を追加します。 カスタム要求は、`<RelyingParty>` 要素に含めることで、アプリケーション用のトークンに含まれます。

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. **TrustFrameworkExtensions.xml** ファイルを開き、`<ClaimsSchema>` 要素とその子要素を `BuildingBlocks` 要素に追加します。

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. 同じ `ClaimType` 定義を **TrustFrameworkBase.xml** に追加します。 `ClaimType` 定義は、基本ファイルと拡張ファイルの両方に追加する必要はありません。 しかし、次の手順で基本ファイルの **TechnicalProfile** に `extension_loyaltyId` を追加します。 そのため、この定義がないと、ポリシー検証で基本ファイルのアップロードが拒否されます。 **TrustFrameworkBase.xml** ファイルの **ProfileEdit** という名前のユーザー体験の実行を追跡すると役立つ場合があります。 エディターで同じ名前のユーザー体験を検索します。 オーケストレーション手順 5 で **TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate** が呼び出されることを確認します。 この **TechnicalProfile** を検索して調査すると、処理の流れについて理解が深まります。

5. **TrustFrameworkBase.xml** ファイルを開き、**TechnicalProfile の SelfAsserted-ProfileUpdate** に、入力および出力要求として `loyaltyId` を追加します。

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. **TrustFrameworkBase.xml**ファイルで、`loyaltyId` 要求を **TechnicalProfile の AAD-UserWriteProfileUsingObjectId** に追加します。 この追加により、ディレクトリ内の現在のユーザーに関する拡張プロパティ内の要求の値が保持されます。

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. **TrustFrameworkBase.xml** ファイルで、ユーザーがサインインするたびに拡張属性の値を読み取る `loyaltyId` 要求を **TechnicalProfile の AAD-UserReadUsingObjectId** に追加します。 ここまで、**TechnicalProfile** は、ローカル アカウントのフローだけで変更されてきました。 ソーシャルまたはフェデレーション アカウントのフローで新しい属性が必要な場合は、**TechnicalProfile** の別のセットを変更する必要があります。 「**次のステップ**」セクションを参照してください。

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>カスタム ポリシーをテストする

1. [Azure AD B2C] ブレードを開き、 **[Identity Experience Framework]**  >  **[カスタム ポリシー]** の順に移動します。
1. アップロードしたカスタム ポリシーを選択します。 **[今すぐ実行]** を選択します。
1. メール アドレスを使用してサインアップします。

アプリケーションに送り返される ID トークンには、**extension_loyaltyId** で始まるカスタム要求として新しい拡張プロパティが含まれます。 次の例を参照してください。

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>次の手順

1. **TechnicalProfile** に従って変更することで、ソーシャル アカウントにサインインするフローに新しい要求を追加します。 ソーシャル アカウントおよびフェデレーション アカウントでは、次の 2 つの **TechnicalProfile** を使用してサインインします。 これらは、ユーザー オブジェクトのロケーターとして **alternativeSecurityId** を使用してユーザー データの書き込みおよび読み取りを行います。

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. 組み込みポリシーとカスタム ポリシー間で同じ拡張属性を使用します。 ポータルのエクスペリエンスを使用して拡張属性 (カスタム属性とも呼ばれます) を追加すると、これらの属性が、すべての B2C テナントに存在する **b2c-extensions-app** を使用して登録されます。 カスタム ポリシーで拡張属性を使用するには、次の手順を行います。

   a. portal.azure.com の B2C テナント内で、 **[Azure Active Directory]** に移動し、 **[アプリの登録]** を選択します。
   b. 自分の **b2c-extensions-app** を検索して選択します。
   c. **Essentials** で、**アプリケーション ID** と**オブジェクト ID** を入力します。
   d. これらを、以下のように **AAD-Common** TechnicalProfile メタデータに含めます。

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. ポータルのエクスペリエンスとの整合性を保ちます。 カスタム ポリシーで使用する前に、ポータルの UI を使用してこれらの属性を作成します。 ポータルで属性 **ActivationStatus** を作成するときに、次のようにこの属性を参照する必要があります。

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>リファレンス

拡張プロパティの詳細については、記事「[ディレクトリ スキーマ拡張機能 | Graph API の概念](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions)」を参照してください。

> [!NOTE]
> * **TechnicalProfile** は、エンドポイントの名前、メタデータ、およびプロトコルを定義する要素の種類 (または関数) です。 **TechnicalProfile** には、Identity Experience Framework によって実行される要求の交換が詳細に示されています。 この関数がオーケストレーションの手順または別の **TechnicalProfile** から呼び出されると、**InputClaims** と **OutputClaims** が呼び出し元によってパラメーターとして指定されます。
> * Graph API の拡張属性には、`extension_ApplicationObjectID_attributename` という規則を使って名前が付けられます。
> * カスタム ポリシーでは、拡張属性を **extension_attributename** として参照します。 この参照では、XML での **ApplicationObjectId** が省略されます。
