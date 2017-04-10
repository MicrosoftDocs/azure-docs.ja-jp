---
title: "Azure Active Directory 認証を使用した基幹業務 Azure アプリの作成 | Microsoft Docs"
description: "Azure Active Directory の認証を使用して Azure App Service に ASP.NET MVC 基幹業務アプリを作成する方法について説明します"
services: app-service\web, active-directory
documentationcenter: .net
author: cephalin
manager: erikre
editor: 
ms.assetid: ad947bdb-4463-43ff-a5e3-91d9b2169b60
ms.service: app-service-web
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: web
ms.date: 09/01/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: a00e3c5ed41aff48a6845c2f07ea3e43580045ee
ms.lasthandoff: 03/01/2017


---
# <a name="create-a-line-of-business-azure-app-with-azure-active-directory-authentication"></a>Azure Active Directory 認証を使用した基幹業務 Azure アプリの作成
この記事では、[Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) で[認証/承認](../app-service/app-service-authentication-overview.md)機能を使って .NET 基幹業務アプリを作成する方法を示します。 また、 [Azure Active Directory Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) を使ってアプリケーション内のディレクトリ データを照会する方法についても説明します。

使用する Azure Active Directory テナントを Azure 専用ディレクトリにすることや、 [オンプレミスの Active Directory と同期](../active-directory/active-directory-aadconnect.md)して、オンプレミス ユーザーやリモート ユーザー向けのシングル サインオン環境を構築することができます。 この記事では、Azure アカウントの既定のディレクトリを使用します。

<a name="bkmk_build"></a>

## <a name="what-you-will-build"></a>学習内容
次の機能を備え、作業項目を追跡する、単純な LOB の "作成、読み取り、更新、削除" (CRUD) アプリケーションを App Service Web Apps に作成します。

* Azure Active Directory に対してユーザーを認証する
* [Azure Active Directory Graph API](http://msdn.microsoft.com/library/azure/hh974476.aspx)
* ASP.NET MVC *No Authentication* テンプレートを使用する

基幹業務 Azure アプリにロールベースのアクセス制御 (RBAC) が必要な場合は、「 [次のステップ](#next)」を参照してください。

<a name="bkmk_need"></a>

## <a name="what-you-need"></a>必要なもの
[!INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

このチュートリアルを完了するには、以下が必要です。

* 各グループのユーザーが属する Azure Active Directory テナント
* Azure Active Directory テナントでアプリケーションを作成するためのアクセス許可
* Visual Studio 2013 Update 4 以降
* [Azure SDK 2.8.1 以降](https://azure.microsoft.com/downloads/)

<a name="bkmk_deploy"></a>

## <a name="create-and-deploy-a-web-app-to-azure"></a>Web アプリを作成して Azure にデプロイする
1. Visual Studio で、**[ファイル]**  >  **[新規]**  >  **[プロジェクト]** をクリックします。
2. **[ASP.NET Web アプリケーション]** を選択し、プロジェクトに名前を付けて、**[OK]** をクリックします。
3. **MVC** テンプレートを選択し、認証を **[認証なし]** に変更します。 **[クラウドにホストする]** が選択されていることを確認し、**[OK]** をクリックします。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/1-create-mvc-no-authentication.png)
4. **[App Service の作成]** ダイアログ ボックスで、**[アカウントの追加]** をクリックし (次に、ドロップダウン リストで **[アカウントの追加]** をクリックして)、Azure アカウントにログインします。
5. ログインしたら、Web アプリを構成します。 リソース グループと App Service プランの **[新規]** ボタンをクリックして、それぞれを新しく作成します。 **[他の Azure サービスの探索]** をクリックして続行します。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/2-create-app-service.png)
6. **[サービス]** タブで、**[+]** をクリックして、アプリの SQL Database を追加します。 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/3-add-sql-database.png)
7. **[SQL Database の構成]** で、**[新規]** をクリックして、SQL Server インスタンスを作成します。
8. **[SQL Server の構成]**で、SQL Server インスタンスを構成します。 **[OK]**、**[OK]**、**[作成]** の順にクリックして、Azure でのアプリの作成を開始します。
9. **[Azure App Service のアクティビティ]**で、アプリの作成完了を確認できます。 **[今すぐ &lt;*アプリ名*> をこの Web App に発行する]**、**[発行]** の順にクリックします。 
   
    完了すると、発行したアプリがブラウザーで開きます。 
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/4-published-shown-in-browser.png)

