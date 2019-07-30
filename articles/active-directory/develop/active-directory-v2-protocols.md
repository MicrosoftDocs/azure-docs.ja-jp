---
title: Microsoft ID プラットフォームでサポートされる認証プロトコルについて学習する | Azure
description: Microsoft ID プラットフォームのエンドポイントでサポートされている OAuth 2.0 および OpenID Connect プロトコルのガイドです。
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 5fb4fa1b-8fc4-438e-b3b0-258d8c145f22
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: b7ffef5c3a7c8dd21654b6364013b1718bea1292
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/01/2019
ms.locfileid: "67482982"
---
# <a name="microsoft-identity-platform-protocols"></a>Microsoft ID プラットフォームのプロトコル

業界標準のプロトコルである OpenID Connect と OAuth 2.0 を使用した Identity-as-a-Service (サービスとしての ID) としての Microsoft ID プラットフォームのエンドポイント。 このサービスは標準に準拠していますが、これらのプロトコルには、実装によって微妙な違いが存在する場合があります。 ここでは、Microsoft のオープンソース ライブラリを使うのではなく、コードから直接 HTTP 要求を送信して処理するか、サード パーティの[オープンソース ライブラリ](reference-v2-libraries.md)を使用する場合に役立つ情報を紹介します。

> [!NOTE]
> Microsoft ID プラットフォーム エンドポイントでは、Azure AD のすべてのシナリオや機能がサポートされているわけではありません。 Microsoft ID プラットフォーム エンドポイントを使用すべきかどうかを判定するには、[Microsoft ID プラットフォームの制限](active-directory-v2-limitations.md)に関するページを参照してください。

## <a name="the-basics"></a>基本

OAuth 2.0 と OpenID Connect におけるフローはほぼすべて、情報のやり取りに 4 つの当事者が関係します。

![OAuth 2.0 ロールを示す図](./media/active-directory-v2-flows/protocols-roles.svg)

* **承認サーバー**は、Microsoft ID プラットフォーム エンドポイントとして、ユーザーの本人性確認、リソースへのアクセス権の付与と取り消し、トークンの発行という役割を担います。 承認サーバーは、ID プロバイダーと呼ばれることもあります。ユーザーの情報とそのアクセス、そしてフロー内の当事者間の信頼関係に関するすべてのことは、承認サーバーによって安全に処理されます。
* **リソース所有者** とは通常、エンド ユーザーを指します。 データを所有し、そのデータ (またはリソース) へのアクセスをサード パーティに許可する権限を持つ当事者です。
* **OAuth クライアント**は、皆さんが開発するアプリです。対応するアプリケーション ID で識別されます。 通常は、OAuth クライアントがエンド ユーザーと情報をやり取りし、承認サーバーにトークンを要求します。 クライアントには、リソース所有者がリソースへのアクセス権を付与する必要があります。
* **リソース サーバー**は、リソースまたはデータが存在する場所です。 承認サーバーを信頼し、OAuth クライアントを安全に認証、承認します。リソースへのアクセスを許可するためにベアラー アクセス トークンが使用されます。

## <a name="app-registration"></a>アプリケーションの登録

