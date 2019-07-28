---
title: Azure Active Directory B2C でソーシャル ID を持つユーザーを移行する | Microsoft Docs
description: Graph API を使った、ソーシャル ID を持つユーザーの Azure AD B2C への移行に関する主要な概念について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/03/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: bca802bb0099b0d854d752db8341dfe74031ef3b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508041"
---
# <a name="azure-active-directory-b2c-migrate-users-with-social-identities"></a>Azure Active Directory B2C:ソーシャル ID を持つユーザーを移行する
ご利用の ID プロバイダーを Azure AD B2C に移行する場合は、ソーシャル ID を持つユーザーも移行する必要がある場合があります。 この記事では、Facebook、LinkedIn、Microsoft、Google などの既存のソーシャル ID アカウントを Azure AD B2C に移行する方法を説明します。 この記事はフェデレーション ID にも適用されますが、これらの移行はあまり一般的ではありません。

## <a name="prerequisites"></a>前提条件
この記事は、ユーザー移行記事の続編であり、ソーシャル ID の移行に焦点を当てています。 作業を開始する前に、[ユーザー移行](active-directory-b2c-user-migration.md)に関する記事をお読みください。

## <a name="social-identities-migration-introduction"></a>ソーシャル ID の移行の概要

* Azure AD B2C では、**ローカル アカウント**のサインイン名 (ユーザー名または電子メール アドレス) はユーザー レコードの `signInNames` コレクションに格納されます。 `signInNames` には、ユーザーのサインイン名を指定する 1 つ以上の `signInName` レコードが含まれます。 各サインイン名は、テナント全体で一意にする必要があります。

* **ソーシャル アカウント** の ID は、`userIdentities` コレクションに格納されます。 エントリでは、facebook.com や `issuerUserId` など、発行者の一意のユーザー識別子である `issuer` (ID プロバイダー名) を指定します。 `userIdentities` 属性には、ソーシャル アカウントの種類を指定する 1 つ以上の UserIdentity レコードと、ソーシャル ID プロバイダーからの一意のユーザー識別子が含まれます。

* **ローカル アカウントをソーシャル ID と結合します**。 前述のように、ローカル アカウントのサインイン名とソーシャル アカウント ID は異なる属性に格納されます。 `signInNames` はローカル アカウントに使われ、`userIdentities` はソーシャル アカウントに使われます。 1 つのユーザー レコードで、1 つの Azure AD B2C アカウントは、ローカル アカウントのみまたはソーシャル アカウントのみとするか、ローカル アカウントとソーシャル ID を結合したものにすることができます。 この動作により、管理するアカウントは 1 つになりますが、ユーザーはローカル アカウントの資格情報またはソーシャル ID でサインインできます。

* `UserIdentity` 種類 - Azure AD B2C テナント内のソーシャル アカウント ユーザーの ID に関する情報が含まれています。
  * `issuer` facebook.com など、ユーザー識別子を発行した ID プロバイダーの文字列表現。
  * `issuerUserId` ソーシャル ID プロバイダーによって使われる base64 形式での一意のユーザー識別子。

    ```JSON
    "userIdentities": [{
          "issuer": "Facebook.com",
          "issuerUserId": "MTIzNDU2Nzg5MA=="
      }
    ]
    ```

* ID プロバイダーによっては、**ソーシャル ユーザー ID** は、アプリケーションごとの特定のユーザーまたは開発アカウントの一意の値です。 ソーシャル プロバイダーによって割り当てられたのと同じアプリケーション ID で Azure AD B2C ポリシーを構成します。 または、同じ開発アカウント内の別のアプリケーションです。

## <a name="use-graph-api-to-migrate-users"></a>Graph API を使ってユーザーを移行する
[Graph API](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-devquickstarts-graph-dotnet) で Azure AD B2C ユーザー アカウントを作成します。 Graph API と通信するには、まず管理特権を持つサービス アカウントを持っている必要があります。 Azure AD 内で、アプリケーションと認証を Azure AD に登録します。 アプリケーションの資格情報は、アプリケーション ID とアプリケーション シークレットです。 アプリケーションはユーザーとしてではなくアプリケーションそれ自体として Graph API を呼び出します。 [ユーザー移行](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-user-migration)に関する記事の手順 1. の指示に従います。

## <a name="required-properties"></a>必須プロパティ
次の一覧は、ユーザーを作成するときに必要なプロパティを示しています。
* **accountEnabled** - true
* **displayName** - ユーザーのアドレス帳に表示される名前です。
* **passwordProfile** -ユーザーのパスワード プロファイルです。 

