---
title: チュートリアル - アプリケーションを登録する - Azure Active Directory B2C
description: Azure portal を使用して Azure Active Directory B2C に Web アプリケーションを登録する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 06/07/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5c46d3153bdc5768836bce198af115f82e8469f3
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "67056286"
---
# <a name="tutorial-register-an-application-in-azure-active-directory-b2c"></a>チュートリアル:Azure Active Directory B2C にアプリケーションを登録する

[アプリケーション](active-directory-b2c-apps.md)が Azure Active Directory (Azure AD) B2C と対話できるようにするには、管理しているテナントに登録する必要があります。 このチュートリアルでは、Azure portal を使用して Web アプリケーションを登録する方法を示します。

この記事では、次のことについて説明します。

> [!div class="checklist"]
> * Web アプリケーションの登録
> * クライアント シークレットの作成

Azure サブスクリプションをお持ちでない場合は、開始する前に [無料アカウント](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) を作成してください。

## <a name="prerequisites"></a>前提条件

独自の [Azure AD B2C テナント](tutorial-create-tenant.md)をまだ作成していない場合、すぐに作成してください。 既存の Azure AD B2C テナントを使用できます。

## <a name="register-a-web-application"></a>Web アプリケーションの登録

1. お使いの Azure AD B2C テナントを含むディレクトリを使用していることを確認してください。確認のために、トップ メニューにある **[ディレクトリとサブスクリプション フィルター]** をクリックして、お使いのテナントを含むディレクトリを選択します。
2. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
3. **[アプリケーション]** を選択し、 **[追加]** を選択します。
4. アプリケーションの名前を入力します。 たとえば、*webapp1* とします。
5. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、 **[はい]** を選択します。
6. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 たとえば、`https://localhost:44316` でローカルにリッスンするように設定します。 ポート番号がわからない場合は、プレースホルダーの値を入力し、後で変更します。

    このチュートリアルのようなテスト目的では、`https://jwt.ms` に設定して、検査のためにトークンの内容が表示されるようにすることができます。 このチュートリアルでは、 **[応答 URL]** を `https://jwt.ms` に設定します。

    応答 URL は `https` スキームで始まる必要があり、すべての応答 URL 値で 1 つの DNS ドメインを共有する必要があります。 たとえば、アプリケーションの応答 URL が `https://login.contoso.com` の場合、URL `https://login.contoso.com/new` のように追加することができます。 または、`https://new.login.contoso.com` のように、`login.contoso.com` の DNS サブドメインを参照することもできます。 アプリケーションに `login-east.contoso.com` と `login-west.contoso.com` の応答 URL を設定する場合は、これらの応答 URL を `https://contoso.com`、`https://login-east.contoso.com`、`https://login-west.contoso.com` の順に追加する必要があります。 後の 2 つの応答 URL を追加できるのは、これらが 1 つ目の `contoso.com` という応答 URL のサブドメインであるためです。

7. **Create** をクリックしてください。

## <a name="create-a-client-secret"></a>クライアント シークレットの作成

お客様のアプリケーションでコードをトークンと交換する場合は、アプリケーション シークレットを作成する必要があります。

1. **[Azure AD B2C - アプリケーション]** ページで、作成したアプリケーション (例: *webapp1*) を選択します。
2. **[キー]** を選択し、 **[キーの生成]** を選択します。
3. **[保存]** を選択し、キーを参照します。 **アプリ キー** の値をメモしておきます。 アプリケーションのコード内で、この値をアプリケーション シークレットとして使用します。

## <a name="next-steps"></a>次の手順

この記事で学習した内容は次のとおりです。

> [!div class="checklist"]
> * Web アプリケーションの登録
> * クライアント シークレットの作成

次に、ユーザーがサインアップ、サインイン、および各自のプロファイルを管理できるようにするユーザー フローの作成方法を確認します。

> [!div class="nextstepaction"]
> [Azure Active Directory B2C 内にユーザー フローを作成する >](tutorial-create-user-flows.md)
