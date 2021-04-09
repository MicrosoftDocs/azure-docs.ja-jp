---
title: OpenID Connect でのサインアップおよびサインインを設定する
titleSuffix: Azure AD B2C
description: Azure Active Directory B2C で任意の OpenID Connect ID プロバイダー (IdP) でのサインアップとサインインを設定します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 03/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9434bd4042798dc05a33401e1884e11a73774936
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102448338"
---
# <a name="set-up-sign-up-and-sign-in-with-openid-connect-using-azure-active-directory-b2c"></a>Azure Active Directory B2C を使用して OpenID Connectでのサインアップおよびサインインを設定する

[OpenID Connect](openid-connect.md) は OAuth 2.0 を基盤として作成された認証プロトコルであり、セキュリティで保護されたユーザー サインインに使用できます。 Azure AD B2C では、このプロトコルを使用するほとんどの ID プロバイダーがサポートされています。 この記事では、カスタム OpenID Connect ID プロバイダーをユーザー フローに追加する方法について説明します。

## <a name="add-the-identity-provider"></a>ID プロバイダーの追加

1. Azure AD B2C テナントの全体管理者として [Azure Portal](https://portal.azure.com/) にサインインします。
1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認します。そのためには、上部のメニューにある **[ディレクトリ + サブスクリプション]** フィルターをクリックし、ご利用のテナントを含むディレクトリを選択します。
1. Azure Portal の左上隅の **[すべてのサービス]** を選択し、 **[Azure AD B2C]** を検索して選択します。
1. **[ID プロバイダー]** を選択してから、 **[新しい OpenID Connect プロバイダー ]** を選択します。
1. **[名前]** を入力します。 たとえば、「*Contoso*」と入力します。

## <a name="configure-the-identity-provider"></a>[ID プロバイダー] を構成します。

どの OpenID Connect ID プロバイダーでも、サインインを実行するために必要な情報の多くを含むメタデータ ドキュメントを記述します。 これには、使用する URL、サービスの公開署名キーの場所などの情報が含まれます。 OpenID Connect メタデータ ドキュメントは、常に `.well-known/openid-configuration` で終わるエンドポイントに配置されます。 追加する OpenID Connect ID プロバイダーについては、そのメタデータの URL を入力します。

## <a name="client-id-and-secret"></a>クライアントの ID とシークレット

ユーザーのサインインを許可するために、ID プロバイダーは、開発者が自身のサービスにアプリケーションを登録する必要があります。 このアプリケーションには、**クライアント ID** および **クライアント シークレット** と呼ばれる ID があります。 これらの値を ID プロバイダーからコピーし、対応するフィールドに入力します。

> [!NOTE]
> クライアント シークレットはオプションです。 ただし、[承認コード フロー](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)を使用する場合は、クライアント シークレットを入力する必要があります。承認コード フローでは、そのシークレットを使用してトークンのコードの交換が行われます。

## <a name="scope"></a>スコープ

スコープは、カスタム ID プロバイダーから収集する情報およびアクセス許可を定義します。 OpenID Connect 要求には、ID プロバイダーから ID トークンを受け取るために `openid` スコープ値を含める必要があります。 ID トークンがないと、ユーザーはカスタム ID プロバイダーを使用して Azure AD B2C にサインインすることはできません。 別のスコープをスペースで区切って追加することもできます。 使用可能な他のスコープについては、カスタム ID プロバイダーのドキュメントを参照してください。

## <a name="response-type"></a>応答の種類

応答の種類は、最初の呼び出しで、カスタム ID プロバイダーの`authorization_endpoint`に送信される情報の種類を表します。 次の応答の種類を使用できます。

* `code`: [承認コード フロー](https://openid.net/specs/openid-connect-core-1_0.html#CodeFlowAuth)に従って、Azure AD B2C にコードが返されます。 次に Azure AD B2C が`token_endpoint`を呼び出して、トークンのコードを交換します。
* `id_token`: ID トークンが、カスタム ID プロバイダーから、Azure AD B2C に返されます。

## <a name="response-mode"></a>応答モード

応答モードは、カスタム ID プロバイダーから Azure AD B2C へのデータの返送に使用する方法を定義します。 次の応答モードを使用できます。

* `form_post`: 最高のセキュリティを得るには、この応答モードをお勧めします。 この応答は、HTTP の `POST` メソッドによって送信され、本文には、`application/x-www-form-urlencoded` 形式を使用してエンコードされたコードまたはトークンが含まれます。
* `query`: このコードまたはトークンは、クエリ パラメーターとして返されます。

## <a name="domain-hint"></a>ドメインのヒント

ドメインのヒントを使用して、ユーザーは利用可能な ID プロバイダーのリストから選択するのではなく、指定した ID プロバイダーのサインイン ページに直接スキップできます。 このような動作を許可にするには、ドメインのヒントの値を入力します。 カスタム ID プロバイダーにジャンプするには、サインインのために Azure AD B2C を呼び出すときに、要求の最後にパラメーター `domain_hint=<domain hint value>` を追加します。

## <a name="claims-mapping"></a>要求のマッピング

カスタム ID プロバイダーが Azure AD B2C に ID トークンを返送した後、Azure AD B2C は、受け取ったトークンからのクレームを Azure AD B2C が認識して使用するクレームにマッピングできる必要があります。 以下の各マッピングについては、カスタム ID プロバイダーのドキュメントを参照して、ID プロバイダーのトークンに返される要求をご理解ください。

* **ユーザー ID**: サインインしたユーザーの "*一意の識別子*" を指定する要求を入力します。
* **表示名**: ユーザーの "*表示名*" または "*フル ネーム*" を指定する要求を入力します。
* **名**: ユーザーの "*名*" を指定する要求を入力します。
* **姓**: ユーザーの "*姓*" を指定する要求を入力します。
* **メール**: ユーザーの "*メール アドレス*" を指定する要求を入力します。

## <a name="add-the-identity-provider-to-a-user-flow"></a>ユーザー フローに ID プロバイダーを追加する 

1. Azure AD B2C テナントで、 **[ユーザー フロー]** を選択します。
1. ID プロバイダーを追加するユーザー フローをクリックします。 
1. **[ソーシャル ID プロバイダー]** の下で、追加した ID プロバイダーを選択します。 たとえば、*Contoso* です。
1. **[保存]** を選択します。
1. ポリシーをテストするには、 **[ユーザー フローを実行します]** を選択します。
1. **[アプリケーション]** には、以前に登録した *testapp1* という名前の Web アプリケーションを選択します。 **[応答 URL]** に `https://jwt.ms` と表示されます。
1. **[ユーザー フローを実行します]** ボタンを選択します。
1. サインアップまたはサインイン ページで、サインインする ID プロバイダーを選択します。 たとえば、*Contoso* です。

サインイン プロセスが成功すると、ブラウザーは `https://jwt.ms` にリダイレクトされ、Azure AD B2C によって返されたトークンの内容が表示されます。
