---
title: Android での Microsoft ID プラットフォームのアカウントとテナント プロファイル | Azure
description: Android 用の Microsoft ID プラットフォーム アカウントの概要
services: active-directory
author: shoatman
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.devlang: java
ms.date: 09/14/2019
ms.author: shoatman
ms.custom: aaddev, devx-track-java
ms.reviewer: shoatman
ms.openlocfilehash: fac66e8f82ea4c04e866b28fed5f8d0860ab81ef
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755976"
---
# <a name="accounts--tenant-profiles-android"></a>アカウントとテナント プロファイル (Android)

この記事では、Microsoft ID プラットフォームにおける `account` の概要を示します。

Microsoft Authentication Library (MSAL) API では、*ユーザー* という用語が *アカウント* という用語に置き換えられています。 1 つの理由は、ユーザー (人間またはソフトウェア エージェント) が複数のアカウントを持っている場合がある、あるいは使用できるためです。 これらのアカウントは、ユーザー自身の組織、またはユーザーがメンバーになっている他の組織に存在する場合があります。

Microsoft ID プラットフォームのアカウントは、次のもので構成されます。

- 一意識別子。  
- アカウントの所有権/制御を示すために使用される 1 つまたは複数の資格情報。
- 次のような属性で構成される 1 つまたは複数のプロファイル:
  - 画像、名、姓、役職、勤務先所在地
- アカウントには、権限のソースまたはレコードのシステムがあります。 これは、アカウントが作成され、そのアカウントに関連付けられている資格情報が格納される場所です。 Microsoft ID プラットフォームのようなマルチテナント システムでは、レコードのシステムは、アカウントが作成された `tenant` です。 このテナントは `home tenant` とも呼ばれます。
- Microsoft ID プラットフォームのアカウントには、次のレコードのシステムがあります。
  - Azure Active Directory (Azure Active Directory B2C を含む)。
  - Microsoft アカウント (Live)。
- Microsoft ID プラットフォームの外部にあるレコードのシステムからのアカウントは、次のような Microsoft ID プラットフォーム内で表されます。
  - 接続されたオンプレミス ディレクトリ (Windows Server Active Directory) からの ID
  - LinkedIn や GitHub などからの外部 ID。
  このような場合、アカウントには、元のレコードのシステムと、Microsoft ID プラットフォーム内のレコードのシステムの両方があります。
- Microsoft ID プラットフォームでは、1 つのアカウントを使用して、複数の組織 (Azure Active Directory テナント) に属しているリソースにアクセスできます。
  - あるレコードのシステム (AAD テナント A) からのアカウントで別のレコードのシステム (AAD テナント B) のリソースにアクセスできることを記録するには、リソースが定義されているテナントでアカウントを表す必要があります。 これを行うには、システム B でシステム A からのアカウントのローカル レコードを作成します。
  - アカウントの表現である、このローカル レコードは、元のアカウントにバインドされます。
  - MSAL では、このローカル レコードを `Tenant Profile` として公開します。
  - テナント プロファイルには、役職、勤務先所在地、連絡先情報など、ローカル コンテキストに適したさまざまな属性を含めることができます。
- 1 つまたは複数のテナントにアカウントが存在する場合があるため、アカウントに複数のプロファイルが含まれる可能性があります。

> [!NOTE]
> MSAL では、Microsoft アカウント システム (Live、MSA) を、Microsoft ID プラットフォーム内の別のテナントとして扱います。 Microsoft アカウント テナントのテナント ID は、`9188040d-6c67-4c5b-b112-36a304b66dad` です

## <a name="account-overview-diagram"></a>アカウントの概要図

![アカウントの概要図](./media/accounts-overview/accounts-overview.svg)

上の図の説明です。

- アカウント `bob@contoso.com` は、オンプレミスの Windows Server Active Directory (元のレコードのオンプレミス システム) に作成されます。
- アカウント `tom@live.com` は Microsoft アカウント テナントに作成されます。
- `bob@contoso.com` では、次の Azure Active Directory テナントの少なくとも 1 つのリソースにアクセスできます。
  - contoso.com (レコードのクラウド システム - レコードのオンプレミス システムにリンクされている)
  - fabrikam.com
  - woodgrovebank.com
  - `bob@contoso.com` 用のテナント プロファイルは、これらの各テナントに存在します。
- `tom@live.com` では、次の Microsoft テナントのリソースにアクセスできます。
  - contoso.com
  - fabrikam.com
  - `tom@live.com` 用のテナント プロファイルは、これらの各テナントに存在します。
- 他のテナントの Tom と Bob に関する情報は、レコードのシステムのものとは異なる場合があります。 役職や勤務先所在地などの属性が異なる場合があります。 各組織 (Azure Active Directory テナント) 内のグループやロールのメンバーである場合があります。 この情報を bob@contoso.com テナント プロファイルと呼びます。

図では、bob@contoso.com および tom@live.com で、異なる Azure Active Directory テナント内のリソースにアクセスすることができます。 詳細については、「[Azure portal で Azure Active Directory B2B コラボレーション ユーザーを追加する](../external-identities/add-users-administrator.md)」を参照してください。

## <a name="accounts-and-single-sign-on-sso"></a>アカウントとシングル サインオン (SSO)

