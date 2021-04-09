---
title: Azure Active Directory のアプリ マニフェストについて
titleSuffix: Microsoft identity platform
description: Azure Active Directory のアプリ マニフェストについて詳しく説明しています。このマニフェストは、Azure AD テナントでのアプリケーションの ID 構成を表しています。また、OAuth 認証、同意エクスペリエンスなどを利用しやすくするために使用されます。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: reference
ms.workload: identity
ms.date: 02/02/2021
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: sureshja
ms.openlocfilehash: 199d85d2805b92e1421330be8f299c769637b649
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/30/2021
ms.locfileid: "104578466"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory のアプリ マニフェスト

アプリケーション マニフェストには、Microsoft ID プラットフォームにあるアプリケーション オブジェクトのすべての属性の定義が含まれます。 それは、アプリケーション オブジェクトを更新するメカニズムとしても機能します。 アプリケーション エンティティとそのスキーマの詳細については、[Graph API のアプリケーション エンティティに関するドキュメント](/graph/api/resources/application)を参照してください。

Azure portal で、あるいは [REST API](/graph/api/resources/application) または [PowerShell](/powershell/module/azuread#applications) を使用してプログラムで、アプリの属性を構成できます。 ただし、一部のシナリオでは、アプリ マニフェストを編集してアプリの属性を構成する必要があります。 これらのシナリオは、次のとおりです。

* アプリを Azure AD マルチテナントと個人用の Microsoft アカウントとして登録した場合、サポートされる Microsoft アカウントを UI で変更することはできません。 代わりに、アプリケーション マニフェスト エディターを使用して、サポートされるアカウントの種類を変更する必要があります。
* アプリでサポートされるアクセス許可とロールを定義するには、アプリケーション マニフェストを変更する必要があります。

## <a name="configure-the-app-manifest"></a>アプリ マニフェストを構成する

アプリケーション マニフェストを構成するには:

1. <a href="https://portal.azure.com/" target="_blank">Azure ポータル</a>にアクセスします。 **Azure Active Directory** サービスを検索して選択します。
1. **[アプリの登録]** を選択します。
1. 構成するアプリを選択します。
1. アプリの **[概要]** ページで、 **[マニフェスト]** セクションを選択します。 Web ベースのマニフェスト エディターが開き、ポータルでマニフェストを編集できます。 必要があれば、 **[ダウンロード]** を選択してローカルでマニフェストを編集します。その後、 **[アップロード]** を使用して、アプリケーションにマニフェストを再適用します。

## <a name="manifest-reference"></a>マニフェスト リファレンス

ここでは、アプリケーション マニフェストで見られる属性について説明します。

### <a name="id-attribute"></a>id 属性

| Key | 値の型 |
| :--- | :--- |
| id | String |

ディレクトリ内のアプリの一意識別子。 この ID は、いずれかのプロトコル トランザクション内のアプリを識別するために使用される識別子ではありません。 これは、ディレクトリ クエリ内のオブジェクトを参照するために使用されます。

例:

```json
    "id": "f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd",
```

### <a name="accesstokenacceptedversion-attribute"></a>accessTokenAcceptedVersion 属性

| Key | 値の型 |
| :--- | :--- |
| accessTokenAcceptedVersion | Null 許容の Int32 |

リソースで想定されているアクセス トークンのバージョンを指定します。 このパラメーターを使うと、アクセス トークンを要求するために使用されたエンドポイントまたはクライアントとは関係なく、生成される JWT のバージョンと形式を変更できます。

使用されるエンドポイント (v1.0 または v2.0) はクライアントによって選択され、id_token のバージョンにのみ影響します。 リソースでは、サポートされるアクセス トークンの形式を明示的に示すように、`accesstokenAcceptedVersion` を構成する必要があります。

`accesstokenAcceptedVersion` に指定できる値は、1、2、または null です。 値が null の場合、このパラメーターの既定値は 1 であり、v1.0 のエンドポイントに対応します。

`signInAudience` が `AzureADandPersonalMicrosoftAccount` の場合は、値は `2` である必要があります。

例:

```json
    "accessTokenAcceptedVersion": 2,
```

### <a name="addins-attribute"></a>addIns 属性

| Key | 値の型 |
| :--- | :--- |
| addIns | コレクション |

使用するサービスが特定のコンテキストでアプリの呼び出しに使用できるカスタム動作を定義します。 たとえば、ファイル ストリームをレンダリングできるアプリケーションでは、その "FileHandler" 機能の `addIns` プロパティを設定できます。 このパラメーターを使うと、Microsoft 365 などのサービスで、ユーザーが作業中のドキュメントのコンテキストでアプリケーションを呼び出すことができます。

例:

```json
    "addIns": [
       {
        "id": "968A844F-7A47-430C-9163-07AE7C31D407",
        "type":" FileHandler",
        "properties": [
           {
              "key": "version",
              "value": "2"
           }
        ]
       }
    ],
```

### <a name="allowpublicclient-attribute"></a>allowPublicClient 属性

| Key | 値の型 |
| :--- | :--- |
| allowPublicClient | Boolean |

フォールバック アプリケーションの種類を指定します。 Azure AD では、既定で replyUrlsWithType からアプリケーションの種類が推測されます。 Azure AD がクライアント アプリの種類を判断できない特定のシナリオがあります。 たとえば、このようなシナリオの 1 つとして、URL リダイレクトなしで HTTP 要求が発生する [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) フローがあります。 そのような場合、Azure AD では、このプロパティの値に基づいて、アプリケーションの種類が解釈されます。 この値が true に設定されている場合、フォールバック アプリケーションの種類は、モバイル デバイス上で実行されているインストール済みのアプリなど、パブリック クライアントとして設定されます。 既定値は false です。これは、フォールバック アプリケーションの種類が、Web アプリなどの機密クライアントであることを意味します。

例:

```json
    "allowPublicClient": false,
```

### <a name="appid-attribute"></a>appId 属性

| Key | 値の型 |
| :--- | :--- |
| appId | String |

Azure AD によってアプリに割り当てられた一意識別子を指定します。

例:

```json
    "appId": "601790de-b632-4f57-9523-ee7cb6ceba95",
```

### <a name="approles-attribute"></a>appRoles 属性

| Key | 値の型 |
| :--- | :--- |
| appRoles | コレクション |

アプリが宣言する可能性のあるロールのコレクションを指定します。 これらのロールは、ユーザー、グループ、またはサービス プリンシパルに割り当てることができます。 その他の例および詳細については、「[アプリケーションにアプリ ロールを追加してトークンで受け取る](howto-add-app-roles-in-azure-ad-apps.md)」を参照してください。

例:

```json
    "appRoles": [
        {
           "allowedMemberTypes": [
               "User"
           ],
           "description": "Read-only access to device information",
           "displayName": "Read Only",
           "id": "601790de-b632-4f57-9523-ee7cb6ceba95",
           "isEnabled": true,
           "value": "ReadOnly"
        }
    ],
```

### <a name="errorurl-attribute"></a>errorUrl 属性

| Key | 値の型 |
| :--- | :--- |
| errorUrl | String |

サポートされていません。

### <a name="groupmembershipclaims-attribute"></a>groupMembershipClaims 属性

| Key | 値の型 |
| :--- | :--- |
|groupMembershipClaims | String |

アプリが期待する、ユーザーまたは OAuth 2.0 アクセス トークンで発行される `groups` 要求を構成します。 この属性を設定するには、次のいずれかの有効な文字列値を使用します。

- `"None"`
- `"SecurityGroup"` (セキュリティ グループおよび Azure AD ロールの場合)
- `"ApplicationGroup"` (このオプションには、アプリケーションに割り当てられているグループのみが含まれます)
- `"DirectoryRole"` (ユーザーがメンバーになっている Azure AD ディレクトリ ロールを取得します)
- `"All"` (これは、サインイン ユーザーがメンバーになっているすべてのセキュリティ グループ、配布グループ、および Azure AD ディレクトリ ロールが取得されます)。

例:

```json
    "groupMembershipClaims": "SecurityGroup",
```

### <a name="optionalclaims-attribute"></a>optionalClaims 属性

| Key | 値の型 |
| :--- | :--- |
| optionalClaims | String |

この特定のアプリのセキュリティ トークン サービスによってトークンで返される省略可能な要求。

現時点では、個人アカウントと Azure AD (アプリ登録ポータルを使用して登録済み) の両方をサポートするアプリは、省略可能な要求を使用できません。 ただし、v2.0 エンドポイントを使用して Azure AD のみに登録されたアプリは、マニフェストで要求した省略可能な要求を取得することができます。 詳細については、[「省略可能な要求」](active-directory-optional-claims.md)に関するページを参照してください。

例:

```json
    "optionalClaims": null,
```


### <a name="identifieruris-attribute"></a>identifierUris 属性

| Key | 値の型 |
| :--- | :--- |
| identifierUris | String Array |

その Azure AD テナント内で、またはアプリがマルチテナントである場合は検証されたカスタム ドメイン内で Web アプリを一意に識別するユーザー定義 URI。

例:

```json
    "identifierUris": "https://MyRegisteredApp",
```

### <a name="informationalurls-attribute"></a>informationalUrls 属性

| Key | 値の型 |
| :--- | :--- |
| informationalUrls | String |

アプリのサービス利用規約とプライバシーに関する声明へのリンクを指定します。 サービス利用規約とプライバシーに関する声明は、ユーザーの同意エクスペリエンスからユーザーに提示されます。 詳しくは、[登録済み Azure AD アプリのサービス利用規約とプライバシーに関する声明を追加する方法](howto-add-terms-of-service-privacy-statement.md)に関するページをご覧ください。

例:

```json
    "informationalUrls": {
        "termsOfService": "https://MyRegisteredApp/termsofservice",
        "support": "https://MyRegisteredApp/support",
        "privacy": "https://MyRegisteredApp/privacystatement",
        "marketing": "https://MyRegisteredApp/marketing"
    },
```

### <a name="keycredentials-attribute"></a>keyCredentials 属性

| Key | 値の型 |
| :--- | :--- |
| keyCredentials | コレクション |

アプリで割り当てられた資格情報、文字列ベースの共有シークレット、および X.509 証明書への参照を保持します。 これらの資格情報は、アクセス トークンを要求するときに使用されます (そのアプリがリソースとしてではなく、クライアントとして機能している場合)。

例:

```json
    "keyCredentials": [
        {
           "customKeyIdentifier":null,
           "endDate":"2018-09-13T00:00:00Z",
           "keyId":"<guid>",
           "startDate":"2017-09-12T00:00:00Z",
           "type":"AsymmetricX509Cert",
           "usage":"Verify",
           "value":null
        }
    ],
```

### <a name="knownclientapplications-attribute"></a>knownClientApplications 属性

| Key | 値の型 |
| :--- | :--- |
| knownClientApplications | String Array |

クライアント アプリとカスタム Web API アプリの 2 つの部分を含むソリューションがある場合に、同意をバンドルするために使用されます。 この値にクライアント アプリの appID を入力すると、ユーザーは、クライアント アプリに 1 回同意するだけで済みます。 クライアントへの同意が Web API への暗黙的な同意を意味することが Azure AD によって認識されます。 クライアントと Web API の両方のサービス プリンシパルが同時に自動的にプロビジョニングされます。 クライアントと Web API アプリの両方が同じテナントに登録されている必要があります。

例:

```json
    "knownClientApplications": ["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"],
```

### <a name="logourl-attribute"></a>logoUrl 属性

| Key | 値の型 |
| :--- | :--- |
| logoUrl | String |

ポータルでアップロードされたロゴへの CDN URL を指す値のみを読み取ります。

例:

```json
    "logoUrl": "https://MyRegisteredAppLogo",
```

### <a name="logouturl-attribute"></a>logoutUrl 属性

| Key | 値の型 |
| :--- | :--- |
| logoutUrl | String |

アプリからログアウトするための URL。

例:

```json
    "logoutUrl": "https://MyRegisteredAppLogout",
```

### <a name="name-attribute"></a>name 属性

| Key | 値の型 |
| :--- | :--- |
| name | String |

アプリの表示名。

例:

```json
    "name": "MyRegisteredApp",
```

### <a name="oauth2allowimplicitflow-attribute"></a>oauth2AllowImplicitFlow 属性

| Key | 値の型 |
| :--- | :--- |
| oauth2AllowImplicitFlow | Boolean |

この Web アプリで OAuth2.0 暗黙的フロー アクセス トークンを要求できるかどうかを指定します。 既定値は false です。 このフラグは、ブラウザーベースのアプリ (JavaScript シングルページ アプリなど) に使用されます。 詳細については、目次に「`OAuth 2.0 implicit grant flow`」と入力し、暗黙的フローに関するトピックを表示します。

例:

```json
    "oauth2AllowImplicitFlow": false,
```

### <a name="oauth2allowidtokenimplicitflow-attribute"></a>oauth2AllowIdTokenImplicitFlow 属性

| Key | 値の型 |
| :--- | :--- |
| oauth2AllowIdTokenImplicitFlow | Boolean |

この Web アプリで OAuth2.0 暗黙的フロー ID トークンを要求できるかどうかを指定します。 既定値は false です。 このフラグは、ブラウザーベースのアプリ (JavaScript シングルページ アプリなど) に使用されます。

例:

```json
    "oauth2AllowIdTokenImplicitFlow": false,
```

### <a name="oauth2permissions-attribute"></a>oauth2Permissions 属性

| Key | 値の型 |
| :--- | :--- |
| oauth2Permissions | コレクション |

Web API (リソース) アプリがクライアント アプリに公開する OAuth 2.0 アクセス許可スコープのコレクションを指定します。 これらのアクセス許可スコープは、同意中にクライアント アプリに付与できます。

例:

```json
    "oauth2Permissions": [
       {
          "adminConsentDescription": "Allow the app to access resources on behalf of the signed-in user.",
          "adminConsentDisplayName": "Access resource1",
          "id": "<guid>",
          "isEnabled": true,
          "type": "User",
          "userConsentDescription": "Allow the app to access resource1 on your behalf.",
          "userConsentDisplayName": "Access resources",
          "value": "user_impersonation"
        }
    ],
```

### <a name="oauth2requiredpostresponse-attribute"></a>oauth2RequiredPostResponse 属性

| Key | 値の型 |
| :--- | :--- |
| oauth2RequiredPostResponse | Boolean |

OAuth 2.0 トークン要求の一部として、Azure AD が GET 要求ではなく、POST 要求を許可するかどうかを指定します。 既定値は false です。これは、GET 要求のみが許可されることを指定します。

例:

```json
    "oauth2RequirePostResponse": false,
```

### <a name="parentalcontrolsettings-attribute"></a>parentalControlSettings 属性

| Key | 値の型 |
| :--- | :--- |
| parentalControlSettings | String |

- `countriesBlockedForMinors` は、未成年者に関してアプリがブロックされる国/地域を指定します。
- `legalAgeGroupRule` は、アプリのユーザーに適用される法的年齢グループ ルールを指定します。 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids`、`BlockMinors` のいずれかに設定できます。

例:

```json
    "parentalControlSettings": {
        "countriesBlockedForMinors": [],
        "legalAgeGroupRule": "Allow"
    },
```

### <a name="passwordcredentials-attribute"></a>passwordCredentials 属性

| Key | 値の型 |
| :--- | :--- |
| passwordCredentials | コレクション |

`keyCredentials` プロパティの説明を参照してください。

例:

```json
    "passwordCredentials": [
      {
        "customKeyIdentifier": null,
        "endDate": "2018-10-19T17:59:59.6521653Z",
        "keyId": "<guid>",
        "startDate":"2016-10-19T17:59:59.6521653Z",
        "value":null
      }
    ],
```

### <a name="preauthorizedapplications-attribute"></a>preAuthorizedApplications 属性

| Key | 値の型 |
| :--- | :--- |
| preAuthorizedApplications | コレクション |

暗黙的に同意するアプリケーションと要求されたアクセス許可がリストされます。 管理者がアプリケーションに同意する必要があります。 preAuthorizedApplications では、ユーザーが要求されたアクセス許可に同意する必要はありません。 PreAuthorizedApplications でリストされるアクセス許可にはユーザーの同意は必要ありません。 しかし、preAuthorizedApplications にリストされていない追加の要求されたアクセス許可にはユーザーの同意が必要です。

例:

```json
    "preAuthorizedApplications": [
       {
          "appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",
          "permissionIds": [
             "8748f7db-21fe-4c83-8ab5-53033933c8f1"
            ]
        }
    ],
```

### <a name="publisherdomain-attribute"></a>publisherDomain 属性

| Key | 値の型 |
| :--- | :--- |
| publisherDomain | String |

アプリケーションの確認された発行元のドメイン。 読み取り専用です。

例:

```json
    "publisherDomain": "{tenant}.onmicrosoft.com",
```

### <a name="replyurlswithtype-attribute"></a>replyUrlsWithType 属性

| Key | 値の型 |
| :--- | :--- |
| replyUrlsWithType | コレクション |

この複数値プロパティは、Azure AD がトークンを返すときに宛先として受け入れる登録された redirect_uri 値の一覧を保持します。 各 URI 値には、関連付けられているアプリの種類の値を含める必要があります。 サポートされる種類の値は次のとおりです。

- `Web`
- `InstalledClient`
- `Spa`

詳細については、[replyUrl の制約と制限](./reply-url.md)に関するページを参照してください。

例:

```json
    "replyUrlsWithType": [
       {
          "url": "https://localhost:4400/services/office365/redirectTarget.html",
          "type": "InstalledClient"
       }
    ],
```

### <a name="requiredresourceaccess-attribute"></a>requiredResourceAccess 属性

| Key | 値の型 |
| :--- | :--- |
| requiredResourceAccess | コレクション |

動的な同意では、静的な同意を使用しているユーザーに対する管理者の同意エクスペリエンスおよびユーザーの同意エクスペリエンスが `requiredResourceAccess` によって作動します。 ただし、このパラメーターを使用しても、一般的な場合のユーザーの同意エクスペリエンスは促進されません。

- `resourceAppId` は、アプリがアクセスする必要があるリソースの一意識別子です。 この値は、ターゲット リソース アプリで宣言された appId に等しくなるようにしてください。
- `resourceAccess` は、指定されたリソースに対してアプリが必要とする OAuth2.0 アクセス許可スコープとアプリ ロールを格納する配列です。 指定されたリソースの `id` と `type` の値が格納されます。

例:

```json
    "requiredResourceAccess": [
        {
            "resourceAppId": "00000002-0000-0000-c000-000000000000",
            "resourceAccess": [
                {
                    "id": "311a71cc-e848-46a1-bdf8-97ff7156d8e6",
                    "type": "Scope"
                }
            ]
        }
    ],
```

### <a name="samlmetadataurl-attribute"></a>samlMetadataUrl 属性

| Key | 値の型 |
| :--- | :--- |
| samlMetadataUrl | String |

アプリの SAML メタデータへの URL。

例:

```json
    "samlMetadataUrl": "https://MyRegisteredAppSAMLMetadata",
```

### <a name="signinurl-attribute"></a>signInUrl 属性

| Key | 値の型 |
| :--- | :--- |
| signInUrl | String |

アプリのホーム ページへの URL を指定します。

例:

```json
    "signInUrl": "https://MyRegisteredApp",
```

### <a name="signinaudience-attribute"></a>signInAudience 属性

| Key | 値の型 |
| :--- | :--- |
| signInAudience | String |

現在のアプリケーションでサポートされる Microsoft アカウントを指定します。 サポートされる値は次のとおりです。
- `AzureADMyOrg` - 自分の組織の Azure AD テナント (たとえば、シングル テナント) に、Microsoft の職場または学校アカウントを持つユーザー
- `AzureADMultipleOrgs` - 任意の組織の Azure AD テナント (たとえば、マルチテナント) に、Microsoft の職場または学校アカウントを持つユーザー
- `AzureADandPersonalMicrosoftAccount` - 個人の Microsoft アカウント、または任意の組織の Azure AD テナントに職場または学校アカウントを持つユーザー
- `PersonalMicrosoftAccount` - Xbox や Skype などのサービスのサインインに使用する個人用アカウント。

例:

```json
    "signInAudience": "AzureADandPersonalMicrosoftAccount",
```

### <a name="tags-attribute"></a>tags 属性

| Key | 値の型 |
| :--- | :--- |
| tags | String Array  |

アプリケーションの分類と特定に使用できるカスタム文字列。

例:

```json
    "tags": [
       "ProductionApp"
    ],
```

## <a name="common-issues"></a>一般的な問題

### <a name="manifest-limits"></a>マニフェストの制限

アプリケーション マニフェストには、appRoles、keyCredentials、knownClientApplications、identifierUris、redirectUris、requiredResourceAccess、oauth2Permissions など複数の属性があり、コレクションと呼ばれています。 任意のアプリケーションの完全なアプリケーション マニフェスト内では、すべてのコレクションを組み合わせたときのエントリの合計数は 1200 個に制限されています。 アプリケーション マニフェストで 100 個のリダイレクト URI を以前に指定した場合、マニフェストを構成する他の組み合わされたコレクション全体で使用できるエントリ数は残りの 1100 個です。

> [!NOTE]
> アプリケーション マニフェストに 1200 個を超えるエントリを追加しようとすると、"**アプリケーション xxxxxx を更新できませんでした。エラーの詳細:マニフェストのサイズ制限を超えました。値の数を減らしてから、要求を再試行してください**" というエラーが表示される場合があります。

### <a name="unsupported-attributes"></a>サポート外の属性

アプリケーション マニフェストでは、Azure AD で、基になるアプリケーション モデルのスキーマが表されます。 基になるスキーマの進化に応じて、マニフェスト エディターが新しいスキーマを反映するように随時更新されます。 このため、アプリケーション マニフェストに新しい属性が表示される場合があります。 まれに、既存の属性が構文上またはセマンティックに変更されていたり、以前に存在していた属性がサポートされなくなっていたりする場合があります。 たとえば、[アプリの登録](https://go.microsoft.com/fwlink/?linkid=2083908)には、アプリの登録 (レガシ) エクスペリエンスでは別の呼称だった新しい属性が表示されます。

| アプリの登録 (レガシ)| アプリの登録           |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

これらの属性については、[マニフェスト リファレンス](#manifest-reference)のセクションをご覧ください。

以前にダウンロードしたマニフェストをアップロードしようとすると、次のいずれかのエラーが表示される場合があります。 マニフェスト エディターで現在サポートされている新しいバージョンのスキーマが、アップロードしようとしているスキーマと一致していないためにこのエラーが発生している可能性があります。

* "xxxxxx アプリケーションを更新できませんでした。 エラーの詳細:無効なオブジェクト識別子 'undefined' です。 []."
* "xxxxxx アプリケーションを更新できませんでした。 エラーの詳細:指定した 1 つ以上のプロパティ値が無効です。 []."
* "xxxxxx アプリケーションを更新できませんでした。 エラーの詳細:この API バージョンで更新用に availableToOtherTenants を設定することはできません。 []."
* "xxxxxx アプリケーションを更新できませんでした。 エラーの詳細:'replyUrls' プロパティの更新は、このアプリケーションでは許可されていません。 代わりに、'replyUrlsWithType' プロパティを使用してください。 []."
* "xxxxxx アプリケーションを更新できませんでした。 エラーの詳細:型名のない値が見つかりましたが、使用できる必要な型がありません。 モデルが指定されている場合、ペイロード内の値ごとに型が必要です。その型は、ペイロードで指定できる型か、呼び出し元による明示的な型か、親値から暗黙的に推定される型にすることができます。 []"

これらのエラーのいずれかが表示された場合、以下の操作を実行することをお勧めします。

1. 以前にダウンロードしたマニフェストのアップロードではなく、マニフェスト エディターで個別に属性を編集します。 関心のある属性を正常に編集するために、[マニフェスト リファレンス](#manifest-reference)の表を使って新旧の属性の構文とセマンティクスを理解します。
1. ワークフローにおいて、後で使用するためにソース リポジトリ内にマニフェストを保存する必要がある場合は、リポジトリに保存されたマニフェストを、**アプリの登録** エクスペリエンスに表示されているものにリベースすることをお勧めします。

## <a name="next-steps"></a>次のステップ

* アプリのアプリ オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、「[Azure AD のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)」を参照してください。
* Microsoft ID プラットフォーム開発者向けの基本的な一部の概念の定義については、「[Microsoft ID プラットフォーム開発者向け用語集](developer-glossary.md)」を参照してください。

次のコメント セクションを使用して、Microsoft のコンテンツの改善に役立つフィードバックを提供してください。

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: /graph/migrate-azure-ad-graph-planning-checklist
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: ./quickstart-register-app.md
[O365-PERM-DETAILS]: /graph/permissions-reference
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/