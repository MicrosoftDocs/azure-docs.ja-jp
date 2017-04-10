---
title: "ASP.NET MVC 5 モバイル Web アプリケーションを Azure App Service に展開する"
description: "ASP.NET MVC 5 Web アプリケーションのモバイル機能を使用して Web アプリケーションを Azure App Service に展開する方法のチュートリアル。"
services: app-service
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 0752c802-8609-4956-a755-686116913645
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/12/2016
ms.author: cephalin;riande
translationtype: Human Translation
ms.sourcegitcommit: 0921b01bc930f633f39aba07b7899ad60bd6a234
ms.openlocfilehash: b23dfdcf99cab46bb4cedc690e00d29d37b3a044
ms.lasthandoff: 03/01/2017


---
# <a name="deploy-an-aspnet-mvc-5-mobile-web-app-in-azure-app-service"></a>ASP.NET MVC 5 モバイル Web アプリケーションを Azure App Service に展開する
このチュートリアルでは、モバイル対応である ASP.NET MVC 5 Web アプリケーションをビルドして Azure App Service に展開する方法の基本を説明します。 このチュートリアルを実行するには、[Visual Studio Express 2013 for Web][Visual Studio Express 2013] が必要です。Visual Studio のプロフェッショナル エディションを既にお持ちの場合は、それを使用することもできます。 [Visual Studio 2015] も使用できますが、スクリーン ショットが異なり、ASP.NET 4.x テンプレートを使用する必要があります。

[!INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

## <a name="what-youll-build"></a>作成するアプリケーション:
このチュートリアルでは、[スタート プロジェクト][StarterProject]に用意されている単純な会議一覧アプリケーションにモバイル機能を追加します。 次のスクリーンショットは、完成したアプリケーションの ASP.NET セッションを Internet Explorer 11 F12 開発ツールのブラウザー エミュレーターで表示したものです。

![][FixedSessionsByTag]

Internet Explorer 11 F12 開発者ツールと [Fiddler ツール][Fiddler]を使用してアプリケーションをデバッグできます。 

## <a name="skills-youll-learn"></a>学習内容
ここでは次の内容について学習します。

* Visual Studio 2013 を使用して、Web アプリケーションを Azure App Service に直接発行する方法
* ASP.NET MVC 5 テンプレートで CSS Bootstrap フレームワークを使用して、モバイル デバイス上での表示品質を高める方法
* iPhone や Android などの特定のモバイル ブラウザーに対応したモバイル専用のビューを作成する方法
* レスポンシブ ビュー (さまざまなデバイスの異なるブラウザーに対応するビュー) の作成方法

## <a name="set-up-the-development-environment"></a>開発環境を設定する
Azure SDK for .NET 2.5.1 以降をインストールして、開発環境を設定します。 

1. Azure SDK for .NET をインストールするには、次のリンクをクリックします。 Visual Studio 2013 をまだインストールしていない場合は、次のリンクをクリックするとインストールされます。 このチュートリアルには、Visual Studio 2013 が必要です。 [Azure SDK for Visual Studio 2013][AzureSDKVs2013]
2. Web Platform Installer のウィンドウで、 **[インストール]** をクリックし、インストールの手順を進めます。

モバイル ブラウザー エミュレーターも必要です。 次のいずれでも動作します。

* [Internet Explorer 11 F12 開発者ツール][EmulatorIE11]のブラウザー エミュレーター (このチュートリアルではすべてのモバイル ブラウザーのスクリーンショットでこのエミュレーターを使用しています)。 Windows Phone 8、Windows Phone 7、Apple iPad のエージェント文字列のプリセットが搭載されています。
* [Google Chrome DevTools][EmulatorChrome] のブラウザー エミュレーター。 多数の Android デバイスに加え、Apple iPhone、Apple iPad、Amazon Kindle Fire 用のプリセットが含まれています。 タッチイベントのエミュレーションにも対応します
* [Opera Mobile Emulator][EmulatorOpera]

次のトピック用に、C\# のソース コードを使用した Visual Studio プロジェクトが用意されています。

* [スタート プロジェクトのダウンロード][StarterProject]
* [完成したプロジェクトのダウンロード][CompletedProject]

## <a name="bkmk_DeployStarterProject"></a>Azure Web アプリにスタート プロジェクトをデプロイする
1. 会議一覧アプリケーションの[スタート プロジェクト][StarterProject]をダウンロードします。
2. Windows エクスプローラーで、ダウンロードした ZIP ファイルを右クリックし、 *[プロパティ]*をクリックします。
3. **[プロパティ]** ダイアログ ボックスで、**[ブロックの解除]** をクリックします  (ブロックを解除すると、Web からダウンロードした *.zip* ファイルを使おうとしたときに表示されるセキュリティに関する警告を回避できます)。
4. ZIP ファイルを右クリックし、 **[すべて展開]** をクリックしてファイルを解凍します。 
5. Visual Studio で *C#\Mvc5Mobile.sln* ファイルを開きます。
6. ソリューション エクスプローラで目的のプロジェクトを右クリックし、 **[発行]**をクリックします。
   
   ![][DeployClickPublish]
7. [Web の発行] で、 **[Microsoft Azure App Service]**をクリックします。
   
   ![][DeployClickWebSites]
8. Azure にまだログインしていない場合は、 **[アカウントの追加]**をクリックします。
   
   ![][DeploySignIn]
9. プロンプトに従って Azure アカウントにログインします。
10. サインインが完了すると、[App Service] ダイアログが表示されます。 **[新規]**をクリックします。
    
    ![][DeployNewWebsite]  
11. **[Web アプリ名]** フィールドで、一意のアプリ名のプレフィックスを指定します。 完全修飾 Web アプリ名は、*&lt;プレフィックス>*.azurewebsites.net となります。 **[リソース グループ]**でリソース グループを選択するか、新しいリソース グループ名を指定します。 **[新規]** をクリックして、新しい App Service プランを作成します。
    
    ![][DeploySiteSettings]
12. 新しい App Service プランを構成し、 **[OK]**をクリックします。 
    
    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7a.png)
