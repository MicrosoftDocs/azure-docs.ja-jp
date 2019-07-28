---
title: OpenID Connect でのサインアップとサインインを設定する - Azure Active Directory B2C | Microsoft Docs
description: Azure Active Directory B2C を使用して OpenID Connectでのサインアップおよびサインインを設定する。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: b2c
ms.openlocfilehash: 774716aeb28ca5d9563f38272a59c8e675a11e4b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508389"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して OpenID Connectでのサインアップおよびサインインを設定する

>[!NOTE]
> この機能はパブリック プレビュー段階にあります。 運用環境で、この機能を使用しないでください。


[OpenID Connect](active-directory-b2c-reference-oidc.md) は OAuth 2.0 を基盤として開発された認証プロトコルであり、ユーザーを安全にサインインさせるために利用されます。 Azure AD B2C では、このプロトコルを使用するほとんどの ID プロバイダーがサポートされています。 この記事では、カスタム OpenID Connect ID プロバイダーをユーザー フローに追加する方法について説明します。

## <a name="add-the-identity-provider"></a>ID プロバイダーの追加

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
2. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
3. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
4. **[ID プロバイダー]** を選択して、 **[追加]** をクリックします。
5. **[ID プロバイダーの種類]** のために **[OpenID Connect (Preview)]** を選択します。

## <a name="configure-the-identity-provider"></a>[ID プロバイダー] を構成します。

すべての OpenID Connect ID プロバイダーは、サインインを実行するために必要な情報の大半を含むメタデータ ドキュメントを記述します。 これには、使用する URL、サービスの公開署名キーの場所などの情報が含まれます。 OpenID Connect メタデータ ドキュメントは、常に `.well-known\openid-configuration` で終わるエンドポイントに配置されます。 追加する OpenID Connect ID プロバイダーについては、そのメタデータの URL を入力します。

ユーザーのサインインを許可するために、ID プロバイダーは、開発者が自身のサービスにアプリケーションを登録する必要があります。 このアプリケーションには、**クライアント ID** および**クライアント シークレット**と呼ばれる ID があります。 これらの値を ID プロバイダーからコピーし、対応するフィールドに入力します。

> [!NOTE]
> クライアント シークレットはオプションです。 ただし、[承認コード フロー](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)を使用する場合は、クライアント シークレットを入力する必要があります。承認コード フローでは、トークンのコードの交換にシークレットが使用されます。

スコープは、カスタム ID プロバイダーから収集する情報およびアクセス許可を定義します。 OpenID Connect 要求には、ID プロバイダーから ID トークンを受け取るために `openid` スコープ値を含める必要があります。 ID トークンがないと、ユーザーはカスタム ID プロバイダーを使用して Azure AD B2C にサインインすることはできません。 別のスコープをスペースで区切って追加することもできます。 使用可能な他のスコープについては、カスタム ID プロバイダーのドキュメントを参照してください。

応答の種類は、最初の呼び出しで、カスタム ID プロバイダーの`authorization_endpoint`に送信される情報の種類を表します。 次の応答の種類を使用できます。

- `code`:[承認コード フロー](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)に従って、Azure AD B2C にコードが返されます。 次に Azure AD B2C が`token_endpoint`を呼び出して、トークンのコードを交換します。
- `token`:アクセス トークンが、カスタム ID プロバイダーから、Azure AD B2C に返されます。
- `id_token`:ID トークンが、カスタム ID プロバイダーから、Azure AD B2C に返されます。

応答モードは、カスタム ID プロバイダーから Azure AD B2C へのデータの返送に使用する方法を定義します。 次の応答モードを使用できます。

- `form_post`:最高のセキュリティを得るには、この応答モードをお勧めします。 この応答は、HTTP の `POST` メソッドによって送信され、本文には、`application/x-www-form-urlencoded` 形式を使用してエンコードされたコードまたはトークンが含まれます。
- `query`:このコードまたはトークンは、クエリ パラメーターとして返されます。

ドメインのヒントを使用して、ユーザーは利用可能な ID プロバイダーのリストから選択するのではなく、指定した ID プロバイダーのサインイン ページに直接スキップできます。 このような動作を許可にするには、ドメインのヒントの値を入力します。 カスタム ID プロバイダーにジャンプするには、サインインのために Azure AD B2C を呼び出すときに、要求の最後にパラメーター `domain_hint=<domain hint value>` を追加します。

カスタム ID プロバイダーが Azure AD B2C に ID トークンを返送した後、Azure AD B2C は、受け取ったトークンからのクレームを Azure AD B2C が認識して使用するクレームにマッピングできる必要があります。 以下の各マッピングについては、カスタム ID プロバイダーのドキュメントを参照して、ID プロバイダーのトークンに返される要求をご理解ください。

- `User ID`:サインインしたユーザーの一意の識別子を指定するクレームを入力します。
- `Display Name`:ユーザーの表示名またはフル ネームを指定するクレームを入力します。
- `Given Name`:ユーザーの名を指定するクレームを入力します。
- `Surname`:ユーザーの姓を指定するクレームを入力します。
- `Email`:ユーザーのメール アドレスを指定するクレームを入力します。