<a name="bkmk_auth"></a>

## <a name="configure-authentication-and-directory-access"></a>認証とディレクトリ アクセスを構成する
1. [Azure ポータル](https://portal.azure.com)にログインします。
2. 左側のメニューで、**[App Services]**  >  ** &lt;*アプリ名*>**  >  **[認証/承認]** をクリックします。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/5-app-service-authentication.png)
3. **[オン]**  >  **[Azure Active Directory]**  >  **[高速]**  >  **[OK]** の順にクリックして、Azure Active Directory 認証をオンにします。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/6-authentication-express.png)
4. コマンド バーの **[保存]** をクリックします。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/7-authentication-save.png)
   
    認証設定が正常に保存されたら、ブラウザーでもう一度アプリに移動します。 既定の設定により、アプリ全体で認証が強制されます。 まだログインしていない場合は、ログイン画面にリダイレクトされます。 ログインすると、アプリが HTTPS によって保護されていることを確認できます。 次に、ディレクトリ データへのアクセスを有効にする必要があります。 
5. [クラシック ポータル](https://manage.windowsazure.com)に移動します。
6. 左側のメニューで、**Active Directory**  >  **既定のディレクトリ**  >  **アプリケーション**  >  **&lt;*アプリ名*>** をクリックします。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/8-find-aad-application.png)
   
    これは、認証/承認機能を有効にするために App Service によって作成された Azure Active Directory アプリケーションです。
7. **[ユーザー]** と **[グループ]** をクリックして、ディレクトリにユーザーとグループが存在することを確認します。 存在しない場合は、テスト用のユーザーとグループを作成します。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-users-groups.png)
8. **[構成]** をクリックして、このアプリケーションを構成します。
9. 下へスクロールして **[キー]** セクションに移動し、期間を選択して、キーを追加します。 **[デリゲートされたアクセス許可]** をクリックし、**[ディレクトリ データの読み取り]** を選択します。 
   [ **Save**] をクリックします。
   
    ![](./media/web-sites-dotnet-lob-application-azure-ad/10-configure-aad-application.png)
10. 設定が保存されたら、スクロールして **[キー]** セクションに戻り、**[コピー]** をクリックして、クライアント キーをコピーします。 
    
     ![](./media/web-sites-dotnet-lob-application-azure-ad/11-get-app-key.png)
    
    > [!IMPORTANT]
    > ここでこのページから移動すると、このクライアント キーにアクセスすることができなくなります。
    > 
    > 
11. 次に、このキーを使って Web アプリを構成する必要があります。 Azure アカウントで [Azure リソース エクスプローラー](https://resources.azure.com) にログインします。
12. ページの上部にある **[読み取り/書き込み]** をクリックして、Azure リソース エクスプローラーで変更を行います。
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/12-resource-manager-writable.png)
13. アプリの認証設定を見つけます。[subscriptions] > **&lt;*サブスクリプション名*>** > **[resourceGroups]**  >  **&lt;*リソース グループ名*>**  >  **[providers]**  >  **[Microsoft.Web]**  >  **[sites]**  >  **&lt;*アプリ名*>**  >  **[config]**  >  **[authsettings]** を展開します。
14. **[編集]**をクリックします。
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/13-edit-authsettings.png)
15. 編集ウィンドウで、`clientSecret` と `additionalLoginParams` プロパティを次のように設定します。
    
        ...
        "clientSecret": "<client key from the Azure Active Directory application>",
        ...
        "additionalLoginParams": ["response_type=code id_token", "resource=https://graph.windows.net"],
        ...