13. [App Service の作成] ダイアログに戻り、 **[作成]**をクリックします。
    
    ![](./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7b.png) 
14. Azure リソースが作成されると、[Web の発行] ダイアログに新しいアプリの設定が入力されます。 **[発行]**をクリックします。
    
    ![][DeployPublishSite]
    
    Visual Studio で Azure Web アプリケーションへの発行が完了すると、デスクトップ ブラウザーが開き、ライブ Web アプリケーションが表示されます。
15. モバイル ブラウザー エミュレーターを起動し、会議アプリケーション (*<prefix>*.azurewebsites.net) の URL をエミュレーターにコピーし、右上のボタンをクリックして **[Browse by tag]**をクリックします。 Internet Explorer 11 を既定のブラウザーとして使用している場合は、`F12` キーを押してから `Ctrl+8` キーを押した後、ブラウザー プロファイルを **Windows Phone** に変更します。 下の図は、縦モードの *AllTags* ビューを示しています ( **[Browse by tag]**をクリックした後)。
    
    ![][AllTags]

> [!TIP]
> Visual Studio の中からでも MVC 5 アプリケーションをデバッグできますが、Web アプリケーションを Microsoft Azure に再度発行すると、モバイル ブラウザーまたはブラウザー エミュレーターからライブ Web アプリケーションを直接確認できます。
> 
> 

モバイル デバイス上でも読みやすい表示になっています。 ブートストラップ CSS フレームワークによって適用された視覚効果も一部表示されています。
**[ASP.NET]** リンクをクリックします。

![][SessionsByTagASP.NET]

ASP.NET タグ ビューは、画面に合わせてズームされています。この処理は、ブートストラップによって自動的に実行されます。 ただし、モバイル ブラウザーに合わせて、このビューをさらに調整することもできます。 たとえば、**[Date]** 列は読みにくくなっています。 このチュートリアルでは、後ほど、*AllTags* ビューをモバイル対応に変更します。

## <a name="bkmk_bootstrap"></a> ブートストラップ CSS フレームワーク
MVC 5 の新機能の 1 つに、ブートストラップの標準サポートがあります。 ブートストラップによって、さまざまなビューの表示品質が瞬時に向上することは先ほど確認したとおりです。 たとえば、ブラウザーの幅が小さくなったときに、上部のナビゲーション バーを自動的に折りたたむことができます。 デスクトップ ブラウザーで、ブラウザー ウィンドウのサイズを変更して、ナビゲーション バーの外観と操作性がどのように変わるかを確認してください。 これが、ブートストラップに組み込まれたレスポンシブ Web デザインです。

ブートストラップを使用しない場合の Web アプリの外観を表示するには、*App\_Start\\BundleConfig.cs* を開き、*bootstrap.js* と *bootstrap.css* を含む行をコメント アウトします。 次のコードは、変更後の `RegisterBundles` メソッドにある最後の&2; つのステートメントを示しています。

     bundles.Add(new ScriptBundle("~/bundles/bootstrap").Include(
              //"~/Scripts/bootstrap.js",
              "~/Scripts/respond.js"));

    bundles.Add(new StyleBundle("~/Content/css").Include(
              //"~/Content/bootstrap.css",
              "~/Content/site.css"));

`Ctrl+F5` キーを押してアプリケーションを実行します。