MSAL トークン キャッシュには、アカウントごとに *単一の更新トークン* が格納されます。 この更新トークンを使用して、複数の Microsoft ID プラットフォーム テナントからのアクセス トークンをサイレントに要求できます。 ブローカーがデバイス上にインストールされている場合、アカウントはブローカーによって管理され、デバイス全体のシングル サインオンが可能になります。

> [!IMPORTANT]
> 企業-消費者間 (B2C) アカウントと更新トークンの動作は、Microsoft の他の ID プラットフォームとは異なります。 詳細については、「[B2C ポリシーとアカウント](#b2c-policies--accounts)」を参照してください。

## <a name="account-identifiers"></a>アカウント ID

MSAL のアカウント ID は、アカウント オブジェクト ID ではありません。 これは、Microsoft ID プラットフォーム内の一意性以外のものを伝えるために解析したり、依存したりすることを目的としたものではありません。

Azure AD Authentication Library (ADAL) との互換性を確保し、ADAL から MSAL への移行を容易にするために、MSAL では、MSAL キャッシュで利用可能なアカウント用の任意の有効な ID を使用してアカウントを検索できます。  たとえば、以下の場合は、各 ID が有効であるため、tom@live.com に対して常に同じアカウント オブジェクトが取得されます。

```java
// The following would always retrieve the same account object for tom@live.com because each identifier is valid

IAccount account = app.getAccount("<tome@live.com msal account id>");
IAccount account = app.getAccount("<tom@live.com contoso user object id>");
IAccount account = app.getAccount("<tom@live.com woodgrovebank user object id>");
```

## <a name="accessing-claims-about-an-account"></a>アカウントに関する要求へのアクセス

また、MSAL では、アクセス トークンを要求するだけでなく、常に各テナントから ID トークンを要求します。 これは、常に次のスコープを要求することによって行われます。

- openid
- profile

ID トークンには要求のリストが含まれます。 `Claims` は、アカウントに関する名前と値のペアであり、要求を行うために使用されます。

前述のように、アカウントが存在する各テナントには、アカウントに関するさまざまな情報が格納される場合があり、役職や勤務先所在地などの属性が含まれますが、これらに限定されるものではありません。

アカウントは複数の組織のメンバーまたはゲストである場合がありますが、MSAL では、アカウントがメンバーとなっているテナントのリストを取得するためにサービスを照会することはありません。 代わりに、MSAL では、行われたトークン要求の結果として、アカウントが存在するテナントのリストが作成されます。

アカウント オブジェクトで公開される要求は、常に、アカウントの 'home tenant'/{authority} からの要求です。 そのアカウントがホーム テナントのトークンの要求に使用されていない場合、MSAL ではアカウント オブジェクトを介して要求を提供できません。  次に例を示します。

```java
// Psuedo Code
IAccount account = getAccount("accountid");

String username = account.getClaims().get("preferred_username");
String tenantId = account.getClaims().get("tid"); // tenant id
String objectId = account.getClaims().get("oid"); // object id
String issuer = account.getClaims().get("iss"); // The tenant specific authority that issued the id_token
```

> [!TIP]
> アカウント オブジェクトから利用可能な要求のリストを表示する場合は、「[id_token の要求](./id-tokens.md#claims-in-an-id_token)」を参照してください

> [!TIP]
> id_token に追加の要求を含める場合は、次のドキュメントに記載されている省略可能な要求に関するドキュメントを参照してください。「[方法: Azure AD アプリに省略可能な要求を提供する](./active-directory-optional-claims.md)」

### <a name="access-tenant-profile-claims"></a>テナント プロファイル要求にアクセスする

他のテナントに示されているアカウントに関する要求にアクセスするには、まず、アカウント オブジェクトを `IMultiTenantAccount` にキャストする必要があります。 アカウントはすべてマルチテナントである場合がありますが、MSAL で利用できるテナント プロファイルの数は、現在のアカウントを使用してトークンを要求したテナントに基づいています。  次に例を示します。

```java
// Psuedo Code
IAccount account = getAccount("accountid");
IMultiTenantAccount multiTenantAccount = (IMultiTenantAccount)account;

multiTenantAccount.getTenantProfiles().get("tenantid for fabrikam").getClaims().get("family_name");
multiTenantAccount.getTenantProfiles().get("tenantid for contoso").getClaims().get("family_name");
```

## <a name="b2c-policies--accounts"></a>B2C ポリシーとアカウント

アカウントの更新トークンは、B2C ポリシー間では共有されません。 そのため、トークンを使用したシングル サインオンを行うことはできません。 これは、シングル サインオンできないことを意味するわけではありません。 シングル サインオンでは、シングル サインオンを有効にするために Cookie を利用できる対話型のエクスペリエンスを使用する必要があることを意味します。

また、MSAL では、異なる B2C ポリシーを使用してトークンを取得する場合、これらが個別のアカウントとして扱われ、それぞれに独自の ID があることを意味します。 アカウントを使用し、`acquireTokenSilent` を使ってトークンを要求する場合は、トークン要求で使用するポリシーに一致するアカウントのリストからアカウントを選択する必要があります。 次に例を示します。

```java
// Get Account For Policy

String policyId = "SignIn";
IAccount signInPolicyAccount = getAccountForPolicyId(app, policyId);

private IAccount getAccountForPolicy(IPublicClientApplication app, String policyId)
{
    List<IAccount> accounts = app.getAccounts();

    foreach(IAccount account : accounts)
   {
        if (account.getClaims().get("tfp").equals(policyId))
        {
            return account;
        }
    }

    return null;
}
```