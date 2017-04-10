---
title: "Azure クラウド サービスと Azure CDN との統合 | Microsoft Docs"
description: "統合 Azure CDN エンドポイントからコンテンツを提供するクラウド サービスをデプロイする方法について説明します"
services: cdn, cloud-services
documentationcenter: .net
author: zhangmanling
manager: erikre
editor: tysonn
ms.assetid: b3c0108f-9ec5-43a8-8fd0-40eafbd32637
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
translationtype: Human Translation
ms.sourcegitcommit: 06bd0112eab46f3347dfb039a99641a37c2b0197
ms.openlocfilehash: 6355c98f5c50d03b54cb4977bff4e51b8dfa669f


---
# <a name="a-nameintroa-integrate-a-cloud-service-with-azure-cdn"></a><a name="intro"></a> クラウド サービスと Azure CDN との統合
クラウド サービスを Azure CDN に統合すると、クラウド サービスの場所からコンテンツを配信できます。 この方法には、次の利点があります。

* クラウド サービスのプロジェクト ディレクトリへの画像、スクリプト、およびスタイルシートのデプロイと更新を簡単に行うことができる。
* クラウド サービスの NuGet パッケージ (たとえば、jQuery バージョン、Bootstrap バージョン) を簡単にアップグレードできる。
* Web アプリケーションと CDN によって配信されるコンテンツをすべて同じ Visual Studio インターフェイスから管理できる。
* Web アプリケーションと CDN によって配信されるコンテンツのデプロイメント ワークフローを一元化できる。
* ASP.NET のバンドルと縮小を Azure CDN と統合できる。

## <a name="what-you-will-learn"></a>学習内容
このチュートリアルで学習する内容は次のとおりです。