折りたたみ可能なナビゲーション バーが、通常の順不同一覧として表示されます。 **[Browse by tag]** を再度クリックし、**[ASP.NET]** をクリックします。
モバイル エミュレーター ビューが画面に合わせてズームされないため、テーブルの右側を表示するには横方向にスクロールする必要があります。

![][SessionsByTagASP.NETNoBootstrap]

変更を元に戻し、モバイル ブラウザーをリフレッシュして、モバイル対応画面が復元されたことを確認します。

ブートストラップは、ASP.NET MVC 5 固有の機能ではなく、あらゆる Web アプリケーションでこの機能を活用できます。 しかし ASP.NET MVC 5 プロジェクト テンプレートにはブートストラップが組み込まれているため、MVC 5 Web アプリケーションでは、この機能を既定で利用できます。

ブートストラップの詳細については、[ブートストラップ][BootstrapSite]のサイトを参照してください。

次のセクションでは、モバイル ブラウザー専用ビューを作成する方法について説明します。

## <a name="bkmk_overrideviews"></a> ビュー、レイアウト、および部分ビューをオーバーライドする
モバイル ブラウザー全般、個々のモバイル ブラウザー、または特定のブラウザーの (レイアウトと部分ビューを含む) 任意のビューをオーバーライドできます。 モバイル専用ビューを用意するには、ビュー ファイルをコピーして *.Mobile* をファイル名に追加します。 たとえば、モバイル *インデックス* ビューを作成するには、*Views\\Home\\Index.cshtml* をコピーして名前を *Views\\Home\\Index.Mobile.cshtml* に変更します。

このセクションでは、モバイル専用のレイアウト ファイルを作成します。

最初に、*Views\\Shared\\\_Layout.cshtml* を *Views\\Shared\\\_Layout.Mobile.cshtml* にコピーします。 *\_Layout.Mobile.cshtml* を開き、タイトルを **MVC5 Application** から **MVC5 Application (Mobile)** に変更します。

ナビゲーション バーの各 `Html.ActionLink` を呼び出しで、各 *ActionLink*リンクの "Browse by" を削除します。 次のコードは、モバイル レイアウト ファイルの完成した `<ul class="nav navbar-nav">` タグです。

    <ul class="nav navbar-nav">
        <li>@Html.ActionLink("Home", "Index", "Home")</li>
        <li>@Html.ActionLink("Date", "AllDates", "Home")</li>
        <li>@Html.ActionLink("Speaker", "AllSpeakers", "Home")</li>
        <li>@Html.ActionLink("Tag", "AllTags", "Home")</li>
    </ul>

*Views\\Home\\AllTags.cshtml* ファイルを *Views\\Home\\AllTags.Mobile.cshtml* にコピーします。 新しいファイルを開き、次のように `<h2>` 要素を「Tags」から「Tags (M)」に変更します。

    <h2>Tags (M)</h2>

デスクトップ ブラウザー、および、モバイル ブラウザー エミュレーターを使用してタグ ページに移動します。 モバイル ブラウザー エミュレーターでは、先ほど変更した&2; か所 (*\_Layout.Mobile.cshtml* と *AllTags.Mobile.cshtml* の各タイトル) が変更されていることがわかります。

![][AllTagsMobile_LayoutMobile]

これに対して、デスクトップでの表示は変更されていません (*\_Layout.cshtml* と *AllTags.cshtml* のタイトル)。

![][AllTagsMobile_LayoutMobileDesktop]

## <a name="bkmk_browserviews"></a> ブラウザー専用のビューを作成する
モバイル専用のビューやデスクトップ専用のビューに加え、個別のブラウザーに対してビューを作成できます。 たとえば、iPhone ブラウザーや Android ブラウザー専用のビューを作成できます。 このセクションでは、iPhone ブラウザーと iPhone バージョンの *AllTags* ビュー用のレイアウトを作成します。

*Global.asax* ファイルを開き、`Application_Start` メソッドの最終行として次のコードを追加します。

    DisplayModeProvider.Instance.Modes.Insert(0, new DefaultDisplayMode("iPhone")
    {
        ContextCondition = (context => context.GetOverriddenUserAgent().IndexOf
            ("iPhone", StringComparison.OrdinalIgnoreCase) >= 0)
    });

このコードでは、"iPhone" という表示モードを定義し、受信された各要求をその定義に対して照合します。 受信された要求が定義した条件に一致する場合 (つまり、ユーザー エージェントに "iPhone" という文字列が含まれている場合)、"iPhone" というサフィックスが含まれる名前のビューが ASP.NET MVC によって検索されます。