16. 上部にある **[Put]** をクリックして、変更を送信します。
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/14-edit-parameters.png)
17. 次に、Azure Active Directory Graph API にアクセスするための認証トークンがあるかどうかをテストするために、ブラウザーで **https://&lt;*アプリ名*>.azurewebsites.net/.auth/me** に移動します。 すべての構成が正しく行われていると、JSON 応答に `access_token` プロパティが表示されます。
    
    `~/.auth/me` URL パスは App Service の認証/承認によって管理されており、認証済みセッションに関する情報がすべて取得できます。 詳細については、「 [Azure App Service での認証および承認](../app-service/app-service-authentication-overview.md)」を参照してください。
    
    > [!NOTE]
    > `access_token` には有効期限があります。 ただし、App Service の認証/承認には、 `~/.auth/refresh`を使用したトークン更新機能が用意されています。 この機能の使用方法の詳細については、「 [App Service Token Store (App Service トークン ストア)](https://cgillum.tech/2016/03/07/app-service-token-store/)」を参照してください。
    > 
    > 

次に、ディレクトリ データを使用してみます。

<a name="bkmk_crud"></a>

## <a name="add-line-of-business-functionality-to-your-app"></a>基幹業務の機能をアプリに追加する
単純な CRUD 作業項目トラッカーを作成します。  

1. ~\Models フォルダーに WorkItem.cs という名前のクラス ファイルを作成し、`public class WorkItem {...}` を次のコードに置き換えます。
   
     using System.ComponentModel.DataAnnotations;
   
     public class WorkItem   {
   
         [Key]
         public int ItemID { get; set; }
         public string AssignedToID { get; set; }
         public string AssignedToName { get; set; }
         public string Description { get; set; }
         public WorkItemStatus Status { get; set; }
     }
   
     public enum WorkItemStatus   {
   
         Open,
         Investigating,
         Resolved,
         Closed
     }
2. プロジェクトをビルドして、新しいモデルから Visual Studio のスキャフォールディング ロジックにアクセスできるようにします。
3. 新しくスキャフォールディングした項目 `WorkItemsController` を ~\Controllers フォルダーに追加します (**Controllers** を右クリックし、**[追加]** をポイントして、**[新規スキャフォールディングアイテム]** を選択します)。 
4. **[Entity Framework を使用したビュー付きの MVC 5 コントローラー]** を選択し、**[追加]** をクリックします。
5. 作成したモデルを選択し、**[+]**、**[追加]** の順にクリックして、データ コンテキストを追加し、**[追加]** をクリックします。
   
   ![](./media/web-sites-dotnet-lob-application-azure-ad/16-add-scaffolded-controller.png)
6. ~\Views\WorkItems\Create.cshtml (自動的にスキャフォールディングされた項目) で、`Html.BeginForm` ヘルパー メソッドを探し、次の強調表示された変更を加えます。  
   
   <pre class="prettyprint">
   @model WebApplication1.Models.WorkItem
   
   @{  ViewBag.Title = &quot;Create&quot;; }
   
   &lt;h2&gt;Create&lt;/h2&gt;
   
   @using (Html.BeginForm(<mark>&quot;Create&quot;, &quot;WorkItems&quot;, FormMethod.Post, new { id = &quot;main-form&quot; }</mark>)) {  @Html.AntiForgeryToken()
   
    &lt;div class=&quot;form-horizontal&quot;&gt;
        &lt;h4&gt;WorkItem&lt;/h4&gt;
        &lt;hr /&gt;
        @Html.ValidationSummary(true, &quot;&quot;, new { @class = &quot;text-danger&quot; })      &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToID, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })          &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = &quot;form-control&quot;<mark>, @type = &quot;hidden&quot;</mark> } })              @Html.ValidationMessageFor(model =&gt; model.AssignedToID, &quot;&quot;, new { @class = &quot;text-danger&quot; })          &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = &quot;form-control&quot; } })
                @Html.ValidationMessageFor(model =&gt; model.Description, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;control-label col-md-2&quot; })
            &lt;div class=&quot;col-md-10&quot;&gt;
                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = &quot;form-control&quot; })
                @Html.ValidationMessageFor(model =&gt; model.Status, &quot;&quot;, new { @class = &quot;text-danger&quot; })
            &lt;/div&gt;
        &lt;/div&gt;
   
        &lt;div class=&quot;form-group&quot;&gt;
            &lt;div class=&quot;col-md-offset-2 col-md-10&quot;&gt;
                &lt;input type=&quot;submit&quot; value=&quot;Create&quot; class=&quot;btn btn-default&quot;<mark> id=&quot;submit-button&quot;</mark> /&gt;
            &lt;/div&gt;
        &lt;/div&gt;
    &lt;/div&gt;
   }
   
   &lt;div&gt;
    @Html.ActionLink(&quot;Back to List&quot;, &quot;Index&quot;) &lt;/div&gt;
   
   @section Scripts {  @Scripts.Render(&quot;~/bundles/jqueryval&quot;)  <mark>&lt;script&gt;
        // People/Group Picker Code      var maxResultsPerPage = 14;      var input = document.getElementById(&quot;AssignedToName&quot;);
   
        // Access token from request header, and tenantID from claims identity
        var token = &quot;@Request.Headers[&quot;X-MS-TOKEN-AAD-ACCESS-TOKEN&quot;]&quot;;
        var tenant =&quot;@(System.Security.Claims.ClaimsPrincipal.Current.Claims
                        .Where(c => c.Type == &quot;http://schemas.microsoft.com/identity/claims/tenantid&quot;)
                        .Select(c => c.Value).SingleOrDefault())&quot;;
   
        var picker = new AadPicker(maxResultsPerPage, input, token, tenant);
   
        // Submit the selected user/group to be asssigned.
        $(&quot;#submit-button&quot;).click({ picker: picker }, function () {
            if (!picker.Selected())
                return;
            $(&quot;#main-form&quot;).get()[0].elements[&quot;AssignedToID&quot;].value = picker.Selected().objectId;
        });
    &lt;/script&gt;</mark> }
   </pre>
   
   Azure Active Directory Graph API の呼び出しを行うために、`AadPicker` オブジェクトで `token` と `tenant` が使用されていることに注意してください。 後で `AadPicker` を追加します。     
   
   > [!NOTE]
   > `~/.auth/me` を使用してクライアント側から `token` と `tenant` を取得することもできます。ただし、これは追加のサーバー呼び出しになります。 次に例を示します。
   > 
   > $.ajax({ dataType: "json", url: "/.auth/me", success: function (data) { var token = data[0].access_token; var tenant = data[0].user_claims .find(c => c.typ === 'http://schemas.microsoft.com/identity/claims/tenantid') .val; } });
   > 
   > 
7. 同じ変更を ~\Views\WorkItems\Edit.cshtml にも加えます。
8. `AadPicker` オブジェクトが定義されているスクリプトをプロジェクトに追加する必要があります。 ~\Scripts フォルダーを右クリックし、**[追加]** をポイントして、**[JavaScript ファイル]** をクリックします。 ファイル名として「 `AadPickerLibrary` 」と入力し、 **[OK]**をクリックします。
9. [こちら](https://raw.githubusercontent.com/cephalin/active-directory-dotnet-webapp-roleclaims/master/WebApp-RoleClaims-DotNet/Scripts/AadPickerLibrary.js)の内容をコピーし ~\Scripts\AadPickerLibrary.js に貼り付けます。
   
   このスクリプトでは、 `AadPicker` オブジェクトが [Azure Active Directory Graph API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) を呼び出して、入力に一致するユーザーとグループを検索します。  
10. ~\Scripts\AadPickerLibrary.js では、[jQuery UI Autocomplete ウィジェット](https://jqueryui.com/autocomplete/)も使用します。 そのため、jQuery UI をプロジェクトに追加する必要があります。 プロジェクトを右クリックし、 **[NuGet パッケージの管理]**を選択します。
11. NuGet パッケージ マネージャーで、[参照] をクリックし、検索バーに「**jquery-ui**」と入力して、**[jQuery.UI.Combined]** をクリックします。
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/17-add-jquery-ui-nuget.png)
12. 右側のウィンドウで **[インストール]** をクリックし、**[OK]** をクリックして、続行します。
13. ~\App_Start\BundleConfig.cs を開き、次の強調表示された変更を加えます。  
    
    <pre class="prettyprint">
    public static void RegisterBundles(BundleCollection bundles) { bundles.Add(new ScriptBundle(&quot;~/bundles/jquery&quot;).Include( &quot;~/Scripts/jquery-{version}.js&quot;<mark>, &quot;~/Scripts/jquery-ui-{version}.js&quot;, &quot;~/Scripts/AadPickerLibrary.js&quot;</mark>));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/jqueryval&quot;).Include(
                    &quot;~/Scripts/jquery.validate*&quot;));
    
        // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
        // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
        bundles.Add(new ScriptBundle(&quot;~/bundles/modernizr&quot;).Include(
                    &quot;~/Scripts/modernizr-*&quot;));
    
        bundles.Add(new ScriptBundle(&quot;~/bundles/bootstrap&quot;).Include(
                    &quot;~/Scripts/bootstrap.js&quot;,
                    &quot;~/Scripts/respond.js&quot;));
    
        bundles.Add(new StyleBundle(&quot;~/Content/css&quot;).Include(
                    &quot;~/Content/bootstrap.css&quot;,
                    &quot;~/Content/site.css&quot;<mark>,
                    &quot;~/Content/themes/base/jquery-ui.css&quot;</mark>));
    }
    </pre>
    
    アプリで JavaScript と CSS ファイルを管理する効率的な方法は他にもありますが、 わかりやすくするために、すべてのビューで読み込まれるバンドルを利用します。