個人用アカウントと職場や学校のアカウントの両方を受け付けるアプリはすべて、[Azure portal](https://aka.ms/appregistrations) の**アプリの登録** エクスペリエンスを通じて登録する必要があります。登録後、OAuth 2.0 または OpenID Connect を使用して、それらのユーザーをサインインさせることができます。 アプリの登録プロセスでは、いくつかの値が収集され、対象のアプリに割り当てられます。

* アプリを一意に識別する**アプリケーション ID**。
* 応答をアプリにリダイレクトして戻すために使用できる**リダイレクト URI** (オプション)
* その他シナリオに応じた値。

詳細については、 [アプリの登録](quickstart-register-app.md)方法を参照してください。

## <a name="endpoints"></a>エンドポイント

アプリは、登録されると、エンドポイントに要求を送ることによって、Microsoft ID プラットフォームと通信します。

```
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/authorize
https://login.microsoftonline.com/{tenant}/oauth2/v2.0/token
```

ここで、 `{tenant}` は次の 4 つの値のいずれかになります。

| 値 | 説明 |
| --- | --- |
| `common` | 個人の Microsoft アカウントと Azure AD の職場/学校アカウントのどちらでもアプリケーションにサインインできます。 |
| `organizations` | Azure AD の職場/学校アカウントを持つユーザーのみがアプリケーションにサインインできます。 |
| `consumers` | 個人の Microsoft アカウント (MSA) を持つユーザーのみがアプリケーションにサインインできます。 |
| `8eaef023-2b34-4da1-9baa-8bc8c9d6a490` または `contoso.onmicrosoft.com` | 特定の Azure AD テナントの職場/学校アカウントを持つユーザーのみがアプリケーションにサインインできます。 Azure AD テナントのフレンドリ ドメイン名か、テナントの GUID 識別子のいずれかを使用できます。 |

これらのエンドポイントと対話する方法について学習するには、「[プロトコル](#protocols)」セクションで特定のアプリの種類を選択し、リンクから詳細情報にアクセスしてください。

> [!TIP]
> Azure AD に登録されたアプリはいずれも、個人用アカウントにサインインしなくても、Microsoft ID プラットフォーム エンドポイントを使用できます。  アプリケーションを作成し直さなくても、このようにして既存のアプリケーションを Microsoft ID プラットフォームと [MSAL](reference-v2-libraries.md) に移行することができます。  

## <a name="tokens"></a>トークン

OAuth 2.0 および OpenID Connect の Microsoft ID プラットフォーム実装では、ベアラー トークンが広範囲に使われています (JWT として表現されたベアラー トークンなど)。 ベアラー トークンは、保護されたリソースへの "ベアラー" アクセスを許可する簡易セキュリティ トークンです。 この意味で、"ベアラー" はトークンを提示できる任意の利用者を表します。 利用者がベアラー トークンを受信するには、まず Microsoft ID プラットフォームによる認証が必要となりますが、転送中や保存時にトークンを保護するために必要な対策を講じていない場合、意図しない利用者によって傍受され、使用されるおそれがあります。 一部のセキュリティ トークンには、許可されていない利用者がトークンを使用できないようにするための組み込みメカニズムがありますが、ベアラー トークンにはこのメカニズムがないため、トランスポート層セキュリティ (HTTPS) などのセキュリティで保護されたチャネルで転送する必要があります。 ベアラー トークンが暗号化されずに転送された場合、悪意のある利用者が中間者攻撃によってトークンを取得し、保護されたリソースへの未承認のアクセスに使用する可能性があります。 後で使用するためにベアラー トークンを保存またはキャッシュするときにも、同じセキュリティ原則が適用されます。 アプリケーションでは、常に安全な方法でベアラー トークンを転送および保存してください。 ベアラー トークンのセキュリティに関する考慮事項の詳細については、 [RFC 6750 セクション 5](https://tools.ietf.org/html/rfc6750)をご覧ください。

Microsoft ID プラットフォーム エンドポイントで使用されている各種トークンの詳細については、[Microsoft ID プラットフォーム エンドポイントのトークンのリファレンス](v2-id-and-access-tokens.md)をご覧ください。

## <a name="protocols"></a>プロトコル

要求の例を理解できる状態になったら、最初に以下のチュートリアルのどれかをご覧ください。 いずれも、特定の認証シナリオに対応しています。 どのフローを見ればよいかわからない場合は、[Microsoft ID プラットフォームを使用して構築できるアプリの種類](v2-app-types.md)に関するページを参照してください。

* [OAuth 2.0 でモバイル アプリケーションおよびネイティブ アプリケーションを作成する](v2-oauth2-auth-code-flow.md)
* [OpenID Connect で Web アプリを作成する](v2-protocols-oidc.md)
* [OAuth 2.0 暗黙的フローでシングルページ アプリを作成する](v2-oauth2-implicit-grant-flow.md)
* [OAuth 2.0 クライアント資格情報フローでデーモンまたはサーバー側プロセスを作成する](v2-oauth2-client-creds-grant-flow.md)
* [OAuth 2.0 の On-Behalf-Of フローを使用して Web API でトークンを取得する](v2-oauth2-on-behalf-of-flow.md)