> [!NOTE]
> iPhone や Android など、モバイル ブラウザー専用の表示モードを追加する場合、最初の引数を `0` に設定して (リストの一番上に挿入)、ブラウザー専用のモードがモバイル テンプレート (*.Mobile.cshtml) よりも優先されるようにします。 逆にモバイル テンプレートをリストの一番上に配置すると、目的の表示モードに優先して選択されます (最初に一致したエントリが選択されますが、モバイル テンプレートはすべてのモバイル ブラウザーで一致します)。 
> 
> 

コードで、`DefaultDisplayMode` を右クリックし、**[解決]**、`using System.Web.WebPages;` の順にクリックします。 `DisplayModeProvider` 型と `DefaultDisplayMode` 型が定義されている `System.Web.WebPages` 名前空間に参照が追加されます。

![][ResolveDefaultDisplayMode]

別の方法として、単純にファイルの `using` セクションに、次の行を手動で追加することもできます。

    using System.Web.WebPages;

変更を保存します。 *Views\\Shared\\\_Layout.Mobile.cshtml* ファイルを *Views\\Shared\\\_Layout.iPhone.cshtml* にコピーします。 新しいファイルを開き、タイトルを `MVC5 Application (Mobile)` から `MVC5 Application (iPhone)` に変更します。

*Views\\Home\\AllTags.Mobile.cshtml* ファイルを *Views\\Home\\AllTags.iPhone.cshtml* にコピーします。 新しいファイルで、 `<h2>` 要素を "Tags (M)" から "Tags (iPhone)" に変更します。

アプリケーションを実行します。 モバイル ブラウザー エミュレーターを実行し、ユーザー エージェントが "iPhone" に設定されていることを確認して、 *AllTags* ビューにアクセスします。 Internet Explorer 11 F12 開発者ツールのエミュレーターを使用している場合は、エミュレーションを次のとおりに構成します。

* [ブラウザー プロファイル] = **[Windows Phone]**
* [ユーザー エージェント文字列] = **カスタム**
* [カスタム文字列] = 「 **Apple-iPhone5C1/1001.525**

次のスクリーンショットは、Internet Explorer 11 F12 開発者ツールのエミュレーターで、カスタムのユーザー エージェント文字列 (iPhone 5C のユーザー エージェント文字列) を使用して *AllTags* ビューを描画した画面を示しています。

![][AllTagsIPhone_LayoutIPhone]

モバイル ブラウザーで **[Speakers]** リンクをタップします。 モバイル ビュー (*AllSpeakers.Mobile.cshtml*) がないため、既定のスピーカー ビュー (*AllSpeakers.cshtml*) がモバイル レイアウト ビュー (*\_Layout.Mobile.cshtml*) を使用して描画されます。 下図に示すように、**MVC5 Application (Mobile)** が *\_Layout.Mobile.cshtml* に定義されています。

![][AllSpeakers_LayoutMobile]

*Views\\\_ViewStart.cshtml* ファイルで次のように `RequireConsistentDisplayMode` を `true` に設定すると、モバイル レイアウト内で既定の (非モバイル) ビューの描画をグローバルに無効化できます。

    @{
        Layout = "~/Views/Shared/_Layout.cshtml";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = true;
    }

`RequireConsistentDisplayMode` が `true` に設定されていると、モバイル レイアウト (*\_Layout.Mobile.cshtml*) はモバイル ビューだけに使用されます (つまり、ビュー ファイルが ***ViewName**.Mobile.cshtml* という形式の場合です)。 モバイル レイアウトが非モバイル ビューでうまく動作しない場合は、`RequireConsistentDisplayMode` を `true` に設定します。 次のスクリーンショットは、`RequireConsistentDisplayMode` が `true` に設定されている場合の *[Speakers]* ページの表示を示しています (文字列 "(Mobile)" は上部のナビゲーション バーに表示されません)。

![][AllSpeakers_LayoutMobileOverridden]

ビュー ファイルで `RequireConsistentDisplayMode` を `false` に設定すると、特定のビューの一貫表示モードを無効化できます。 次のマークアップは、*Views\\Home\\AllSpeakers.cshtml* ファイルで `RequireConsistentDisplayMode` を `false` に設定します。

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All speakers";
        DisplayModeProvider.Instance.RequireConsistentDisplayMode = false;
    }

このセクションでは、モバイル レイアウトとビューの作成方法および iPhone などの特定のデバイス専用のレイアウトとビューの作成方法を説明しました。
しかし、ブートストラップ CSS フレームワークの最大の利点は、レスポンシブ レイアウトの使用により、単一のスタイルシートをデスクトップ、携帯電話、タブレットの各ブラウザーに適用し、一貫した外観と操作性を実現できることです。 次のセクションでは、ブートストラップを利用したモバイル対応ビューの作成方法を説明します。