* [Azure CDN エンドポイントをクラウド サービスと統合して、Azure CDN から Web ページの静的コンテンツを配信する](#deploy)
* [クラウド サービスの静的コンテンツのキャッシュ設定を構成する](#caching)
* [Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)
* [Visual Studio のスクリプトのデバッグ エクスペリエンスを維持しながらバンドルされたコンテンツおよび縮小されたコンテンツを配信する](#bundling)
* [Azure CDN がオフラインのときのスクリプトおよび CSS のフォールバックを構成する](#fallback)

## <a name="what-you-will-build"></a>学習内容
既定の ASP.NET MVC テンプレートを使用してクラウド サービス Web ロールをデプロイし、統合された Azure CDN からコンテンツ (たとえば、画像、コントローラー アクションの結果、既定の JavaScript ファイルおよび CSS ファイル) を配信するコードを追加します。さらに、CDN がオフラインになった場合に提供されるバンドルのフォールバック メカニズムを構成するコードを作成します。

## <a name="what-you-will-need"></a>前提条件
このチュートリアルの前提条件は次のとおりです。

* アクティブな [Microsoft Azure アカウント](/account/)
* Visual Studio 2015 と [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。
> 
> * 無料で [Azure アカウントを開く](https://azure.microsoft.com/pricing/free-trial/)ことができます。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Websites など無料の Azure サービスをご利用になれます。
> * [MSDN サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) こともできます - MSDN サブスクリプションにより、有料の Azure のサービスを使用できるクレジットが毎月与えられます。
> 
> 

<a name="deploy"></a>

## <a name="deploy-a-cloud-service"></a>クラウド サービスのデプロイ
このセクションでは、Visual Studio 2015 の既定の ASP.NET MVC アプリケーション テンプレートをクラウド サービス Web ロールにデプロイした後、これを新しい CDN エンドポイントと統合します。 次の手順に従ってください。

1. Visual Studio 2015 で、メニュー バーから **[ファイル]、[新規]、[プロジェクト]、[クラウド]、[Azure クラウド サービス]** の順に選択して新しい Azure クラウド サービスを作成します。 クラウド サービスに名前を付けて、 **[OK]**をクリックします。
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)
2. **[ASP.NET Web ロール]** を選択し、**>** ボタンをクリックします。 [OK] をクリックします。
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)
3. **[MVC]** を選択し、**[OK]** をクリックします。
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)
4. この Web ロールを Azure クラウド サービスに発行します。 クラウド サービス プロジェクトを右クリックし、 **[発行]**を選択します。
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)
5. まだ Microsoft Azure にサインインしていない場合は、**[アカウントの追加...]** ドロップダウンをクリックして、**[アカウントの追加]** メニュー項目をクリックします。
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)
6. サインイン ページで、Azure アカウントをアクティブ化する際に使用した Microsoft アカウントでサインインします。
7. サインインしたら、[ **次へ**] をクリックします。
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)
8. この段階でまだクラウド サービス アカウントまたはストレージ アカウントを作成していない場合は、Visual Studio の指示に従ってこれらのアカウントを作成します。 **[クラウド サービスとアカウントの作成]** ダイアログで、目的のサービスの名前を入力し、目的のリージョンを選択します。 **[作成]**をクリックします。
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)
9. 発行の設定ページで、構成を確認し、 **[発行]**をクリックします。
   
    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)
   
   > [!NOTE]
   > クラウド サービスの発行には時間がかかります。 [すべてのロールの Web のデプロイを有効にする] オプションを使用すると、Web ロールに対する高速な (ただし一時的な) 更新を提供することでクラウド サービスのデバッグが高速化されます。 このオプションの詳細については、「 [Azure Tools を使用したクラウド サービスの発行](http://msdn.microsoft.com/library/ff683672.aspx)」を参照してください。
   > 
   > 
   
    **[Microsoft Azure のアクティビティ ログ]** に発行状態が **[完了]** と表示されたら、このクラウド サービスと統合される CDN エンドポイントを作成します。
   
   > [!WARNING]
   > 発行後、デプロイされたクラウド サービスにエラー画面が表示された場合、デプロイしたクラウド サービスが [.NET 4.5.2 を含まないゲスト OS](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates)を使用している可能性があります。  この問題を回避するには、 [.NET 4.5.2 をスタートアップ タスクとしてデプロイ](../cloud-services/cloud-services-dotnet-install-dotnet.md)します。
   > 
   > 

## <a name="create-a-new-cdn-profile"></a>新しい CDN プロファイルを作成する
CDN プロファイルは、CDN エンドポイントのコレクションです。  各プロファイルには、1 つ以上の CDN エンドポイントが含まれます。  複数のプロファイルを使って、インターネット ドメイン、Web アプリケーション、またはその他の一部の基準別に CDN エンドポイントを整理する必要が生じる場合があります。

> [!TIP]
> このチュートリアルに使用する CDN プロファイルが既にある場合は、 [[新しい CDN エンドポイントの作成]](#create-a-new-cdn-endpoint)に進みます。
> 
> 

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>[新しい CDN エンドポイントの作成]
**ストレージ アカウントに対する新しい CDN エンドポイントを作成するには**

1. [Microsoft Azure 管理ポータル](https://portal.azure.com)で CDN プロファイルに移動します。  これは、前の手順でダッシュボードにピン留めしている可能性があります。  ピン留めしていない場合は、**[参照]**、**[CDN プロファイル]** の順にクリックし、エンドポイントの追加先のプロファイルをクリックします。
   
    CDN プロファイル ブレードが表示されます。
   
    ![CDN プロファイル][cdn-profile-settings]
2. **[エンドポイントの追加]** ボタンをクリックします。
   
    ![[エンドポイントの追加] ボタン][cdn-new-endpoint-button]
   
    **[エンドポイントの追加]** ブレードが表示されます。
   
    ![[エンドポイントの追加] ブレード][cdn-add-endpoint]
3. この CDN エンドポイントの **名前** を入力します。  この名前は、ドメイン `<EndpointName>.azureedge.net`でキャッシュされたリソースにアクセスする際に使用します。
4. **[配信元の種類]** ドロップダウンで、 *[クラウド サービス]*を選択します。  
5. **[配信元ホスト名]** ドロップダウンで、クラウド サービスを選択します。
6. **[元のパス]**、**[配信元のホスト ヘッダー]**、および **[プロトコル/配信元ポート]** の既定値はそのまま使用します。  少なくとも&1; つのプロトコル (HTTP または HTTPS) を指定する必要があります。
7. **[追加]** ボタンをクリックして、新しいエンドポイントを作成します。
8. エンドポイントが作成されると、プロファイルのエンドポイントの一覧に表示されます。 一覧には、キャッシュされたコンテンツへのアクセスに使用する URL と元のドメインが表示されます。
   
    ![CDN エンドポイント][cdn-endpoint-success]
   
   > [!NOTE]
   > エンドポイントはすぐには使用できません。  登録が CDN ネットワークに反映されるまでに最大で 90 分かかる場合があります。 CDN ドメイン名を直ちに使用しようとするユーザーは、CDN を経由してコンテンツを取得できるようになるまでは状態コード 404 を受け取る場合があります。
   > 
   > 

## <a name="test-the-cdn-endpoint"></a>CDN エンドポイントをテストする
発行状態が **[完了]** と表示されたら、ブラウザー ウィンドウを開き、**http://<cdnName>*.azureedge.net/Content/bootstrap.css** に移動します。 この設定では、次の URL を使用します。

    http://camservice.azureedge.net/Content/bootstrap.css

この URL は、CDN エンドポイントの次のオリジン URL に対応します。

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

**http://*&lt;cdnName>*.azureedge.net/Content/bootstrap.css** に移動すると、発行された Web アプリケーションから bootstrap.css をダウンロードまたは開くよう求められます。

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

同様に、CDN エンドポイントから、**http://*&lt;サービス名>*.cloudapp.net/** のパブリックにアクセスできる任意の URL にアクセスできます。 次に例を示します。

* /Script パスの .js ファイル
* /Content パスの任意のコンテンツ ファイル
* 任意のコントローラー/アクション
* CDN エンドポイントでクエリ文字列が有効になっている場合、クエリ文字列を含む任意の URL

実際、上記の構成の場合、**http://*&lt;cdnName>*.azureedge.net/** からのクラウド サービス全体をホストできます。**http://camservice.azureedge.net/** に移動すると、Home/Index からのアクション結果が得られます。

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

ただし、これが、Azure CDN を介してクラウド サービス全体を提供するのが常に適切なやり方 (または一般的に適切なやり方) であることにはなりません。 いくつかの注意点があります。

* 現時点で、Azure CDN ではプライベートなコンテンツを配信できないため、この方法ではサイト全体をパブリックにする必要があります。
* 予定されたメンテナンスであろうとユーザー エラーであろうと、CDN エンドポイントが何かの理由でオフラインになると、顧客をオリジン URL **http://*&lt;serviceName>*.cloudapp.net/** にリダイレクトできる場合を除き、クラウド サービス全体がオフラインになります。
* カスタムの Cache-Control 設定 (「[クラウド サービスの静的ファイルのキャッシュ オプションを構成する](#caching)」を参照してください) を使用した場合でも、CDN エンドポイントによって高度に動的なコンテンツのパフォーマンスが向上するわけではありません。 上に示すように CDN エンドポイントからホーム ページを読み込もうとした場合、非常に単純な既定のホーム ページを初めて読み込むときに 5 秒以上かかります。 このページに毎分更新する必要がある動的コンテンツが含まれていたとしたら、クライアント エクスペリエンスはどうなるでしょうか。 動的コンテンツを CDN エンドポイントから配信するにはキャッシュの有効期限が短く設定されている必要があります。これは、CDN エンドポイントで頻繁にキャッシュ ミスが発生することになります。 その結果、クラウド サービスのパフォーマンスが低下し、CDN の目的が果たせなくなります。

また、クラウド サービスで Azure CDN から配信するコンテンツをケースバイケースで決定する方法もあります。 CDN エンドポイントから個々のコンテンツ ファイルにアクセスする方法については既に説明しました。 CDN エンドポイントを介して特定のコントローラー アクションを配信する方法については、「 [Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)」で説明します。

<a name="caching"></a>

## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>クラウド サービスの静的ファイルのキャッシュ オプションを構成する
Azure CDN 統合をクラウド サービスに組み込むと、CDN エンドポイントで静的コンテンツをどのようにキャッシュするかを指定できます。 そのためには、Web ロール プロジェクト (たとえば、WebRole1) の *Web.config* を開き、`<staticContent>` 要素を `<system.webServer>` に追加します。 次の XML では、3 日間で有効期限が切れるキャッシュを追加しています。  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

この操作を実行すると、クラウド サービス内のすべての静的ファイルは、CDN キャッシュ内で同じ規則に従います。 キャッシュ設定をより細かく制御するには、 *Web.config* ファイルをフォルダーに追加し、そこに設定を追加します。 たとえば、*Web.config* ファイルを *\Content* フォルダーに追加して、その内容を次の XML で置き換えます。

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

この設定は、*\Content* フォルダーのすべての静的ファイルを 15 日間キャッシュすることを指定しています。

`<clientCache>` 要素を構成する方法については、「[Client Cache &lt;clientCache> (クライアント キャッシュ <clientCache>)](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache)」を参照してください。

「 [Azure CDN を介してコントローラー アクションからコンテンツを配信する](#controller)」では、CDN キャッシュ内のコントローラー アクションの結果に対してキャッシュ設定を構成する方法についても説明します。

<a name="controller"></a>

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Azure CDN を介してコントローラー アクションからコンテンツを配信する
クラウド サービス Web ロールを Azure CDN と統合すると、Azure CDN を介してコントローラー アクションからコンテンツを簡単に配信できます。 (上に示した) Azure CDN を介して直接クラウド サービスを提供する方法のほか、[Maarten Balliauw](https://twitter.com/maartenballiauw) 氏は、[Azure CDN による Web の遅延時間の短縮](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)のビデオでおもしろい MemeGenerator コントローラーを使用した方法を紹介しています。 ここではその方法を再現します。

クラウド サービスで次のような Chuck Norris の若いときの画像 ( [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)による撮影) に基づいてミームを生成するとします。

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

ここで使用する `Index` アクションでは、画像内にこのようなジョークを指定できます。顧客がこのアクションに投稿するとミームが生成されます。 題材が Chuck Norris のため、このページは全世界で受け入れられると予想されます。 これは、Azure CDN で半動的コンテンツを配信するよい例です。

上記の手順を実行して、このコントローラー アクションを設定します。

1. *\Controllers* フォルダーに、*MemeGeneratorController.cs* という名前の新しい .cs ファイルを作成し、その内容を次のコードで置き換えます。 強調表示されている箇所は CDN 名で置き換えてください。  
   
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;
   
        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();
   
                public ActionResult Index()
                {
                    return View();
                }
   
                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }
   
                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }
   
                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }
   
                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }
   
                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);
   
                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }
   
                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }
   
                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);
   
                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }
   
                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }
