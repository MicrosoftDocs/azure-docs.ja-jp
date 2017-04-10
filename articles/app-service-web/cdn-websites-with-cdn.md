---
title: "Azure App Service で Azure CDN を使用する"
description: "統合 Azure CDN エンドポイントからのコンテンツを提供する Web アプリを Azure App Service にデプロイする方法について説明するチュートリアル"
services: app-service\web,cdn
documentationcenter: .net
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: b81ee930-dd6d-4b65-971f-c4cb7902168c
ms.service: app-service
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 07/01/2016
ms.author: cephalin
translationtype: Human Translation
ms.sourcegitcommit: b1a633a86bd1b5997d5cbf66b16ec351f1043901
ms.openlocfilehash: 0b1f13f816c7836557cd6f646f2d9b38f7bc5b14
ms.lasthandoff: 02/16/2017


---
# <a name="use-azure-cdn-in-azure-app-service"></a>Azure App Service で Azure CDN を使用する
[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) は、[Azure CDN](/services/cdn/) との統合が可能で、顧客の近くにあるサーバー ノードから Web Apps のコンテンツをグローバルに配信することにより、[App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) が備えるグローバル スケーリング機能を向上させることができます (現在のノードの場所を示す最新のリストについては、[こちら](http://msdn.microsoft.com/library/azure/gg680302.aspx)を参照してください)。 静的な画像を配信するシナリオでは、この統合により、Azure App Service Web Apps のパフォーマンスが飛躍的に向上し、Web Apps のユーザー エクスペリエンスが世界中で大幅に改善します。 

Web App と Azure CDN の統合には、次の利点があります。

* コンテンツのデプロイメント (画像、スクリプト、およびスタイルシート) を、Web アプリの [継続的なデプロイメント](app-service-continuous-deployment.md) プロセスの一部として統合
* Azure App Service の Web アプリの NuGet パッケージ (たとえば、jQuery バージョン、Bootstrap バージョン) を簡単にアップグレードできる 
* Web アプリケーションと CDN によって配信されるコンテンツを同じ Visual Studio インターフェイスから管理
* ASP.NET のバンドルと縮小を Azure CDN と統合できる。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## <a name="what-you-will-build"></a>学習内容
Visual Studio の既定の ASP.NET MVC テンプレートを使用して Web アプリを Azure App Service にデプロイし、統合された Azure CDN からコンテンツ (たとえば、画像、コントローラー アクションの結果、既定の JavaScript ファイルおよび CSS ファイル) を配信するコードを追加します。さらに、CDN がオフラインになった場合に提供されるバンドルのフォールバック メカニズムを構成するコードを作成します。

## <a name="what-you-will-need"></a>前提条件
このチュートリアルの前提条件は次のとおりです。

* アクティブな [Microsoft Azure アカウント](/account/)
* Visual Studio 2015 と [Azure SDK for .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)。 Visual Studio を使用する場合、手順が異なることがあります。

> [!NOTE]
> このチュートリアルを完了するには、Azure アカウントが必要です。
> 
> * 無料で [Azure アカウントを開く](https://azure.microsoft.com/pricing/free-trial/)ことができます。Azure の有料サービスを試用できるクレジットが提供されます。このクレジットを使い切ってもアカウントは維持されるため、Web Apps など無料の Azure サービスをご利用になれます。
> * [Visual Studio サブスクライバーの特典を有効にする](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) こともできます - Visual Studio サブスクリプションにより、有料の Azure サービスで使用できるクレジットが毎月提供されます。
> 
> Azure アカウントにサインアップする前に Azure App Service の使用を開始したい場合は、「[Azure App Service アプリケーションの作成](https://azure.microsoft.com/try/app-service/)」を参照してください。そこでは、App Service で有効期間の短いスターター Web アプリをすぐに作成できます。 このサービスの利用にあたり、クレジット カードは必要ありません。契約も必要ありません。
> 
> 

## <a name="deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint"></a>統合された CDN エンドポイントで Azure に Web アプリをデプロイする
このセクションでは、Visual Studio 2015 の既定の ASP.NET MVC アプリケーション テンプレートを App Service にデプロイした後、これを新しい CDN エンドポイントと統合します。 次の手順に従ってください。

1. Visual Studio 2015 で、メニュー バーから **[ファイル]、[新規]、[プロジェクト]、[Web]、[ASP.NET Web アプリケーション]** の順に移動し、新しい ASP.NET Web アプリケーションを作成します。 クラウド サービスに名前を付けて、 **[OK]**をクリックします。
   
    ![](media/cdn-websites-with-cdn/1-new-project.png)
2. **[MVC]** を選択し、**[OK]** をクリックします。
   
    ![](media/cdn-websites-with-cdn/2-webapp-template.png)
3. Azure アカウントにまだログインしていない場合は、右上隅の [アカウント] アイコンをクリックし、ダイアログに従ってAzure アカウントにログインします。 ログインしたら、次のようにアプリを構成し、 **[新規]** をクリックしてアプリ用の新しい App Service プランを作成します。  
   
    ![](media/cdn-websites-with-cdn/3-configure-webapp.png)
4. 新しい App Service プランをダイアログで次のように構成し、 **[OK]**をクリックします。 
   
    ![](media/cdn-websites-with-cdn/4-app-service-plan.png)
5. **[作成]** をクリックして、Web アプリを作成します。
   
    ![](media/cdn-websites-with-cdn/5-create-website.png)
6. ASPに移動し、新しい ASP.NET Web アプリケーションを作成します。NET アプリケーションが作成されたら、Azure App Service アクティビティ ウィンドウで **発行 `<app name>` をこの Web アプリに発行する**に移動し、新しい ASP.NET Web アプリケーションを作成します。 **[発行]** をクリックしてプロセスを完了します。
   
    ![](media/cdn-websites-with-cdn/6-publish-website.png)
   
    発行が完了すると、発行済みの Web アプリがブラウザーに表示されます。 
7. CDN エンドポイントを作成するには、 [Azure ポータル](https://portal.azure.com)にログインします。 
8. **[+ 新規]** > **[メディア + CDN]** > **[CDN]** をクリックします。
   
    ![](media/cdn-websites-with-cdn/create-cdn-profile.png)
9. **[CDN]**、**[場所]**、**[リソース グループ]**、**[価格レベル]** の順に指定して、**[作成]** をクリックします。
   
    ![](media/cdn-websites-with-cdn/7-create-cdn.png)    
10. **[CDN のプロファイル]** ブレードで、**[+ エンドポイント]** ボタンをクリックします。 名前を付け、**[配信元の種類]** ドロップダウンで **[Web アプリ]** と、**[配信元のホスト名]** ドロップダウンで Web アプリを選択して、**[追加]** をクリックします。  
    
     ![](media/cdn-websites-with-cdn/cdn-profile-blade.png)

    > [AZURE.NOTE] CDN エンドポイントが作成されると、その CDN URL と統合先の元のドメインが **[エンドポイント]** ブレードに表示されます。 ただし、新しい CDN エンドポイントの構成がすべての CDN ノードの場所に完全に反映されるまで少し時間がかかる場合があります。 

1. **[エンドポイント]** ブレードに戻り、作成した CDN エンドポイントの名前をクリックします。
   
    ![](media/cdn-websites-with-cdn/8-select-cdn.png)
2. **[構成]** をクリックします。 **[構成]** ブレードでは、**[クエリ文字列のキャッシュ動作]** ドロップダウンで **[一意の URL をすべてキャッシュ]** を選択して、**[保存]** ボタンをクリックします。

    ![](media/cdn-websites-with-cdn/9-enable-query-string.png)

これを有効にすると、同一のリンクが異なるクエリ文字列でアクセスされた場合は別のエントリとしてキャッシュされます。

> [!NOTE]
> チュートリアルのこのセクションでのクエリ文字列の有効化は必須ではありませんが、ここでの変更がすべての CDN ノードに反映されるまで時間がかかるため、できる限り早めに有効にしておくと便利です。また、クエリ文字列非対応コンテンツで CDN キャッシュが停滞するのを防ぐためでもあります (CDN コンテンツの更新については後で説明します)。
> 
> 

1. 次に、CDN エンドポイントのアドレスに移動します。 エンドポイントの準備が完了している場合、Web アプリが表示されます。 **HTTP 404** エラーが表示された場合は、CDN エンドポイントの準備ができていません。 CDN の構成がすべてのエッジ ノードに反映されるまで、最大で&1; 時間待機することが必要になる場合があります。 
   
    ![](media/cdn-websites-with-cdn/11-access-success.png)
2. 次に、ASP.NET プロジェクトで **~/Content/bootstrap.css** ファイルにアクセスしてみます。 ブラウザーのウィンドウで **http://*&lt;CDN 名>*.azureedge.net/Content/bootstrap.css** に移動します。 この設定では、次の URL を使用します。
   
        http://az673227.azureedge.net/Content/bootstrap.css
   
    この URL は、CDN エンドポイントの次のオリジン URL に対応します。
   
        http://cdnwebapp.azurewebsites.net/Content/bootstrap.css
   
    **http://*&lt;CDN 名>*.azureedge.net/Content/bootstrap.css** に移動すると、Azure の Web アプリから bootstrap.css をダウンロードするよう求められます。 
   
    ![](media/cdn-websites-with-cdn/12-file-access.png)

同様に、CDN エンドポイントから、**http://*&lt;サービス名>*.cloudapp.net/** のパブリックにアクセスできる任意の URL にアクセスできます。 次に例を示します。

* /Script パスの .js ファイル
* /Content パスの任意のコンテンツ ファイル
* 任意のコントローラー/アクション 
* CDN エンドポイントでクエリ文字列が有効になっている場合、クエリ文字列を含む任意の URL
* すべてのコンテンツがパブリックの場合、Azure Web アプリ全体

Azure CDN から Azure Web アプリ全体を配信することが常に (または一般的に) 最適とは限りません。 いくつかの注意点があります。

* Azure CDN ではプライベートなコンテンツを配信できないため、この方法ではサイト全体をパブリックにする必要があります。
* 予定されたメンテナンスであろうとユーザー エラーであろうと、CDN エンドポイントが何かの理由でオフラインになると、顧客をオリジン**http://*&lt;サイト名>*.azurewebsites.net/** にリダイレクトできる場合を除き、Web アプリ全体がオフラインになります。 
* カスタムの Cache-Control 設定 (「[Azure Web アプリの静的ファイルのキャッシュ オプションを構成する](#configure-caching-options-for-static-files-in-your-azure-web-app)」を参照してください) を使用した場合でも、CDN エンドポイントによって高度に動的なコンテンツのパフォーマンスが向上するわけではありません。 上に示すように CDN エンドポイントからホーム ページを読み込もうとした場合、非常に単純な既定のホーム ページを初めて読み込むときに 5 秒以上かかります。 このページに毎分更新する必要がある動的コンテンツが含まれていたとしたら、クライアント エクスペリエンスはどうなるでしょうか。 動的コンテンツを CDN エンドポイントから配信するにはキャッシュの有効期限が短く設定されている必要があります。これは、CDN エンドポイントで頻繁にキャッシュ ミスが発生することになります。 その結果、Azure Web アプリのパフォーマンスが低下し、CDN の目的が果たせなくなります。

また、Azure Web アプリで Azure CDN から配信するコンテンツをケースバイケースで決定する方法もあります。 CDN エンドポイントから個々のコンテンツ ファイルにアクセスする方法については既に説明しました。 CDN エンドポイントを介して特定のコントローラー アクションを配信する方法については、「 [Azure CDN を介してコントローラー アクションからコンテンツを配信する](#serve-content-from-controller-actions-through-azure-cdn)」で説明します。

## <a name="configure-caching-options-for-static-files-in-your-azure-web-app"></a>Azure Web アプリの静的ファイルのキャッシュ オプションを構成する
Azure CDN 統合を Azure Web アプリに組み込むと、CDN エンドポイントで静的コンテンツをどのようにキャッシュするかを指定できます。 そのためには、ASP.NET プロジェクト (例: **cdnwebapp**) から *Web.config* を開き `<system.webServer>` に `<staticContent>` 要素を追加します。 次の XML では、3 日間で有効期限が切れるキャッシュを追加しています。  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

この操作を実行すると、Azure Web アプリ内のすべての静的ファイルは、CDN キャッシュ内で同じ規則に従います。 キャッシュ設定をより細かく制御するには、 *Web.config* ファイルをフォルダーに追加し、そこに設定を追加します。 たとえば、*Web.config* ファイルを *\Content* フォルダーに追加して、その内容を次の XML で置き換えます。

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

次のセクションでは、CDN キャッシュ内のコントローラー アクションの結果に対してキャッシュ設定を構成する方法についても説明します。

## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Azure CDN を介してコントローラー アクションからコンテンツを配信する
Azure CDN と Web Apps を統合すると、Azure CDN を介してコントローラー アクションからコンテンツを比較的簡単に配信できます。 この場合も、CDN を介して Azure Web アプリ全体を配信する場合は、すべてのコントローラー アクションが既に CDN から到達可能なため、この手順は不要です。 ただし、「 [統合された CDN エンドポイントで Azure に Web アプリをデプロイする](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint)」で指摘したとおり、この手順の代わりに、Azure CDN から配信するコントローラー アクションを選択できます。 [Maarten Balliauw](https://twitter.com/maartenballiauw) 氏が、[Azure CDN による Web の遅延時間の短縮](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)に関する講演の中でおもしろい MemeGenerator コントローラーを使用した方法を紹介しています。 ここではその方法を再現します。

Web アプリで次のような Chuck Norris の若いときの画像 ( [Alan Light](http://www.flickr.com/photos/alan-light/218493788/)による撮影) に基づいてミームを生成するとします。

![](media/cdn-websites-with-cdn/cdn-5-memegenerator.PNG)

ここで使用する `Index` アクションでは、画像内にこのようなジョークを指定できます。顧客がこのアクションに投稿するとミームが生成されます。 題材が Chuck Norris のため、このページは全世界で受け入れられると予想されます。 これは、Azure CDN で半動的コンテンツを配信するよい例です。 

上記の手順を実行して、このコントローラー アクションを設定します。

1. *\Controllers* フォルダーに、*MemeGeneratorController.cs* という名前の新しい .cs ファイルを作成し、その内容を次のコードで置き換えます。 `~/Content/chuck.bmp` を実際のファイル パスに、`yourCDNName` を CDN 名に置き換えてください。

        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;

        namespace cdnwebapp.Controllers
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
                return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
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

1. 既定の `Index()` アクション内で右クリックし、 **[ビューの追加]**を選択します。
   
    ![](media/cdn-websites-with-cdn/cdn-6-addview.PNG)
2. 次の既定の設定をそのまま受け入れ、 **[追加]**をクリックします。
   
   ![](media/cdn-websites-with-cdn/cdn-7-configureview.PNG)
3. 新しい *Views\MemeGenerator\Index.cshtml* を開き、その内容を、ジョークを送信するための次の単純な HTML で置き換えます。
   
        <h2>Meme Generator</h2>
   
        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>
4. もう一度 Azure Web アプリに発行し、ブラウザーで **http://*&lt;サービス名>*.cloudapp.net/MemeGenerator/Index** に移動します。 

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

ローカル デバッガーが接続されている場合は、ローカル リダイレクトにより通常のデバッグ エクスペリエンスが得られます。 Azure Web アプリで実行されている場合は、次の場所にリダイレクトされます。

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

この URL は、CDN エンドポイントの次のオリジン URL に対応します。

    http://<yourSiteName>.azurewebsites.net/cdn/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

URL の書き換え規則が適用された後、CDN エンドポイントにキャッシュされる実際のファイルは次のファイルになります。

    http://<yourSiteName>.azurewebsites.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

次に、`Generate` メソッドの `OutputCacheAttribute` 属性を使用して、Azure CDN に有効な、アクション結果をキャッシュする方法を指定できます。 次のコードでは、キャッシュの有効期限が 1 時間 (3,600 秒) に設定されています。

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

同様に、任意のキャッシュ オプションを使用し、Azure CDN を介して Azure Web アプリの任意のコントローラー アクションからコンテンツを配信できます。

次のセクションでは、バンドルされたスクリプト、縮小されたスクリプト、および CSS を Azure CDN 経由で提供する方法について説明します。 

## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>ASP.NET のバンドルと縮小を Azure CDN と統合できる。
スクリプトや CSS スタイルシートはあまり頻繁に変更されないため、Azure CDN キャッシュの有力な候補になります。 Azure CDN を介して Web アプリ全体を配信することは、バンドルや縮小を Azure CDN と統合する方法として最も簡単です。 ただし、「 [Azure CDN エンドポイントを Azure Web アプリと統合して、Azure CDN から Web ページの静的コンテンツを配信する](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint)」で説明した理由から、この方法を選択しない場合のために、ASP.NET のバンドルと縮小の開発者エクスペリエンスを維持しながらこれを実現する方法を紹介します。

* 優れたデバッグ モード エクスペリエンス
* 合理的なデプロイ
* スクリプトや CSS のバージョンをアップグレードするためのクライアントの即時の更新
* CDN エンドポイントのに障害に対するフォールバック メカニズム
* 最小限のコードの変更

[Azure CDN エンドポイントを Azure Web アプリと統合して、Azure CDN から Web ページの静的コンテンツを配信する](#deploy-a-web-app-to-azure-with-an-integrated-cdn-endpoint)操作に関するセクションで作成した ASP.NET プロジェクトで、*App_Start\BundleConfig.cs* を開き、`bundles.Add()` メソッドの呼び出しを確認します。

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

最初の `bundles.Add()` ステートメントは、スクリプト バンドルを仮想ディレクトリ `~/bundles/jquery` で追加します。 次に、*Views\Shared\_Layout.cshtml* を開いて、スクリプト バンドル タグがどのようにレンダリングされているかを確認します。 次の Razor コードが見つかります。

    @Scripts.Render("~/bundles/jquery")

Azure Web アプリでこの Razor コードが実行されると、次のようなスクリプト バンドルのための `<script>` タグがレンダリングされます。 

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

ただし、`F5` キーを押してこのコードを Visual Studio 内で実行した場合は、バンドル内の各スクリプト ファイルが個別にレンダリングされます (上記のケースでは、バンドルに含まれているスクリプト ファイルは&1; つだけです)。

    <script src="/Scripts/jquery-1.10.2.js"></script>

これにより、JavaScript コードを開発環境でデバッグできる一方で、運用環境での同時クライアント接続数 (バンドル) を削減し、ファイルのダウンロード パフォーマンス (縮小) を高めることができます。 これは、Azure CDN 統合によって得られる優れた機能です。 加えて、レンダリングされたバンドルには自動生成されたバージョン文字列が既に含まれます。そこで、この機能を複製することで、NuGet を介して jQuery バージョンを更新するたびにできる限り迅速にクライアント側にその更新が反映されるようにすることができます。

ASP.NET のバンドルおよび縮小を CDN エンドポイントと統合するには、次の手順に従います。

1. *App_Start\BundleConfig.cs* に戻り、CDN アドレスを指定する別の [Bundle コンストラクター](http://msdn.microsoft.com/library/jj646464.aspx)を使用するように `bundles.Add()` メソッドを変更します。 そのためには、 `RegisterBundles` メソッドの定義を次のコードで置き換えます。  
   
        public static void RegisterBundles(BundleCollection bundles)
        {
          bundles.UseCdn = true;
          var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
            .GetName().Version.ToString();
          var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?" + version;
   
          bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                "~/Scripts/jquery-{version}.js"));
   
          bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                "~/Scripts/jquery.validate*"));
   
          // Use the development version of Modernizr to develop with and learn from. Then, when you're
          // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
          bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizr")).Include(
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

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?<W.X.Y.Z>"))

    このコンストラクターは、ASP.NET のバンドルおよび縮小機能に対して、ローカルにデバッグされたときに個々のスクリプト ファイルをレンダリングする一方で、指定された CDN アドレスを使用して対象のスクリプトにアクセスすることを指定しています。 ここで、この巧妙に作成された CDN URL の&2; つの重要な特性に注意してください。

    - この CDN URL のオリジンは、実際には Web アプリケーション内のスクリプト バンドルの仮想ディレクトリである `http://<yourSiteName>.azurewebsites.net/bundles/jquery?<W.X.Y.Z>`です。
    - CDN コンストラクターを使用しているため、バンドルのための CDN スクリプト タグには、レンダリングされた URL 内の自動生成されたバージョン文字列が含まれません。 Azure CDN でキャッシュ ミスを強制的に発生させるには、スクリプト バンドルを変更するたびに一意のバージョン文字列を手動で生成する必要があります。 また、この一意のバージョン文字列は、バンドルがデプロイされた後の Azure CDN でのキャッシュ ヒットを最大化するために、デプロイの有効期間を通じて一定に保たれる必要があります。

1. クエリ文字列 `<W.X.Y.Z>` は、ASP.NET プロジェクトの *Properties\AssemblyInfo.cs* から取得されます。 Azure に発行するたびにアセンブリ バージョンを増分する操作を含むデプロイ ワークフローを導入できます。 また、ワイルドカード文字 * を使って単にプロジェクトの *Properties\AssemblyInfo.cs* を変更して、ビルドするたびに自動的にバージョン文字列を増分するよう設定することもできます。 たとえば、 `AssemblyVersion` を次のように変更します。
   
        [assembly: AssemblyVersion("1.0.0.*")]
   
    デプロイの有効期間を通じて一意の文字列を生成する操作を合理化するための他の戦略も使用できます。
2. ASP.NET アプリケーションを再発行し、ホーム ページにアクセスします。
3. ページの HTML コードを表示します。 Azure Web アプリに変更を再発行するたびに一意のバージョン文字列を付けて CDN URL がレンダリングされているのを確認できます。 次に例を示します。  
   
        ...
        <link href="http://az673227.azureedge.net/Content/css?1.0.0.25449" rel="stylesheet"/>
        <script src="http://az673227.azureedge.net/bundles/modernizer?1.0.0.25449"></script>
        ...
        <script src="http://az673227.azureedge.net/bundles/jquery?1.0.0.25449"></script>
        <script src="http://az673227.azureedge.net/bundles/bootstrap?1.0.0.25449"></script>
        ...
4. Visual Studio で、 `F5`キーを押して ASP.NET アプリケーションをデバッグします。 
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

## <a name="fallback-mechanism-for-cdn-urls"></a>CDN URL のフォールバック メカニズム
何かの理由で Azure CDN エンドポイントに障害が発生した場合に備えて、JavaScript または Bootstrap を読み込むためのフォールバック オプションとしてオリジン Web サーバーにアクセスできるようにしておくと便利です。 CDN が利用できないために Web アプリの画像が失われることは深刻な事態ですが、スクリプトやスタイルシートで提供される重要なページ機能が失われることは、さらに深刻な事態です。

[Bundle](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) クラスには、CDN 障害に対するフォールバック メカニズムを構成するための [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) プロパティがあります。 このプロパティを使用するには、次の手順に従います。

1. ASP.NET プロジェクトで、それぞれの [Bundle コンストラクター](http://msdn.microsoft.com/library/jj646464.aspx)に CDN URL を追加した *App_Start\BundleConfig.cs* を開き、示されたているように&4; か所に `CdnFallbackExpression` コードを追加して、フォールバック メカニズムを既定のバンドルに追加します。  
   
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
   
          // Use the development version of Modernizr to develop with and learn from. Then, when you're
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
2. CSS の対処法を使用するには、ASP.NET プロジェクトの *App_Start* フォルダーに *StyleBundleExtensions.cs* という名前の新しい .cs ファイルを作成し、その内容を [GitHub に公開されているコード](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs)で置き換えます。 
3. *App_Start\StyleFundleExtensions.cs* で、名前空間の名前を ASP.NET アプリケーションの名前空間 (例: **cdnwebapp**) に変更します。 
4. `App_Start\BundleConfig.cs` に戻り、最後の `bundles.Add` ステートメントを、次のコードに置き換えます。  
   
        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
          .IncludeFallback("~/Content/css", "sr-only", "width", "1px")
          .Include(
            "~/Content/bootstrap.css",
            "~/Content/site.css"));
   
    この新しいメソッドでは、同じアイデアを使用するスクリプトを HTML に挿入します。つまり、DOM を調べて CSS バンドルに定義されているクラス名、規則名、および規則値が一致するかどうかを確認し、一致しない場合はオリジン Web サーバーにフォールバックします。
5. もう一度 Azure Web アプリに発行し、ホーム ページにアクセスします。 
6. ページの HTML コードを表示します。 次のようなスクリプトが挿入されていることを確認できます。    
   
    ```
    ...
    <link href="http://az673227.azureedge.net/Content/css?1.0.0.25474" rel="stylesheet"/>
   <script>(function() {
                var loadFallback,
                    len = document.styleSheets.length;
                for (var i = 0; i < len; i++) {
                    var sheet = document.styleSheets[i];
                    if (sheet.href.indexOf('http://az673227.azureedge.net/Content/css?1.0.0.25474') !== -1) {
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
   
    <script src="http://az673227.azureedge.net/bundles/modernizer?1.0.0.25474"></script>
     <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>
    ... 
    <script src="http://az673227.azureedge.net/bundles/jquery?1.0.0.25474"></script>
    <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>
   
     <script src="http://az673227.azureedge.net/bundles/bootstrap?1.0.0.25474"></script>
     <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>
    ...
    ```
   
    CSS バンドルに対して挿入されたスクリプトには、次の行に `CdnFallbackExpression` プロパティのエラーの残りがまだ含まれている点に注意してください。
   
        }())||document.write('<script src="/Content/css"><\/script>');</script>
   
    ただし、(すぐ上の行の) || 式の最初の部分は常に true を返すため、document.write() 関数が実行されることはありません。
7. フォールバック スクリプトが動作するかどうかをテストするには、CDN エンドポイントのブレードに戻り、 **[停止]**をクリックします。
   
    ![](media/cdn-websites-with-cdn/13-test-fallback.png)
8. Azure Web アプリのブラウザー ウィンドウを更新します。 これで、すべてのスクリプトとスタイルシートが正しく読み込まれていることを確認できます。

## <a name="more-information"></a>詳細情報
* [Azure Content Delivery Network (CDN) の概要](../cdn/cdn-overview.md)
* [Azure CDN の使用](../cdn/cdn-create-new-endpoint.md)
* [クラウド サービスと Azure CDN との統合](../cdn/cdn-cloud-service-with-cdn.md)
* [ASP.NET のバンドルおよび縮小](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)