## <a name="bkmk_Improvespeakerslist"></a> スピーカー一覧を強化する
いま見たように、 *Speakers* ビューは読み取れますが、リンクが小さく、モバイル デバイスではタップが困難です。 このセクションでは、 *AllSpeakers* ビューをモバイル対応に設定し、大きくてタップしやすいリンクが表示され、スピーカーをすばやく見つけることのできる検索ボックスを表示します。

ブートストラップの [リンクされたリスト グループ][linked list group]のスタイリングを使用して、*Speakers* ビューを強化します。 *Views\\Home\\AllSpeakers.cshtml* で、Razor ファイルの内容を次のコードに置き換えます。

     @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, "SessionsBySpeaker", new { speaker }, new { @class = "list-group-item" })
        }
    </div>

`<div>` タグ内の `class="list-group"` 属性によって、ブートストラップのリストのスタイリングが適用され、`class="input-group-item"` 属性によって、ブートストラップのリスト項目のスタイリングが各リンクに適用されます。

モバイル ブラウザーの表示を更新します。 更新されたビューは次のようになります。

![][AllSpeakersFixed]

ブートストラップの[リンクされたリスト グループ][linked list group]のスタイリングを使用すると、各リンクのボックス全体がクリック可能になり、ユーザー エクスペリエンスが大幅に向上します。 デスクトップ ビューに切り替えると、外観と操作性が一貫していることがわかります。

![][AllSpeakersFixedDesktop]

モバイル ブラウザー ビューは強化されましたが、スピーカーの長い一覧をスクロールするのは困難です。 ブートストラップは、検索フィルターを標準で提供していませんが、数行のコードによって追加できます。 まずビューに検索ボックスを追加した後、JavaScript コードを関連付けてフィルター機能を設定します。 次に示すように、*Views\\Home\\AllSpeakers.cshtml* で、\<form\> タグを \<h2\> タグの直後に追加します。

    @model IEnumerable<string>

    @{
        ViewBag.Title = "All Speakers";
    }

    <h2>Speakers</h2>

    <form class="input-group">
        <span class="input-group-addon"><span class="glyphicon glyphicon-search"></span></span>
        <input type="text" class="form-control" placeholder="Search speaker">
    </form>
    <br />
    <div class="list-group">
        @foreach (var speaker in Model)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class = "list-group-item" })
        }
    </div>

`<form>` タグと `<input>` タグの両方にブートストラップのスタイルが適用されていることに注意してください。 `<span>` 要素によって、ブートストラップの [glyphicon][glyphicon] が検索ボックスに追加されます。

*Scripts* フォルダーで、*filter.js* という JavaScript ファイルを追加します。 ファイルを開き、次のコードを貼り付けます。

    $(function () {

        // reset the search form when the page loads
        $("form").each(function () {
            this.reset();
        });

        // wire up the events to the <input> element for search/filter
        $("input").bind("keyup change", function () {
            var searchtxt = this.value.toLowerCase();
            var items = $(".list-group-item");

            // show all speakers that begin with the typed text and hide others
            for (var i = 0; i < items.length; i++) {
                var val = items[i].text.toLowerCase();
                val = val.substring(0, searchtxt.length);
                if (val == searchtxt) {
                    $(items[i]).show();
                }
                else {
                    $(items[i]).hide();
                }
            }
        });
    });

さらに、登録済みのバンドルに filter.js を含める必要があります。 *App\_Start\\BundleConfig.cs* を開き、最初のバンドルを変更します。 次のように、最初の (**jquery** バンドルの) `bundles.Add` ステートメントを変更して、*Scripts\\filter.js* を含めます。

     bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                "~/Scripts/jquery-{version}.js",
                "~/Scripts/filter.js"));

**jquery** バンドルは、既定の *\_Layout* ビューによって既に描画されています。 後で、同じ JavaScript コードを利用して、フィルター機能を他のリスト ビューに適用できます。

モバイル ブラウザーを情報に更新し、 *AllSpeakers* ビューに移動します。 検索ボックスに、「sc」と入力します。 入力した検索文字列に従って、スピーカー一覧がフィルタリングされます。

![][AllSpeakersFixedSearchBySC]

## <a name="bkmk_improvetags"></a> タグ一覧を強化する
*Speakers* ビューと同様に、*Tags* ビューも読み取れますが、リンクが小さく、モバイル デバイスではタップが困難です。 *Tags* ビューも *Speakers* ビューと同じように修正できます。上記のコードの変更を使用している場合、*Views\\Home\\AllTags.cshtml* で次の `Html.ActionLink` メソッド構文を使用することを除き、手順は同じです。

    @Html.ActionLink(tag, 
                     "SessionsByTag", 
                     new { tag }, 
                     new { @class = "list-group-item" })

