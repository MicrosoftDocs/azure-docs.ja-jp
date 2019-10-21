---
title: チュートリアル - アプリケーションを登録する - Azure Active Directory B2C
description: Azure portal を使用して Azure Active Directory B2C に Web アプリケーションを登録する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 08/23/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 06b2c273f41bfa74ee968b6fd6676e83767ce8b2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71063268"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C にアプリケーションを登録する

[アプリケーション](active-directory-b2c-apps.md)が Azure Active Directory B2C (Azure AD B2C) と対話できるようにするには、管理しているテナントに登録する必要があります。 このチュートリアルでは、Azure portal を使用して Web アプリケーションを登録する方法を示します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Web アプリケーションの登録
> * クライアント シークレットの作成

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

独自の [Azure AD B2C テナント](tutorial-create-tenant.md)をまだ作成していない場合、すぐに作成してください。 既存の Azure AD B2C テナントを使用できます。

## <a name="register-a-web-application"></a>Web アプリケーションの登録

1. ご利用の Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。そのためには、トップ メニューにある **[ディレクトリ + サブスクリプション]** フィルターを選択して、ご利用のテナントを含むディレクトリを選択します。
1. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
1. **[アプリケーション]** を選択し、 **[追加]** を選択します。
1. アプリケーションの名前を入力します。 たとえば、*webapp1* とします。
1. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、 **[はい]** を選択します。
1. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 たとえば、`https://localhost:44316` でローカルにリッスンするように設定します。 ポート番号がわからない場合は、プレースホルダーの値を入力し、後で変更します。

    このチュートリアルのようなテスト目的では、`https://jwt.ms` に設定して、検査のためにトークンの内容が表示されるようにすることができます。 このチュートリアルでは、 **[応答 URL]** を `https://jwt.ms` に設定します。

    応答 URL には、次の制限が適用されます。

    * 応答 URL は、スキーム `https` で始まる必要があります。
    * 応答 URL では大文字と小文字が区別されます。 大文字と小文字の区別は、実行中のアプリケーションの URL パスの場合と一致している必要があります。 たとえば、ご利用のアプリケーションがそのパス `.../abc/response-oidc` の一部として含まれている場合は、応答 URL 内では `.../ABC/response-oidc` と指定しないでください。 Web ブラウザーでは大文字と小文字を区別を区別するものとしてパスが処理されるため、`.../abc/response-oidc` に関連付けられている cookie は、大文字と小文字が一致しない `.../ABC/response-oidc` URL にリダイレクトされた場合に除外される可能性があります。

1. **[作成]** をクリックして、アプリケーションの登録を完了します。

## <a name="create-a-client-secret"></a>クライアント シークレットの作成

お客様のアプリケーションでコードをトークンと交換する場合は、アプリケーション シークレットを作成する必要があります。

1. **[Azure AD B2C - アプリケーション]** ページで、作成したアプリケーション (例: *webapp1*) を選択します。
1. **[キー]** を選択し、 **[キーの生成]** を選択します。
1. **[保存]** を選択し、キーを参照します。 **アプリ キー** の値をメモしておきます。 アプリケーションのコード内で、この値をアプリケーション シークレットとして使用します。

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Web アプリケーションの登録
> * クライアント シークレットの作成

次に、ユーザーがサインアップ、サインイン、および各自のプロファイルを管理できるようにするユーザー フローの作成方法を確認します。

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 内にユーザー フローを作成する >](tutorial-create-user-flows.md)
