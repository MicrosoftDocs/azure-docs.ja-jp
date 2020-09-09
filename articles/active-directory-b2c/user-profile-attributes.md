---
title: Azure Active Directory B2C のユーザー プロファイル属性
description: Azure AD B2C ディレクトリのユーザー プロファイルでサポートされているユーザー リソースの種類の属性について説明します。 組み込みの属性、拡張機能、および属性が Microsoft Graph にどのようにマップされるかについて説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 3/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 1e6965e15b7482935148ae7fcd2edf0f3cc722b2
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738559"
---
# <a name="user-profile-attributes"></a>ユーザー プロファイルの属性

Azure Active Directory (Azure AD) B2C ディレクトリのユーザー プロファイルには、一連の組み込み属性 (名、姓、市区町村、郵便番号、電話番号など) があります。 外部のデータ ストアを必要とせずに、ユーザー プロファイルを独自のアプリケーション データで拡張できます。 Azure AD B2C ユーザー プロファイルで使用できるほとんどの属性は、Microsoft Graph でもサポートされています。 この記事では、サポートされている Azure AD B2C ユーザー プロファイルの属性について説明します。 また、これらの属性のうち Microsoft Graph でサポートされていないものや、Azure AD B2C で使用すべきではない Microsoft Graph の属性についても説明します。

> [!IMPORTANT]
> 機密性の高い個人データ (アカウントの資格情報、政府の識別番号、カード所有者のデータ、金融口座データ、医療情報、機密性の高い経歴情報など) を保存するために組み込み属性または拡張属性を使用しないでください。