14. 最後に、~\Global.asax の `Application_Start()` メソッドに次のコード行を追加します。 `Ctrl`+`.` キーを押して、エラーを修正します。
    
        AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
    
    > [!NOTE]
    > このコード行を追加する必要があるのは、既定の MVC テンプレートでは、一部の操作に <code>[ValidateAntiForgeryToken]</code> 装飾が使用されるためです。 [Brock Allen](https://twitter.com/BrockLAllen) が「[MVC 4, AntiForgeryToken and Claims (MVC 4、AntiForgeryToken とクレーム)](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/)」で説明している動作により、HTTP POST が偽造防止トークンの検証に次の理由で失敗する可能性があります。
    > 
    > * 既定で偽造防止トークンに必要となる http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider を Azure Active Directory が送信しない。
    > * Azure Active Directory が AD FS とディレクトリを同期している場合、AD FS トラストは既定で http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider の要求を送信しない。ただし、AD FS を手動で構成してこの要求を送信することはできます。
    > 
    > `ClaimTypes.NameIdentifies` は、Azure Active Directory が提供する要求 `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` を指定します。  
    > 
    > 
15. 変更を発行します。 プロジェクトを右クリックし、 **[発行]**をクリックします。
16. **[設定]** をクリックし、SQL Database への接続文字列があることを確認します。**[データベースを更新する]** を選択して、モデルのスキーマを変更し、**[発行]** をクリックします。
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/18-publish-crud-changes.png)
17. ブラウザーで https://&lt;*appname*>.azurewebsites.net/workitems に移動し、**[新規作成]** をクリックします。
18. **[AssignedToName]** ボックスをクリックします。 ドロップダウン リストに Azure Active Directory テナントのユーザーとグループが表示されます。 入力して絞り込むことができます。また、`Up` キーまたは `Down` キーを使うか、クリックして、ユーザーまたはグループを選択できます。 
    
    ![](./media/web-sites-dotnet-lob-application-azure-ad/19-use-aadpicker.png)