> [!NOTE]
> ソーシャル アカウントのみ (ローカル アカウントの資格情報なし) の場合は、依然としてパスワードを指定する必要があります。 Azure AD B2C では、ソーシャル アカウントに指定したパスワードが無視されます。

* **userPrincipalName** - ユーザー プリンシパル名 (someuser@contoso.com)。 ユーザー プリンシパル名には、テナントの検証済みドメインの 1 つが含まれている必要があります。 UPN を指定するには、新しい GUID 値を生成し、`@` とテナント名を連結します。
* **mailNickname** - ユーザーのメールの別名です。 この値には、userPrincipalName に使うのと同じ ID を指定できます。 
* **signInNames** - ユーザーのサインイン名を指定する 1 つ以上の SignInName レコードです。 各サインイン名は、企業/テナント全体で一意にする必要があります。 ソーシャル アカウントのみの場合、このプロパティは空のままにすることができます。
* **userIdentities** - ソーシャル アカウントの種類を指定する 1 つ以上の UserIdentity レコードと、ソーシャル ID プロバイダーからの一意のユーザー識別子です。
* [オプション] **otherMails** - ソーシャル アカウントのみの場合は、ユーザーの電子メール アドレスです 

詳細については、次を参照してください。[Graph API リファレンス](/previous-versions/azure/ad/graph/api/users-operations#CreateLocalAccountUser)

## <a name="migrate-social-account-only"></a>ソーシャル アカウント (のみ) の移行
ローカル アカウントの資格情報なしでソーシャル アカウントのみを作成するには、 Graph API に HTTPS POST 要求を送信します。 要求本文には、作成するソーシャル アカウント ユーザーのプロパティが含まれます。 少なくとも、必須プロパティを指定する必要があります。 


**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

次のフォーム データを送信します。 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8",
    "signInNames": [],
    "creationType": null,
    "displayName": "Sara Bell",
    "givenName": "Sara",
    "surname": "Bell",
    "passwordProfile": {
        "password": "Test1234",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": null,
    "userIdentities": [{
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ],
    "otherMails": ["sara@live.com"],
    "userPrincipalName": "c8c3d3b8-60cf-4c76-9aa7-eb3235b190c8@tenant-name.onmicrosoft.com"
}
```
## <a name="migrate-social-account-with-local-account"></a>ソーシャル アカウントとローカル アカウントの移行
ソーシャル ID と結合されたローカル アカウントを作成するには、 Graph API に HTTPS POST 要求を送信します。 要求本文には、作成するソーシャル アカウント ユーザーのプロパティが含まれます。 少なくとも、必須プロパティを指定する必要があります。 

**POST**  https://graph.windows.net/tenant-name.onmicrosoft.com/users

次のフォーム データを送信します。 

```JSON
{
    "objectId": null,
    "accountEnabled": true,
    "mailNickname": "5164db16-3eee-4629-bfda-dcc3326790e9",
    "signInNames": [{
            "type": "emailAddress",
            "value": "david@contoso.com"
        }
    ],
    "creationType": "LocalAccount",
    "displayName": "David Hor",
    "givenName": "David",
    "surname": "Hor",
    "passwordProfile": {
        "password": "1234567",
        "forceChangePasswordNextLogin": false
    },
    "passwordPolicies": "DisablePasswordExpiration,DisableStrongPassword",
    "userIdentities": [{
            "issuer": "contoso.com",
            "issuerUserId": "ZGF2aWRAY29udG9zby5jb20="
        }
    ],
    "otherMails": [],
    "userPrincipalName": "5164db16-3eee-4629-bfda-dcc3326790e9@tenant-name.onmicrosoft.com"
}
```

## <a name="frequently-asked-questions"></a>よく寄せられる質問
### <a name="how-can-i-know-the-issuer-name"></a>発行者名はどうすればわかりますか?
発行者名、または ID プロバイダー名は、ポリシーで構成されます。 `issuer` で指定する値がわからない場合は、次の手順に従います。
1. いずれかのソーシャル アカウントでサインインします
2. JWT トークンから、`sub` 値をコピーします。 通常、`sub` には Azure AD B2C におけるユーザーのオブジェクト ID が含まれます。 または、Azure Portal から、ユーザーのプロパティを開き、オブジェクト ID をコピーします。
3. [Azure AD Graph Explorer](https://graphexplorer.azurewebsites.net) を開きます
4. 管理者としてサインインします。
5. 次の GET 要求を実行します。 userObjectId を、コピーしたユーザー ID に置き換えます。 **GET** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId
6. Azure AD B2C から返される JSON 内で `userIdentities` 要素を特定します。
7. [オプション] `issuerUserId` 値をデコードすることもできます。

> [!NOTE]
> B2C テナントのローカルである B2C テナント管理者アカウントを使います。 アカウント名の構文は admin@tenant-name.onmicrosoft.com です。

### <a name="is-it-possible-to-add-social-identity-to-an-existing-local-account"></a>ソーシャル ID を既存のローカル アカウントに追加することはできますか?
はい。 ローカル アカウントが作成された後で、ソーシャル ID を追加できます。 HTTPS PATCH 要求を実行します。 userObjectId を、更新するユーザー ID に置き換えます。 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

次のフォーム データを送信します。 

```JSON
{
    "userIdentities": [
        {
            "issuer": "Facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

### <a name="is-it-possible-to-add-multiple-social-identities"></a>複数のソーシャル ID を追加することはできますか?
はい。 1 つの Azure AD B2C アカウントに複数のソーシャル ID を追加することができます。 HTTPS PATCH 要求を実行します。 userObjectId をユーザー ID に置き換えます。 

**PATCH** https://graph.windows.net/tenant-name.onmicrosoft.com/users/userObjectId

次のフォーム データを送信します。 

```JSON
{
    "userIdentities": [
        {
            "issuer": "google.com",
            "issuerUserId": "MjQzMjE2NTc4NTQ="
        },
        {
            "issuer": "facebook.com",
            "issuerUserId": "MTIzNDU2Nzg5MA=="
        }
    ]
}
```

## <a name="optional-user-migration-application-sample"></a>[オプション] ユーザー移行アプリケーションのサンプル
[サンプル アプリ V2 をダウンロードして実行](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-user-migration)します。 サンプル アプリ V2 では、単一アカウント内のローカル アカウント、ソーシャル アカウント、ローカルおよびソーシャル ID などのダミー ユーザー データを含む JSON ファイルを使います。  JSON ファイルを編集するには、`AADB2C.UserMigration.sln` Visual Studio ソリューションを開きます。 `AADB2C.UserMigration` プロジェクトで、`UsersData.json` ファイルを開きます。 このファイルにはユーザー エンティティの一覧が含まれています。 各ユーザー エンティティには、次のプロパティがあります。
* **signInName** - ローカル アカウントの場合は、サインインする電子メール アドレス
* **displayName** - ユーザーの表示名
* **firstName** - ユーザーの名
* **lastName** - ユーザーの姓
* **password** - ローカル アカウントの場合、ユーザーのパスワード (空にすることができます)
* **issuer**- ソーシャル アカウントの場合、ID プロバイダー名
* **issuerUserId** - ソーシャルアカウントの場合、ソーシャル ID プロバイダーによって使われる一意のユーザー識別子。 値は、クリア テキストでなければなりません。 サンプル アプリは、この値を base64 にエンコードします。
* **email** - ソーシャル アカウントのみの (結合されていない) 場合、ユーザーの電子メール アドレス

```JSON
{
  "userType": "emailAddress",
  "Users": [
    {
      // Local account only
      "signInName": "James@contoso.com",
      "displayName": "James Martin",
      "firstName": "James",
      "lastName": "Martin",
      "password": "Pass!w0rd"
    },
    {
      // Social account only
      "issuer": "Facebook.com",
      "issuerUserId": "1234567890",
      "email": "sara@contoso.com",
      "displayName": "Sara Bell",
      "firstName": "Sara",
      "lastName": "Bell"
    },
    {
      // Combine local account with social identity
      "signInName": "david@contoso.com",
      "issuer": "Facebook.com",
      "issuerUserId": "0987654321",
      "displayName": "David Hor",
      "firstName": "David",
      "lastName": "Hor",
      "password": "Pass!w0rd"
    }
  ]
}
```

> [!NOTE]
> サンプル内の UsersData.json ファイルを自分のデータで更新しない場合、サンプル ローカル アカウント資格情報ではサインインできますが、ソーシャル アカウントの例ではサインインできません。 ソーシャル アカウントを移行するには、実際のデータを提供します。

サンプル アプリの使い方について詳しくは、「[Azure Active Directory B2C: ユーザー移行](active-directory-b2c-user-migration.md)」をご覧ください。