外部システムとの統合も可能です。 たとえば、認証には Azure AD B2C を使用できますが、顧客データの信頼できるソースとしての機能は、外部の顧客関係管理 (CRM) や顧客ロイヤルティ データベースに委任します。 詳細については、[リモート プロファイル](https://github.com/azure-ad-b2c/samples/tree/master/policies/remote-profile) ソリューションに関する記事を参照してください。

次の表に、Azure AD B2C ディレクトリのユーザー プロファイルでサポートされている[ユーザー リソースの種類](https://docs.microsoft.com/graph/api/resources/user)の属性の一覧を示します。 各属性について次の情報を提供します。

- Azure AD B2C で使用される属性名 (その後のかっこ内は Microsoft Graph での属性名 (異なる場合))
- 属性のデータ型
- 属性の説明
- 属性を Azure portal で使用できるかどうか
- 属性をユーザー フローで使用できるかどうか
- 属性をカスタム ポリシーの [Azure AD 技術プロファイル](active-directory-technical-profile.md)で使用できるかどうか、およびどのセクション (&lt;InputClaims&gt;、&lt;OutputClaims&gt;、または &lt;PersistedClaims&gt;) で使用できるか

|名前     |Type     |説明|Azure portal|ユーザー フロー|カスタム ポリシー|
|---------|---------|----------|------------|----------|-------------|
|accountEnabled  |Boolean|ユーザー アカウントが有効か無効か: アカウントが有効の場合は **true**、それ以外の場合は **false**。|はい|いいえ|Persisted、Output|
|ageGroup        |String|ユーザーの年齢グループ。 指定できる値: null、Undefined、Minor、Adult、NotAdult。|はい|いいえ|Persisted、Output|
|alternativeSecurityId ([Identities](manage-user-accounts-graph-api.md#identities-property))|String|外部 ID プロバイダーからの単一ユーザー ID。|いいえ|いいえ|Input、Persisted、Output|
|alternativeSecurityIds ([Identities](manage-user-accounts-graph-api.md#identities-property))|代替のセキュリティ ID コレクション|外部 ID プロバイダーからのユーザー ID のコレクション。|いいえ|いいえ|Persisted、Output|
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
|otherMails      |文字列コレクション|ユーザーのその他の電子メール アドレスの一覧。 例: ["bob@contoso.com", "Robert@fabrikam.com"]。|はい (連絡用電子メール アドレス)|いいえ|Persisted、Output|
|password        |String|ユーザー作成時のローカル アカウントのパスワード。|いいえ|いいえ|Persisted|
|passwordPolicies     |String|パスワードのポリシー。 コンマで区切られた複数のポリシー名で構成される文字列です。 例: "DisablePasswordExpiration, DisableStrongPassword"。|いいえ|いいえ|Persisted、Output|
|physicalDeliveryOfficeName (officeLocation)|String|ユーザーの勤務先のオフィス所在地。 最大長 128。|はい|いいえ|Persisted、Output|
|postalCode      |String|ユーザーの住所の郵便番号。 郵便番号は、ユーザーの国/地域に固有です。 米国では、この属性に ZIP コードが含まれます。 最大長 40。|はい|いいえ|Persisted、Output|
|preferredLanguage    |String|ユーザーの優先言語。 ISO 639-1 コードに従う必要があります。 例: "en-US"。|いいえ|いいえ|Persisted、Output|
|refreshTokensValidFromDateTime|DateTime|この日時より前に発行された更新トークンは無効です。無効な更新トークンを使用して新しいアクセス トークンを取得すると、アプリケーションでエラーが発生します。 この場合、アプリケーションは、承認エンドポイントに対して要求を行うことによって、新しい更新トークンを取得する必要があります。 読み取り専用です。|いいえ|いいえ|出力|
|signInNames ([Identities](manage-user-accounts-graph-api.md#identities-property)) |String|ディレクトリ内の任意の種類のローカル アカウント ユーザーの一意のサインイン名。 ローカル アカウントの種類を指定せずにサインイン値でユーザーを取得するには、これを使用します。|いいえ|いいえ|入力|
|signInNames.userName ([Identities](manage-user-accounts-graph-api.md#identities-property)) |String|ディレクトリ内のローカル アカウント ユーザーの一意のユーザー名。 特定のサインイン ユーザー名でユーザーを作成または取得するには、これを使用します。 パッチ操作中に PersistedClaims 単独でこれを指定すると、他の種類の signInNames は削除されます。 新しい種類の signInNames を追加する場合は、既存の signInNames も保持する必要があります。|いいえ|いいえ|Input、Persisted、Output|
|signInNames.phoneNumber ([Identities](manage-user-accounts-graph-api.md#identities-property)) |String|ディレクトリ内のローカル アカウント ユーザーの一意の電話番号。 特定のサインイン電話番号でユーザーを作成または取得するには、これを使用します。 パッチ操作中に PersistedClaims 単独でこれを指定すると、他の種類の signInNames は削除されます。 新しい種類の signInNames を追加する場合は、既存の signInNames も保持する必要があります。|いいえ|いいえ|Input、Persisted、Output|
|signInNames.emailAddress ([Identities](manage-user-accounts-graph-api.md#identities-property))|String|ディレクトリ内のローカル アカウント ユーザーの一意のメール アドレス。 特定のサインイン電子メール アドレスでユーザーを作成または取得するには、これを使用します。 パッチ操作中に PersistedClaims 単独でこれを指定すると、他の種類の signInNames は削除されます。 新しい種類の signInNames を追加する場合は、既存の signInNames も保持する必要があります。|いいえ|いいえ|Input、Persisted、Output|
|state           |String|ユーザーの住所の都道府県。 最大長 128。|はい|はい|Persisted、Output|
|streetAddress   |String|ユーザーの勤務地の番地。 最大長 1024。|はい|はい|Persisted、Output|
|strongAuthentication AlternativePhoneNumber<sup>1</sup>|String|多要素認証に使用される、ユーザーの 2 次電話番号。|はい|いいえ|Persisted、Output|
|strongAuthenticationEmailAddress<sup>1</sup>|String|ユーザーの SMTP アドレス。 例: "bob@contoso.com"。この属性は、ユーザー名ポリシーでのサインインで、ユーザーの電子メール アドレスを保存するために使用されます。 この電子メール アドレスは、パスワード リセット フローで使用されます。|はい|いいえ|Persisted、Output|
|strongAuthenticationPhoneNumber<sup>1</sup>|String|多要素認証に使用される、ユーザーの 1 次電話番号。|はい|いいえ|Persisted、Output|
|surname         |String|ユーザーの姓。 最大長 64。|はい|はい|Persisted、Output|
|telephoneNumber (businessPhones の最初のエントリ)|String|ユーザーの勤務地の 1 次電話番号。|はい|いいえ|Persisted、Output|
|userPrincipalName    |String|ユーザーのユーザー プリンシパル名 (UPN)。 UPN は、インターネット標準 RFC 822 に基づくユーザーのインターネット形式のログイン名です。 ドメインがテナントの確認済みドメインのコレクションに存在している必要があります。 このプロパティは、アカウントを作成するときに必要です。 変更不可。|いいえ|いいえ|Input、Persisted、Output|
|usageLocation   |String|法的な要請のためにライセンスを割り当てられるユーザーが、各国またはリージョンでサービスを利用できるかどうかを確認するのに必要です。 NULL 値は許可されません。 2 文字の国またはリージョン番号 (ISO 標準 3166)。 例 :"US"、"JP"、"GB"。|はい|いいえ|Persisted、Output|
|userType        |String|ディレクトリ内のユーザー タイプを分類するために使用できる文字列値。 値は Member にする必要があります。 読み取り専用です。|読み取り専用|いいえ|Persisted、Output|
|userState (externalUserState)<sup>2</sup>|String|Azure AD B2B アカウント専用。招待が PendingAcceptance であるか Accepted であるかを示します。|いいえ|いいえ|Persisted、Output|
|userStateChangedOn (externalUserStateChangeDateTime)<sup>2</sup>|DateTime|UserState プロパティに対する最新の変更のタイムスタンプを示します。|いいえ|いいえ|Persisted、Output|
|<sup>1 </sup>Microsoft Graph ではサポート対象外<br><sup>2 </sup>Azure AD B2C では使用不可||||||


## <a name="extension-attributes"></a>拡張属性

次のような場合、通常は独自の属性を作成する必要があります。

- 顧客向けアプリケーションを、**LoyaltyNumber** のような属性に対して維持する必要がある。
- ID プロバイダーは、**uniqueUserGUID** のような、保存する必要がある一意のユーザー識別子を保持しています。
- カスタム ユーザー体験を、**migrationStatus** のようなユーザーの状態に対して維持する必要がある。

Azure AD B2C では、各ユーザー アカウントで保存される属性セットを拡張できます。 拡張属性は、ディレクトリ内のユーザー オブジェクトの[スキーマを拡張](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)します。 拡張属性は、ユーザーに関するデータを保持できる場合でも、アプリケーション オブジェクトにしか登録できません。 拡張属性は、b2c-extensions-app というアプリケーションに関連付けられます。 Azure AD B2C は、ユーザー データを保存するためにこのアプリケーションを使用します。このため、このアプリケーションは変更しないでください。 このアプリケーションは Azure Active Directory アプリの登録で確認できます。

> [!NOTE]
> - 最大 100 の拡張属性を任意のユーザー アカウントに書き込むことができます。
> - b2c-extensions-app アプリケーションが削除されると、これらの拡張属性は、格納されているデータと共に、すべてのユーザーから削除されます。
> - 拡張属性は、アプリケーションによって削除されると、すべてのユーザー アカウントから削除され、その値も削除されます。
> - 拡張属性の基になる名前は、"Extension_" + アプリケーション ID + "_" + 属性名の形式で生成されます。 たとえば、拡張属性 LoyaltyNumber を作成し、b2c-extensions-app アプリケーション ID が 831374b3-bd50-41bf-aa54-263ec9e050fc の場合、基になる拡張属性名は extension_831374b3bd5041bfaa54263ec9e050fc_LoyaltyNumber となります。 ユーザー アカウントを作成または更新するために Graph API クエリを実行する場合は、この基になる名前を使用します。

スキーマ拡張でプロパティを定義する場合、次のデータ型がサポートされます。

|プロパティの種類 |解説  |
|--------------|---------|
|Boolean    | 使用可能な値: **True** または **False**。 |
|DateTime   | ISO 8601 形式で指定する必要があります。 UTC で保存されます。   |
|Integer    | 32 ビット値。               |
|String     | 最大 256 文字。     |

## <a name="next-steps"></a>次のステップ
拡張属性に関する詳細情報:
- [スキーマの拡張機能](https://docs.microsoft.com/graph/extensibility-overview#schema-extensions)
- [ユーザー フローでカスタム属性を定義する](user-flow-custom-attributes.md)
- [カスタム ポリシーでカスタム属性を定義する](custom-policy-custom-attributes.md)