19. **[作成]** をクリックして変更を保存します。 作成された作業項目に対して **[編集]** をクリックして、同じ動作を確認します。

これで、Azure で基幹業務アプリが実行され、ディレクトリにアクセスできるようになりました。 Graph API を使ってできることは他にもたくさんあります。 「 [Azure AD Graph API reference (Azure AD Graph API リファレンス)](https://msdn.microsoft.com/library/azure/ad/graph/api/api-catalog)」をご覧ください。

<a name="next"></a>

## <a name="next-step"></a>次のステップ
基幹業務 Azure アプリにロールベースのアクセス制御 (RBAC) が必要な場合は、Azure Active Directory チームが提供するサンプルの [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims) をご覧ください。 このサンプルでは、Azure Active Directory アプリケーションに対してロールを有効にし、 `[Authorize]` 装飾を使用してユーザーを承認する方法を示しています。

基幹業務アプリがオンプレミスのデータにアクセスする必要がある場合は、「 [Azure App Service のハイブリッド接続を使用してオンプレミスのリソースにアクセスする](web-sites-hybrid-connection-get-started.md)」をご覧ください。

<a name="bkmk_resources"></a>

## <a name="further-resources"></a>他の関連リソース
* [Azure App Service での認証および承認](../app-service/app-service-authentication-overview.md)
* [Azure アプリでのオンプレミス Active Directory を使用した認証](web-sites-authentication-authorization.md)
* [AD FS 認証を使用した基幹業務 Azure アプリの作成](web-sites-dotnet-lob-application-adfs.md)
* [App Service Auth and the Azure AD Graph API (App Service の認証と Azure AD Graph API)](https://cgillum.tech/2016/03/25/app-service-auth-aad-graph-api/)
* [Microsoft Azure Active Directory のサンプルとドキュメント](https://github.com/AzureADSamples)
* [Azure Active Directory がサポートしているトークンと要求の種類](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[Protect the Application with SSL and the Authorize Attribute]: web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute

