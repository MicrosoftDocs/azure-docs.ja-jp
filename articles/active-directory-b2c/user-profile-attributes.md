---
title: Azure Active Directory B2C のユーザー プロファイル属性
description: Azure AD B2C ディレクトリのユーザー プロファイルでサポートされているユーザー リソースの種類の属性について説明します。 組み込みの属性、拡張機能、および属性が Microsoft Graph にどのようにマップされるかについて説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 7dfad71d05a882e3a3941a96e12489adb5fb3234
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/09/2021
ms.locfileid: "102500531"
---
# <a name="user-profile-attributes"></a>ユーザー プロファイルの属性

Azure Active Directory (Azure AD) B2C ディレクトリのユーザー プロファイルには、一連の組み込み属性 (名、姓、市区町村、郵便番号、電話番号など) があります。 外部のデータ ストアを必要とせずに、ユーザー プロファイルを独自のアプリケーション データで拡張できます。 

Azure AD B2C ユーザー プロファイルで使用できるほとんどの属性は、Microsoft Graph でもサポートされています。 この記事では、サポートされている Azure AD B2C ユーザー プロファイルの属性について説明します。 また、これらの属性のうち Microsoft Graph でサポートされていないものや、Azure AD B2C で使用すべきではない Microsoft Graph の属性についても説明します。

> [!IMPORTANT]
> 機密性の高い個人データ (アカウントの資格情報、政府の識別番号、カード所有者のデータ、金融口座データ、医療情報、機密性の高い経歴情報など) を保存するために組み込み属性または拡張属性を使用しないでください。

外部システムとの統合も可能です。 たとえば、認証には Azure AD B2C を使用できますが、顧客データの信頼できるソースとしての機能は、外部の顧客関係管理 (CRM) や顧客ロイヤルティ データベースに委任します。 詳細については、[リモート プロファイル](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) ソリューションに関する記事を参照してください。

## <a name="azure-ad-user-resource-type"></a>Azure AD ユーザー リソースの種類

次の表に、Azure AD B2C ディレクトリのユーザー プロファイルでサポートされている[ユーザー リソースの種類](/graph/api/resources/user)の属性の一覧を示します。 各属性について次の情報を提供します。

- Azure AD B2C で使用される属性名 (その後のかっこ内は Microsoft Graph での属性名 (異なる場合))
- 属性のデータ型
- 属性の説明
- 属性を Azure portal で使用できるかどうか
- 属性をユーザー フローで使用できるかどうか
- 属性をカスタム ポリシーの [Azure AD 技術プロファイル](active-directory-technical-profile.md)で使用できるかどうか、およびどのセクション (&lt;InputClaims&gt;、&lt;OutputClaims&gt;、または &lt;PersistedClaims&gt;) で使用できるか