デスクトップ ブラウザーを更新すると、次のように表示されます。

![][AllTagsFixedDesktop]

モバイル ブラウザーを更新すると、次のように表示されます。 

![][AllTagsFixed]

> [!NOTE]
> モバイル ブラウザーで元の一覧書式設定が残り、ブートストラップのスタイリングが正常に機能していないように見えますが、これは先ほどモバイル専用のビューを作成したためです。 ここではブートストラップ CSS フレームワークを使用してレスポンシブ Web デザインを作成するため、これらのモバイル専用のビューとモバイル専用のレイアウト ビューを削除してください。 その後、モバイル ブラウザーを更新すると、ブートストラップのスタイリングが表示されます。
> 
> 

## <a name="bkmk_improvedates"></a> 日付一覧を強化する
*Speakers* ビューおよび *Tags* ビューと同じように *Dates* ビューもモバイル デバイスで使いやすいように強化できます。上記のコードの変更を使用している場合、*Views\\Home\\AllDates.cshtml* で `Html.ActionLink` メソッドの構文を使用することを除き、手順は同じです。

    @Html.ActionLink(date.ToString("ddd, MMM dd, h:mm tt"), 
                     "SessionsByDate", 
                     new { date }, 
                     new { @class = "list-group-item" })

モバイル ブラウザー ビューを更新すると、次のように表示されます。

![][AllDatesFixed]

*Dates* ビューはさらに、date-time 値を date ごとに整理して表示品質を高めることができます。 これには、ブートストラップの[パネル][panels] スタイリングを使用します。 *Views\\Home\\AllDates.cshtml* ファイルの内容を、次のコードに置き換えます。

    @model IEnumerable<DateTime>

    @{
        ViewBag.Title = "All Dates";
    }

    <h2>Dates</h2>

    @foreach (var dategroup in Model.GroupBy(x=>x.Date))
    {
        <div class="panel panel-primary">
            <div class="panel-heading">
                @dategroup.Key.ToString("ddd, MMM dd")
            </div>
            <div class="panel-body list-group">
                @foreach (var date in dategroup)
                {
                    @Html.ActionLink(date.ToString("h:mm tt"), 
                                     "SessionsByDate", 
                                     new { date }, 
                                     new { @class = "list-group-item" })
                }
            </div>
        </div>
    }

このコードでは、一覧の日付ごとに別個の `<div class="panel panel-primary">` タグを作成し、これまでと同じそれぞれのリンクに対して[リンクされたリスト グループ][linked list group]を使用します。 このコードを実行すると、モバイル ブラウザーの表示は次のようになります。

![][AllDatesFixed2]

デスクトップ ブラウザーに切り替えます。 モバイル ブラウザーと一貫性を保った画面が表示されます。

![][AllDatesFixed2Desktop]

## <a name="bkmk_improvesessionstable"></a> SessionsTable ビューを強化する
このセクションでは、 *SessionsTable* ビューがより適切にモバイルに対応するように調整します。 変更箇所は、これまでに作成した他のビューよりも広範囲にわたります。

モバイル ブラウザーで **[Tags]** をタップし、検索ボックスに「`asp`」と入力します。

![][AllTagsFixedSearchByASP]

**[ASP.NET]** リンクをタップします。

![][SessionsTableTagASP.NET]

ご覧のように、現在、デスクトップ ブラウザーで適切に表示できるように設計された表形式で表示されています。 ただし、モバイル ブラウザーで読み取るのは少し困難です。 これを修正するために、*Views\\Home\\SessionsTable.cshtml* を開き、ファイルの内容を次のコードに置き換えます。

    @model IEnumerable<Mvc5Mobile.Models.Session>

    <h2>@ViewBag.Title</h2>

    <div class="container">
        <div class="row">
            @foreach (var session in Model)
            {
                <div class="col-md-4">
                    <div class="list-group">
                        @Html.ActionLink(session.Title, 
                                         "SessionByCode", 
                                         new { session.Code }, 
                                         new { @class="list-group-item active" })
                        <div class="list-group-item">
                            <div class="list-group-item-text">
                                @Html.Partial("_SpeakersLinks", session)
                            </div>
                            <div class="list-group-item-info">
                                @session.DateText
                            </div>
                            <div class="list-group-item-info small hidden-xs">
                                @Html.Partial("_TagsLinks", session)
                            </div>
                        </div>
                    </div>
                </div>
            }
        </div>
    </div>

このコードでは、次の 3 つの処理を行います。

