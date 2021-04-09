---
title: Azure AD アプリに省略可能な要求を提供する
titleSuffix: Microsoft identity platform
description: Microsoft ID プラットフォームによって発行された SAML 2.0 および JSON Web トークン (JWT) トークンに、カスタムまたは追加の要求を追加する方法。
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: how-to
ms.workload: identity
ms.date: 1/06/2021
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, keyam
ms.custom: aaddev
ms.openlocfilehash: 7c0394e765923c027cc15a6278ee451fb13ed1b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100104282"
---
# <a name="how-to-provide-optional-claims-to-your-app"></a>方法:アプリに省略可能な要求を提供する

アプリケーション開発者は、Azure AD アプリケーションで省略可能な要求を使用して、アプリケーションに送信されるトークンに含めたい要求を指定できます。

次の処理に省略可能な要求を使用できます。

- アプリケーションのトークンに含める追加の要求を選択する。
- Microsoft ID プラットフォームからトークンで返される特定の要求の動作を変更する。
- アプリケーションのカスタムの要求を追加してアクセスする。

標準の要求の一覧については、[アクセス トークン](access-tokens.md)と [id_token](id-tokens.md) の要求のドキュメントを参照してください。

省略可能な要求は v1.0 と v2.0 の両方の形式のトークンと、SAML トークンでサポートされていますが、v1.0 から v2.0 に移行すると、最大限の価値が得られます。 [Microsoft ID プラットフォーム](./v2-overview.md)の目標の 1 つは、トークン サイズを小さくしてクライアントのパフォーマンスを最適化することです。 その結果、以前のバージョンではアクセス トークンと ID トークンに含まれていた一部の要求は、v2.0 トークンでは削除されたため、アプリケーションごとに具体的に要求する必要があります。

**表 1:適用性**

| アカウントの種類               | v1.0 トークン | v2.0 トークン |
|----------------------------|-------------|-------------|
| 個人用 Microsoft アカウント | 該当なし         | サポートされています   |
| Azure AD アカウント           | サポートされています   | サポートされています   |

## <a name="v10-and-v20-optional-claims-set"></a>v1.0 と v2.0 の省略可能な要求セット

