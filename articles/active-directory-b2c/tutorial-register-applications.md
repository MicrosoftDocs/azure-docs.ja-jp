---
title: チュートリアル:アプリケーションを登録する
titleSuffix: Azure AD B2C
description: このチュートリアルでは、Azure portal を使用して Azure Active Directory B2C に Web アプリケーションを登録する方法について説明します。
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: tutorial
ms.date: 04/10/2020
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0fd062bd0e58ecc714e4f450c93384e47e743b65
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/07/2020
ms.locfileid: "87922015"
---
# <a name="tutorial-register-a-web-application-in-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C に Web アプリケーションを登録する

[アプリケーション](application-types.md)が Azure Active Directory B2C (Azure AD B2C) と対話できるようにするには、管理しているテナントに登録する必要があります。 このチュートリアルでは、Azure portal を使用して Web アプリケーションを登録する方法を示します。

この記事では、次の方法について説明します。

> [!div class="checklist"]
> * Web アプリケーションの登録
> * クライアント シークレットの作成

代わりにネイティブ アプリ (iOS、Android、モバイル & デスクトップなど) を使用している場合は、[ネイティブ クライアント アプリケーションを登録する方法](add-native-application.md)を参照してください。

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

独自の [Azure AD B2C テナント](tutorial-create-tenant.md)をまだ作成していない場合、ここで作成してください。 既存の Azure AD B2C テナントを使用できます。

## <a name="register-a-web-application"></a>Web アプリケーションの登録

アプリケーションを Azure AD B2C テナントに登録するには、Microsoft の新しい統合**アプリの登録**エクスペリエンスかレガシ **アプリケーション (レガシ)** エクスペリエンスを使用できます。 [この新しいエクスペリエンスの詳細を参照してください](https://aka.ms/b2cappregtraining)。

#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリの登録]** を選択し、 **[新規登録]** を選択します。
1. アプリケーションの**名前**を入力します。 たとえば、*webapp1* とします。
1. **[サポートされているアカウントの種類]** で、 **[任意の組織のディレクトリまたは任意の ID プロバイダーのアカウント] を選択します。ユーザーの認証には Azure AD B2C を選択します**。
1. **[リダイレクト URI]** で、 **[Web]** を選択し、URL テキスト ボックスに「`https://jwt.ms`」と入力します。

    リダイレクト URI とは、ユーザーが承認サーバー (この場合は Azure AD B2C) とのやり取りが完了したときにユーザーが送信される、また承認が成功したときにアクセス トークンまたは認証コードが送信されるエンドポイントです。 実稼働アプリケーションでは、通常は `https://contoso.com/auth-response` などの、お使いのアプリが実行されているパブリック アクセスが可能なエンドポイントです。 このチュートリアルの場合のようなテスト目的では、トークンのデコードされたコンテンツを表示する Microsoft が所有する Web アプリケーションである `https://jwt.ms` に設定できます (トークンのコンテンツがお使いのブラウザー外に出ることはありません)。 アプリの開発時には、お使いのアプリケーションがローカルでリッスンする `https://localhost:5000` などのエンドポイントを追加する場合があります。 お使いの登録済みアプリケーションでは、いつでもリダイレクト URI を追加したり、変更したりすることができます。

    リダイレクト URI には、次の制限があります。

    * 応答 URL は、スキーム `https` で始まる必要があります。
    * 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 たとえば、ご利用のアプリケーションがそのパス `.../abc/response-oidc` の一部として含まれている場合は、応答 URL 内では `.../ABC/response-oidc` と指定しないでください。 Web ブラウザーでは大文字と小文字を区別を区別するものとしてパスが処理されるため、`.../abc/response-oidc` に関連付けられている cookie は、大文字と小文字が一致しない `.../ABC/response-oidc` URL にリダイレクトされた場合に除外される可能性があります。

1. **[アクセス許可]** で、 *[openid と offline_access アクセス許可に対して管理者の同意を付与します]* チェック ボックスをオンにします。
1. **[登録]** を選択します。

アプリケーションの登録が完了したら、暗黙的な許可フローを有効にします。

1. 左側のメニューの **[管理]** セクションで、 **[認証]** を選択します。
1. **[暗黙的な許可]** で、 **[アクセス トークン]** と **[ID トークン]** の両方のチェック ボックスをオンにします。
1. **[保存]** を選択します。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. [Azure portal](https://portal.azure.com) にサインインします。
1. ポータル ツール バーにある **[ディレクトリ + サブスクリプション]** アイコンを選択し、Azure AD B2C テナントを含むディレクトリを選択します。
1. Azure portal で、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション (レガシ)]** を選択し、 **[追加]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、*webapp1* とします。
1. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、 **[はい]** を選択します。
1. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 たとえば、`https://localhost:44316` でローカルにリッスンするように設定します。 ポート番号がわからない場合は、プレースホルダーの値を入力し、後で変更します。

    このチュートリアルのようなテスト目的では、`https://jwt.ms` に設定して、検査のためにトークンの内容が表示されるようにすることができます。 このチュートリアルでは、 **[応答 URL]** を `https://jwt.ms` に設定します。

    応答 URL には、次の制限が適用されます。

    * 応答 URL は、スキーム `https` で始まる必要があります。
    * 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 たとえば、ご利用のアプリケーションがそのパス `.../abc/response-oidc` の一部として含まれている場合は、応答 URL 内では `.../ABC/response-oidc` と指定しないでください。 Web ブラウザーでは大文字と小文字を区別を区別するものとしてパスが処理されるため、`.../abc/response-oidc` に関連付けられている cookie は、大文字と小文字が一致しない `.../ABC/response-oidc` URL にリダイレクトされた場合に除外される可能性があります。

1. **[作成]** を選択して、アプリケーションの登録を完了します。

* * *

## <a name="create-a-client-secret"></a>クライアント シークレットの作成

お客様のアプリケーションで認証コードをアクセス トークンと交換する場合は、アプリケーション シークレットを作成する必要があります。


#### <a name="app-registrations"></a>[アプリの登録](#tab/app-reg-ga/)

1. **[Azure AD B2C - アプリの登録]** ページで、作成したアプリケーション (例: *webapp1*) を選択します。
1. 左側のメニューで、 **[管理]** の **[証明書とシークレット]** を選択します。
1. **[新しいクライアント シークレット]** を選択します。
1. **[説明]** ボックスにクライアント シークレットの説明を入力します。 たとえば、*clientsecret1* のようにします。
1. **[有効期限]** で、シークレットが有効な期間を選択してから、 **[追加]** を選択します。
1. シークレットの**値**を記録します。 アプリケーションのコード内で、この値をアプリケーション シークレットとして使用します。

#### <a name="applications-legacy"></a>[アプリケーション (レガシ)](#tab/applications-legacy/)

1. **[Azure AD B2C - アプリケーション]** ページで、作成したアプリケーション (例: *webapp1*) を選択します。
1. **[キー]** を選択し、 **[キーの生成]** を選択します。
1. **[保存]** を選択し、キーを参照します。 **アプリ キー** の値をメモしておきます。 アプリケーションのコード内で、この値をアプリケーション シークレットとして使用します。

* * *

## <a name="next-steps"></a>次のステップ

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Web アプリケーションの登録
> * クライアント シークレットの作成

次に、ユーザーがサインアップ、サインイン、および各自のプロファイルを管理できるようにするユーザー フローの作成方法を確認します。

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 内にユーザー フローを作成する >](tutorial-create-user-flows.md)