* ブートストラップの[カスタムのリンクされたリスト グループ][custom linked list group]を使用して、セッション情報を縦&1; 列に並べ、すべての情報をモバイル ブラウザーで読み取れるようにします (list-group-item-text などのクラスを使用します)。
* レイアウトに[グリッド システム][grid system]を適用して、デスクトップ ブラウザーではセッション項目を横並びに表示し、モバイル ブラウザーでは縦に表示します (col-md-4 クラスを使用します)。
* [レスポンシブ ユーティリティ][responsive utilities]を使用して、モバイル ブラウザーで表示したときに、セッション タグを非表示にします (hidden-xs クラスを使用します)。

タイトル リンクをタップして、それぞれのセッションに移動することもできます。 次の図にはコードの変更が反映されています。

![][FixedSessionsByTag]

適用したブートストラップのグリッド システムによって、モバイル ブラウザーのセッションが自動的に縦並びに配置されます。 また、タグが非表示になります。 デスクトップ ブラウザーに切り替えます。

![][SessionsTableFixedTagASP.NETDesktop]

デスクトップ ブラウザーでは、タグが表示されています。 また、適用したブートストラップのグリッド システムによって、セッション項目が&2; 列に表示されています。 ブラウザーを広げると、配置が&3; 列表示に変わります。

## <a name="bkmk_improvesessionbycode"></a> SessionByCode ビューを強化する
最後に、 *SessionByCode* ビューをモバイル対応に修正します。

モバイル ブラウザーで **[Tags]** をタップし、検索ボックスに「`asp`」と入力します。

![][AllTagsFixedSearchByASP]

**[ASP.NET]** リンクをタップします。 ASP.NET タグのセッションが表示されます。

![][FixedSessionsByTag]

**[Building a Single Page Application with ASP.NET and AngularJS]** リンクをタップします。

![][SessionByCode3-644]

既定のデスクトップ ビューでも問題はありませんが、ブートストラップの GUI コンポーネントを使用して簡単に表示品質を高めることができます。

*Views\\Home\\SessionByCode.cshtml* を開き、内容を次のマークアップで置き換えます。

    @model Mvc5Mobile.Models.Session

    @{
        ViewBag.Title = "Session details";
    }
    <h3>@Model.Title (@Model.Code)</h3>
    <p>
        <strong>@Model.DateText</strong> in <strong>@Model.Room</strong>
    </p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Speakers
        </div>
        @foreach (var speaker in Model.Speakers)
        {
            @Html.ActionLink(speaker, 
                             "SessionsBySpeaker", 
                             new { speaker }, 
                             new { @class="panel-body" })
        }
    </div>

    <p>@Model.Abstract</p>

    <div class="panel panel-primary">
        <div class="panel-heading">
            Tags
        </div>
        @foreach (var tag in Model.Tags)
        {
            @Html.ActionLink(tag, 
                             "SessionsByTag", 
                             new { tag }, 
                             new { @class = "panel-body" })
        }
    </div>

新しいマークアップでは、ブートストラップのパネル スタイリングによりモバイル ビューが強化されています。 

モバイル ブラウザーの表示を更新します。 次の図には行ったコードの変更が反映されています。

![][SessionByCodeFixed3-644]

## <a name="wrap-up-and-review"></a>まとめ
このチュートリアルでは、ASP.NET MVC 5 を使用してモバイル対応の Web アプリケーションを開発する方法を学習しました。 学習した内容は次のとおりです。

* ASP.NET MVC 5 アプリケーションを App Service の Web アプリケーションに展開する
* ブートストラップを使用した MVC 5 アプリケーションでのレスポンシブな Web レイアウトの作成
* ビュー全般や個別のビューのレイアウト、ビュー、および部分ビューのオーバーライド
* `RequireConsistentDisplayMode` プロパティを使用した、レイアウトや部分的なオーバーライドの実行の制御
* iPhone ブラウザーなど、特定のブラウザー専用のビューの作成
* Razor コードでのブートストラップのスタイリングの適用

