---
title: "Azure AD v2 の ASP.NET Web サーバーの概要 - テスト | Microsoft Docs"
description: "OpenID Connect 標準を使用する従来の Web ブラウザー ベースのアプリケーションで、ASP.NET ソリューションに Microsoft のサインインを実装する"
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mbaldwin
editor: 
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/09/2017
ms.author: andret
ms.custom: aaddev
ms.translationtype: Human Translation
ms.sourcegitcommit: ef74361c7a15b0eb7dad1f6ee03f8df707a7c05e
ms.openlocfilehash: 00cb963e85111274c36c3a84489894811ad2dabd
ms.contentlocale: ja-jp
ms.lasthandoff: 05/25/2017


---
## <a name="test-your-code"></a>コードのテスト

`F5` を押して、Visual Studio でプロジェクトを実行します。 ブラウザーが開き、*http://localhost:{ポート}* に移動します。*[Microsoft アカウントでサインイン]* ボタンが表示されます。 ボタンをクリックしてサインインします。

テストの準備ができたら、職場、学校 (Azure Active Directory)、または個人の (live.com、outlook.com) アカウントを使用してサインインします。 

![[Microsoft アカウントでサインイン] ボタンが表示されたブラウザー ウィンドウ](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin.png)

![[Microsoft アカウントでサインイン] ボタンが表示されたブラウザー ウィンドウ](media/active-directory-serversidewebapp-aspnetwebappowin-test/aspnetbrowsersignin2.png)

#### <a name="expected-results"></a>予想される結果
サインインすると、Microsoft アプリケーション登録ポータルのアプリケーション登録情報で指定した HTTPS の URL をもつ Web サイトのホーム ページにリダイレクトされます。 このページには*こんにちは {ユーザー}* とサインアウトのリンクの他に、ユーザーの要求を確認するリンク、つまり前に作成した承認コントローラーのリンクが表示されます。

### <a name="see-users-claims"></a>ユーザーの要求を確認する
ハイパーリンクを選択して、ユーザーの要求を確認します。 認証されたユーザーのみが利用できるコントローラーとビューに移動します。

#### <a name="expected-results"></a>予想される結果
 ログオンしたユーザーの次の基本プロパティが記載されたテーブルが表示されます。

| プロパティ | 値 | Description|
|---|---|---|
| 名前 | {ユーザーのフルネーム} | ユーザーの姓と名
|ユーザー名 | <span>user@domain.com</span>| ログオンしたユーザーの識別に使用されるユーザー名
| [件名]| {件名}|Web 上でユーザーのログオンを一意に識別する文字列|
| テナント ID| {Guid}| ユーザーが所属する Azure Active Directory の組織を一意に表す *guid*|

さらに、認証要求に含まれるすべてのユーザー要求が記載されたテーブルが表示されます。 ID トークンに含まれるすべての要求の一覧とその説明については、[こちらの記事](https://docs.microsoft.com/azure/active-directory/develop/active-directory-token-and-claims "ID トークン内の要求の一覧")をご覧ください。


### <a name="test-accessing-a-method-that-has-an-authorize-attribute-optional"></a>*[Authorize]* 属性を持つメソッドへのアクセスをテストする (省略可能)
この手順では、匿名ユーザーとして認証済みのコントローラーへのアクセスをテストします。<br/>
ユーザーのサインアウトのリンクを選択し、サインアウトのプロセスを完了します。<br/>
お使いのブラウザーで「http://localhost:{ポート}/authenticated」を入力し、`[Authorize]` 属性で保護されているコントローラーにアクセスします。

#### <a name="expected-results"></a>予想される結果
ビュー表示の認証を要求するプロンプトが表示されます。

## <a name="additional-information"></a>追加情報

<!--start-collapse-->
### <a name="protect-your-entire-web-site"></a>Web サイト全体を保護する
`Global.asax` の `Application_Start` メソッドで `GlobalFilters` に `AuthorizeAttribute` を追加して、Web サイト全体を保護します。

```csharp
GlobalFilters.Filters.Add(new AuthorizeAttribute());
```
<!--end-collapse-->

<div></div>
<br/>

> [!NOTE]
> **アプリケーションへのサインインを、1 つの組織のユーザーに制限する方法**

> 既定では、個人アカウント (outlook.com、live.com など) だけでなく、Azure Active Directory と統合したすべての会社や組織の職場/学校アカウントを使用して、アプリケーションにサインインできます。 

> アプリケーションが Azure Active Directory と統合した 1 つの組織からのみサインインを受け入れるように設定するには、*web.config* 内の `Tenant` パラメーターを、`Common` から該当する組織のテナント名 (例: *contoso.onmicrosoft.com*) に置き換えます。 その後、*OWIN Startup クラス*内の `ValidateIssuer` 引数を `true` に変更します。

> 特定の組織の一覧に記載されたユーザーのみを許可するには、`ValidateIssuer` を true に設定し、`ValidIssuers` パラメーターを使用して組織の一覧を指定します。

> または、IssuerValidator パラメーターを使用して発行者を検証するカスタム メソッドを実装することもできます。 `TokenValidationParameters` について詳しくは、[こちら](https://msdn.microsoft.com/library/system.identitymodel.tokens.tokenvalidationparameters.aspx "TokenValidationParameters に関する MSDN の記事")の MSDN 記事をご覧ください。


