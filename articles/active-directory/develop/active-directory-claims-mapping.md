---
title: Azure AD テナントのアプリ要求をカスタマイズする (PowerShell)
titleSuffix: Microsoft identity platform
description: このページでは、Azure Active Directory の要求のマッピングについて説明します。
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.workload: identity
ms.topic: how-to
ms.date: 08/25/2020
ms.author: ryanwi
ms.reviewer: paulgarn, hirsin, jeedes, luleon
ms.openlocfilehash: 2d65889a841655fe27994d3855f30f7a7e20e1ed
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "94647598"
---
# <a name="how-to-customize-claims-emitted-in-tokens-for-a-specific-app-in-a-tenant-preview"></a>方法:テナントの特定のアプリケーションに対するトークンに出力された要求のカスタマイズ (プレビュー)

> [!NOTE]
> この機能は、現在ポータルで提供されている[要求のカスタマイズ](active-directory-saml-claims-customization.md)に取って代わり、そのカスタマイズに優先します。 このドキュメントで詳しく説明している Graph/PowerShell の方法に加えて、ポータルを使用して、同じアプリケーションの要求をカスタマイズすると、そのアプリケーションに対して発行されたトークンは、ポータルの構成を無視します。 このドキュメントで詳しく説明した方法で行った構成は、ポータルには反映されません。

この機能は、テナント管理者が、テナントの特定のアプリケーションに対するトークンに出力された要求をカスタマイズするときに使用します。 要求のマッピング ポリシーを使用すると、次の操作を行うことができます。

- トークンに含める要求を選択する。
- まだ存在しない要求の種類を作成する。
- 特定の要求で出力されたデータのソースを選択または変更する。

> [!NOTE]
> この機能は現在パブリック プレビューの段階です。 変更を元に戻すか、削除できるように準備しておいてください。 機能は、パブリック プレビュー期間中、すべての Azure Active Directory (Azure AD) サブスクリプションで使用できます。 ただし、この機能が一般公開された後は、機能の一部で Azure AD Premium サブスクリプションが必要になる場合があります。 この機能は、WS-Fed、SAML、OAuth、OpenID Connect の各プロトコルについて、要求のマッピング ポリシーの構成をサポートしています。

## <a name="claims-mapping-policy-type"></a>要求のマッピング ポリシーの種類

Azure AD では、**ポリシー** オブジェクトは、組織の個々のアプリケーションまたはすべてのアプリケーションに適用される規則のセットを表します。 それぞれのポリシーの種類は、割り当てられているオブジェクトに適用されるプロパティのセットを含む一意の構造体を持ちます。

要求のマッピング ポリシーは **ポリシー** オブジェクトの一種であり、特定のアプリケーションに対して発行されたトークンに出力される要求を変更します。

## <a name="claim-sets"></a>要求セット

トークンで使用する方法とタイミングを定義する要求のセットがあります。

| 要求セット | 説明 |
|---|---|
| コア要求セット | ポリシーに関係なく、すべてのトークンに提示されます。 また、この要求は制限付きと見なされ、変更できません。 |
| 基本要求セット | コア要求セットの他にトークンに対して既定で出力される要求が含まれます。 要求のマッピング ポリシーを使用して基本要求を省略したり変更したりできます。 |
| 制限付き要求セット | ポリシーを使用して変更することはできません。 データ ソースを変更できず、要求を生成するときに変換が適用されません。 |

### <a name="table-1-json-web-token-jwt-restricted-claim-set"></a>表 1:JSON Web トークン (JWT) 制限付き要求セット