|名前     |種類     |説明|Azure portal|ユーザー フロー|カスタム ポリシー|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|ユーザー アカウントが有効か無効か: アカウントが有効の場合は **true**、それ以外の場合は **false**。|はい|いいえ|Persisted、Output|
|ageGroup        |String|ユーザーの年齢グループ。 指定できる値: null、Undefined、Minor、Adult、NotAdult。|はい|いいえ|Persisted、Output|
|alternativeSecurityId ([Identities](#identities-attribute))|String|外部 ID プロバイダーからの単一ユーザー ID。|いいえ|いいえ|Input、Persisted、Output|
|alternativeSecurityIds ([Identities](#identities-attribute))|代替のセキュリティ ID コレクション|外部 ID プロバイダーからのユーザー ID のコレクション。|いいえ|いいえ|Persisted、Output|
|city            |String|ユーザーのいる市区町村。 最大長 128。|はい|はい|Persisted、Output|
|consentProvidedForMinor|String|未成年について同意が提供されているかどうか。 使用できる値: null、granted、denied、または notRequired。|はい|いいえ|Persisted、Output|
|country         |String|ユーザーがいる国/地域。 例:"US" または "UK"。 最大長 128。|はい|はい|Persisted、Output|
|createdDateTime|DateTime|ユーザー オブジェクトが作成された日付。 読み取り専用。|いいえ|いいえ|Persisted、Output|
|creationType    |String|ユーザー アカウントが Azure Active Directory B2C テナントのローカル アカウントとして作成された場合、値は LocalAccount または nameCoexistence です。 読み取り専用。|いいえ|いいえ|Persisted、Output|
|dateOfBirth     |Date|誕生日。|いいえ|いいえ|Persisted、Output|
|department      |String|ユーザーが勤務する部署の名前。 最大長 64。|はい|いいえ|Persisted、Output|
|displayName     |String|ユーザーの表示名。 最大長 256。|はい|はい|Persisted、Output|
|facsimileTelephoneNumber<sup>1</sup>|String|ユーザーの業務用ファックスの電話番号。|はい|いいえ|Persisted、Output|
|givenName       |String|ユーザーの名。 最大長 64。|はい|はい|Persisted、Output|
|jobTitle        |String|ユーザーの役職。 最大長 128。|はい|はい|Persisted、Output|
|immutableId     |String|オンプレミスの Active Directory から移行されたユーザーに通常使用される識別子。|いいえ|いいえ|Persisted、Output|
|legalAgeGroupClassification|String|法的年齢グループの分類。 読み取り専用で、ageGroup および consentProvidedForMinor プロパティに基づいて計算されます。 使用可能な値: null、minorWithOutParentalConsent、minorWithParentalConsent、minorNoParentalConsentRequired、notAdult、および adult。|はい|いいえ|Persisted、Output|
|legalCountry<sup>1</sup>  |String|国またはリージョン (法的な目的)。|いいえ|いいえ|Persisted、Output|
|mail            |String|ユーザーの SMTP アドレス (例 "bob@contoso.com")。 読み取り専用です。|いいえ|いいえ|Persisted、Output|
|mailNickName    |String|ユーザーのメール エイリアス。 最大長 64。|いいえ|いいえ|Persisted、Output|
|mobile (mobilePhone) |String|ユーザーのメインの携帯電話番号。 最大長 64。|はい|いいえ|Persisted、Output|
|netId           |String|Net ID。|いいえ|いいえ|Persisted、Output|
|objectId        |String|ユーザーの一意識別子であるグローバル一意識別子 (GUID)。 例:12345678-9abc-def0-1234-56789abcde。 読み取り専用で、変更できません。|読み取り専用|はい|Input、Persisted、Output|
|otherMails      |文字列コレクション|ユーザーの他の電子メール アドレスの一覧。 例: ["bob@contoso.com", "Robert@fabrikam.com"]。|はい (連絡用電子メール アドレス)|いいえ|Persisted、Output|
|password        |String|ユーザー作成時のローカル アカウントのパスワード。|いいえ|いいえ|Persisted|
|passwordPolicies     |String|パスワードのポリシー。 コンマで区切られた複数のポリシー名で構成される文字列です。 たとえば、"DisablePasswordExpiration, DisableStrongPassword"。|いいえ|いいえ|Persisted、Output|
|physicalDeliveryOfficeName (officeLocation)|String|ユーザーの勤務先のオフィス所在地。 最大長 128。|はい|いいえ|Persisted、Output|
|postalCode      |String|ユーザーの住所の郵便番号。 郵便番号は、ユーザーの国/地域に固有です。 米国では、この属性に ZIP コードが含まれます。 最大長 40。|はい|いいえ|Persisted、Output|
|preferredLanguage    |String|ユーザーの優先言語。 優先される言語形式は RFC 4646 を基準とします。 名前は、言語に関連付けられた ISO 639 の 2 文字の小文字カルチャ コードと、国または地域に関連付けられた ISO 3166 の 2 文字の大文字サブカルチャ コードの組み合わせです。 例: "en-US" や "es-ES"。|いいえ|いいえ|Persisted、Output|
|refreshTokensValidFromDateTime (signInSessionsValidFromDateTime)|DateTime|この日時より前に発行された更新トークンは無効です。無効な更新トークンを使用して新しいアクセス トークンを取得すると、アプリケーションでエラーが発生します。 この場合、アプリケーションは、承認エンドポイントに対して要求を行うことによって、新しい更新トークンを取得する必要があります。 読み取り専用です。|いいえ|いいえ|出力|
|signInNames ([Identities](#identities-attribute)) |String|ディレクトリ内の任意の種類のローカル アカウント ユーザーの一意のサインイン名。 ローカル アカウントの種類を指定せずにサインイン値でユーザーを取得するには、この属性を使用します。|いいえ|いいえ|入力|
|signInNames.userName ([Identities](#identities-attribute)) |String|ディレクトリ内のローカル アカウント ユーザーの一意のユーザー名。 特定のサインイン ユーザー名でユーザーを作成または取得するには、この属性を使用します。 パッチ操作中に PersistedClaims 単独でこれを指定すると、他の種類の signInNames は削除されます。 新しい種類の signInNames を追加する場合は、既存の signInNames も保持する必要があります。|いいえ|いいえ|Input、Persisted、Output|
|signInNames.phoneNumber ([Identities](#identities-attribute)) |String|ディレクトリ内のローカル アカウント ユーザーの一意の電話番号。 特定のサインイン電話番号でユーザーを作成または取得するには、この属性を使用します。 パッチ操作中に PersistedClaims 単独でこの属性を指定すると、他の種類の signInNames は削除されます。 新しい種類の signInNames を追加する場合は、既存の signInNames も保持する必要があります。|いいえ|いいえ|Input、Persisted、Output|
|signInNames.emailAddress ([Identities](#identities-attribute))|String|ディレクトリ内のローカル アカウント ユーザーの一意のメール アドレス。 特定のサインイン電子メール アドレスでユーザーを作成または取得するには、これを使用します。 パッチ操作中に PersistedClaims 単独でこの属性を指定すると、他の種類の signInNames は削除されます。 新しい種類の signInNames を追加する場合は、既存の signInNames も保持する必要があります。|いいえ|いいえ|Input、Persisted、Output|
|state           |String|ユーザーの住所の都道府県。 最大長 128。|はい|はい|Persisted、Output|
|streetAddress   |String|ユーザーの勤務地の番地。 最大長 1024。|はい|はい|Persisted、Output|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|多要素認証に使用される、ユーザーの 2 次電話番号。|はい|いいえ|Persisted、Output|
|strongAuthenticationEmailAddress<sup>1</sup>|String|ユーザーの SMTP アドレス。 例: "bob@contoso.com"。この属性は、ユーザー名ポリシーでのサインインで、ユーザーの電子メール アドレスを保存するために使用されます。 この電子メール アドレスは、パスワード リセット フローで使用されます。|はい|いいえ|Persisted、Output|
|strongAuthenticationPhoneNumber<sup>2</sup>|String|多要素認証に使用される、ユーザーの 1 次電話番号。|はい|いいえ|Persisted、Output|
|surname         |String|ユーザーの姓。 最大長 64。|はい|はい|Persisted、Output|
|telephoneNumber (businessPhones の最初のエントリ)|String|ユーザーの勤務地の 1 次電話番号。|はい|いいえ|Persisted、Output|
|userPrincipalName    |String|ユーザーのユーザー プリンシパル名 (UPN)。 UPN は、インターネット標準 RFC 822 に基づくユーザーのインターネット形式のログイン名です。 ドメインがテナントの確認済みドメインのコレクションに存在している必要があります。 このプロパティは、アカウントを作成するときに必要です。 変更不可。|いいえ|いいえ|Input、Persisted、Output|
|usageLocation   |String|法的な要請のためにライセンスを割り当てられるユーザーが、各国またはリージョンでサービスを利用できるかどうかを確認するのに必要です。 NULL 値は許可されません。 2 文字の国またはリージョン番号 (ISO 標準 3166)。 例 :"US"、"JP"、"GB"。|はい|いいえ|Persisted、Output|
|userType        |String|ディレクトリ内のユーザー タイプを分類するために使用できる文字列値。 値は Member にする必要があります。 読み取り専用です。|読み取り専用|いいえ|Persisted、Output|
|userState (externalUserState)<sup>3</sup>|String|Azure AD B2B アカウント専用。招待が PendingAcceptance であるか Accepted であるかを示します。|いいえ|いいえ|Persisted、Output|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|UserState プロパティに対する最新の変更のタイムスタンプを示します。|いいえ|いいえ|Persisted、Output|

<sup>1 </sup>Microsoft Graph ではサポート対象外<br><sup>2 </sup>詳細については、「[MFA 電話番号属性](#mfa-phone-number-attribute)」を参照してください<br><sup>3 </sup>Azure AD B2C では使用不可

## <a name="display-name-attribute"></a>表示名属性

`displayName` は、ユーザーに対して Azure portal ユーザー管理で表示され、Azure AD B2C がアプリケーションに返すアクセス トークンで表示される名前です。 このプロパティは必須です。

## <a name="identities-attribute"></a>ID 属性

顧客アカウント (コンシューマー、パートナー、または一般ユーザー) は、次の ID の種類に関連付けることができます。

- **ローカル** ID - ユーザー名とパスワードは、Azure AD B2C ディレクトリにローカルに格納されます。 これらの ID は、"ローカル アカウント" とよく呼ばれます。
- **フェデレーション** ID - "*ソーシャル*" または "*エンタープライズ*" アカウントとも呼ばれ、ユーザーの ID は、Facebook、Microsoft、ADFS、Salesforce などのフェデレーション ID プロバイダーによって管理されます。

顧客アカウントを持つユーザーは、複数の ID でサインインできます。 たとえば、ユーザー名、電子メール、従業員 ID、政府 ID などです。 1 つのアカウントで、同じパスワードを持つ複数の ID (ローカルとソーシャルの両方) を持つことができます。 

Microsoft Graph API では、ローカル ID とフェデレーション ID の両方が、[objectIdentity](/graph/api/resources/objectidentity) 型のユーザー `identities` 属性に格納されます。 `identities` コレクションは、ユーザー アカウントへのサインインに使用される一連の ID を表します。 このコレクションにより、ユーザーは、関連付けられた任意の ID を使用してユーザー アカウントにサインインできます。 識別子属性には最大 10 個の [objectIdentity](/graph/api/resources/objectidentity) オブジェクトを含めることができます。 各オブジェクトには次のプロパティが含まれます。

| 名前   | 種類 |説明|
|:---------------|:--------|:----------|
|signInType|string| お使いのディレクトリ内のユーザー サインインの種類を指定します。 ローカル アカウントの場合: `emailAddress`、`emailAddress1`、`emailAddress2`、`emailAddress3`、`userName`、または他の任意の種類。 ソーシャル アカウントは `federated` に設定する必要があります。|
|発行者|string|ID の発行者を指定します。 ローカル アカウント (**signInType** が `federated` でない) の場合、このプロパティは、ローカル B2C テナントの既定のドメイン名 (`contoso.onmicrosoft.com` など) になります。 ソーシャル ID (**signInType** が `federated`) の場合、値は発行者の名前 (`facebook.com` など) になります。|
|issuerAssignedId|string|発行者がユーザーに割り当てる一意識別子を指定します。 **issuer** と **issuerAssignedId** の組み合わせは、テナント内で一意である必要があります。 ローカル アカウントの場合、**signInType** が `emailAddress` または `userName` に設定されているときは、ユーザーのサインイン名を表します。<br>**signInType** が次のように設定されている場合: <ul><li>`emailAddress` (または、`emailAddress1` のように `emailAddress` で始まる)。**issuerAssignedId** は有効なメール アドレスである必要があります</li><li>`userName` (または、その他の値)。**issuerAssignedId** は、[メール アドレスの有効なローカル部分](https://tools.ietf.org/html/rfc3696#section-3)である必要があります</li><li>`federated`。**issuerAssignedId** は、フェデレーション アカウントの一意識別子を表します</li></ul>|

次の **Identities** 属性。サインイン名を含むローカル アカウント ID、サインインとしての電子メール アドレス、およびソーシャル ID が含まれます。 

 ```json
 "identities": [
     {
       "signInType": "userName",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "johnsmith"
     },
     {
       "signInType": "emailAddress",
       "issuer": "contoso.onmicrosoft.com",
       "issuerAssignedId": "jsmith@yahoo.com"
     },
     {
       "signInType": "federated",
       "issuer": "facebook.com",
       "issuerAssignedId": "5eecb0cd"
     }
   ]
 ```

フェデレーション ID の場合、ID プロバイダーによっては、**issuerAssignedId** は、アプリケーションごとの特定のユーザーまたは開発アカウントの一意の値です。 ソーシャル プロバイダーまたは同じ開発アカウント内の別のアプリケーションによって割り当てられたのと同じアプリケーション ID で Azure AD B2C ポリシーを構成します。 

## <a name="password-profile-property"></a>パスワード プロファイル プロパティ

ローカル ID の場合、**passwordProfile** 属性が必須であり、これにはユーザーのパスワードが含まれています。 `forceChangePasswordNextSignIn` 属性は、ユーザーが次回サインイン時にパスワードをリセットする必要があるかどうかを示します。 パスワードの強制リセットを処理するには、[パスワードの強制リセット フローを設定します](force-password-reset.md)。

フェデレーション (ソーシャル) ID の場合、**passwordProfile** 属性は必須ではありません。

```json
"passwordProfile" : {
    "password": "password-value",
    "forceChangePasswordNextSignIn": false
  }
```

## <a name="password-policy-attribute"></a>パスワード ポリシー属性

Azure AD B2C の (ローカル アカウントの) パスワード ポリシーは、Azure Active Directory の[強力なパスワード強度](../active-directory/authentication/concept-sspr-policy.md)ポリシーに基づいています。 Azure AD B2C のサインアップまたはサインインとパスワード リセットの各ポリシーでは、この強力なパスワード強度を必要とし、パスワードに有効期限がありません。

ユーザー移行シナリオでは、移行するアカウントのパスワード強度が、Azure AD B2C によって適用された[強力なパスワード強度](../active-directory/authentication/concept-sspr-policy.md)より弱い場合は、強力なパスワードという要件を無効にすることができます。 既定のパスワード ポリシーを変更するには、`passwordPolicies` 属性を `DisableStrongPassword` に設定します。 たとえば、ユーザー作成要求を次のように変更できます。

```json
"passwordPolicies": "DisablePasswordExpiration, DisableStrongPassword"
```

## <a name="mfa-phone-number-attribute"></a>MFA 電話番号属性

多要素認証 (MFA) に電話を使用する場合は、ユーザー ID を確認するために携帯電話を使用します。 新しい電話番号をプログラムによって[追加](/graph/api/authentication-post-phonemethods)し、電話番号の[更新](/graph/api/b2cauthenticationmethodspolicy-update)、[取得](/graph/api/b2cauthenticationmethodspolicy-get)、[削除](/graph/api/phoneauthenticationmethod-delete)を行うには、MS Graph API [phone authentication method](/graph/api/resources/phoneauthenticationmethod) を使用します。

Azure AD B2C の[カスタム ポリシー](custom-policy-overview.md)では、電話番号は要求の種類 `strongAuthenticationPhoneNumber` を通じて利用できます。

## <a name="extension-attributes"></a>拡張属性

すべての顧客向けアプリケーションには、収集する情報についての固有の要件があります。 Azure AD B2C テナントには、名、姓、郵便番号などのプロパティに格納された組み込みの一連の情報が用意されています。 Azure AD B2C では、各顧客アカウントに格納されている一連のプロパティを拡張できます。 詳細については、「[Azure Active Directory B2C でユーザー属性を追加してユーザー入力をカスタマイズする](configure-user-input.md)」を参照してください。

拡張属性は、ディレクトリ内のユーザー オブジェクトの[スキーマを拡張](/graph/extensibility-overview#schema-extensions)します。 拡張属性は、ユーザーに関するデータを保持できる場合でも、アプリケーション オブジェクトにしか登録できません。 拡張属性は、`b2c-extensions-app` というアプリケーションに関連付けられます。 Azure AD B2C は、ユーザー データを保存するためにこのアプリケーションを使用します。このため、このアプリケーションは変更しないでください。 このアプリケーションは Azure Active Directory アプリの登録で確認できます。

> [!NOTE]
> - 最大 100 の拡張属性を任意のユーザー アカウントに書き込むことができます。
> - b2c-extensions-app アプリケーションが削除されると、これらの拡張属性は、格納されているデータと共に、すべてのユーザーから削除されます。
> - 拡張属性は、アプリケーションによって削除されると、すべてのユーザー アカウントから削除され、その値も削除されます。

Graph API の拡張属性には、`extension_ApplicationClientID_AttributeName` の規則を使用して名前が付けられます。ここで `ApplicationClientID` は `b2c-extensions-app` アプリケーションの **アプリケーション (クライアント) ID** です (Azure portal の **[アプリの登録]**  >  **[すべてのアプリケーション]** にあります)。 拡張属性名で表されるように、**アプリケーション (クライアント) ID** にはハイフンが含まれないことに注意してください。 次に例を示します。

```json
"extension_831374b3bd5041bfaa54263ec9e050fc_loyaltyNumber": "212342"
```

スキーマ拡張で属性を定義する場合、次のデータ型がサポートされます。

|Type |解説  |
|--------------|---------|
|Boolean    | 使用可能な値: **True** または **False**。 |
|DateTime   | ISO 8601 形式で指定する必要があります。 UTC で保存されます。   |
|Integer    | 32 ビット値。               |
|String     | 最大 256 文字。     |

## <a name="next-steps"></a>次のステップ

拡張属性に関する詳細情報:

- [スキーマの拡張機能](/graph/extensibility-overview#schema-extensions)
- [カスタム属性を定義する](user-flow-custom-attributes.md)
