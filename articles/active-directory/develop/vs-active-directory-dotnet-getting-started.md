---
title: .NET MVC プロジェクトで Azure AD の使用を開始する | Azure
description: Visual Studio 接続済みサービスを使用して Azure Active Directory を接続または作成した後に、.NET MVC プロジェクトで Azure AD の使用を開始する方法について説明します。
author: ghogen
manager: jillfra
ms.prod: visual-studio-windows
ms.technology: vs-azure
ms.workload: azure-vs
ms.topic: how-to
ms.date: 03/12/2018
ms.author: ghogen
ms.custom: devx-track-csharp, aaddev, vs-azure
ms.openlocfilehash: 15a7c873e4d1e5c962a89b03f2a5cafc88843192
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "88165466"
---
# <a name="getting-started-with-azure-active-directory-aspnet-mvc-projects"></a>Azure Active Directory (ASP.NET MVC プロジェクト) の使用の開始

> [!div class="op_single_selector"]
> - [作業の開始](vs-active-directory-dotnet-getting-started.md)
> - [変更内容](vs-active-directory-dotnet-what-happened.md)

この記事では、Visual Studio の **[プロジェクト] > [接続済みサービス]** コマンドを使用して Active Directory を ASP.NET MVC プロジェクトに追加した後のガイダンスを説明します。 プロジェクトにサービスをまだ追加していない場合は、いつでも行うことができます。

接続済みサービスを追加したときにプロジェクトに加えられる変更について詳しくは、[MVC プロジェクトの変更点](vs-active-directory-dotnet-what-happened.md)に関する記事をご覧ください。

## <a name="requiring-authentication-to-access-controllers"></a>コントローラーへのアクセスに対して認証を要求する

プロジェクトに含まれるすべてのコントローラーには、 `[Authorize]` 属性が設定されています。 この属性により、ユーザーがこれらのコントローラーにアクセスする際に認証が求められます。 これらのコントローラーに匿名でアクセスできるようにするには、コントローラーからこの属性を削除します。 より細かなレベルでアクセス許可を設定するには、コントローラー クラスではなく、認証を必要とするそれぞれのメソッドに対してこの属性を割り当てます。

## <a name="adding-signin--signout-controls"></a>SignIn/SignOut コントロールを追加する

ビューに SignIn/SignOut コントロールを追加するには、`_LoginPartial.cshtml` 部分ビューを使用してこの機能をいずれかのビューに追加します。 この機能を標準 `_Layout.cshtml` ビューに追加した例を次に示します。 (div class="navbar-collapse collapse" の最後の要素にご注目ください):

```html
<!DOCTYPE html>
 <html>
 <head>
     <meta charset="utf-8" />
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>@ViewBag.Title - My ASP.NET Application</title>
    @Styles.Render("~/Content/css")
    @Scripts.Render("~/bundles/modernizr")
</head>
<body>
    <div class="navbar navbar-inverse navbar-fixed-top">
        <div class="container">
            <div class="navbar-header">
                <button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse">
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                    <span class="icon-bar"></span>
                </button>
                @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" })
            </div>
            <div class="navbar-collapse collapse">
                <ul class="nav navbar-nav">
                    <li>@Html.ActionLink("Home", "Index", "Home")</li>
                    <li>@Html.ActionLink("About", "About", "Home")</li>
                    <li>@Html.ActionLink("Contact", "Contact", "Home")</li>
                </ul>
                @Html.Partial("_LoginPartial")
            </div>
        </div>
    </div>
    <div class="container body-content">
        @RenderBody() 
        <hr />
        <footer>
            <p>&copy; @DateTime.Now.Year - My ASP.NET Application</p>
        </footer>
    </div>
    @Scripts.Render("~/bundles/jquery")
    @Scripts.Render("~/bundles/bootstrap")
    @RenderSection("scripts", required: false)
</body>
</html>
```

## <a name="next-steps"></a>次のステップ

- [Azure AD の認証シナリオ](./authentication-vs-authorization.md)
- [ASP.NET Web アプリへの "Microsoft でサインイン" の追加](quickstart-v2-aspnet-webapp.md)