| 要求の種類 (名前) |
| ----- |
| _claim_names |
| _claim_sources |
| access_token |
| account_type |
| acr |
| actor |
| actortoken |
| aio |
| altsecid |
| amr |
| app_chain |
| app_displayname |
| app_res |
| appctx |
| appctxsender |
| appid |
| appidacr |
| assertion |
| at_hash |
| aud |
| auth_data |
| auth_time |
| authorization_code |
| azp |
| azpacr |
| c_hash |
| ca_enf |
| cc |
| cert_token_use |
| client_id |
| cloud_graph_host_name |
| cloud_instance_name |
| cnf |
| code |
| controls |
| credential_keys |
| csr |
| csr_type |
| deviceid |
| dns_names |
| domain_dns_name |
| domain_netbios_name |
| e_exp |
| email |
| endpoint |
| enfpolids |
| exp |
| expires_on |
| grant_type |
| graph |
| group_sids |
| groups |
| hasgroups |
| hash_alg |
| home_oid |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration` |
| `http://schemas.microsoft.com/ws/2008/06/identity/claims/expired` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name` |
| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` |
| iat |
| identityprovider |
| idp |
| in_corp |
| instance |
| ipaddr |
| isbrowserhostedapp |
| iss |
| jwk |
| key_id |
| key_type |
| mam_compliance_url |
| mam_enrollment_url |
| mam_terms_of_use_url |
| mdm_compliance_url |
| mdm_enrollment_url |
| mdm_terms_of_use_url |
| nameid |
| nbf |
| netbios_name |
| nonce |
| oid |
| on_prem_id |
| onprem_sam_account_name |
| onprem_sid |
| openid2_id |
| password |
| polids |
| pop_jwk |
| preferred_username |
| previous_refresh_token |
| primary_sid |
| puid |
| pwd_exp |
| pwd_url |
| redirect_uri |
| refresh_token |
| refreshtoken |
| request_nonce |
| resource |
| role |
| roles |
| scope |
| scp |
| sid |
| signature |
| signin_state |
| src1 |
| src2 |
| sub |
| tbid |
| tenant_display_name |
| tenant_region_scope |
| thumbnail_photo |
| tid |
| tokenAutologonEnabled |
| trustedfordelegation |
| unique_name |
| upn |
| user_setting_sync_url |
| username |
| uti |
| ver |
| verified_primary_email |
| verified_secondary_email |
| wids |
| win_ver |

### <a name="table-2-saml-restricted-claim-set"></a>表 2:SAML 制限付き要求セット

| 要求の種類 (URI) |
| ----- |
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expiration`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/expired`|
|`http://schemas.microsoft.com/identity/claims/accesstoken`|
|`http://schemas.microsoft.com/identity/claims/openid2_id`|
|`http://schemas.microsoft.com/identity/claims/identityprovider`|
|`http://schemas.microsoft.com/identity/claims/objectidentifier`|
|`http://schemas.microsoft.com/identity/claims/puid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier [MR1]`|
|`http://schemas.microsoft.com/identity/claims/tenantid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationinstant`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/authenticationmethod`|
|`http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groups`|
|`http://schemas.microsoft.com/claims/groups.link`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/role`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/wids`|
|`http://schemas.microsoft.com/2014/09/devicecontext/claims/iscompliant`|
|`http://schemas.microsoft.com/2014/02/devicecontext/claims/isknown`|
|`http://schemas.microsoft.com/2012/01/devicecontext/claims/ismanaged`|
|`http://schemas.microsoft.com/2014/03/psso`|
|`http://schemas.microsoft.com/claims/authnmethodsreferences`|
|`http://schemas.xmlsoap.org/ws/2009/09/identity/claims/actor`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/samlissuername`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/confirmationkey`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authorizationdecision`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/authentication`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/sid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarygroupsid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlyprimarysid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/denyonlysid`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/denyonlywindowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdeviceclaim`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsdevicegroup`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsfqbnversion`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowssubauthority`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsuserclaim`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/x500distinguishedname`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/spn`|
|`http://schemas.microsoft.com/ws/2008/06/identity/claims/ispersistent`|
|`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/privatepersonalidentifier`|
|`http://schemas.microsoft.com/identity/claims/scope`|

## <a name="claims-mapping-policy-properties"></a>要求のマッピング ポリシーのプロパティ

出力される要求内容とデータの送信元を制御するには、要求のマッピング ポリシーのプロパティを使用します。 ポリシーが設定されていない場合は、アプリケーションが受信するように選んだコア要求セット、基本要求セット、[省略可能な要求](active-directory-optional-claims.md)を含むトークンが発行されます。

> [!NOTE]
> コア要求セット内の要求については、このプロパティの設定に関係なく、すべてのトークンに提示されます。

### <a name="include-basic-claim-set"></a>Include 基本要求セット

**文字列:** IncludeBasicClaimSet

**データ型**: ブール値 (True または False)

**概要:** このプロパティは、基本要求セットが、このポリシーの影響を受けるトークンに含まれるかどうかを判断します。

- True に設定されている場合、基本要求セット内のすべての要求が、ポリシーの影響を受けるトークンに出力されます。
- False に設定されている場合、基本要求セット内の要求は、同じポリシーの要求スキーマ プロパティに個別に追加されない限り、トークンに追加されません。



### <a name="claims-schema"></a>要求スキーマ

**文字列:** ClaimsSchema

**データ型**: 1 つ以上の要求スキーマ エントリを含む JSON BLOB

**概要:** このプロパティは、基本要求セットやコア要求セットのほか、ポリシーの影響を受けるトークンにどの要求を提示するかを定義します。
このプロパティで定義されている要求スキーマ エントリごとに、特定の情報が必要です。 データのソース (**Value**、**Source/ID ペア**、または **Source/ExtensionID ペア**) と、どの要求としてデータを出力するか (**要求の種類**) を指定します。

### <a name="claim-schema-entry-elements"></a>要求スキーマ エントリ要素

**値:** 値要素では、静的な値が、要求に出力されるデータとして定義されます。

**ソース/ID ペア:** ソース要素と ID 要素では、要求内のデータのソースが定義されます。

**Source/ExtensionID ペア:** Source と ExtensionID 要素は、要求内のデータのソースであるディレクトリ スキーマ拡張属性を定義します。 詳細については、「[要求でディレクトリ スキーマ拡張属性を使用する](active-directory-schema-extensions.md)」を参照してください。

ソース要素を次のいずれかの値に設定します。

- "user":要求のデータは、ユーザー オブジェクトのプロパティです。
- "application":要求のデータは、アプリケーション (クライアント) のサービス プリンシパルのプロパティです。
- "resource":要求のデータは、リソースのサービス プリンシパルのプロパティです。
- "audience":要求のデータは、トークン (クライアントかリソースのいずれかのサービス プリンシパル) の対象ユーザーであるサービス プリンシパルのプロパティです。
- "company":要求のデータは、リソース テナントの会社のオブジェクトのプロパティです。
- "transformation":要求のデータは、要求からの変換です (この記事の後述の「要求の変換」セクションを参照してください)。

ソースが transformation の場合、**TransformationID** 要素も、この要求の定義に含める必要があります。

ID 要素により、ソースのどのプロパティが要求の値を提供するかが特定されます。 次の表は、ソースの各値に対して有効な ID の値を示しています。

#### <a name="table-3-valid-id-values-per-source"></a>表 3:ソースごとに有効な ID 値

| source | id | 説明 |
|-----|-----|-----|
| User | surname | surname |
| User | givenname | 名 |
| User | displayName | 表示名 |
| User | objectid | ObjectID |
| User | mail | 電子メール アドレス |
| User | userprincipalname | ユーザー プリンシパル名 |
| User | department|部署|
| User | onpremisessamaccountname | オンプレミスの SAM のアカウント名 |
| User | netbiosname| NetBios 名 |
| User | dnsdomainname | DNS ドメイン名 |
| User | onpremisesecurityidentifier | オンプレミスのセキュリティ ID |
| User | companyname| 組織名 |
| User | streetaddress | 番地 |
| User | postalcode | 郵便番号 |
| User | preferredlanguage | 優先言語 |
| User | onpremisesuserprincipalname | オンプレミスの UPN |*
| User | mailNickname | メールのニックネーム |
| User | extensionattribute1 | 拡張属性 1 |
| User | extensionattribute2 | 拡張属性 2 |
| User | extensionattribute3 | 拡張属性 3 |
| User | extensionattribute4 | 拡張属性 4 |
| User | extensionattribute5 | 拡張属性 5 |
| User | extensionattribute6 | 拡張属性 6 |
| User | extensionattribute7 | 拡張属性 7 |
| User | extensionattribute8 | 拡張属性 8 |
| User | extensionattribute9 | 拡張属性 9 |
| User | extensionattribute10 | 拡張属性 10 |
| User | extensionattribute11 | 拡張属性 11 |
| User | extensionattribute12 | 拡張属性 12 |
| User | extensionattribute13 | 拡張属性 13 |
| User | extensionattribute14 | 拡張属性 14 |
| User | extensionattribute15 | 拡張属性 15 |
| User | othermail | その他のメール |
| User | country | 国/リージョン |
| User | city | City |
| User | state | State |
| User | jobtitle | 役職 |
| User | employeeid | 従業員 ID |
| User | facsimiletelephonenumber | ファックスの電話番号 |
| User | assignedroles | ユーザーに割り当てられたアプリ ロールの一覧|
| アプリケーション、リソース、対象ユーザー | displayName | 表示名 |
| アプリケーション、リソース、対象ユーザー | objectid | ObjectID |
| アプリケーション、リソース、対象ユーザー | tags | サービス プリンシパル タグ |
| [会社] | tenantcountry | テナントの国/リージョン |

