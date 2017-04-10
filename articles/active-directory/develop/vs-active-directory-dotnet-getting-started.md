---
title: "Visual Studio MVC プロジェクトで Azure AD の使用を開始する | Microsoft Docs"
description: "Visual Studio 接続済みサービスを使用して Azure Active Directory を接続または作成した後に、MVC プロジェクトで Azure AD の使用を開始する方法について説明します。"
services: active-directory
documentationcenter: 
author: TomArcher
manager: douge
editor: 
ms.assetid: 1c8b6a58-5144-4965-a905-625b9ee7b22b
ms.service: active-directory
ms.workload: web
ms.tgt_pltfrm: vs-getting-started
ms.devlang: na
ms.topic: article
ms.date: 03/01/2017
ms.author: tarcher
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: a58f328795f85e5392d1bd4c305aabb48b65798c
ms.lasthandoff: 03/21/2017


---
# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Azure Active Directory と Visual Studio 接続済みサービスの概要 (MVC プロジェクト)
> [!div class="op_single_selector"]
> * [作業の開始](vs-active-directory-dotnet-getting-started.md)
> * [変更内容](vs-active-directory-dotnet-what-happened.md)
> 
> 

## <a name="requiring-authentication-to-access-controllers"></a>コントローラーへのアクセスに対して認証を要求する
プロジェクトに含まれるすべてのコントローラーには、 **Authorize** 属性が設定されています。 この属性により、ユーザーがこれらのコントローラーにアクセスする際に認証が求められます。 これらのコントローラーに匿名でアクセスできるようにするには、コントローラーからこの属性を削除します。 より細かなレベルでアクセス許可を設定するには、コントローラー クラスではなく、認証を必要とするそれぞれのメソッドに対してこの属性を割り当てます。

## <a name="adding-signin--signout-controls"></a>SignIn/SignOut コントロールを追加する
ビューに SignIn/SignOut コントロールを追加するには、**_LoginPartial.cshtml** 部分ビューを使用してこの機能をいずれかのビューに追加します。 この機能を標準 **_Layout.cshtml** ビューに追加した例を次に示します。 (div class="navbar-collapse collapse" の最後の要素にご注目ください):

<pre>
    &lt;!DOCTYPE html&gt; 
     &lt;html&gt; 
     &lt;head&gt; 
         &lt;meta charset="utf-8" /&gt; 
        &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
        &lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
        @Styles.Render("~/Content/css") @Scripts.Render("~/bundles/modernizr") &lt;/head&gt; 
    &lt;body&gt; 
        &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
            &lt;div class="container"&gt; 
                &lt;div class="navbar-header"&gt; 
                    &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                        &lt;span class="icon-bar"&gt;&lt;/span&gt; 
                    &lt;/button&gt; 
                    @Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) &lt;/div&gt; 
                &lt;div class="navbar-collapse collapse"&gt; 
                    &lt;ul class="nav navbar-nav"&gt; 
                        &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
                        &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
                    &lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/div&gt; 
            &lt;/div&gt; 
        &lt;/div&gt; 
        &lt;div class="container body-content"&gt; 
            @RenderBody() &lt;hr /&gt; 
            &lt;footer&gt; 
                &lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
            &lt;/footer&gt; 
        &lt;/div&gt; 
        @Scripts.Render("~/bundles/jquery") @Scripts.Render("~/bundles/bootstrap") @RenderSection("scripts", required: false) &lt;/body&gt; 
    &lt;/html&gt;
</pre>

## <a name="next-steps"></a>次のステップ
- [Azure Active Directory の詳細を確認する](https://azure.microsoft.com/services/active-directory/) 


