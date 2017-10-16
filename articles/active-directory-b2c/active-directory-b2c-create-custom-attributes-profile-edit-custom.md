---
title: "Azure Active Directory B2C: カスタム ポリシーに独自の属性を追加し、プロファイルの編集で使用する | Microsoft Docs"
description: "拡張プロパティ、カスタム属性を使用し、ユーザー インターフェイスにそれらを含めるチュートリアル"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: f3e4eb6fedf850dbb827fd2a10593249d2f17ef1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: カスタム プロファイル編集ポリシーのカスタム属性の作成と使用

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

この記事では、Azure AD B2C ディレクトリにカスタム属性を作成し、プロファイルの編集ユーザー体験でこの新しい属性をカスタム要求として使用します。

## <a name="prerequisites"></a>前提条件

[カスタム ポリシーの概要](active-directory-b2c-get-started-custom.md)に関する記事の手順を完了します。

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>カスタム属性を使用して、カスタム ポリシーを使用している Azure Active Directory B2C の顧客に関する情報を収集する
Azure Active Directory (Azure AD) B2C ディレクトリには、組み込みの属性セット (名、姓、市区町村、郵便番号、userPrincipalName など) が用意されています。多くの場合、独自の属性を作成する必要があります。  For example:
* 顧客向けアプリケーションでは、"LoyaltyNumber" などの属性を保持する必要があります。
* ID プロバイダーは、"uniqueUserGUID" など、保存する必要がある一意のユーザー識別子を保持しています。
* カスタム ユーザー体験では、"migrationStatus" など、ユーザーの状態を保持する必要があります。

Azure AD B2C では、各ユーザー アカウントで保存される属性セットを拡張できます。 また、 [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md)を使用してこれらの属性を読み書きすることもできます。

拡張プロパティは、ディレクトリ内のユーザー オブジェクトのスキーマを拡張します。  用語の拡張機能プロパティ、カスタム属性、およびカスタム要求では、この記事のコンテキストと同じ内容を参照します。名前は、コンテキスト (アプリケーション、オブジェクト、ポリシー) によって異なります。

拡張プロパティは、ユーザーに関するデータを保持できる場合でも、アプリケーション オブジェクトにしか登録できません。 プロパティは、アプリケーションに関連付けられます。 アプリケーション オブジェクトには、拡張プロパティを登録するための書き込みアクセス権が付与されている必要があります。 1 つのオブジェクトに対して、100 個の拡張プロパティ (すべての型とすべてのアプリケーションでの合計) を書き込むことができます。 拡張プロパティは、対象のディレクトリ タイプに追加され、Azure AD B2C ディレクトリ テナント内ですぐに利用できる状態になります。
アプリケーションを削除すると、このような拡張プロパティも、それらに含まれている、すべてのユーザーに関するデータと共に削除されます。 拡張プロパティは、アプリケーションによって削除されると、対象ディレクトリ オブジェクトで削除され、その値も削除されます。

拡張プロパティは、テナント内の登録されたアプリケーションのコンテキストにのみ存在します。 そのアプリケーションのオブジェクト ID は、それを使用する TechnicalProfile に含まれている必要があります。

>[!NOTE]
>Azure AD B2C ディレクトリには、通常、`b2c-extensions-app` という名前の Web アプリが含まれています。  このアプリケーションは、主に、Azure Portal で作成されたカスタム要求用の B2C 組み込みポリシーによって使用されます。  このアプリケーションを使用して拡張プロパティを B2C カスタム ポリシーに登録することは、上級ユーザーのみにお勧めします。  これを行うための手順については、この記事の「次のステップ」のセクションをご覧ください。


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>拡張プロパティを格納するための新しいアプリケーションを作成する