既定でアプリケーションが使用できる省略可能な要求セットを以下に示します。 アプリケーションにカスタムの省略可能な要求を追加する方法については、後述の[ディレクトリ拡張](#configuring-directory-extension-optional-claims)に関するセクションを参照してください。 要求を **アクセス トークン** に追加する場合、その要求は、アプリケーション (Web API) "*によって*" 求められた要求ではなく、アプリケーション "*に対して*" 要求されたアクセス トークンに適用されます。 クライアントがどのように API にアクセスしても、API に対する認証のために使用するアクセス トークンに、適切なデータが確実に存在します。

> [!NOTE]
>このような要求の大部分は v1.0 および v2.0 トークンの JWT に含めることができますが、「トークンの種類」列に記載されている場合を除き、SAML トークンに含めることはできません。 コンシューマー アカウントにより、"ユーザーの種類" 列でマークされている要求のサブセットがサポートされます。  表示されている要求の多くは、コンシューマー ユーザーに適用されません (テナントがなく、`tenant_ctry` 値がないためです)。

**表 2: v1.0 と v2.0 の省略可能な要求セット**

| 名前                       |  説明   | トークンの種類 | ユーザーの種類 | Notes  |
|----------------------------|----------------|------------|-----------|--------|
| `auth_time`                | ユーザーが最後に認証された時刻。 OpenID Connect の仕様を参照してください。| JWT        |           |  |
| `tenant_region_scope`      | リソースのテナントのリージョン | JWT        |           | |
| `sid`                      | セッション ID。セッションごとのユーザーのサインアウトに使用されます。 | JWT        |  個人用アカウントと Azure AD アカウント。   |         |
| `verified_primary_email`   | ユーザーの PrimaryAuthoritativeEmail が送信元です      | JWT        |           |         |
| `verified_secondary_email` | ユーザーの SecondaryAuthoritativeEmail が送信元です   | JWT        |           |        |
| `vnet`                     | VNET 指定子情報。 | JWT        |           |      |
| `fwd`                      | IP アドレス。| JWT    |   | 要求側クライアントの元の IPv4 アドレスを追加します (VNET 内の場合) |
| `ctry`                     | ユーザーの国またはリージョン | JWT |  | Azure AD は、存在する場合、`ctry` の省略可能な要求を返します。フィールドの値は、FR、JP、SZ などの標準の 2 文字の国またはリージョン番号です。 |
| `tenant_ctry`              | リソース テナントの国 | JWT | | 管理者がテナント レベルを設定することを除き `ctry` と同じです。また、標準の 2 文字値にする必要があります。 |
| `xms_pdl`             | 優先されるデータの場所   | JWT | | Multi-Geo テナントの場合、優先されるデータの場所は、ユーザーがどの地域にいるかを示す 3 文字のコードです。 詳細については、[優先されるデータの場所に関する Azure AD Connect のドキュメント](../hybrid/how-to-connect-sync-feature-preferreddatalocation.md)を参照してください。<br/>たとえば、アジア太平洋の場合は `APC` です。 |
| `xms_pl`                   | ユーザーの優先する言語  | JWT ||ユーザーの優先する言語 (設定されている場合)。 ゲスト アクセスのシナリオの場合、ソースはホーム テナントです。 LL-CC ("en-us") という形式です。 |
| `xms_tpl`                  | テナントの優先する言語| JWT | | リソース テナントの優先する言語 (設定されている場合)。 LL ("en") という形式です。 |
| `ztdid`                    | ゼロタッチ デプロイ ID | JWT | | [Windows AutoPilot](/windows/deployment/windows-autopilot/windows-10-autopilot) に使用されるデバイス ID |
| `email`                    | このユーザーのアドレス指定可能なメール アドレス (ユーザーが持っている場合)。  | JWT、SAML | MSA、Azure AD | ユーザーがテナントのゲストである場合、この値は既定で含まれます。  マネージド ユーザー (テナント内のユーザー) の場合は、この省略可能な要求により、または OpenID スコープで (v2.0 の場合のみ)、それを要求する必要があります。  マネージド ユーザーの場合、メール アドレスは [Office 管理ポータル](https://portal.office.com/adminportal/home#/users)で設定する必要があります。|
| `acct`                | テナント内のユーザー アカウントの状態 | JWT、SAML | | ユーザーがテナントのメンバーである場合、値は `0` です。 ユーザーがゲストの場合、値は `1` です。 |
| `groups`| グループ要求の省略可能な形式 |JWT、SAML| |[アプリケーション マニフェスト](reference-app-manifest.md)の GroupMembershipClaims 設定と共に使用されます (こちらも設定する必要があります)。 詳細については、後述の[グループ要求](#configuring-groups-optional-claims)に関する説明を参照してください。 グループ要求の詳細については、[グループ要求の構成方法](../hybrid/how-to-connect-fed-group-claims.md)に関する記事を参照してください
| `upn`                      | UserPrincipalName | JWT、SAML  |           | username_hint パラメーターで使用できるユーザーの識別子。  そのユーザーの持続的な識別子ではないため、ユーザー情報の一意な識別 (データベース キーとして、など) には使用しません。 代わりに、ユーザー オブジェクト ID (`oid`) をデータベース キーとして使用します。 [代替ログイン ID](../authentication/howto-authentication-use-email-signin.md) を使用してサインインするユーザーには、ユーザー プリンシパル名 (UPN) は表示されません。 代わりに、サインイン状態をユーザーに表示するには、ID トークン要求 (v1 トークンの場合は `preferred_username` または `unique_name`、v2 トークンの場合は `preferred_username`) を使用します。 この要求は自動的に含まれますが、省略可能な要求として、ゲスト ユーザーの場合に動作を変更するために追加のプロパティをアタッチする要求を指定することもできます。  |
| `idtyp`                    | トークンの種類   | JWT のアクセス トークン | 特殊: アプリ専用アクセス トークンのみ |  トークンがアプリ専用トークンの場合、値は `app` です。 API を使用してトークンがアプリ トークンかアプリ + ユーザー トークンかを判断する場合、これが最も正確な方法です。|

## <a name="v20-specific-optional-claims-set"></a>v2.0 固有の省略可能な要求セット

これらの要求は常に v1.0 Azure AD トークンに含まれますが、要求されない限り v2.0 トークンには含まれません。 これらの要求は、JWT (ID トークンとアクセス トークン) にのみ適用されます。

**表 3: v2.0 のみの省略可能な要求**

| JWT の要求     | 名前                            | 説明                                | Notes |
|---------------|---------------------------------|-------------|-------|
| `ipaddr`      | IP アドレス                      | ログインしたクライアントの IP アドレス。   |       |
| `onprem_sid`  | オンプレミスのセキュリティ ID |                                             |       |
| `pwd_exp`     | パスワードの有効期限        | パスワードの有効期限が切れる日時。 |       |
| `pwd_url`     | パスワードの変更 URL             | ユーザーがパスワードを変更するためにアクセスできる URL。   |   |
| `in_corp`     | 企業ネットワーク内        | クライアントが企業ネットワークからログインしている場合に通知します。 そうでない場合、この要求は含まれません。   |  MFA の[信頼できる IP](../authentication/howto-mfa-mfasettings.md#trusted-ips) の設定に基づきます。    |
| `family_name` | 姓                       | ユーザー オブジェクトで定義されたユーザーの姓を示します。 <br>"family_name":"Miller" | MSA と Azure AD でサポートされています。 `profile` スコープが必要です。   |
| `given_name`  | 名                      | ユーザー オブジェクトに設定されたユーザーの名を示します。<br>"given_name":"Frank"                   | MSA と Azure AD でサポートされています。  `profile` スコープが必要です。 |
| `upn`         | ユーザー プリンシパル名 | username_hint パラメーターで使用できるユーザーの識別子。  そのユーザーの持続的な識別子ではないため、ユーザー情報の一意な識別 (データベース キーとして、など) には使用しません。 代わりに、ユーザー オブジェクト ID (`oid`) をデータベース キーとして使用します。 [代替ログイン ID](../authentication/howto-authentication-use-email-signin.md) を使用してサインインするユーザーには、ユーザー プリンシパル名 (UPN) は表示されません。 代わりに、次の `preferred_username` 要求を使用して、ユーザーにサインイン状態を表示します。 | 要求の構成については、下の[追加のプロパティ](#additional-properties-of-optional-claims)を参照してください。 `profile` スコープが必要です。|

## <a name="v10-specific-optional-claims-set"></a>v1.0 固有の省略可能な要求セット

v2 トークン形式の機能強化の一部は、セキュリティと信頼性の向上に役立つため、v1 トークン形式を使用するアプリで利用できます。 これらは、v2 エンドポイントから要求された ID トークンにも、v2 トークン形式を使用する API 用のアクセス トークンにも適用されません。 これらは、JWT にのみ適用され、SAML トークンは対象外となります。 

**表 4: v1.0 のみの省略可能なクレーム**


| JWT の要求     | 名前                            | 説明 | Notes |
|---------------|---------------------------------|-------------|-------|
|`aud`          | 対象ユーザー | JWT には常に存在しますが、v1 アクセス トークンでは、リソースのクライアント ID のほか、任意の appID URI、末尾にスラッシュがある場合とない場合など、さまざまな方法で出力することができます。 このランダム化は、トークンの検証を実行するときに、コーディングが困難になる場合があります。  [この要求の追加のプロパティ](#additional-properties-of-optional-claims)を使用すると、常に v1 アクセス トークン内のリソースのクライアント ID に確実に設定されるようにすることができます。 | v1 JWT アクセス トークンのみ|
|`preferred_username` | 推奨ユーザー名        | v1 トークン内の優先ユーザー名要求を提供します。 これにより、トークンの種類に関係なく、アプリでユーザー名のヒントを提供したり、人間が判読できる表示名を表示したりするのが簡単になります。  `upn` や `unique_name` などを使用するのでなく、この省略可能な要求を使用することをお勧めします。 | v1 ID トークンとアクセス トークン |

### <a name="additional-properties-of-optional-claims"></a>省略可能な要求の追加のプロパティ

一部の省略可能な要求は、要求が返される方法を変更するように構成することができます。 このような追加のプロパティは、ほとんどの場合、異なるデータが期待されるオンプレミス アプリケーションの移行のために使用されます。 たとえば、`include_externally_authenticated_upn_without_hash` は UPN 内のハッシュ マーク (`#`) を処理できないクライアントに役立ちます。

**表 4:省略可能な要求を構成するための値**

| プロパティ名  | 追加のプロパティ名 | 説明 |
|----------------|--------------------------|-------------|
| `upn`          |                          | SAML 応答と JWT 応答の両方や、v1.0 および v2.0 トークンに使用できます。 |
|                | `include_externally_authenticated_upn`  | リソース テナントに格納されているゲスト UPN が含まれます。 たとえば、`foo_hometenant.com#EXT#@resourcetenant.com` のように指定します。 |
|                | `include_externally_authenticated_upn_without_hash` | ハッシュ マーク (`#`) がアンダースコア (`_`) に置き換えられる点を除き、上と同じです。例: `foo_hometenant.com_EXT_@resourcetenant.com`|
| `aud`          |                          | v1 アクセス トークンでは、これは `aud` 要求の形式を変更するために使用されます。  v2 トークンまたはいずれかのバージョンの ID トークンへの影響はありません。ここで、`aud` 要求は常にクライアント ID です。 この構成を使用すると、API による対象ユーザーの検証が確実により簡単になります。 リソースはアクセス トークンを所有しているので、要求内のリソースでは、アクセス トークンに影響を与えるすべての省略可能なクレームと同様に、この省略可能なクレームも設定する必要があります。|
|                | `use_guid`               | リソース (API) のクライアント ID を GUID 形式で `aud` 要求として常に生成します。これはランタイム依存ではありません。 たとえば、リソースによってこのフラグが設定され、そのクライアント ID が `bb0a297b-6a42-4a55-ac40-09a501456577` の場合、そのリソースのアクセス トークンを要求するすべてのアプリに、`aud` : `bb0a297b-6a42-4a55-ac40-09a501456577` を含むアクセス トークンが送られます。 </br></br> この要求が設定されていない場合、API では、`api://MyApi.com`、`api://MyApi.com/`、`api://myapi.com/AdditionalRegisteredField`、またはその API のアプリ ID URI として設定されたその他の値の `aud` 要求と、リソースのクライアント ID を使用してトークンが取得されます。 |

#### <a name="additional-properties-example"></a>追加のプロパティの例

```json
"optionalClaims": {
    "idToken": [
        {
            "name": "upn",
            "essential": false,
            "additionalProperties": [
                "include_externally_authenticated_upn"
            ]
        }
    ]
}
```

この OptionalClaims オブジェクトにより、ID トークンがクライアントに返され、追加のホーム テナントとリソース テナントの情報を持つ `upn` 要求が含められます。 トークンの `upn` 要求は、ユーザーが (認証に異なる IDP を使用する) テナントのゲストである場合にのみ変更されます。

## <a name="configuring-optional-claims"></a>省略可能な要求の構成

> [!IMPORTANT]
> アクセス トークンは、クライアントではなく、**常に** リソースのマニフェストを使用して生成されます。  そのため、`...scope=https://graph.microsoft.com/user.read...` 要求では、リソースは Microsoft Graph API になります。  したがって、アクセス トークンは、クライアントのマニフェストではなく、Microsoft Graph API マニフェストを使用して作成されます。  アプリケーションのマニフェストを変更しても、Microsoft Graph API のトークンが変更されることはありません。  `accessToken` の変更が有効であることを確認するには、他のアプリではなく、自分のアプリケーションのトークンを要求します。

UI またはアプリケーション マニフェストを使用して、アプリケーションの省略可能な要求を構成できます。

1. <a href="https://portal.azure.com/" target="_blank">Azure ポータル</a>にアクセスします。 
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** の **[アプリの登録]** を選択します。
1. 省略可能な要求を構成するアプリケーションを一覧から選択します。

**UI を使用した省略可能な要求の構成：**

[![UI で省略可能な要求を構成する](./media/active-directory-optional-claims/token-configuration.png)](./media/active-directory-optional-claims/token-configuration.png)

1. **[管理]** で、 **[トークン構成]** を選択します。
   - UI オプション **[トークン構成]** ブレードは、アプリケーション マニフェストを変更することで構成できる Azure AD B2C テナントに登録されているアプリでは使用できません。 詳細については、[Azure Active Directory B2C のカスタム ポリシーを使用した要求の追加とユーザー入力のカスタマイズ](../../active-directory-b2c/configure-user-input.md)に関するページを参照してください  

1. **[省略可能な要求を追加]** を選択します。
1. 構成するトークンの型を選択します。
1. 追加する省略可能な要求を選択します。
1. **[追加]** を選択します。


**アプリケーションマニフェストを使用した省略可能な要求の構成：**

[![アプリ マニフェストを使用して省略可能な要求を構成する方法を示します](./media/active-directory-optional-claims/app-manifest.png)](./media/active-directory-optional-claims/app-manifest.png)

1. **[管理]** で、 **[マニフェスト]** を選択します。 Web ベースのマニフェスト エディターが開き、マニフェストを編集できます。 必要があれば、 **[ダウンロード]** を選択してローカルでマニフェストを編集します。その後、 **[アップロード]** を使用して、アプリケーションにマニフェストを再適用します。 アプリケーションマニフェストの詳細については、「[Azure AD アプリケーションマニフェストについて](reference-app-manifest.md)」を参照してください。

    次のアプリケーション マニフェストのエントリにより、auth_time、ipaddr、および upn の省略可能な要求が ID、アクセス、および SAML トークンに追加されます。

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "accessToken": [
            {
                "name": "ipaddr",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "upn",
                "essential": false
            },
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": false
            }
        ]
    }
    ```

2. 終わったら、 **[保存]** を選択します。 これで、指定された省略可能な要求がアプリケーションのトークンに含まれるようになります。


### <a name="optionalclaims-type"></a>OptionalClaims 型

アプリケーションから要求する省略可能な要求を宣言します。 アプリケーションは、セキュリティ トークン サービスから受信できる 3 種類の各トークン (ID トークン、アクセス トークン、SAML 2 トークン) で返される省略可能な要求を構成できます。 アプリケーションは、トークンの種類ごとに返される異なる省略可能な要求セットを構成できます。 アプリケーション エンティティの OptionalClaims プロパティは、OptionalClaims オブジェクトです。

**表 5:OptionalClaims 型のプロパティ**

| 名前          | Type                       | 説明                                           |
|---------------|----------------------------|-------------------------------------------------------|
| `idToken`     | コレクション (OptionalClaim) | JWT ID トークンで返される省略可能な要求。     |
| `accessToken` | コレクション (OptionalClaim) | JWT アクセス トークンで返される省略可能な要求。 |
| `saml2Token`  | コレクション (OptionalClaim) | JWT SAML トークンで返される省略可能な要求。       |

### <a name="optionalclaim-type"></a>OptionalClaim 型

アプリケーションまたはサービス プリンシパルに関連付けられている省略可能な要求が含まれます。 [OptionalClaims](/graph/api/resources/optionalclaims) 型の idToken、accessToken、および saml2Token プロパティは、OptionalClaim のコレクションです。
特定の要求でサポートされている場合は、AdditionalProperties フィールドを使用して OptionalClaim の動作を変更することもできます。

**表 6:OptionalClaim 型のプロパティ**

| 名前                   | Type                    | 説明                                                                                                                                                                                                                                                                                                   |
|------------------------|-------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `name`                 | Edm.String              | 省略可能な要求の名前。                                                                                                                                                                                                                                                                               |
| `source`               | Edm.String              | 要求のソース (ディレクトリ オブジェクト)。 定義済みの要求と、拡張プロパティのユーザー定義の要求があります。 ソース値が null の場合、この要求は定義済みの省略可能な要求です。 ソース値が user の場合、name プロパティの値はユーザー オブジェクトの拡張プロパティです。 |
| `essential`            | Edm.Boolean             | 値が true の場合、エンド ユーザーから要求された特定のタスクの承認エクスペリエンスを円滑にするために、クライアントに指定された要求が必要です。 既定値は false です。                                                                                                                 |
| `additionalProperties` | コレクション (Edm.String) | 要求の追加のプロパティ。 このコレクションにプロパティが存在する場合、name プロパティに指定された省略可能な要求の動作が変更されます。                                                                                                                                                   |

## <a name="configuring-directory-extension-optional-claims"></a>ディレクトリ拡張機能の省略可能な要求の構成

標準の省略可能な要求セットに加え、拡張機能を含むようにトークンを構成することもできます。 詳細については、[Microsoft Graph extensionProperty のドキュメント](/graph/api/resources/extensionproperty)を参照してください。

スキーマとオープン拡張機能は、省略可能な要求ではサポートされず、AAD-Graph スタイルのディレクトリ拡張機能のみがサポートされます。 ユーザーが設定した追加の識別子や重要な構成オプションなど、アプリで使用できる追加のユーザー情報をアタッチする場合にこの機能が便利です。 例については、このページの最後を参照してください。

ディレクトリ スキーマ拡張機能は、Azure AD のみの機能です。 アプリケーション マニフェストでカスタム拡張機能が要求され、MSA ユーザーがアプリにログインした場合、これらの拡張機能は返されません。

### <a name="directory-extension-formatting"></a>ディレクトリ拡張の形式

アプリケーション マニフェストを使用してディレクトリ拡張機能の省略可能な要求を構成する場合は、拡張機能の完全な名前 (形式: `extension_<appid>_<attributename>`) を使用します。 `<appid>` は、要求を必須にしているアプリケーションの ID と一致する必要があります。

JWT 内では、このような要求は `extn.<attributename>` という形式の名前で発行されます。

SAML トークン内では、このような要求は `http://schemas.microsoft.com/identity/claims/extn.<attributename>` という URI 形式で発行されます。

## <a name="configuring-groups-optional-claims"></a>グループの省略可能な要求を構成する

このセクションでは、グループ要求で使用されるグループ属性を、既定のグループ objectID からオンプレミス Windows Active Directory から同期される属性へ変更するための、省略可能な要求の構成オプションについて説明します。 UI またはアプリケーション マニフェストを使用して、アプリケーションのグループの省略可能な要求を構成できます。

> [!IMPORTANT]
> オンプレミス属性からのグループ要求に関する重要な注意事項などの詳細については、「[Azure AD を使用してアプリケーションに対するグループ要求を構成する](../hybrid/how-to-connect-fed-group-claims.md)」をご覧ください。

**UI を使用したグループの省略可能な要求の構成：**

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 認証が完了したら、ページの右上隅から Azure AD テナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. **[管理]** の **[アプリの登録]** を選択します。
1. 省略可能な要求を構成するアプリケーションを一覧から選択します。
1. **[管理]** で、 **[トークン構成]** を選択します。
1. **[Add groups claim]\(グループの要求の追加\)** を選択します。
1. 返すグループの種類を選択します ( **[セキュリティ グループ]** 、 **[ディレクトリ ロール]** 、 **[すべてのグループ]** 、 **[アプリケーションに割り当てられているグループ]** )。 **[アプリケーションに割り当てられているグループ]** オプションには、アプリケーションに割り当てられたグループのみが含まれます。 **[すべてのグループ]** オプションには、**SecurityGroup**、**DirectoryRole**、および **DistributionList** が含まれますが、 **[アプリケーションに割り当てられているグループ]** は含まれません。 
1. 省略可能: 特定のトークンの種類のプロパティを選択して、オンプレミスのグループ属性を含めるようにグループの要求値を変更したり、要求の種類をロールに変更したりします。
1. **[保存]** を選択します。

**アプリケーション マニフェストを使用したグループの省略可能な要求の構成：**

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 認証が完了したら、ページの右上隅から Azure AD テナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. 省略可能な要求を構成するアプリケーションを一覧から選択します。
1. **[管理]** で、 **[マニフェスト]** を選択します。
1. マニフェスト エディターを使用して、次のエントリを追加します。

   有効な値は次のとおりです。

   - "All" (このオプションには [SecurityGroup]、[DirectoryRole]、および [DistributionList] が含まれます)
   - "SecurityGroup"
   - "DirectoryRole"
   - "ApplicationGroup " (このオプションには、アプリケーションに割り当てられているグループのみが含まれます)

   次に例を示します。

    ```json
    "groupMembershipClaims": "SecurityGroup"
    ```

   既定では、グループの ObjectID は、グループ要求の値に出力されます。  オンプレミス グループ属性を含むように要求の値を変更するか、または要求の種類をロールに変更するには、次のように OptionalClaims 構成を使用します。

1. グループ名の構成を [optional claims]\(オプションの要求\) に設定します。

   [optional claims]\(オプションの要求\) セクションにあるオンプレミス AD グループ属性をトークン内のグループに含める場合は、オプション要求の適用先となるトークンの種類、要求されるオプション要求の名前、および必要な追加のプロパティを指定します。  次に示す複数のトークンの種類が、一覧に表示される可能性があります。

   - OIDC ID トークン用の idToken
   - OAuth アクセス トークン用の accessToken
   - SAML トークン用の Saml2Token

   Saml2Token の種類は、SAML1.1 および SAML2.0 両方の形式のトークンに適用されます。

   関連する各トークンの種類に対して、マニフェストにある OptionalClaims セクションを使用するようにグループ要求を変更します。 OptionalClaims スキーマは次のようになっています。

    ```json
    {
        "name": "groups",
        "source": null,
        "essential": false,
        "additionalProperties": []
    }
    ```

   | 省略可能な要求のスキーマ | 値 |
   |----------|-------------|
   | **name:** | 必ず "groups" になります |
   | **source:** | 使用されていません。 省略するか、null を指定します |
   | **essential:** | 使用されていません。 省略するか、false を指定します |
   | **additionalProperties:** | その他のプロパティのリスト。  有効なオプション: "sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name"、"emit_as_roles" |

   additionalProperties では、"sam_account_name"、"dns_domain_and_sam_account_name"、"netbios_domain_and_sam_account_name" のいずれか 1 つのみが必要です。  複数ある場合、最初の 1 つが使用され、それ以外は無視されます。

   アプリケーションによっては、ロール要求内にユーザーに関するグループ情報が必要になります。  要求の種類をグループ要求からロール要求に変更するには、"emit_as_roles" を追加のプロパティに付け加えます。  グループの値が、ロール要求内に出力されます。

   "emit_as_roles" が使用された場合、ユーザーが割り当て済みとして構成されているアプリケーション ロールは、ロール要求には表示されません。

**例:**

1) OAuth アクセス トークンで netbiosDomain\sAMAccountName 形式でグループ名をグループとして出力する

    **UI の構成：**

    [![省略可能な要求の構成](./media/active-directory-optional-claims/groups-example-1.png)](./media/active-directory-optional-claims/groups-example-1.png)

    **アプリケーションマニフェストのエントリ：**

    ```json
    "optionalClaims": {
        "accessToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "dns_domain_and_sam_account_name"
                ]
            }
        ]
    }
    ```

2) SAML および OIDC ID トークンのロール要求として、 netbiosDomain\sAMAccountName 形式で返されるグループ名を出力します

    **UI の構成：**

    [![マニフェストの省略可能な要求](./media/active-directory-optional-claims/groups-example-2.png)](./media/active-directory-optional-claims/groups-example-2.png)

    **アプリケーションマニフェストのエントリ：**

    ```json
    "optionalClaims": {
        "saml2Token": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ],
        "idToken": [
            {
                "name": "groups",
                "additionalProperties": [
                    "netbios_name_and_sam_account_name",
                    "emit_as_roles"
                ]
            }
        ]
    }
    ```

## <a name="optional-claims-example"></a>省略可能な要求の例

このセクションでは、シナリオを使って、省略可能な要求機能をアプリケーションに使用する手順について説明します。
アプリケーションの ID 構成に関するプロパティを更新し、省略可能な要求を有効にして構成するには、複数のオプションがあります。

- **トークン構成** UI を使用できます (次の例を参照)
- **マニフェスト** を使用できます (次の例を参照)。 マニフェストの概要については、まず [Azure AD アプリケーション マニフェストの概要に関するドキュメント](./reference-app-manifest.md)を参照してください。
- また、[Microsoft Graph API](/graph/use-the-api) を使用してアプリケーションを更新するアプリケーションを作成することもできます。 Microsoft Graph API リファレンス ガイドにある [OptionalClaims](/graph/api/resources/optionalclaims) という種類を使用すると、省略可能な要求の構成に役立ちます。

**例:**

次の例では、**トークン構成** UI と **マニフェスト** を使用して、アプリケーション用のアクセス、ID、および SAML トークンに省略可能な要求を追加します。 アプリケーションが受け取ることができるトークンの型ごとに、さまざまな省略可能な要求が追加されます：

- ID トークンには、完全な形式 (`<upn>_<homedomain>#EXT#@<resourcedomain>`) のフェデレーション ユーザーの UPN が含まれるようになります。
- 他のクライアントがこのアプリケーションに要求するアクセス トークンには、auth_time 要求が含まれるようになります。
- SAML トークンに skypeId ディレクトリ スキーマ拡張機能が含まれるようになります (この例では、このアプリのアプリ ID は ab603c56068041afb2f6832e2a17e237 です)。 SAML トークンは Skype ID を `extension_skypeId` として公開します。

**UI の構成：**

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 認証が完了したら、ページの右上隅から Azure AD テナントを選択します。

1. **Azure Active Directory** を検索して選択します。

1. **[管理]** の **[アプリの登録]** を選択します。

1. 省略可能な要求を構成するアプリケーションを一覧から探して選択します。

1. **[管理]** で、 **[トークン構成]** を選択します。

1. **[省略可能な要求の追加]** を選択し、 **[ID]** トークンの型を選択して、要求の一覧から **[upn]** を選択し、 **[追加]** を選択します。

1. **[省略可能な要求の追加]** を選択し、 **[アクセス]** トークンの型を選択して、要求の一覧から **auth_time** を選択し、 **[追加]** を選択します。

1. [トークン構成の概要] 画面で、 **[upn]** の横にある鉛筆アイコンを選択し、 **[外部で認証された]** の切り替えを選択して、 **[保存]** を選択します。

1. **[省略可能な要求の追加]** を選択し、 **[SAML]** トークンの型を選択して、要求の一覧から **[extn. skypeID]** を選択し (skypeID という Azure AD ユーザーオブジェクトを作成した場合にのみ適用されます)、 **[追加]** を選択します。

    [![SAML トークンの省略可能な要求](./media/active-directory-optional-claims/token-config-example.png)](./media/active-directory-optional-claims/token-config-example.png)

**マニフェストの構成：**

1. <a href="https://portal.azure.com/" target="_blank">Azure portal</a> にサインインします。
1. 認証が完了したら、ページの右上隅から Azure AD テナントを選択します。
1. **Azure Active Directory** を検索して選択します。
1. 省略可能な要求を構成するアプリケーションを一覧から探して選択します。
1. **[管理]** で、 **[マニフェスト]** を選択してインライン マニフェスト エディターを開きます。
1. このエディターを使用して、マニフェストを直接編集できます。 マニフェストは、[アプリケーション エンティティ](./reference-app-manifest.md)のスキーマに従っています。保存されると、マニフェストの書式が自動的に構成されます。 新しい要素が `OptionalClaims` プロパティに追加されます。

    ```json
    "optionalClaims": {
        "idToken": [
            {
                "name": "upn",
                "essential": false,
                "additionalProperties": [
                    "include_externally_authenticated_upn"
                ]
            }
        ],
        "accessToken": [
            {
                "name": "auth_time",
                "essential": false
            }
        ],
        "saml2Token": [
            {
                "name": "extension_ab603c56068041afb2f6832e2a17e237_skypeId",
                "source": "user",
                "essential": true
            }
        ]
    }
    ```

1. マニフェストの更新が完了したら、 **[保存]** を選択してマニフェストを保存します。

## <a name="next-steps"></a>次のステップ

Azure AD に用意されている標準の要求について詳しく学びます。

- [ID トークン](id-tokens.md)
- [アクセス トークン](access-tokens.md)