**TransformationID:** TransformationID 要素は、ソース要素が "transformation" に設定されている場合にのみ指定する必要があります。

- この要素は、この要求のデータを生成する方法を定義する、**ClaimsTransformation** プロパティの変換エントリの ID 要素と一致する必要があります。

**要求の種類:** **JwtClaimType** 要素と **SamlClaimType** 要素は、この要求スキーマ エントリが、どの要求を参照するかを定義します。

- JwtClaimType には、JWT に出力する要求の名前を含める必要があります。
- SamlClaimType には、SAML トークンに出力する要求の URI を含める必要があります。

* **onPremisesUserPrincipalName 属性:** 代替 ID を使用する場合、オンプレミスの userPrincipalName 属性は Azure AD の onPremisesUserPrincipalName 属性と同期されます。 この属性は、代替 ID が構成されている場合にのみ使用できますが、MS Graph ベータ版 (https://graph.microsoft.com/beta/me/ ) から使用することもできます。

> [!NOTE]
> 制限付き要求セット内の要求の名前と URI を、要求の種類の要素に使用することはできません。 詳細については、この記事の後述の「例外と制限事項」セクションを参照してください。

### <a name="claims-transformation"></a>要求の変換

**文字列:** ClaimsTransformation

**データ型**: 1 つ以上の変換エントリを含む JSON BLOB

**概要:** このプロパティを使用して、共通の変換をソース データに適用し、要求スキーマで指定された要求の出力データを生成します。

**ID:** ID 要素を使用して、TransformationID 要求スキーマ エントリのこの変換エントリを参照します。 この値は、このポリシー内の変換エントリごとに一意である必要があります。

**TransformationMethod:** TransformationMethod 要素は、要求のデータを生成するときに実行される操作を特定します。

選択した方法に基づいて、一連の入力と出力が想定されます。 **InputClaims** 要素、**InputParameters** 要素、**OutputClaims** 要素を使用して入出力を定義します。

#### <a name="table-4-transformation-methods-and-expected-inputs-and-outputs"></a>表 4:変換方法と想定される入出力

|TransformationMethod|想定される入力|想定される出力|説明|
|-----|-----|-----|-----|
|Join|string1, string2, separator|outputClaim|入力文字列の間に区切り記号を使用して、その文字列を結合します。 例: string1:"foo@bar.com" , string2:"sandbox" , separator:"." の結果は outputClaim:"foo@bar.com.sandbox" になります|
|ExtractMailPrefix|電子メールまたは UPN|抽出された文字列|ExtensionAttributes 1-15 または、UPN やユーザーのメール アドレス値 (johndoe@contoso.com など) を格納するその他のあらゆるスキーマ拡張。 メール アドレスのローカル部分を抽出します。 例: mail:"foo@bar.com" の結果は outputClaim:"foo" になります。 \@ 記号がない場合、元の入力文字列がそのまま返されます。|

**InputClaims:** InputClaims 要素を使用して、要求スキーマ エントリから変換にデータを渡します。 この要素には 2 つの属性があります。**ClaimTypeReferenceId** と **TransformationClaimType** です。

- **ClaimTypeReferenceId** は要求スキーマ エントリの ID 要素と結合され、該当する入力要求を検索します。
- **TransformationClaimType** は、この入力に一意の名前を指定するときに使用されます。 この名前は、変換方法に対する想定される入力のいずれかと一致する必要があります。

**InputParameters:** InputParameters 要素を使用して、定数値を変換に渡します。 この要素には 2 つの属性があります。**Value** と **ID** です。

- **Value** は、渡される実際の定数値です。
- **ID** は、入力に一意の名前を指定するときに使用されます。 この名前は、変換方法に対する想定される入力のいずれかと一致する必要があります。

**OutputClaims:** OutputClaims 要素を使用して、変換によって生成されたデータを保持し、そのデータを要求スキーマ エントリに関連付けます。 この要素には 2 つの属性があります。**ClaimTypeReferenceId** と **TransformationClaimType** です。

- **ClaimTypeReferenceId** は要求スキーマ エントリの ID と結合され、該当する出力要求を検索します。
- **TransformationClaimType** は、出力に一意の名前を指定するときに使用されます。 この名前は、変換方法に対する想定される出力のいずれかと一致する必要があります。

### <a name="exceptions-and-restrictions"></a>例外と制限事項

**SAML NameID と UPN:** NameID と UPN の値のソース属性と、許可される要求変換には、制限があります。 表 5 と表 6 を参照して、使用できる値を確認してください。

#### <a name="table-5-attributes-allowed-as-a-data-source-for-saml-nameid"></a>表 5:SAML NameID のデータ ソースとして許可されている属性

|source|id|説明|
|-----|-----|-----|
| User | mail|電子メール アドレス|
| User | userprincipalname|ユーザー プリンシパル名|
| User | onpremisessamaccountname|オンプレミスの Sam のアカウント名|
| User | employeeid|従業員 ID|
| User | extensionattribute1 | 拡張属性 1 |
| User | extensionattribute2 | 拡張属性 2 |
| User | extensionattribute3 | 拡張属性 3 |
| User | extensionattribute4 | 拡張属性 4 |
| User | extensionattribute5 | 拡張属性 5 |
| User | extensionattribute6 | 拡張属性 6 |
| User | extensionattribute7 | 拡張属性 7 |
| User | extensionattribute8 | 拡張属性 8 |
| User | extensionattribute9 | 拡張属性 9 |
| User | extensionattribute10 | 拡張属性 10 |
| User | extensionattribute11 | 拡張属性 11 |
| User | extensionattribute12 | 拡張属性 12 |
| User | extensionattribute13 | 拡張属性 13 |
| User | extensionattribute14 | 拡張属性 14 |
| User | extensionattribute15 | 拡張属性 15 |

#### <a name="table-6-transformation-methods-allowed-for-saml-nameid"></a>表 6:SAML NameID の許可されている変換方法

| TransformationMethod | 制限 |
| ----- | ----- |
| ExtractMailPrefix | なし |
| Join | 結合されているサフィックスは、リソース テナントの確認済みドメインである必要があります。 |

### <a name="custom-signing-key"></a>カスタム署名キー

要求のマッピング ポリシーを有効にするには、カスタム署名キーをサービス プリンシパル オブジェクトに割り当てる必要があります。 これにより、要求のマッピング ポリシーの作成者によってトークンが変更されたことを示す受信確認が確実にアプリケーションに届くため、アプリケーションは悪意のあるアクターによって作成された要求のマッピング ポリシーから保護されます。 カスタム署名キーを追加するには、Azure PowerShell コマンドレット [`New-AzureADApplicationKeyCredential`](/powerShell/module/Azuread/New-AzureADApplicationKeyCredential) を使用して、ご自身のアプリケーション オブジェクトの証明書キー資格情報を作成します。

要求のマッピングが有効なアプリでは、[OpenID Connect メタデータ要求](v2-protocols-oidc.md#fetch-the-openid-connect-metadata-document)に `appid={client_id}` を追加して、トークン署名キーを検証する必要があります。 使用する必要がある OpenID Connect メタデータ ドキュメントのフォーマットは次のとおりです。

```
https://login.microsoftonline.com/{tenant}/v2.0/.well-known/openid-configuration?appid={client-id}
```

### <a name="cross-tenant-scenarios"></a>テナント間のシナリオ

要求のマッピング ポリシーは、ゲスト ユーザーには適用されません。 要求のマッピング ポリシーがサービス プリンシパルに割り当てられたアプリケーションに、ゲスト ユーザーがアクセスしようとすると、既定のトークンが発行されます (ポリシーの効力がなくなります)。

## <a name="claims-mapping-policy-assignment"></a>要求のマッピング ポリシーの割り当て

要求のマッピング ポリシーは、サービス プリンシパル オブジェクトにのみ割り当てることができます。

### <a name="example-claims-mapping-policies"></a>要求のマッピング ポリシーのサンプル

Azure AD では、特定のサービス プリンシパルに対するトークンに発行された要求をカスタマイズできる場合、多くのシナリオが考えられます。 このセクションでは、要求のマッピング ポリシーの種類を使用する方法を理解するうえで役に立つ、一般的なシナリオをいくつか取り上げて説明します。

要求のマッピングポリシーを作成するときに、トークンのディレクトリ スキーマ拡張属性から要求を出力することもできます。 `ClaimsSchema` 要素の *ID* ではなく、拡張属性の *ExtensionID* を使用します。  拡張属性の詳細については、[ディレクトリ スキーマ拡張属性の使用](active-directory-schema-extensions.md)に関するページをご覧ください。

#### <a name="prerequisites"></a>前提条件

次の例では、サービス プリンシパルのポリシーを作成、更新、リンク、および削除します。 Azure AD に慣れていない場合は、こうした例を確認する前に、[Azure AD テナントを取得する方法](quickstart-create-new-tenant.md)について学習することをお勧めします。

使用を開始するには、次の手順を実行します。

1. 最新版の [Azure AD PowerShell モジュール パブリック プレビュー リリース](https://www.powershellgallery.com/packages/AzureADPreview)をダウンロードします。
1. Connect コマンドを実行して、Azure AD 管理者アカウントにサインインします。 新しいセッションを開始するたびにこのコマンドを実行します。

   ``` powershell
   Connect-AzureAD -Confirm
   ```
1. 組織に作成されているすべてのポリシーを表示するには、次のコマンドを実行します。 ポリシーが想定どおりに作成されていることを確認するために、次のシナリオのほとんどの操作の後にこのコマンドを実行することをお勧めします。

   ``` powershell
   Get-AzureADPolicy
   ```

#### <a name="example-create-and-assign-a-policy-to-omit-the-basic-claims-from-tokens-issued-to-a-service-principal"></a>例:サービス プリンシパルに対して発行されたトークンから基本要求を省略するために、ポリシーを作成して割り当てる

この例では、リンクされたサービス プリンシパルに対して発行されたトークンから、基本要求セットを削除するポリシーを作成します。

1. 要求のマッピング ポリシーを作成します。 このポリシーは、特定のサービス プリンシパルにリンクされ、トークンから基本要求セットを削除します。
   1. ポリシーを作成するには、このコマンドを実行します。

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"false"}}') -DisplayName "OmitBasicClaims" -Type "ClaimsMappingPolicy"
      ```
   2. 新しいポリシーを表示し、ポリシーの ObjectId を取得するには、次のコマンドを実行します。

      ``` powershell
      Get-AzureADPolicy
      ```
1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。
   1. 組織のすべてのサービス プリンシパルを表示するには、[Microsoft Graph API にクエリを実行](/graph/traverse-the-graph)します。 または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) で、Azure AD アカウントにサインインします。
   2. サービス プリンシパルの ObjectId がある場合は、次のコマンドを実行します。

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-to-include-the-employeeid-and-tenantcountry-as-claims-in-tokens-issued-to-a-service-principal"></a>例:サービス プリンシパルに対して発行されたトークンに、EmployeeID と TenantCountry を要求として含めるために、ポリシーを作成して割り当てる

この例では、リンクされたサービス プリンシパルに対して発行されたトークンに、EmployeeID と TenantCountry を追加するポリシーを作成します。 EmployeeID は、SAML トークンと JWT の両方で名前要求の種類として出力されます。 TenantCountry は、SAML トークンと JWT の両方で国/リージョン要求の種類として出力されます。 この例では、操作を続行し、トークンに基本要求セットを含めます。

1. 要求のマッピング ポリシーを作成します。 このポリシーは、特定のサービス プリンシパルにリンクされ、EmployeeID 要求と TenantCountry 要求をトークンに追加します。
   1. ポリシーを作成するには、次のコマンドを実行します。

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema": [{"Source":"user","ID":"employeeid","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/employeeid","JwtClaimType":"name"},{"Source":"company","ID":"tenantcountry","SamlClaimType":"http://schemas.xmlsoap.org/ws/2005/05/identity/claims/country","JwtClaimType":"country"}]}}') -DisplayName "ExtraClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 新しいポリシーを表示し、ポリシーの ObjectId を取得するには、次のコマンドを実行します。

      ``` powershell
      Get-AzureADPolicy
      ```
1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。
   1. 組織のすべてのサービス プリンシパルを表示するには、[Microsoft Graph API にクエリを実行](/graph/traverse-the-graph)します。 または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) で、Azure AD アカウントにサインインします。
   2. サービス プリンシパルの ObjectId がある場合は、次のコマンドを実行します。

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

#### <a name="example-create-and-assign-a-policy-that-uses-a-claims-transformation-in-tokens-issued-to-a-service-principal"></a>例:サービス プリンシパルに対して発行されたトークンで要求変換を使用するポリシーを作成して割り当てる

この例では、リンクされたサービス プリンシパルに対して発行された JWT に、カスタム要求 "JoinedData" を出力するポリシーを作成します。 この要求には、ユーザー オブジェクトの extensionattribute1 属性に格納されたデータと ".sandbox" を結合して作成された値が追加されます。 この例では、トークンで基本要求セットを除外します。

1. 要求のマッピング ポリシーを作成します。 このポリシーは、特定のサービス プリンシパルにリンクされ、EmployeeID 要求と TenantCountry 要求をトークンに追加します。
   1. ポリシーを作成するには、次のコマンドを実行します。

      ``` powershell
      New-AzureADPolicy -Definition @('{"ClaimsMappingPolicy":{"Version":1,"IncludeBasicClaimSet":"true", "ClaimsSchema":[{"Source":"user","ID":"extensionattribute1"},{"Source":"transformation","ID":"DataJoin","TransformationId":"JoinTheData","JwtClaimType":"JoinedData"}],"ClaimsTransformations":[{"ID":"JoinTheData","TransformationMethod":"Join","InputClaims":[{"ClaimTypeReferenceId":"extensionattribute1","TransformationClaimType":"string1"}], "InputParameters": [{"ID":"string2","Value":"sandbox"},{"ID":"separator","Value":"."}],"OutputClaims":[{"ClaimTypeReferenceId":"DataJoin","TransformationClaimType":"outputClaim"}]}]}}') -DisplayName "TransformClaimsExample" -Type "ClaimsMappingPolicy"
      ```

   2. 新しいポリシーを表示し、ポリシーの ObjectId を取得するには、次のコマンドを実行します。

      ``` powershell
      Get-AzureADPolicy
      ```
1. サービス プリンシパルにポリシーを割り当てます。 サービス プリンシパルの ObjectId も取得する必要があります。
   1. 組織のすべてのサービス プリンシパルを表示するには、[Microsoft Graph API にクエリを実行](/graph/traverse-the-graph)します。 または、[Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) で、Azure AD アカウントにサインインします。
   2. サービス プリンシパルの ObjectId がある場合は、次のコマンドを実行します。

      ``` powershell
      Add-AzureADServicePrincipalPolicy -Id <ObjectId of the ServicePrincipal> -RefObjectId <ObjectId of the Policy>
      ```

## <a name="see-also"></a>関連項目

- SAML トークンで発行された要求を Azure portal でカスタマイズする方法については、「[方法: エンタープライズ アプリケーションの SAML トークンで発行された要求のカスタマイズ](active-directory-saml-claims-customization.md)」を参照してください。
- 拡張属性の詳細については、[要求でのディレクトリ スキーマ拡張属性の使用](active-directory-schema-extensions.md)に関するページをご覧ください。
