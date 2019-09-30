---
title: Web アプリケーションを追加する - Azure Active Directory B2C | Microsoft Docs
description: Active Directory B2C テナントに Web アプリケーションを追加する方法について説明します。
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.author: marsma
ms.date: 04/16/2019
ms.custom: mvc
ms.topic: conceptual
ms.service: active-directory
ms.subservice: B2C
ms.openlocfilehash: 50a4ead58cc70524ec464e52ce546b36f9685df5
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064531"
---
# <a name="add-a-web-api-application-to-your-azure-active-directory-b2c-tenant"></a>Azure Active Directory B2C テナントに Web API アプリケーションを追加する

 アクセス トークンを提示するクライアント アプリケーションからの要求を Web API リソースが受け付けて応答できるように、ご利用のテナントに Web API リソースを登録します。 この記事では、Azure Active Directory B2C (Azure AD B2C) にアプリケーションを登録する方法について説明します。

1. [Azure Portal](https://portal.azure.com) にサインインします。
2. ご自分の Azure AD B2C テナントが含まれるディレクトリを必ず使用してください。 上部メニューで **[ディレクトリ + サブスクリプション]** フィルターを選択し、ご利用のテナントが含まれるディレクトリを選択します。
3. Azure portal の左上隅にある **[すべてのサービス]** を選択してから、 **[Azure AD B2C]** を検索して選択します。
4. **[アプリケーション]** を選択し、 **[追加]** を選択します。
5. アプリケーションの名前を入力します。 たとえば、*webapi1* とします。
6. **[Include web app/ web API]\(Web アプリ/Web API を含める\)** と **[暗黙的フローを許可する]** には、 **[はい]** を選択します。
7. **[応答 URL]** には、ご使用のアプリケーションが要求したすべてのトークンを Azure AD B2C が返すエンドポイントを入力します。 運用環境のアプリケーションでは、応答 URL を `https://localhost:44332` などの値に設定することになるでしょう。 テスト目的の場合は、応答 URL を `https://jwt.ms` に設定します。
8. **[アプリケーション ID URI]** には、ご使用の Web API で使用される ID を入力します。 ドメインを含んだ完全な識別子 URI が自動的に生成されます。 たとえば、「 `https://contosotenant.onmicrosoft.com/api` 」のように入力します。
9. **Create** をクリックしてください。
10. プロパティ ページで、アプリケーション ID を記録しておきます。これは、Web アプリケーションを構成するときに使用します。

## <a name="configure-scopes"></a>スコープを構成する

スコープを使用すると、保護されたリソースへのアクセスを統制できます。 スコープは、スコープベースのアクセス制御を実装するために Web API によって使用されます。 たとえば Web API のユーザーが、読み取りと書き込みの両方のアクセス権限を持つ場合もあれば、読み取りアクセス権限しか持たない場合もあります。 このチュートリアルでは、スコープを使用して、Web API の読み取りアクセス許可と書き込みアクセス許可を定義します。

1. **[アプリケーション]** を選択し、*webapi1* を選択します。
2. **[公開済みスコープ]** を選択します。
3. **スコープ**に「`Read`」と入力し、説明に「`Read access to the application`」と入力します。
4. **スコープ**に「`Write`」と入力し、説明に「`Write access to the application`」と入力します。
5. **[Save]** をクリックします。

公開済みスコープを使用すると、クライアント アプリケーションに Web API へのアクセス許可を付与することができます。

## <a name="grant-permissions"></a>アクセス許可を付与する

保護された Web API をアプリケーションから呼び出すには、その API へのアクセス許可をアプリケーションに付与する必要があります。 たとえば「[チュートリアル: Azure Active Directory B2C にアプリケーションを登録する](tutorial-register-applications.md)」では、Azure AD B2C に *webapp1* という Web アプリケーションが作成されます。 このアプリケーションを使用して、Web API を呼び出すことができます。

1. **[アプリケーション]** を選択し、自分の Web アプリケーションを選択します。
2. **[API アクセス]** を選択し、 **[追加]** を選択します。
3. **[API の選択]** ドロップダウンで、*webapi1* を選択します。
4. **[スコープの選択]** ドロップダウンで、前に定義した **Read** スコープと **Write** スコープを選択します。
5. Click **OK**.

アプリケーションが登録されて、保護された Web API を呼び出すことができるようになります。 アプリケーションを使用するには、ユーザーは Azure AD B2C での認証を行います。 アプリケーションで、保護された Web API にアクセスするための認可の付与が、Azure AD B2C から取得されます。
