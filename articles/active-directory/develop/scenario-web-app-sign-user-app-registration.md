---
title: ユーザーをサインインさせる Web アプリ (アプリの登録) - Microsoft ID プラットフォーム
description: ユーザーをサインインさせる Web アプリをビルドする方法について学習します (アプリの登録)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ae638f8cbef29c5d167a3ab59188169cbd934ef
ms.sourcegitcommit: 6e6813f8e5fa1f6f4661a640a49dc4c864f8a6cb
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/17/2019
ms.locfileid: "67150224"
---
# <a name="web-app-that-signs-in-users---app-registration"></a>ユーザーをサインインさせる Web アプリ - アプリの登録

このページでは、ユーザーをサインインさせる Web アプリにおけるアプリの登録の特性について説明されています。

アプリケーションを登録するには、次を使用することができます。

- [Web アプリ クイックスタート](#register-an-app-using-the-quickstarts) - アプリケーションを初めて作成するすばらしい体験に加えて、Azure portal のクイックスタートには、 **[この変更を行う]** という名前のボタンが含まれます。 既存のアプリであっても、このボタンを使用して必要なプロパティを設定できます。 これらのプロパティの値を自分のケースに適応させる必要があります。 具体的には、自分のアプリ用の Web API URL は、提案された既定値とは異なる可能性があり、URI のサインアウトにも影響があります。
- [手動でアプリケーションを登録する](#register-an-app-using-azure-portal) Azure portal
- PowerShell とコマンドライン ツール

## <a name="register-an-app-using-the-quickstarts"></a>クイックスタートを使用してアプリを登録する

このリンクに移動する場合、Web アプリケーションの作成のブーストラップを作成できます。

- [ASP.NET Core](https://aka.ms/aspnetcore2-1-aad-quickstart-v2)
- [ASP.NET](https://ms.portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs)

### <a name="register-an-app-using-azure-portal"></a>Azure portal を使用してアプリを登録する

> [!NOTE]
> 使用するポータルは、アプリケーションが Microsoft Azure パブリック クラウド、各国のクラウドまたはソブリン クラウドのいずれで実行されるかによって異なります。 詳細については、[各国のクラウド](./authentication-national-cloud.md#app-registration-endpoints)に関する記事をご覧ください。

1. 職場または学校アカウントか、個人の Microsoft アカウントを使用して、[Azure portal](https://portal.azure.com) にサインインします。 または、選択した各国のクラウドの Azure portal にサインインします。
1. ご利用のアカウントで複数のテナントにアクセスできる場合は、右上隅でアカウントを選択し、ポータルのセッションを目的の Azure AD テナントに設定します。
1. 左側のナビゲーション ウィンドウで、 **[Azure Active Directory]** サービスを選択し、 **[アプリの登録]**  >  **[新規登録]** を選択します。
1. **[アプリケーションの登録]** ページが表示されたら、以下のアプリケーションの登録情報を入力します。
   1. アプリケーションに対してサポートされるアカウントの種類を選びます (「[Supported Account types](./v2-supported-account-types.md)」 (サポートされるアカウントの種類) を参照してください)
   1. **[名前]** セクションに、アプリのユーザーに表示されるわかりやすいアプリケーション名を入力します (例: `AspNetCore-WebApp`)。
   1. **[リダイレクト URI]** に、アプリケーションの種類、および認証に成功した後に返されたトークンの応答を受け入れる URI の接続先を追加します。 たとえば、「 `https://localhost:44321/` 」のように入力します。  **[登録]** を選択します。
1. **[認証]** メニューを選択し、次の情報を追加します。
   1. **[応答 URL]** に「`https://localhost:44321/signin-oidc`」を追加します。
   1. **[詳細設定]** セクションの **[ログアウト URL]** を「`https://localhost:44321/signout-oidc`」に設定します。
   1. **[暗黙的な許可]** の **[ID トークン]** チェック ボックスをオンにします。
   1. **[保存]** を選択します。

### <a name="register-an-app-using-powershell"></a>PowerShell を使用してアプリを登録する

> [!NOTE]
> 現在、Azure AD PowerShell では、次のサポートされるアカウントの種類でのみアプリケーションを作成します。
>
> - MyOrg (この組織のディレクトリ内のアカウントのみ)
> - AnyOrg (任意の組織のディレクトリ内のアカウント)
>
> 個人の Microsoft アカウントでユーザーをサインインさせるアプリケーションを作成する場合 (例: Skype、XBox、Outlook.com)、まずマルチテナント アプリケーション (サポートされるアカウントの種類 = 任意の組織のディレクトリ内のアカウント) を作成して、Azure portal からアプリケーション マニフェストの `signInAudience` プロパティを変更することができます。 これは ASP.NET Core チュートリアルの手順 [1.3](https://github.com/Azure-Samples/active-directory-aspnetcore-webapp-openidconnect-v2/tree/master/1-WebApp-OIDC/1-3-AnyOrgOrPersonal#step-1-register-the-sample-with-your-azure-ad-tenant) で詳しく説明されています (また、任意の言語で Web アプリに一般化できます)。

## <a name="next-steps"></a>次の手順

> [!div class="nextstepaction"]
> [アプリのコード構成](scenario-web-app-sign-user-app-configuration.md)
