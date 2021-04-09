---
title: チュートリアル:シングルページ アプリケーションを登録する
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Azure portal を使用して Azure Active Directory B2C にシングルページ アプリケーション (SPA) を登録する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 08/19/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d2eaf1dce432821dcfc693dc69dcf975a3d8be8d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "92503863"
---
# <a name="tutorial-register-a-single-page-application-spa-in-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C にシングルページ アプリケーション (SPA) を登録する

[アプリケーション](application-types.md)が Azure Active Directory B2C (Azure AD B2C) と対話できるようにするには、管理しているテナントに登録する必要があります。 このチュートリアルでは、Azure portal を使用してシングルページ アプリケーション ("SPA") を登録する方法を示します。

## <a name="overview-of-authentication-options"></a>認証オプションの概要

多くの最新の Web アプリケーションは、クライアント側のシングルページ アプリケーション ("SPA") として構築されています。 開発者は、JavaScript または SPA フレームワーク (Angular、Vue、React など) を使用してそれらを作成します。 これらのアプリケーションは Web ブラウザーで実行され、その認証には、従来のサーバー側 Web アプリケーションとは異なる特性があります。

Azure AD B2C により、シングルページ アプリケーションでユーザーをサインインさせ、バックエンド サービスまたは Web API にアクセスするためのトークンを取得する **2 つ** のオプションが提供されます。

### <a name="authorization-code-flow-with-pkce"></a>認可コード フロー (PKCE あり)
- [OAuth 2.0 認証コード フロー (PKCE あり)](./authorization-code-flow.md)。 この認証コード フローでは、認証されたユーザーを表す **ID** トークンと保護されている API を呼び出すために必要な **アクセス** トークンの認証コードを交換することをアプリケーションに許可します。 また、ユーザーが操作しなくてもユーザーの代わりにリソースへの長期間アクセスを提供する **更新** トークンが返されます。 

これが **推奨される** 方法です。 有効期間が制限された更新トークンを使用すると、Safari ITP のような[最新のブラウザーの Cookie プライバシー制限](../active-directory/develop/reference-third-party-cookies-spas.md)にアプリケーションを適合させることができます。

このフローを活用するために、アプリケーションで、これをサポートする認証ライブラリ ([MSAL.js 2.x](https://github.com/Azure-Samples/ms-identity-b2c-javascript-spa) など) を使用できます。 

![シングルページ アプリケーション認証](./media/tutorial-single-page-app/spa-app-auth.svg)

### <a name="implicit-grant-flow"></a>暗黙的な許可のフロー
- [OAuth 2.0 暗黙的フロー](implicit-flow-single-page-application.md)。 [MSAL.js 1.x](https://github.com/Azure-Samples/active-directory-b2c-javascript-msal-singlepageapp) のような一部のフレームワークでは、暗黙的な許可フローのみがサポートされます。 暗黙的な許可フローでは、**ID** と **アクセス** トークンを取得することがアプリケーションに許可されます。 認証コード フロートは異なり、暗黙的な許可フローでは **更新トークン** が返されません。 

![シングルページ アプリケーション暗黙的](./media/tutorial-single-page-app/spa-app.svg)

この認証フローには、Electron や React-Native などのクロスプラットフォーム JavaScript フレームワークを使用するアプリケーション シナリオは含まれません。 それらのシナリオでは、ネイティブ プラットフォームと対話するための追加の機能が必要になります。

## <a name="prerequisites"></a>前提条件

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

独自の [Azure AD B2C テナント](tutorial-create-tenant.md)をまだ作成していない場合、ここで作成してください。 既存の Azure AD B2C テナントを使用できます。

## <a name="register-the-spa-application"></a>SPA アプリケーションの登録

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの **名前** を入力します。 たとえば、*spaapp1* のように入力します。
1. **[サポートされているアカウントの種類]** で、 **[Accounts in any identity provider or organizational directory (for authenticating users with user flows)]\((ユーザー フローを使用してユーザーを認証するための) 任意の ID プロバイダーまたは組織のディレクトリのアカウント\)** を選択します
1. **[リダイレクト URI]** で、 **[Single-page application (SPA)]\(シングルページ アプリケーション (SPA)\)** を選択し、URL テキスト ボックスに「`https://jwt.ms`」と入力します。

    リダイレクト URI とは、ユーザーが承認サーバー (この場合は Azure AD B2C) とのやり取りが完了したときにユーザーが送信される、また承認が成功したときにアクセス トークンまたは認証コードが送信されるエンドポイントです。 実稼働アプリケーションでは、通常は `https://contoso.com/auth-response` などの、お使いのアプリが実行されているパブリック アクセスが可能なエンドポイントです。 このチュートリアルの場合のようなテスト目的では、トークンのデコードされたコンテンツを表示する Microsoft が所有する Web アプリケーションである `https://jwt.ms` に設定できます (トークンのコンテンツがお使いのブラウザー外に出ることはありません)。 アプリの開発時には、お使いのアプリケーションがローカルでリッスンする `http://localhost:5000` などのエンドポイントを追加する場合があります。 お使いの登録済みアプリケーションでは、いつでもリダイレクト URI を追加したり、変更したりすることができます。

    リダイレクト URI には、次の制限があります。

    * `localhost` を使用しない場合、応答 URL はスキーム `https` で始まる必要があります。
    * 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 たとえば、ご利用のアプリケーションがそのパス `.../abc/response-oidc` の一部として含まれている場合は、応答 URL 内では `.../ABC/response-oidc` と指定しないでください。 Web ブラウザーでは大文字と小文字を区別を区別するものとしてパスが処理されるため、`.../abc/response-oidc` に関連付けられている cookie は、大文字と小文字が一致しない `.../ABC/response-oidc` URL にリダイレクトされた場合に除外される可能性があります。

1. **[アクセス許可]** で、 *[openid と offline_access アクセス許可に対して管理者の同意を付与します]* チェック ボックスをオンにします。
1. **[登録]** を選択します。


## <a name="enable-the-implicit-flow"></a>暗黙的フローの有効化
暗黙的フローを使用する場合は、アプリの登録で暗黙的な許可フローを有効にする必要があります。

1. 左側のメニューの **[管理]** セクションで、 **[認証]** を選択します。
1. **[暗黙的な許可]** で、 **[アクセス トークン]** と **[ID トークン]** の両方のチェック ボックスをオンにします。
1. **[保存]** を選択します。

## <a name="migrate-from-the-implicit-flow"></a>暗黙的フローからの移行

暗黙的フローを使用する既存のアプリケーションを使用している場合は、認可コード フローをサポートするフレームワーク ([MSAL.js 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-js/tree/dev/lib/msal-browser) など) を使用して、認可コード フローを使用するように移行することをお勧めします。

アプリの登録によって表される、すべての運用シングルページ アプリケーションが認可コード フローを使用している場合は、暗黙的な許可フローの設定を無効にします。 

1. 左側のメニューの **[管理]** セクションで、 **[認証]** を選択します。
1. **[暗黙的な許可]** で、 **[アクセス トークン]** と **[ID トークン]** の両方のチェック ボックスをオフにします。
1. **[保存]** を選択します。

暗黙的フローを有効 (オン) なままにしておくと、暗黙的フローを使用するアプリケーションは引き続き機能します。

* * *

## <a name="next-steps"></a>次のステップ

次に、ユーザーがサインアップ、サインイン、および各自のプロファイルを管理できるようにするユーザー フローの作成方法を確認します。

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 内にユーザー フローを作成する >](tutorial-create-user-flows.md)