2. 既定の `Index()` アクション内で右クリックし、 **[ビューの追加]**を選択します。
   
    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)
3. 次の既定の設定をそのまま受け入れ、 **[追加]**をクリックします。
   
   ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)
4. 新しい *Views\MemeGenerator\Index.cshtml* を開き、その内容を、ジョークを送信するための次の単純な HTML で置き換えます。
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
5. もう一度クラウド サービスを発行して、ブラウザーで **http://*&lt;serviceName>*.cloudapp.net/MemeGenerator/Index** に移動します。

フォームの値を `/MemeGenerator/Index` に送信すると、`Index_Post` アクション メソッドにより、`Show` アクション メソッドへのリンクとそれぞれの入力識別子が返されます。 リンクをクリックすると、次のコードに到達します。  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

ローカル デバッガーが接続されている場合は、ローカル リダイレクトにより通常のデバッグ エクスペリエンスが得られます。 クラウド サービスで実行されている場合は、次の場所にリダイレクトされます。

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

この URL は、CDN エンドポイントの次のオリジン URL に対応します。

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


次に、`Generate` メソッドの `OutputCacheAttribute` 属性を使用して、Azure CDN に有効な、アクション結果をキャッシュする方法を指定できます。 次のコードでは、キャッシュの有効期限が 1 時間 (3,600 秒) に設定されています。

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

