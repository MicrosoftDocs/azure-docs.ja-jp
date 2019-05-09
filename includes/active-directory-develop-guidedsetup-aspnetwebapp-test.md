---
title: インクルード ファイル
description: インクルード ファイル
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/19/2018
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 8795c9ab0a4dbb76327d0ead48ed33fb0cff9e86
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/12/2019
ms.locfileid: "59528535"
---
## <a name="test-your-code"></a>コードのテスト

Visual Studio でお使いのアプリケーションをテストするには、**F5** キーを押してプロジェクトを実行します。 ブラウザーで http://<span></span>localhost:{ポート} の場所を開くと、**[Microsoft アカウントでサインイン]** ボタンが表示されます。 ボタンを選択して、サインイン プロセスを開始します。

テストを実行する準備が整ったら、Microsoft Azure Active Directory (Azure AD) アカウント (職場または学校のアカウント) または個人用の Microsoft アカウント (<span>live.</span>com または <span>outlook.</span>com) を使用してサインインします。

![Microsoft アカウントでのサインイン](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin.png)
<br/><br/>
![Microsoft アカウントへのサインイン](media/active-directory-develop-guidedsetup-aspnetwebapp-test/aspnetbrowsersignin2.png)

#### <a name="view-application-results"></a>アプリケーションの結果を表示する

サインインした後、ユーザーは Web サイトのホーム ページにリダイレクトされます。 ホーム ページは、Microsoft アプリケーション登録ポータルのアプリケーション登録情報で指定した HTTPS の URL です。 ホーム ページには、ようこそメッセージ *"Hello \<ユーザー>"*、サインアウトのためのリンク、およびユーザーの要求を表示するリンクが含まれます。 ユーザーの要求へのリンクは、先ほど作成した *Claims* コントローラーを参照します。

### <a name="browse-to-see-the-users-claims"></a>ユーザーの要求を参照して表示する

ユーザーの要求を表示するには、リンクを選択して、承認されたユーザーのみが使用できるコントローラー ビューを参照します。

#### <a name="view-the-claims-results"></a>要求の結果を表示する

コントローラー ビューを参照した後は、ユーザーの基本プロパティを示す次の表を確認する必要があります。

|プロパティ |値 |説明 |
|---|---|---|
|**Name** |ユーザーのフルネーム | ユーザーの姓と名。
|**ユーザー名** |user<span>@domain.com</span> | ユーザーの識別に使用されているユーザー名。
|**[件名]** |サブジェクト |Web でユーザーを一意に特定する文字列。|
|**テナント ID** |Guid | ユーザーの Azure AD 組織を一意に表す **guid**。|

さらに、認証要求内にあるすべての要求を示した表を確認する必要があります。 詳細については、[Azure AD ID トークンにある要求の一覧](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims)をご覧ください。

### <a name="test-access-to-a-method-that-has-an-authorize-attribute-optional"></a>Authorize 属性を持つメソッドへのアクセスをテストする (省略可能)

`Authorize` 属性で保護されているコント ローラーに対する匿名ユーザーとしてのアクセスをテストするには、次の手順に従います。

1. ユーザーのサインアウトのリンクを選択し、サインアウトのプロセスを完了します。
2. お使いのブラウザーで「 http://<span></span>localhost:{ポート}/claims」を入力し、`Authorize` 属性で保護されているコントローラーにアクセスします。

#### <a name="expected-results-after-access-to-a-protected-controller"></a>保護されているコントローラーへアクセスした後に期待される結果

保護されているコントローラー ビューを使用するために、認証を求めるメッセージが表示されます。

## <a name="advanced-options"></a>[詳細オプション]

<!--start-collapse-->
### <a name="protect-your-entire-website"></a>Web サイト全体を保護する
Web サイト全体を保護するには、**Global.asax** ファイルで、`AuthorizeAttribute` 属性を `Application_Start` メソッドの `GlobalFilters` フィルターに追加します。

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

### <a name="restrict-who-can-sign-in-to-your-application"></a>アプリケーションにサインインできるユーザーを制限する

このガイドに従ってアプリケーションを構築すると、既定では、個人アカウント (outlook.com、live.com などを含む) だけでなく、Azure Active Directory と統合されたすべての会社や組織の職場/学校アカウントのサインインを受け入れることになります。 これは、SaaS アプリケーションに推奨されるオプションです。

アプリケーションへのユーザーのサインイン アクセスを制限するために、複数のオプションを使用できます。

#### <a name="option-1-restrict-users-from-only-one-organizations-active-directory-instance-to-sign-in-to-your-application-single-tenant"></a>オプション 1:アプリケーションへのサインインを 1 つの組織の Active Directory インスタンスのユーザーのみに制限する (シングルテナント)

このオプションは、"*LOB アプリケーション*" で一般的なシナリオです。アプリケーションが特定の Azure Active Directory インスタンスに属するアカウント (そのインスタンスの "*ゲスト アカウント*" を含む) からのサインインのみを受け入れるようにする場合は、以下の手順を実行します。

1. **Web.config** ファイルで、`Tenant` パラメーターの値を `Common` から `contoso.onmicrosoft.com` などの組織のテナント名に変更します。
2. [OWIN Startup クラス](#configure-the-authentication-pipeline)の `ValidateIssuer` 引数を `true` に設定します。

#### <a name="option-2-restrict-access-to-your-application-to-users-in-a-specific-list-of-organizations"></a>オプション 2:アプリケーションへのアクセスを組織の特定のリスト内のユーザーに制限する

許可されている組織の一覧にある Azure AD 組織に属するユーザー アカウントのみに対して、サインイン アクセスを制限できます。
1. [OWIN Startup クラス](#configure-the-authentication-pipeline)の `ValidateIssuer` 引数を `true` に設定します。
2. `ValidIssuers` パラメーターの値を、許可される組織の一覧に設定します。

#### <a name="option-3-use-a-custom-method-to-validate-issuers"></a>オプション 3: カスタム メソッドを使用して発行者を検証する

**IssuerValidator** パラメーターを使用して、カスタム メソッドを実装して発行者を検証できます。 このパラメーターの使用方法の詳細については、[TokenValidationParameters クラス](/previous-versions/visualstudio/dn464192(v=vs.114))に関するページをご覧ください。