## <a name="see-also"></a>関連項目
* [レスポンシブ Web デザインの&9; つの基本原則](http://blog.froont.com/9-basic-principles-of-responsive-web-design/)
* [ブートストラップ][BootstrapSite]
* [ブートストラップの公式ブログ][Official Bootstrap Blog]
* [Tutorial Republic による Twitter Bootstrap Tutorial][Twitter Bootstrap Tutorial from Tutorial Republic]
* [The Bootstrap Playground][The Bootstrap Playground]
* [W3C 勧告: モバイル Web アプリケーションのベスト プラクティス][W3C Recommendation Mobile Web Application Best Practices]
* [W3C のメディア クエリに関する勧告候補][W3C Candidate Recommendation for media queries]

## <a name="whats-changed"></a>変更内容
* Websites から App Service への変更ガイドについては、「 [Azure App Service と既存の Azure サービス](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- Internal Links -->
[Deploy the starter project to an Azure web app]: #bkmk_DeployStarterProject
[Bootstrap CSS Framework]: #bkmk_bootstrap
[Override the Views, Layouts, and Partial Views]: #bkmk_overrideviews
[Create Browser-Specific Views]:#bkmk_browserviews
[Improve the Speakers List]: #bkmk_Improvespeakerslist
[Improve the Tags List]: #bkmk_improvetags
[Improve the Dates List]: #bkmk_improvedates
[Improve the SessionsTable View]: #bkmk_improvesessionstable
[Improve the SessionByCode View]: #bkmk_improvesessionbycode

<!-- External Links -->
[Visual Studio Express 2013]: http://www.visualstudio.com/downloads/download-visual-studio-vs#d-express-web
[Visual Studio 2015]: https://www.visualstudio.com/downloads/download-visual-studio-vs
[AzureSDKVs2013]: http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409
[Fiddler]: http://www.fiddler2.com/fiddler2/
[EmulatorIE11]: http://msdn.microsoft.com/library/ie/dn255001.aspx
[EmulatorChrome]: https://developers.google.com/chrome-developer-tools/docs/mobile-emulation
[EmulatorOpera]: http://www.opera.com/developer/tools/mobile/
[StarterProject]: http://go.microsoft.com/fwlink/?LinkID=398780&clcid=0x409
[CompletedProject]: http://go.microsoft.com/fwlink/?LinkID=398781&clcid=0x409
[BootstrapSite]: http://getbootstrap.com/
[WebPIAzureSdk23NetVS13]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/WebPIAzureSdk23NetVS13.png
[linked list group]: http://getbootstrap.com/components/#list-group-linked
[glyphicon]: http://getbootstrap.com/components/#glyphicons
[panels]: http://getbootstrap.com/components/#panels
[custom linked list group]: http://getbootstrap.com/components/#list-group-custom-content
[grid system]: http://getbootstrap.com/css/#grid
[responsive utilities]: http://getbootstrap.com/css/#responsive-utilities
[Official Bootstrap Blog]: http://blog.getbootstrap.com/
[Twitter Bootstrap Tutorial from Tutorial Republic]: http://www.tutorialrepublic.com/twitter-bootstrap-tutorial/
[The Bootstrap Playground]: http://www.bootply.com/
[W3C Recommendation Mobile Web Application Best Practices]: http://www.w3.org/TR/mwabp/
[W3C Candidate Recommendation for media queries]: http://www.w3.org/TR/css3-mediaqueries/

<!-- Images -->
[DeployClickPublish]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-1.png
[DeployClickWebSites]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-2.png
[DeploySignIn]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-3.png
[DeployUsername]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-4.png
[DeployPassword]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-5.png
[DeployNewWebsite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-6.png
[DeploySiteSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-7.png
[DeployPublishSite]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/deploy-to-azure-website-8.png
[MobileHomePage]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/mobile-home-page.png
[FixedSessionsByTag]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-Fixed.png
[AllTags]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags.png
[SessionsByTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET.png
[SessionsByTagASP.NETNoBootstrap]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsByTag-ASP.NET-NoBootstrap.png
[AllTagsMobile_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile.png
[AllTagsMobile_LayoutMobileDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsMobile-_LayoutMobile-Desktop.png
[ResolveDefaultDisplayMode]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/Resolve-DefaultDisplayMode.png
[AllTagsIPhone_LayoutIPhone]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTagsIPhone-_LayoutIPhone.png
[AllSpeakers_LayoutMobile]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile.png
[AllSpeakers_LayoutMobileOverridden]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-_LayoutMobile-Overridden.png
[AllSpeakersFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed.png
[AllSpeakersFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-Desktop.png
[AllSpeakersFixedSearchBySC]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllSpeakers-Fixed-SearchBySC.png
[AllTagsFixedDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-Desktop.png 
[AllTagsFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed.png
[AllDatesFixed]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed.png
[AllDatesFixed2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2.png
[AllDatesFixed2Desktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllDates-Fixed2-Desktop.png
[AllTagsFixedSearchByASP]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/AllTags-Fixed-SearchByASP.png
[SessionsTableTagASP.NET]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Tag-ASP.NET.png
[SessionsTableFixedTagASP.NETDesktop]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionsTable-Fixed-Tag-ASP.NET-Desktop.png
[SessionByCode3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-3-644.png
[SessionByCodeFixed3-644]: ./media/web-sites-dotnet-deploy-aspnet-mvc-mobile-app/SessionByCode-Fixed-3-644.png