同様に、任意のキャッシュ オプションを使用して、Azure CDN を介してクラウド サービスの任意のコントローラー アクションからコンテンツを配信できます。

次のセクションでは、バンドルされたスクリプト、縮小されたスクリプト、および CSS を Azure CDN 経由で提供する方法について説明します。

<a name="bundling"></a>

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>ASP.NET のバンドルと縮小を Azure CDN と統合できる。
スクリプトや CSS スタイルシートはあまり頻繁に変更されないため、Azure CDN キャッシュの有力な候補になります。 Azure CDN を介して Web ロール全体を提供するやり方は、バンドルや縮小を Azure CDN と統合する方法として最も簡単です。 ただし、このやり方を希望しない方のために、次のような ASP.NET のバンドルと縮小の開発者エクスペリエンスを維持しながらこれを実現する方法を紹介します。

* 優れたデバッグ モード エクスペリエンス
* 合理的なデプロイ
* スクリプトや CSS のバージョンをアップグレードするためのクライアントの即時の更新
* CDN エンドポイントのに障害に対するフォールバック メカニズム
* 最小限のコードの変更

[Azure CDN エンドポイントを Azure Websites と統合し、Azure CDN から Web ページの静的コンテンツを配信](#deploy)して作成した **WebRole1** プロジェクトで、*App_Start\BundleConfig.cs* を開き、`bundles.Add()` メソッドの呼び出しを確認します。

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

最初の `bundles.Add()` ステートメントは、スクリプト バンドルを仮想ディレクトリ `~/bundles/jquery` で追加します。 次に、*Views\Shared\_Layout.cshtml* を開いて、スクリプト バンドル タグがどのようにレンダリングされているかを確認します。 次の Razor コードが見つかります。

    @Scripts.Render("~/bundles/jquery")

Azure Web ロールでこの Razor コードが実行されると、次のようなスクリプト バンドルのための `<script>` タグがレンダリングされます。

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

ただし、 `F5`キーを押してこのコードを Visual Studio 内で実行した場合は、バンドル内の各スクリプト ファイルが個別にレンダリングされます (上記のケースでは、バンドルに含まれているスクリプト ファイルは&1; つだけです)。

    <script src="/Scripts/jquery-1.10.2.js"></script>

これにより、JavaScript コードを開発環境でデバッグできる一方で、運用環境での同時クライアント接続数 (バンドル) を削減し、ファイルのダウンロード パフォーマンス (縮小) を高めることができます。 これは、Azure CDN 統合によって得られる優れた機能です。 加えて、レンダリングされたバンドルには自動生成されたバージョン文字列が既に含まれます。そこで、この機能を複製することで、NuGet を介して jQuery バージョンを更新するたびにできる限り迅速にクライアント側にその更新が反映されるようにすることができます。

ASP.NET のバンドルおよび縮小を CDN エンドポイントと統合するには、次の手順に従います。

1. *App_Start\BundleConfig.cs* に戻り、CDN アドレスを指定する別の [Bundle コンストラクター](http://msdn.microsoft.com/library/jj646464.aspx)を使用するように `bundles.Add()` メソッドを変更します。 そのためには、 `RegisterBundles` メソッドの定義を次のコードで置き換えます。  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    `<yourCDNName>` は Azure CDN の名前で置き換えてください。
   
    このコードでは、 `bundles.UseCdn = true` を設定し、巧妙に作成された CDN URL を各バンドルに追加しています。 たとえば、コードの最初のコンストラクター
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
   
    は、次のコードと同じです。
   
        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))
   
    このコンストラクターは、ASP.NET のバンドルおよび縮小機能に対して、ローカルにデバッグされたときに個々のスクリプト ファイルをレンダリングする一方で、指定された CDN アドレスを使用して対象のスクリプトにアクセスすることを指定しています。 ここで、この巧妙に作成された CDN URL の&2; つの重要な特性に注意してください。
   
   * この CDN URL のオリジンは、実際にはクラウド サービスのスクリプト バンドルの仮想ディレクトリである `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`です。
   * CDN コンストラクターを使用しているため、バンドルのための CDN スクリプト タグには、レンダリングされた URL 内の自動生成されたバージョン文字列が含まれません。 Azure CDN でキャッシュ ミスを強制的に発生させるには、スクリプト バンドルを変更するたびに一意のバージョン文字列を手動で生成する必要があります。 また、この一意のバージョン文字列は、バンドルがデプロイされた後の Azure CDN でのキャッシュ ヒットを最大化するために、デプロイの有効期間を通じて一定に保たれる必要があります。
   * クエリ文字列 v=<W.X.Y.Z> は、Web ロール プロジェクトの *Properties\AssemblyInfo.cs* から取得されます。 Azure に発行するたびにアセンブリ バージョンを増分する操作を含むデプロイ ワークフローを導入できます。 また、ワイルドカード文字 * を使って単にプロジェクトの *Properties\AssemblyInfo.cs* を変更して、ビルドするたびに自動的にバージョン文字列を増分するよう設定することもできます。 次に例を示します。
     
        [assembly: AssemblyVersion("1.0.0.*")]
     
     デプロイの有効期間を通じて一意の文字列を生成する操作を合理化するための他の戦略も使用できます。
