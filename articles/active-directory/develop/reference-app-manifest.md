---
title: Azure Active Directory のアプリ マニフェストについて | Microsoft Docs
description: Azure Active Directory のアプリ マニフェストについて詳しく説明しています。このマニフェストは、Azure AD テナントでのアプリケーションの ID 構成を表しています。また、OAuth 認証、同意エクスペリエンスなどを利用しやすくするために使用されます。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 4804f3d4-0ff1-4280-b663-f8f10d54d184
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/13/2019
ms.author: celested
ms.custom: aaddev
ms.reviewer: sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: 17b7a383eb8df8fcfd358bce226168e5fbeb42f7
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/22/2019
ms.locfileid: "60009397"
---
# <a name="azure-active-directory-app-manifest"></a>Azure Active Directory のアプリ マニフェスト

アプリケーション マニフェストには、Microsoft ID プラットフォームにあるアプリケーション オブジェクトのすべての属性の定義が含まれます。 それは、アプリケーション オブジェクトを更新するメカニズムとしても機能します。 アプリケーション エンティティとそのスキーマの詳細については、[Graph API のアプリケーション エンティティに関するドキュメント](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity)を参照してください。

Azure portal で、あるいは [REST API](https://docs.microsoft.com/en-us/previous-versions/azure/ad/graph/api/entity-and-complex-type-reference#application-entity) または [PowerShell](https://docs.microsoft.com/en-us/powershell/module/azuread/?view=azureadps-2.0#applications) を使用してプログラムで、アプリの属性を構成できます。 ただし、一部のシナリオでは、アプリ マニフェストを編集してアプリの属性を構成する必要があります。 これらのシナリオは、次のとおりです。

* アプリを Azure AD マルチテナントと個人用の Microsoft アカウントとして登録した場合、サポートされる Microsoft アカウントを UI で変更することはできません。 代わりに、アプリケーション マニフェスト エディターを使用して、サポートされるアカウントの種類を変更する必要があります。
* アプリでサポートされるアクセス許可とロールを定義する必要がある場合は、アプリケーション マニフェストを変更する必要があります。

## <a name="configure-the-app-manifest"></a>アプリ マニフェストを構成する

アプリケーション マニフェストを構成するには:

1. [Azure portal](https://portal.azure.com) にサインインします。
1. **[Azure Active Directory]** サービスを選択し、**[アプリの登録]** または **[アプリの登録 (プレビュー)]** を選択します。
1. 構成するアプリを選択します。
1. アプリの **[概要]** ページで、**[マニフェスト]** セクションを選択します。 Web ベースのマニフェスト エディターが開き、ポータルでマニフェストを編集できます。 必要があれば、**[ダウンロード]** を選択してローカルでマニフェストを編集します。その後、**[アップロード]** を使用して、アプリケーションにマニフェストを再適用します。

## <a name="manifest-reference"></a>マニフェスト リファレンス

> [!NOTE]
> **[説明]** の後に **[値の例]** 列が表示されない場合は、ご利用のブラウザー ウィンドウを最大化し、**[値の例]** 列が表示されるまでスクロール/スワイプします。

| キー  | 値の型 | 説明  | 値の例 |
|---------|---------|---------|---------|
| `accessTokenAcceptedVersion` | Null 許容の Int32 | リソースで想定されているアクセス トークンのバージョンを指定します。 これにより、アクセス トークンを要求するために使用されたエンドポイントまたはクライアントとは関係なく、生成される JWT のバージョンと形式が変更されます。<br/><br/>使用されるエンドポイント (v1.0 または v2.0) はクライアントによって選択され、id_token のバージョンにのみ影響します。 リソースでは、サポートされるアクセス トークンの形式を明示的に示すように、`accesstokenAcceptedVersion` を構成する必要があります。<br/><br/>`accesstokenAcceptedVersion` に指定できる値は、1、2、または null です。 値が null の場合の既定値は 1 で、v1.0 のエンドポイントに対応します。 | `2` |
| `addIns` | コレクション | 使用するサービスが特定のコンテキストでアプリの呼び出しに使用できるカスタム動作を定義します。 たとえば、ファイル ストリームをレンダリングできるアプリケーションでは、その "FileHandler" 機能の addIns プロパティを設定できます。 これにより、Office 365 などのサービスで、ユーザーが作業中のドキュメントのコンテキストでアプリケーションを呼び出すことができます。 | <code>{<br>&nbsp;&nbsp;&nbsp;"id":"968A844F-7A47-430C-9163-07AE7C31D407"<br>&nbsp;&nbsp;&nbsp;"type": "FileHandler",<br>&nbsp;&nbsp;&nbsp;"properties": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{"key": "version", "value": "2" }<br>&nbsp;&nbsp;&nbsp;]<br>}</code>|
| `allowPublicClient` | Boolean | フォールバック アプリケーションの種類を指定します。 Azure AD では、既定で replyUrlsWithType からアプリケーションの種類が推測されます。 Azure AD でクライアント アプリの種類を判別できない特定のシナリオがあります (URL リダイレクトなしで HTTP 要求が行われる [ROPC](https://tools.ietf.org/html/rfc6749#section-4.3) フローなど)。 そのような場合、Azure AD では、このプロパティの値に基づいて、アプリケーションの種類が解釈されます。 この値が true に設定されている場合、フォールバック アプリケーションの種類は、モバイル デバイス上で実行されているインストール済みのアプリなど、パブリック クライアントとして設定されます。 既定値は false です。これは、フォールバック アプリケーションの種類が、Web アプリなどの機密クライアントであることを意味します。 | `false` |
| `availableToOtherTenants` | Boolean | アプリケーションが他のテナントと共有されている場合は true、それ以外の場合は false です。 <br><br> _注:これは、[アプリの登録 (プレビュー)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) エクスペリエンスで `signInAudience` に置き換えられます。_ | |
| `appId` | String | Azure AD によってアプリに割り当てられた一意識別子を指定します。 | `"601790de-b632-4f57-9523-ee7cb6ceba95"` |
| `appRoles` | コレクション | アプリが宣言する可能性のあるロールのコレクションを指定します。 これらのロールは、ユーザー、グループ、またはサービス プリンシパルに割り当てることができます。 その他の例および詳細については、「[アプリケーションにアプリ ロールを追加してトークンで受け取る](howto-add-app-roles-in-azure-ad-apps.md)」を参照してください。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"allowedMemberTypes": [<br>&emsp;&nbsp;&nbsp;&nbsp;"User"<br>&nbsp;&nbsp;&nbsp;],<br>&nbsp;&nbsp;&nbsp;"description":"Read-only access to device information",<br>&nbsp;&nbsp;&nbsp;"displayName":"Read Only",<br>&nbsp;&nbsp;&nbsp;"id":guid,<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"value":"ReadOnly"<br>&nbsp;&nbsp;}<br>]</code>  |
| `displayName` | String | アプリの表示名。 <br><br> _注:これは、[アプリの登録 (プレビュー)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) エクスペリエンスで `name` に置き換えられます。_ | `"MyRegisteredApp"` |
| `errorUrl` | String | サポートされていません。 | |
| `groupMembershipClaims` | String | アプリが期待する、ユーザーまたは OAuth 2.0 アクセス トークンで発行される `groups` 要求を構成します。 この属性を設定するには、次のいずれかの有効な文字列値を使用します。<br/><br/>- `"None"`<br/>- `"SecurityGroup"` (セキュリティ グループおよび Azure AD ロールの場合)<br/>- `"All"` (これは、サインイン ユーザーがメンバーになっているすべてのセキュリティ グループ、配布グループ、および Azure AD ディレクトリ ロールが取得されます)。 | `"SecurityGroup"` |
| `homepage` | String | アプリケーションのホームページの URL です。 <br><br> _注:これは、[アプリの登録 (プレビュー)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) エクスペリエンスで `signInUrl` に置き換えられます。_ | `"https://MyRegisteredApp"` |
| `objectId` | String | ディレクトリ内のアプリの一意識別子。 <br><br> _注:これは、[アプリの登録 (プレビュー)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) エクスペリエンスで `id` に置き換えられます。_ | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `optionalClaims` | String | この特定のアプリのセキュリティ トークン サービスによってトークンで返される省略可能な要求。<br>現時点では、個人アカウントと Azure AD (アプリ登録ポータルを使用して登録済み) の両方をサポートするアプリは、省略可能な要求を使用できません。 ただし、v2.0 エンドポイントを使用して Azure AD のみに登録されたアプリは、マニフェストで要求した省略可能な要求を取得することができます。 詳細については、[省略可能な要求](active-directory-optional-claims.md)に関するページを参照してください。 | `null` |
| `id` | String | ディレクトリ内のアプリの一意識別子。 この ID は、いずれかのプロトコル トランザクション内のアプリを識別するために使用される識別子ではありません。 これは、ディレクトリ クエリ内のオブジェクトを参照するために使用されます。 | `"f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"` |
| `identifierUris` | String Array | その Azure AD テナント内で、またはアプリがマルチテナントである場合は検証されたカスタム ドメイン内で Web アプリを一意に識別するユーザー定義 URI。 | <code>[<br>&nbsp;&nbsp;"https://MyRegisteredApp"<br>]</code> |
| `informationalUrls` | String | アプリのサービス利用規約とプライバシーに関する声明へのリンクを指定します。 サービス利用規約とプライバシーに関する声明は、ユーザーの同意エクスペリエンスからユーザーに提示されます。 詳しくは、[登録済み Azure AD アプリのサービス利用規約とプライバシーに関する声明を追加する方法](howto-add-terms-of-service-privacy-statement.md)に関するページをご覧ください。 | <code>{<br>&nbsp;&nbsp;&nbsp;"marketing":"https://MyRegisteredApp/marketing",<br>&nbsp;&nbsp;&nbsp;"privacy":"https://MyRegisteredApp/privacystatement",<br>&nbsp;&nbsp;&nbsp;"support":"https://MyRegisteredApp/support",<br>&nbsp;&nbsp;&nbsp;"termsOfService":"https://MyRegisteredApp/termsofservice"<br>}</code> |
| `keyCredentials` | コレクション | アプリで割り当てられた資格情報、文字列ベースの共有シークレット、および X.509 証明書への参照を保持します。 これらの資格情報は、アクセス トークンを要求するときに使用されます (そのアプリがリソースとしてではなく、クライアントとして機能している場合)。 | <code>[<br>&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-09-13T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2017-09-12T00:00:00Z",<br>&nbsp;&nbsp;&nbsp;"type":"AsymmetricX509Cert",<br>&nbsp;&nbsp;&nbsp;"usage":"Verify",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;}<br>]</code> |
| `knownClientApplications` | String Array | クライアント アプリとカスタム Web API アプリの 2 つの部分を含むソリューションがある場合に、同意をバンドルするために使用されます。 この値にクライアント アプリの appID を入力すると、ユーザーは、クライアント アプリに 1 回同意するだけで済みます。 Azure AD は、クライアントへの同意が Web API への暗黙的な同意を示し、クライアントと Web API の両方のサービス プリンシパルを同時に自動的にプロビジョニングすることを認識します。 クライアントと Web API アプリの両方が同じテナントに登録されている必要があります。 | `["f7f9acfc-ae0c-4d6c-b489-0a81dc1652dd"]` |
| `logoUrl` | String | ポータルでアップロードされたロゴへの CDN URL を指す値のみを読み取ります。 | `"https://MyRegisteredAppLogo"` |
| `logoutUrl` | String | アプリからログアウトするための URL。 | `"https://MyRegisteredAppLogout"` |
| `name` | String | アプリの表示名。 | `"MyRegisteredApp"` |
| `oauth2AllowImplicitFlow` | Boolean | この Web アプリで OAuth2.0 暗黙的フロー アクセス トークンを要求できるかどうかを指定します。 既定値は false です。 このフラグは、ブラウザー ベースのアプリ (Javascript シングル ページ アプリなど) に使用されます。 詳細については、目次に「`OAuth 2.0 implicit grant flow`」と入力し、暗黙的フローに関するトピックを表示します。 | `false` |
| `oauth2AllowIdTokenImplicitFlow` | Boolean | この Web アプリで OAuth2.0 暗黙的フロー ID トークンを要求できるかどうかを指定します。 既定値は false です。 このフラグは、ブラウザー ベースのアプリ (Javascript シングル ページ アプリなど) に使用されます。 | `false` |
| `oauth2Permissions` | コレクション | Web API (リソース) アプリがクライアント アプリに公開する OAuth 2.0 アクセス許可スコープのコレクションを指定します。 これらのアクセス許可スコープは、同意中にクライアント アプリに付与できます。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"adminConsentDescription":"Allow the app to access resources on behalf of the signed-in user.",<br>&nbsp;&nbsp;&nbsp;"adminConsentDisplayName":"Access resource1",<br>&nbsp;&nbsp;&nbsp;"id":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"isEnabled":true,<br>&nbsp;&nbsp;&nbsp;"type":"User",<br>&nbsp;&nbsp;&nbsp;"userConsentDescription":"Allow the app to access resource1 on your behalf.",<br>&nbsp;&nbsp;&nbsp;"userConsentDisplayName":"Access resources",<br>&nbsp;&nbsp;&nbsp;"value":"user_impersonation"<br>&nbsp;&nbsp;}<br>] </code>|
| `oauth2RequiredPostResponse` | Boolean | OAuth 2.0 トークン要求の一部として、Azure AD が GET 要求ではなく、POST 要求を許可するかどうかを指定します。 既定値は false です。これは、GET 要求のみが許可されることを指定します。 | `false` |
| `parentalControlSettings` | String | `countriesBlockedForMinors` は、未成年者に関してアプリがブロックされる国を指定します。<br>`legalAgeGroupRule` は、アプリのユーザーに適用される法的年齢グループ ルールを指定します。 `Allow`、`RequireConsentForPrivacyServices`、`RequireConsentForMinors`、`RequireConsentForKids`、`BlockMinors` のいずれかに設定できます。  | <code>{<br>&nbsp;&nbsp;&nbsp;"countriesBlockedForMinors":[],<br>&nbsp;&nbsp;&nbsp;"legalAgeGroupRule":"Allow"<br>} </code> |
| `passwordCredentials` | コレクション | `keyCredentials` プロパティの説明を参照してください。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;"customKeyIdentifier":null,<br>&nbsp;&nbsp;&nbsp;"endDate":"2018-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"keyId":"\<guid>",<br>&nbsp;&nbsp;&nbsp;"startDate":"2016-10-19T17:59:59.6521653Z",<br>&nbsp;&nbsp;&nbsp;"value":null<br>&nbsp;&nbsp;&nbsp;}<br>] </code> |
| `preAuthorizedApplications` | コレクション | 暗黙的に同意するアプリケーションと要求されたアクセス許可がリストされます。 管理者がアプリケーションに同意する必要があります。 preAuthorizedApplications では、ユーザーが要求されたアクセス許可に同意する必要はありません。 PreAuthorizedApplications でリストされるアクセス許可にはユーザーの同意は必要ありません。 しかし、preAuthorizedApplications にリストされていない追加の要求されたアクセス許可にはユーザーの同意が必要です。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"appId": "abcdefg2-000a-1111-a0e5-812ed8dd72e8",<br>&nbsp;&nbsp;&nbsp;&nbsp;"permissionIds": [<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"8748f7db-21fe-4c83-8ab5-53033933c8f1"<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>]</code> |
| `publicClient` | Boolean | このアプリケーションがパブリック クライアント (モバイル デバイス上で実行されているインストール済みのアプリケーションなど) であるかどうかを指定します。 <br><br> _注:これは、[アプリの登録 (プレビュー)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) エクスペリエンスで `allowPublicClient` に置き換えられます。_ | |
| `publisherDomain` | String | アプリケーションの確認された発行元のドメイン。 読み取り専用です。 | https://www.contoso.com |
| `replyUrls` | 文字列配列 | この複数値プロパティは、Azure AD がトークンを返すときに宛先として受け入れる登録された redirect_uri 値の一覧を保持します。 <br><br> _注:これは、[アプリの登録 (プレビュー)](https://developer.microsoft.com/en-us/graph/blogs/new-app-registration/) エクスペリエンスで `replyUrlsWithType` に置き換えられます。_ | |
| `replyUrlsWithType` | コレクション | この複数値プロパティは、Azure AD がトークンを返すときに宛先として受け入れる登録された redirect_uri 値の一覧を保持します。 各 uri 値には、関連付けられているアプリの種類の値を含める必要があります。 サポートされる種類の値は `Web`、`InstalledClient` です。 | <code>"replyUrlsWithType":&nbsp;[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"url":&nbsp;"https://localhost:4400/services/office365/redirectTarget.html",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":&nbsp;"InstalledClient"&nbsp;&nbsp;&nbsp;<br>&nbsp;&nbsp;}<br>]</code> |
| `requiredResourceAccess` | コレクション | 動的な同意では、静的な同意を使用しているユーザーに対する管理者の同意エクスペリエンスおよびユーザーの同意エクスペリエンスが `requiredResourceAccess` によって作動します。 ただし、一般的なケースでは、これによってユーザーの同意エクスペリエンスが作動することはありません。<br>`resourceAppId` は、アプリがアクセスする必要があるリソースの一意識別子です。 この値は、ターゲット リソース アプリで宣言された appId に等しくなるようにしてください。<br>`resourceAccess` は、指定されたリソースに対してアプリが必要とする OAuth2.0 アクセス許可スコープとアプリ ロールを格納する配列です。 指定されたリソースの `id` と `type` の値が格納されます。 | <code>[<br>&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAppId":"00000002-0000-0000-c000-000000000000",<br>&nbsp;&nbsp;&nbsp;&nbsp;"resourceAccess":[<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"id":"311a71cc-e848-46a1-bdf8-97ff7156d8e6",<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"type":"Scope"<br>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;}<br>&nbsp;&nbsp;&nbsp;&nbsp;]<br>&nbsp;&nbsp;}<br>] </code> |
| `samlMetadataUrl` | String | アプリの SAML メタデータへの URL。 | `https://MyRegisteredAppSAMLMetadata` |
| `signInUrl` | String | アプリのホーム ページへの URL を指定します。 | `https://MyRegisteredApp` |
| `signInAudience` | String | 現在のアプリケーションでサポートされる Microsoft アカウントを指定します。 サポートされる値は次のとおりです。<ul><li>**AzureADMyOrg** - 自分の組織の Azure AD テナント (つまり、シングル テナント) における Microsoft の職場または学校アカウントを持つユーザー</li><li>**AzureADMultipleOrgs** - 任意の組織の Azure AD テナント (つまり、マルチテナント) での Microsoft の職場または学校アカウントを持つユーザー</li> <li>**AzureADandPersonalMicrosoftAccount** - 個人の Microsoft アカウント、または任意の組織の Azure AD テナントでの職場または学校アカウントを持つユーザー</li></ul> | `AzureADandPersonalMicrosoftAccount` |
| `tags` | String Array | アプリケーションの分類と特定に使用できるカスタム文字列。 | <code>[<br>&nbsp;&nbsp;"ProductionApp"<br>]</code> |

## <a name="common-issues"></a>一般的な問題

### <a name="manifest-limits"></a>マニフェストの制限

アプリケーション マニフェストには、approles、keycredentials、knownClientApplications、identifierUris、rediretUris、requiredResourceAccess、oauth2Permissions など複数の属性があり、コレクションと呼ばれています。 任意のアプリケーションの完全なアプリケーション マニフェスト内では、すべてのコレクションを組み合わせたときのエントリの合計数は 1200 個に制限されています。 アプリケーション マニフェストで指定されたリダイレクト URI が既に 100 個ある場合、マニフェストを構成する他の組み合わされたコレクション全体で使用できるエントリ数は残りの 1100 個です。

> [!NOTE]
> アプリケーション マニフェストに 1200 個を超えるエントリを追加しようとすると、"**アプリケーション xxxxxx を更新できませんでした。エラーの詳細:マニフェストのサイズ制限を超えました。値の数を減らしてから、要求を再試行してください**" というエラーが表示される場合があります。

### <a name="unsupported-attributes"></a>サポート外の属性

アプリケーション マニフェストでは、Azure AD で、基になるアプリケーション モデルのスキーマが表されます。 基になるスキーマの進化に応じて、マニフェスト エディターが新しいスキーマを反映するように随時更新されます。 このため、アプリケーション マニフェストに新しい属性が表示される場合があります。 まれに、既存の属性が構文上またはセマンティックに変更されていたり、以前に存在していた属性がサポートされなくなっていたりする場合があります。 たとえば、[アプリの登録 (プレビュー)](https://developer.microsoft.com/graph/blogs/new-app-registration/) (アプリの登録 (GA) エクスペリエンスでは別の呼称) では新しい属性が表示されます。


| アプリの登録 (GA)    | アプリの登録 (プレビュー) |
|---------------------------|-----------------------------|
| `availableToOtherTenants` | `signInAudience`            |
| `displayName`             | `name`                      |
| `errorUrl`                | -                           |
| `homepage`                | `signInUrl`                 |
| `objectId`                | `Id`                        |
| `publicClient`            | `allowPublicClient`         |
| `replyUrls`               | `replyUrlsWithType`         |

これらの属性については、[マニフェスト リファレンス](#manifest-reference)のセクションをご覧ください。

以前にダウンロードしたマニフェストをアップロードしようとすると、次のいずれかのエラーが表示される場合があります。 マニフェスト エディターで現在サポートされている新しいバージョンのスキーマが、アップロードしようとしているスキーマと一致していない可能性があります。

- "**xxxxxx アプリケーションを更新できませんでした。エラーの詳細:無効なオブジェクト識別子 'undefined' です。[]。**"
- "**xxxxxx アプリケーションを更新できませんでした。エラーの詳細:指定した 1 つ以上のプロパティ値が無効です。[]。**"
- "**xxxxxx アプリケーションを更新できませんでした。エラーの詳細:この api バージョンで更新用に availableToOtherTenants を設定することはできません。[]。**"
- "**xxxxxx アプリケーションを更新できませんでした。エラーの詳細:'replyUrls' プロパティの更新は、このアプリケーションでは許可されていません。代わりに、'replyUrlsWithType' プロパティを使用してください。[]。**"
- "**xxxxxx アプリケーションを更新できませんでした。エラーの詳細:型名のない値が見つかりましたが、使用できる必要な型がありません。モデルが指定されている場合、ペイロード内の値ごとに、ペイロードで指定できる型が必要です。その型は、呼び出し元による明示的な型か、親値から暗黙的に推定される型にすることができます。[]**"

これらのエラーのいずれかが表示された場合、以下を実行することをお勧めします。

1. 以前にダウンロードしたマニフェストのアップロードではなく、マニフェスト エディターで個別に属性を編集します。 関心のある属性を正常に編集するために、[マニフェスト リファレンス](#manifest-reference)の表を使って新旧の属性の構文とセマンティクスを理解します。 
1. 後で使用するためにソース リポジトリにマニフェストを保存する必要がある場合は、リポジトリに保存されたマニフェストを、**アプリの登録 (プレビュー)** エクスペリエンスに表示されるマニフェストでリベースすることをお勧めします。

## <a name="next-steps"></a>次の手順

* アプリのアプリ オブジェクトとサービス プリンシパル オブジェクトの関係の詳細については、「[Azure AD のアプリケーション オブジェクトとサービス プリンシパル オブジェクト](app-objects-and-service-principals.md)」を参照してください。
* Microsoft ID プラットフォーム開発者向けの基本的な一部の概念の定義については、「[Microsoft ID プラットフォーム開発者向け用語集](developer-glossary.md)」をご覧ください。

次のコメント セクションを使用して、Microsoft のコンテンツの改善に役立つフィードバックを提供してください。

<!--article references -->
[AAD-APP-OBJECTS]:app-objects-and-service-principals.md
[AAD-DEVELOPER-GLOSSARY]:developer-glossary.md
[AAD-GROUPS-FOR-AUTHORIZATION]: http://www.dushyantgill.com/blog/2014/12/10/authorization-cloud-applications-using-ad-groups/
[ADD-UPD-RMV-APP]:quickstart-v1-integrate-apps-with-azure-ad.md
[APPLICATION-ENTITY]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#application-entity
[APPLICATION-ENTITY-APP-ROLE]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#approle-type
[APPLICATION-ENTITY-OAUTH2-PERMISSION]: https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#oauth2permission-type
[AZURE-PORTAL]: https://portal.azure.com
[DEV-GUIDE-TO-AUTH-WITH-ARM]: http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/
[GRAPH-API]: active-directory-graph-api.md
[IMPLICIT-GRANT]:v1-oauth2-implicit-grant-flow.md
[INTEGRATING-APPLICATIONS-AAD]: https://azure.microsoft.com/documentation/articles/active-directory-integrating-applications/
[O365-PERM-DETAILS]: https://msdn.microsoft.com/office/office365/HowTo/application-manifest
[O365-SERVICE-DAEMON-APPS]: https://msdn.microsoft.com/office/office365/howto/building-service-apps-in-office-365
[RBAC-CLOUD-APPS-AZUREAD]: http://www.dushyantgill.com/blog/2014/12/10/roles-based-access-control-in-cloud-applications-using-azure-ad/
