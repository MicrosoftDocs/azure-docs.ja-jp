---
title: Azure AD v2.0 エンドポイントを使用したアプリの登録 | Microsoft Docs
description: Azure v2.0 エンドポイントを使用してアプリを Microsoft に登録して、Microsoft サービスへのサインインとアクセスを有効にする方法について説明します。
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: bb2f701f-3bc3-4759-94a5-8b9d53a8a0b6
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/02/2018
ms.author: celested
ms.reviewer: lenalepa
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6221fb7575fc267030929b449cba48cff8563f27
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2019
ms.locfileid: "58122556"
---
# <a name="quickstart-register-an-app-with-the-azure-active-directory-v20-endpoint"></a>クイック スタート:Azure Active Directory v2.0 エンドポイントを使用してアプリを登録する

[!INCLUDE [active-directory-develop-applies-v2](../../../includes/active-directory-develop-applies-v2.md)]

個人用 Microsoft アカウント (MSA) と職場や学校のアカウント (Azure AD) のサインインの両方を受け付けるアプリを構築するには、Azure Active Directory (Azure AD) v2.0 エンドポイントを使用してアプリを登録する必要があります。 現時点では、Azure AD または MSA で利用している既存のアプリは使用できません。新しいアプリを作成してください。

Azure AD のシナリオと機能のすべてが、v2.0 エンドポイントでサポートされるわけではありません。 v2.0 エンドポイントを使用する必要があるかどうかを判断するには、[v2.0 の制限事項](active-directory-v2-limitations.md)に関するページをお読みください。

> [!NOTE]
> 新しいアプリを登録する場合は、 Azure portal の新しい**アプリの登録 (プレビュー)** エクスペリエンスをお試しください。 使用するには、[アプリの登録 (プレビュー)](quickstart-register-app.md) に関するページを参照してください。

## <a name="step-1-sign-in-to-the-microsoft-application-registration-portal"></a>手順 1:Microsoft アプリケーション登録ポータルにサインインする

1. [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) で Microsoft アプリ登録ポータルにアクセスします。
1. Microsoft の個人または職場/学校アカウントのいずれかでサインインします。 いずれのアカウントもをお持ちでない場合は、新しい個人アカウントを新規登録します。
1. サインアップできましたか? おそらく空の Microsoft アプリの一覧が表示されているはずです。 これを変更してみましょう。

## <a name="step-2-register-an-app"></a>手順 2:アプリを登録します

1. **[アプリの追加]** を選択し、名前を付けます。
    ポータルにより、アプリにグローバルに一意のアプリケーション ID が割り当てられます。これは、後ほどコードで使用します。 API を呼び出すためのアクセス トークンが必要なサーバー側コンポーネントがアプリに含まれる場合 (Office、Azure、独自の Web API など)、**アプリケーションのシークレット**を作成することもできます。
1. 次に、アプリで使用する**プラットフォーム**を追加します。
    * Web ベースのアプリの場合、サインイン メッセージを送信できる **リダイレクト URI** を指定します。
    * モバイル アプリの場合、自動的に作成される既定のリダイレクト URI をメモしておきます。
    * Web API の場合、Web API にアクセスするための既定のスコープが自動的に作成されます。
        **[スコープの追加]** ボタンを使用して、スコープを追加することもできます。 **事前承認済みアプリケーション** フォームを使って、Web API を使用するように事前承認されたアプリケーションを追加することもできます。
1. 必要に応じて、**[プロファイル]** セクションでサインイン ページの外観をカスタマイズします。 
1. 移動する前に、変更内容を**保存**します。

> [!NOTE]
> [https://apps.dev.microsoft.com/](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) を使用して登録したアプリケーションは、このポータルへのサインインに使用しているアカウントのホーム テナントに登録されます。 つまり、個人の Microsoft アカウントを使用して Azure AD テナントにアプリケーションを登録することはできません。 アプリケーションを特定のテナントに登録する場合は、そのテナントで作成したアカウントを使ってサインインしてください。

## <a name="next-steps"></a>次の手順

Microsoft アプリを構築したら、いずれかの v2.0 quickstart2 を実行します。 以下に、推奨事項をいくつか示します。

[!INCLUDE [active-directory-v2-quickstart-table](~/includes/active-directory-v2-quickstart-table.md)]