2. クラウド サービスを再発行し、ホーム ページにアクセスします。
3. ページの HTML コードを表示します。 クラウド サービスに変更を再発行するたびに一意のバージョン文字列を付けて CDN URL がレンダリングされているのを確認できます。 次に例を示します。  
   
        ...
   
        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>
   
        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>
   
        ...
   
        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>
   
        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>
   
        ...
4. Visual Studio で、 `F5`キーを押してクラウド サービスをデバッグします。
5. ページの HTML コードを表示します。 Visual Studio での一貫したデバッグ エクスペリエンスが得られるように個別にレンダリングされた各スクリプト ファイルを確認できます。  
   
        ...
   
            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>
   
            <script src="/Scripts/modernizr-2.6.2.js"></script>
   
        ...
   
            <script src="/Scripts/jquery-1.10.2.js"></script>
   
            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>
   
        ...   

<a name="fallback"></a>

## <a name="fallback-mechanism-for-cdn-urls"></a>CDN URL のフォールバック メカニズム
何かの理由で Azure CDN エンドポイントに障害が発生した場合に備えて、JavaScript または Bootstrap を読み込むためのフォールバック オプションとしてオリジン Web サーバーにアクセスできるようにしておくと便利です。 CDN が利用できないために Web サイトの画像が失われることは深刻な事態ですが、スクリプトやスタイルシートで提供される重要なページ機能が失われることは、さらに深刻な事態です。

[Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) クラスには、CDN 障害に対するフォールバック メカニズムを構成するための [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) プロパティがあります。 このプロパティを使用するには、次の手順に従います。

1. Web ロール プロジェクトで、それぞれの [Bundle コンストラクター](http://msdn.microsoft.com/library/jj646464.aspx)に CDN URL を追加した *App_Start\BundleConfig.cs* を開き、次の強調表示された変更を加えて、既定のバンドルにフォールバック メカニズムを追加します。  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;
   
            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));
   
            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));
   
            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));
   
            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                  "~/Scripts/bootstrap.js",
                                  "~/Scripts/respond.js"));
   
            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }
   
    `CdnFallbackExpression` が null でない場合は、HTML にスクリプトが挿入されます。このスクリプトは、バンドルが正常に読み込まれたかどうかをテストして、正常に読み込まれていない場合はオリジン Web サーバーのバンドルに直接アクセスします。 このプロパティは、それぞれの CDN バンドルが適切に読み込まれたかどうかをテストする JavaScript 式に設定する必要があります。 各バンドルをテストするために必要な式は、コンテンツによって異なります。 上記の既定のバンドルに対して、次のように定義されています。
   
   * `window.jquery` は jquery-{version}.js に定義されています。
   * `$.validator` は jquery.validate.js に定義されています。
   * `window.Modernizr` は modernizer-{version}.js に定義されています。
   * `$.fn.modal` は bootstrap.js に定義されています。
     
     `~/Cointent/css` バンドルには CdnFallbackExpression を設定していません。 これは、期待される `<script>` タグではなくフォールバック CSS の `<link>` タグが挿入されるという [System.Web.Optimization のバグ](https://aspnetoptimization.codeplex.com/workitem/104)が存在するためです。
     
     ただし、[Ember Consulting Group](https://github.com/EmberConsultingGroup) から優れた[スタイル バンドルのフォールバック](https://github.com/EmberConsultingGroup/StyleBundleFallback)が提供されています。
2. CSS の対処法を使用するには、Web ロール プロジェクトの *App_Start* フォルダーに *StyleBundleExtensions.cs* という名前の新しい .cs ファイルを作成し、その内容を [GitHub に公開されているコード](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs)で置き換えます。
3. *App_Start\StyleFundleExtensions.cs* で、名前空間の名前を Web ロールの名前 (**WebRole1** など) に変更します。
4. `App_Start\BundleConfig.cs` に戻り、最後の `bundles.Add` ステートメントを、次の強調表示されたコードで置き換えます。  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));
   
    この新しいメソッドでは、同じアイデアを使用するスクリプトを HTML に挿入します。つまり、DOM を調べて CSS バンドルに定義されているクラス名、規則名、および規則値が一致するかどうかを確認し、一致しない場合はオリジン Web サーバーにフォールバックします。
5. クラウド サービスを発行し、ホーム ページにアクセスします。
6. ページの HTML コードを表示します。 次のようなスクリプトが挿入されていることを確認できます。    
   
        ...
   
        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
   
        ...
   
            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
   
        ...

    CSS バンドルに対して挿入されたスクリプトには、次の行に `CdnFallbackExpression` プロパティのエラーの残りがまだ含まれている点に注意してください。

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    ただし、(すぐ上の行の) || 式の最初の部分は常に true を返すため、document.write() 関数が実行されることはありません。

## <a name="more-information"></a>詳細情報
* [Azure Content Delivery Network (CDN) の概要](http://msdn.microsoft.com/library/azure/ff919703.aspx)
* [Azure CDN の使用](cdn-create-new-endpoint.md)
* [ASP.NET のバンドルおよび縮小](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)

[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png



<!--HONumber=Jan17_HO4-->