1. 閲覧セッションを開き、[Azure Portal](https://portal.azure.com) に移動して、構成する B2C ディレクトリの管理者資格情報でサインインします。
1. 左側のナビゲーション メニューで、**[Azure Active Directory]** をクリックします。 [その他のサービス >] をクリックしないと表示されない場合があります。
1. **[アプリの登録]** を選択し、**[新しいアプリケーションの登録]** をクリックします。
1. 以下の推奨エントリを指定します。
  * Web アプリケーションの名前を指定します: **WebApp-GraphAPI-DirectoryExtensions**
  * アプリケーションの種類: Web アプリ/API
  * サインオン URL: https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions
1. [**作成] を選択します。 正常に完了したことが**通知**に表示されます。
1. 新しく作成された Web アプリケーション **WebApp-GraphAPI-DirectoryExtensions** を選択します。
1. 設定として **[必要なアクセス許可]** を選択します。
1. API として **Windows Azure Active Directory** を選択します。
1. [アプリケーションのアクセス許可] で **[ディレクトリ データの読み取りと書き込み]** チェック ボックスをオンにし、**[保存]** を選択します。
1. **[アクセス許可の付与]** を選択し、確認のために **[はい]** をクリックします。
1. [WebApp-GraphAPI-DirectoryExtensions] > [設定] > [プロパティ] の順に移動し、次の識別子をクリップボードにコピーして保存します。
*  **アプリケーション ID**。 例: `103ee0e6-f92d-4183-b576-8c3739027780`
* **オブジェクト ID**。 例: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>カスタム ポリシーを変更して ApplicationObjectId を追加する

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

>[!NOTE]
><TechnicalProfile Id="AAD-Common"> は、その要素が、次の要素を使用することによってすべての Azure Active Directory TechnicalProfile に含まれて再利用されるため、"共通" と呼ばれます。`<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>TechnicalProfile が新しく作成された拡張プロパティに初めて書き込むときに、1 回限りのエラーが発生することがあります。  拡張機能プロパティは、初めて使われるときに作成されます。  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>ユーザー体験で新しい拡張機能プロパティ/カスタム属性を使用する


1. ポリシーの編集ユーザー体験が記述されている証明書利用者 (RP) ファイルを開きます。  初めての場合は、RP-PolicyEdit ファイルの構成済みバージョンを Azure Portal の Azure B2C カスタム ポリシーのセクションから直接ダウンロードすることをお勧めします。  または、ストレージ フォルダーから XML ファイルを開きます。
2. カスタム要求 `loyaltyId` を追加します。  カスタム要求は、`<RelyingParty>` 要素に含めることによって、パラメーターとして UserJourney TechnicalProfile に渡され、アプリケーション用のトークンに含まれます。
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. 要求の定義を、次のように、拡張ポリシー ファイル `TrustFrameworkExtensions.xml` の `<ClaimsSchema>` 要素の内部に追加します。
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. 同じ要求の定義を基本ポリシー ファイル `TrustFrameworkBase.xml` に追加します。  
>`ClaimType` 定義は、通常、基本ファイルと拡張ファイルの両方に追加する必要はありません。ただし、次の手順で基本ファイルの TechnicalProfile に extension_loyaltyId を追加するため、この定義がないと、ポリシー検証で基本ファイルのアップロードが拒否されます。
>TrustFrameworkBase.xml ファイルの "ProfileEdit" という名前のユーザー体験の実行を追跡すると役立つ場合があります。  エディターで同じ名前のユーザー体験を検索し、オーケストレーション手順 5. で TechnicalProfileReferenceID="SelfAsserted-ProfileUpdate" が呼び出されることを確認します。  この TechnicalProfile を検索して調査すると、処理の流れについて理解が深まります。
5. TechnicalProfile "SelfAsserted-ProfileUpdate" で、入力および出力要求として loyaltyId を追加します。
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

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. ディレクトリの現在のユーザーに関する、拡張プロパティ内の要求の値を保持する要求を TechnicalProfile "AAD-UserWriteProfileUsingObjectId" に追加します。
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
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. ユーザーがログインするたびに拡張属性の値を読み取る要求を TechnicalProfile "AAD-UserReadUsingObjectId" に追加します。 ここまで、TechnicalProfile は、ローカル アカウントのフローだけで変更されてきました。  ソーシャル/フェデレーション アカウントのフローで新しい属性が必要な場合は、TechnicalProfile の別のセットを変更する必要があります。 「次のステップ」をご覧ください。

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
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
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>IncludeTechnicalProfile 要素では、この TechnicalProfile に AAD-Common のすべての要素が追加されています。

## <a name="test-the-custom-policy-using-run-now"></a>[今すぐ実行] を使用してカスタム ポリシーをテストする
1. **[Azure AD B2C] ブレード**を開き、**[Identity Experience Framework] > [カスタム ポリシー]** に移動します。
1. アップロードしたカスタム ポリシーを選択し、**[Run now]**(今すぐ実行) ボタンをクリックします。
1. メール アドレスを使用してサインアップできることを確認します。

アプリケーションに送り返される ID トークンには、extension_loyaltyId で始まるカスタム要求として新しい拡張プロパティが含まれます。 例を参照してください。

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

## <a name="next-steps"></a>次のステップ

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>以下に示す TechnicalProfile を変更することで、新しい要求をソーシャル アカウント ログインのフローに追加します。 この 2 つの TechnicalProfile は、ユーザー オブジェクトのロケーターとして alternativeSecurityId を使用してユーザー データの書き込みと読み取りを行うために、ソーシャル/フェデレーション アカウント ログインで使用されます。
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

組み込みポリシーとカスタム ポリシー間で同じ拡張属性を使用します。
ポータルのエクスペリエンスを使用して拡張属性 (カスタム属性とも呼ばれます) を追加すると、これらの属性が、すべての B2C テナントに存在する **b2c-extensions-app を使用して登録されます。  カスタム ポリシーでこれらの拡張属性を使用するには、次の手順に従います。
1. portal.azure.com の B2C テナント内で、**[Azure Active Directory]** に移動し、**[アプリの登録]** を選択します。
2. 自分の **b2c-extensions-app** を検索して選択します。
3. 'Essentials' で、**アプリケーション ID** と**オブジェクト ID** を記録します。
4. これらを、以下のように AAD-Common 技術プロファイル メタデータに含めます。

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

ポータルのエクスペリエンスとの整合性を保つには、カスタム ポリシーで使用する "*前*" に、ポータルの UI を使用してこれらの属性を作成します。  ポータルで属性 "ActivationStatus" を作成するときに、次のようにこの属性を参照する必要があります。

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>リファレンス

* **Technical Profile (TP)** は、エンドポイントの名前、メタデータ、プロトコルを定義し、Identity Experience Framework が実行する必要のある要求の交換の詳細を定義する "*関数*" と見なすことができる要素の種類です。  この "*関数*" がオーケストレーションの手順または別の TechnicalProfile から呼び出されると、InputClaims と OutputClaims が呼び出し元によってパラメーターとして指定されます。


* 拡張プロパティの詳細な取り扱いについては、記事「[ディレクトリ スキーマ拡張機能 | Graph API の概念](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)」を参照してください。

>[!NOTE]
>Graph API の拡張属性には、`extension_ApplicationObjectID_attributename` という規則を使って名前が付けられます。 カスタム ポリシーは extension_attributename として拡張属性を表します。したがって、XML では ApplicationObjectId が省略されます。
