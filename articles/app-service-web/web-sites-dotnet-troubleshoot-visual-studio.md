---
title: "Visual Studio を使用した Azure App Service のトラブルシューティング"
description: "Visual Studio 2013 に組み込まれているリモート デバッグ、トレース、ログ記録のツールを使用して、Azure の Web アプリケーションのトラブルシューティングを行う方法について説明します。"
services: app-service
documentationcenter: .net
author: tdykstra
manager: erikre
editor: 
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.author: rachelap
translationtype: Human Translation
ms.sourcegitcommit: fcbd9e10e4cc336dc6ea37f84201249e14b1af91
ms.openlocfilehash: d22c9e2026c3efc63c5a3baa7ad4505aa269e31d
ms.lasthandoff: 12/08/2016


---
# <a name="troubleshoot-a-web-app-in-azure-app-service-using-visual-studio"></a>Visual Studio を使用した Azure App Service のトラブルシューティング
## <a name="overview"></a>概要
Visual Studio のツールを活用し、[App Service](http://go.microsoft.com/fwlink/?LinkId=529714) の Web アプリを[デバッグ モード](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx)でリモートから実行するか、アプリケーションのログと Web サーバーのログを参照することによってデバッグする方法を説明します。

[!INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

学習内容:

* Visual Studio から利用できる Azure Web アプリケーション管理機能。
* Visual Studio のリモート ビューを使用して、リモート Web アプリケーションにすぐに変更を加える方法。
* Azure でプロジェクトが実行されているときに、Web アプリケーションと Web ジョブ両方をリモートからデバッグ モードで実行する方法。
* アプリケーションのトレース ログを作成する方法と、ログが作成されている最中にそれらを確認する方法。
* Web サーバーのログ (詳細なエラー メッセージ、失敗した要求トレースを含む) を確認する方法。
* Azure のストレージ アカウントに診断ログを送り、そこでログを確認する方法。

Visual Studio Ultimate がある場合は、デバッグに [IntelliTrace](http://msdn.microsoft.com/library/vstudio/dd264915.aspx) を使用することもできます。 IntelliTrace については、このチュートリアルでは説明しません。

## <a name="prerequisites"></a>前提条件
このチュートリアルでは、[Azure と ASP.NET の使用][GetStarted]に関するページで設定した開発環境、Web プロジェクト、および Azure Web アプリを使用します。 Web ジョブのセクションでは、[Azure Web ジョブ SDK の使用][GetStartedWJ]に関するページで作成したアプリケーションが必要です。

このチュートリアルで示すコード サンプルは、C# MVC Web アプリケーションに対応していますが、トラブルシューティング手順は Visual Basic および Web フォームの各アプリケーションでも同じです。

このチュートリアルは、Visual Studio 2015 または 2013 の使用を前提としています。 Visual Studio 2013 を使用している場合、 [Update 4](http://go.microsoft.com/fwlink/?LinkID=510314) 以降が Web ジョブ機能の前提条件です。

ストリーミング ログ機能は、.NET Framework 4 以降を対象とするアプリケーションでのみ動作します。

## <a name="sitemanagement"></a>Web アプリの構成と管理
Visual Studio は、 [Azure ポータル](http://go.microsoft.com/fwlink/?LinkId=529715)で利用できる Web アプリ管理機能や構成設定に一部アクセスできるようになっています。 このセクションでは、 **サーバー エクスプローラー**を使用することで、その対象となる機能や設定について取り上げます。 最新の Azure の統合機能を確認するために、 **Cloud Explorer** もお試しください。 どちらのウィンドウも **[表示]** メニューから開くことができます。

1. まだ Visual Studio から Azure にサインインしていない場合は、**サーバー エクスプローラー**の **[Azure に接続]** をクリックします。

    または、アカウントへのアクセスを可能にする管理証明書をインストールします。 証明書をインストールする方針を選択した場合は、**サーバー エクスプローラー**で、**[Azure]** ノードを右クリックし、コンテキスト メニューの **[サブスクリプションの管理およびフィルター]** をクリックします。 **[Azure サブスクリプションの管理]** ダイアログ ボックスで、**[証明書]** タブをクリックし、**[インポート]** をクリックします。 操作手順に従い、Azure アカウント用のサブスクリプション ファイル ( *.publishsettings* ファイル) をダウンロードしてインポートします。

   > [!NOTE]
   > サブスクリプション ファイルをダウンロードする場合は、ソース コード ディレクトリの外にあるフォルダー (Downloads フォルダーなど) に保存し、インポートが完了したらそのファイルを削除します。 悪意のあるユーザーがサブスクリプション ファイルへのアクセス許可を取得すると、Azure サービスを編集、作成、削除できるためです。
   >
   >

    Visual Studio から Azure リソースへの接続の詳細については、「 [アカウント、サブスクリプション、管理ロールの管理](http://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)」を参照してください。
2. **サーバー エクスプローラー**で **[Azure]** を展開し、**[App Service]** を展開します。
3. [Azure と ASP.NET の使用][GetStarted]に関するページで作成した Web アプリを含むリソース グループを展開し、Web アプリ ノードを右クリックして、**[設定の表示]** をクリックします。

    ![サーバー エクスプローラーの [設定の表示]](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    **[Azure Web アプリ]** タブが表示され、Visual Studio から利用できる Web アプリ管理タスクや構成タスクが確認できます。

    ![Azure Web アプリ ウィンドウ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    このチュートリアルでは、ログとトレースのドロップダウンを使用します。 また、リモート デバッグも使用し、さまざまな方法でこの機能を有効にします。

    このウィンドウの [アプリケーション設定] ボックスと [接続文字列] ボックスについては、 [Azure Web Apps: アプリケーション文字列と接続文字列の動作](http://blogs.msdn.com/b/windowsazure/archive/2013/07/17/windows-azure-web-sites-how-application-strings-and-connection-strings-work.aspx)に関するページを参照してください。

    このウィンドウでは実行できない Web アプリ管理タスクを行う場合は、 **[管理ポータルで開く]** をクリックし、ブラウザー ウィンドウを開いて Azure ポータルにアクセスします。

## <a name="remoteview"></a>サーバー エクスプローラーでの Web アプリ ファイルへのアクセス
通常は、Web.config ファイルで `customErrors` フラグを `On` または `RemoteOnly` に設定して Web プロジェクトをデプロイします。これは、何か問題が発生したときに、役に立つエラー メッセージを表示しないことを意味します。 表示されるエラーの多くは、次のようなページになります。

**'/' アプリケーションのサーバー エラー:**

![あまり役に立たないエラー ページ](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**エラーが発生しました:**

![あまり役に立たないエラー ページ](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Web サイト側でページを表示できません**

![あまり役に立たないエラー ページ](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

多くの場合、エラーの原因を最も簡単に見つける方法は、詳細なエラー メッセージを有効にすることです。その結果、既に示した最初のスクリーンショットのように、対応方法が表示されます。 ここでは、デプロイした Web.config ファイルの変更が必要です。 プロジェクト内の *Web.config* ファイルを編集し、プロジェクトを再デプロイすること、または [Web.config の変換](http://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations)を作成し、デバッグ ビルドをデプロイすることもできますが、より簡単な方法があります。*リモート ビュー*機能を使用して、リモート Web アプリから**ソリューション エクスプローラー**でファイルを直接表示し、編集することができます。

1. **サーバー エクスプローラー**で、**[Azure]**、**[App Service]**、Web アプリがあるリソース グループ、Web アプリのノードの順に展開します。

    Web アプリケーションのコンテンツ ファイルとログ ファイルへのアクセス許可を付与するノードが表示されます。
2. **[ファイル]** ノードを展開し、 *[Web.config]* ファイルをダブルクリックします。

    ![[Web.config] を開く](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio は、リモート Web アプリケーションから Web.config ファイルを開き、タイトル バーではファイル名 の横に [リモート] と表示されています。
3. 次の行を `system.web` 要素に追加します。

    `<customErrors mode="Off"></customErrors>`

    ![Web.config の編集](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. あまり役に立たないエラー メッセージを示しているブラウザーを最新の情報に更新すると、次の例のような詳細なエラー メッセージが表示されます。

    ![詳細なエラー メッセージ](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (ここで示されているエラーは、赤く表示されている行を *Views\Home\Index.cshtml* に追加することによって作成したものです。)

Web.config ファイルを編集することは、Azure Web アプリケーションにあるファイルの読み取りと編集を可能にして、トラブルシューティングが容易にする状況における 1 つの例にすぎません。

## <a name="remotedebug"></a>Web アプリのリモート デバッグ
詳細なエラー メッセージで十分な情報が表示されておらず、エラーをローカルで再現できない場合は、トラブルシューティングの別の方法として、リモートでデバッグ モードを実行することができます。 ブレークポイントの設定、メモリの直接操作、コードのステップ実行、さらにコード パスの変更を実行できます。

リモート デバッグは、Visual Studio の各 Express Edition では機能しません。

このセクションでは、[Azure と ASP.NET の使用][GetStarted]に関するセクションで作成したプロジェクトを使用してリモート デバッグを実行する方法を示します。

1. [Azure と ASP.NET の使用][GetStarted]に関するセクションで作成した Web プロジェクトを開きます。
2. *Controllers\HomeController.cs* を開きます。
3. `About()` メソッドを削除し、その位置に次のコードを挿入します。

        public ActionResult About()
        {
            string currentTime = DateTime.Now.ToLongTimeString();
            ViewBag.Message = "The current time is " + currentTime;
            return View();
        }
4. `ViewBag.Message` の行に[ブレークポイントを設定](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx)します。
5. **ソリューション エクスプローラー**で目的のプロジェクトを右クリックし、**[発行]** をクリックします。
6. **[プロファイル]** ボックスの一覧から、[Azure と ASP.NET の使用][GetStarted]に関するページで使用したものと同じプロファイルを選択します。
7. **[設定]** タブをクリックして **[構成]** を **[デバッグ]** に変更し、**[発行]** をクリックします。

    ![デバッグ モードでの発行](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)
8. デプロイが完了し、ブラウザーが起動して Web アプリケーションの Azure URL が表示されたら、ブラウザーを閉じます。
9. **サーバー エクスプローラー**で、Web アプリを右クリックしてから **[デバッガーの接続]** をクリックします。

    ![[デバッガーの接続]](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    ブラウザーが自動的に起動し、Azure で実行されているホーム ページが表示されます。 デバッグに必要な設定を Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。 この待ち時間が生じるのは、Web アプリケーションでのデバッグ モードの初回実行時に限られます。 以後 48 時間は、デバッグを再度実行しても、待ち時間は生じません。

    **注:** デバッガーの起動時に問題がある場合、**サーバー エクスプローラー**ではなく**クラウド エクスプローラー**を使用して起動してみてください。
10. メニューの **[About]** をクリックします。

     Visual Studio がブレークポイントで停止します。コードが実行されている場所は、ローカル コンピューターではなく Azure 上です。
11. `currentTime` 変数にマウスを合わせて、時刻値を表示します。

     ![Azure で実行されているコード内の変数をデバッグ モードで表示](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

     表示される時刻は、Azure サーバーの時刻です。ローカル コンピューターとはタイム ゾーンが異なります。
12. `currentTime` 変数に新しい値 ("Now running in Azure" など) を入力します。
13. F5 キーを押して実行を継続します。

     Azure で実行中の [About] ページに、先ほど currentTime 変数に対して入力した新しい値が表示されます。

     ![[About] ページ (値の変更後)](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remotedebugwj"></a> Web ジョブのリモート デバッグ
このセクションでは、「 [Azure Web ジョブ SDK の使用](websites-dotnet-webjobs-sdk.md)」で作成したプロジェクトと Web アプリを使用してリモートでデバッグする方法を示します。

このセクションで示す機能は、Visual Studio 2013 Update 4 以降でのみ使用できます。

リモート デバッグは、継続的な Web ジョブでのみ動作します。 スケジュールされたオンデマンドの Web ジョブでは、デバッグはサポートされていません。

1. [Azure Web ジョブ SDK の使用][GetStartedWJ]に関するページで作成した Web プロジェクトを開きます。
2. ContosoAdsWebJob プロジェクトで、 *Functions.cs*を開きます。
3. `GnerateThumbnail` メソッドの最初のステートメントに[ブレークポイントを設定](http://www.visualstudio.com/get-started/debug-your-app-vs.aspx)します。

    ![ブレークポイントの設定](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)
4. **ソリューション エクスプローラー**で Web プロジェクト (Web ジョブ プロジェクトではない) を右クリックし、**[発行]** をクリックします。
5. **[プロファイル]** ボックスの一覧から、「 [Azure Web ジョブ SDK の使用](websites-dotnet-webjobs-sdk.md)」で使用したものと同じプロファイルを選択します。
6. **[設定]** タブをクリックして **[構成]** を **[デバッグ]** に変更し、**[発行]** をクリックします。

    Visual Studio によって Web プロジェクトと Web ジョブ プロジェクトがデプロイされ、ブラウザーで Web アプリケーションの Azure URL が表示されます。
7. **サーバー エクスプローラー**で、**[Azure]、[App Service]、[使用するリソース グループ]、[使用する Web アプリ]、[Web ジョブ]、[継続]** の順に展開し、**[ContosoAdsWebJob]** を右クリックします。
8. **[デバッガーの接続]**をクリックします。

    ![[デバッガーの接続]](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    ブラウザーが自動的に起動し、Azure で実行されているホーム ページが表示されます。 デバッグに必要な設定を Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。 この待ち時間が生じるのは、Web アプリケーションでのデバッグ モードの初回実行時に限られます。 次回のデバッガーの接続では、初回の実行から 48 時間以内の場合、待ち時間は発生しません。
9. Contoso Ads ホーム ページを表示している Web ブラウザーで、新しい広告を作成します。

    広告を作成すると、キュー メッセージが作成されます。キュー メッセージは Web ジョブによって取得され、処理されます。 Web ジョブ SDK がキュー メッセージを処理する関数を呼び出すと、コードがブレークポイントにヒットします。
10. デバッガーがブレークポイントで停止すると、プログラムがクラウドを実行している間に、変数の値を確認、変更することができます。 次の図では、デバッガーは、GenerateThumbnail メソッドに渡された blobInfo オブジェクトの内容を示しています。

     ![デバッガーの blobInfo オブジェクト](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)
11. F5 キーを押して実行を継続します。

     GenerateThumbnail メソッドはサムネイルの作成を完了します。
12. ブラウザーで、[インデックス] ページを更新して、サムネイルを表示します。
13. Visual Studio で、Shift キーを押しながら F5 キーを押すと、デバッグは停止します。
14. **サーバー エクスプローラー**で、ContosoAdsWebJob ノードを右クリックし、**[ダッシュボードの表示]** をクリックします。
15. Azure の資格情報を使用してサインインし、Web ジョブ名をクリックして、Web ジョブのページに移動します。

     ![ContosoAdsWebJob をクリック](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     GenerateThumbnail 関数が最近実行されたことが、ダッシュボードに示されます。

     (次回、**[ダッシュボードの表示]** をクリックするときには、サインインする必要はありません。ブラウザーが Web ジョブのページに直接移動します。)
16. 関数名をクリックすると、関数の実行について詳細が表示されます。

     ![機能の詳細](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

関数が [ログを書き込んだ](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)場合は、 **[ToggleOutput]** をクリックするとログを表示できます。

## <a name="notes-about-remote-debugging"></a>リモート デバッグに関する注意
* 運用環境におけるデバッグ モードの実行はお勧めできません。 運用環境の Web アプリケーションが複数のサーバー インスタンスにスケール アウトされていない場合、デバッグを行うと、Web サーバーが他の要求に応答できなくなります。 しかし、Web サーバーのインスタンスが複数存在する場合、デバッガーのアタッチ先となるインスタンスは無作為に決定されるため、そのインスタンスに後続のブラウザーの要求を確実に渡すことができません。 また、運用環境にデバッグ ビルドをデプロイすることも一般的ではありません。リリース ビルドに対してはコンパイラが最適化を行うため、ソース コードの状況を行レベルで把握することは不可能です。 運用環境の問題をトラブルシューティングするのに最も適しているリソースは、アプリケーション トレースと Web サーバーのログです。
* リモート デバッグ時、ブレークポイントで長時間停止させることは避けてください。 数分以上停止しているプロセスは、応答していないプロセスと見なされ、Azure によりシャットダウンされます。
* デバッグ中は、サーバーから Visual Studio にデータが送信されるため、帯域幅の使用料に影響が及ぶ可能性があります。 帯域幅使用料については、 [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を参照してください。
* *Web.config* ファイルの `compilation` 要素の `debug` 属性が true に設定されていることを確認します。 デバッグ ビルド構成で発行するときは、true が既定値です。

        <system.web>
          <compilation debug="true" targetFramework="4.5" />
          <httpRuntime targetFramework="4.5" />
        </system.web>
* デバッグ対象となるコードにデバッガーがステップ インしない場合、[マイ コードのみ] の設定を変更してみてください。  詳細については、「 [ステップ実行をマイ コードのみに制限する](http://msdn.microsoft.com/library/vstudio/y740d9d3.aspx#BKMK_Restrict_stepping_to_Just_My_Code)」を参照してください。
* リモート デバッグ機能を有効にしたときに、サーバー上でタイマーが開始され、48 時間後にこの機能が自動的に無効になります。 この 48 時間の上限はセキュリティとパフォーマンス上の理由で設定されています。 必要に応じて、この機能を何回でも簡単に有効に戻すことができます。 積極的にデバッグを実行している場合以外は、この機能を無効にしたままにすることをお勧めします。
* 手動でデバッガーを Web アプリケーション プロセス (w3wp.exe) だけでなく、任意のプロセスに接続できます。 Visual Studio のデバッグ モードの使い方の詳細については、MSDN のトピック「 [Visual Studio でのデバッグ](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx)」を参照してください。

## <a name="logsoverview"></a>診断ログの概要
Azure の Web アプリケーションで動作する ASP.NET アプリケーションでは、次の種類のログが作成されます。

* **アプリケーション トレース ログ**<br/>
  アプリケーションが [System.Diagnostics.Trace](http://msdn.microsoft.com/library/system.diagnostics.trace.aspx) クラスのメソッドを呼び出すことによって作成されます。
* **Web サーバー ログ**<br/>
  Web サーバーは、Web アプリに届くすべての HTTP 要求について、それぞれログ エントリを作成します。
* **詳細なエラー メッセージ ログ**<br/>
  失敗した HTTP 要求 (状態コードコード 400 以上の要求) について、より詳しい情報を記した HTML ページが Web サーバーによって作成されます。
* **失敗した要求トレース ログ**<br/>
  失敗した HTTP 要求についての詳しいトレース情報を記録した XML ファイルが Web サーバーによって作成されます。 また、ブラウザーで XML の体裁を設定するための XSL ファイルも作成されます。

ログ出力は Web アプリケーションのパフォーマンスに影響を及ぼすため、Azure では、必要に応じてログの種類ごとにその有効と無効を切り替えることができるようになっています。 アプリケーション ログについては、特定の重大度レベルを超えるログだけを記録するように指定できます。 新しい Web アプリケーションを作成した時点ではすべてのログが既定で無効になります。

ログは、Web アプリのファイル システムにあり、FTP 経由でアクセス可能な *LogFiles* フォルダー内のファイルに書き込まれます。 Web サーバーのログとアプリケーションのログは、Azure のストレージ アカウントに出力することもできます。 ストレージ アカウントには、ファイル システムよりも大量のログを保持することができます。 ファイル システムを使用した場合、保存できるログの上限は 100 MB です。 (ファイル システムのログは、短期間のみ保持されます。 上限に達すると、古いログ ファイルは削除され、新しいログ ファイルのための領域が確保されます。)  

## <a name="apptracelogs"></a>アプリケーションのトレース ログの作成と表示
このセクションでは、次のタスクを実行します。

* [Azure と ASP.NET の使用][GetStarted]に関するページで作成した Web プロジェクトに、トレース ステートメントを追加します。
* プロジェクトをローカル実行したときのログを確認します。
* Azure で実行中のアプリケーションによって生成されたログを確認します。

Web ジョブでアプリケーション ログを作成する方法については、「 [Web ジョブ SDK を使用して Azure キュー ストレージを操作する方法 - ログの記述方法](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#logs)」を参照してください。 ログの表示とログを Azure に格納する方法の制御に関する次の手順は、Web ジョブによって作成されたアプリケーション ログにも適用されます。

### <a name="add-tracing-statements-to-the-application"></a>アプリケーションへのトレース ステートメントの追加
1. `System.Diagnostics` の `Trace` ステートメントと `using` ステートメントを追加するために、*Controllers\HomeController.cs* を開き、`Index`、`About`、`Contact` のメソッドを次のコードで置き換えます。

        public ActionResult Index()
        {
            Trace.WriteLine("Entering Index method");
            ViewBag.Message = "Modify this template to jump-start your ASP.NET MVC application.";
            Trace.TraceInformation("Displaying the Index page at " + DateTime.Now.ToLongTimeString());
            Trace.WriteLine("Leaving Index method");
            return View();
        }

        public ActionResult About()
        {
            Trace.WriteLine("Entering About method");
            ViewBag.Message = "Your app description page.";
            Trace.TraceWarning("Transient error on the About page at " + DateTime.Now.ToShortTimeString());
            Trace.WriteLine("Leaving About method");
            return View();
        }

        public ActionResult Contact()
        {
            Trace.WriteLine("Entering Contact method");
            ViewBag.Message = "Your contact page.";
            Trace.TraceError("Fatal error on the Contact page at " + DateTime.Now.ToLongTimeString());
            Trace.WriteLine("Leaving Contact method");
            return View();
        }        
2. `using System.Diagnostics;` ステートメントをファイルの先頭に追加します。

### <a name="view-the-tracing-output-locally"></a>ローカルでのトレース出力の表示
1. F5 キーを押してデバッグ モードでアプリケーションを実行します。

    既定のトレース リスナーは、すべてのトレース出力を他のデバッグ出力と一緒に **出力** ウィンドウに書き込みます。 次の図は、 `Index` メソッドに追加したトレース ステートメントからの出力結果を示したものです。

    ![[デバッグ] ウィンドウでのトレース](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    以降の手順では、コンパイルせずにデバッグ モードで、トレース出力を Web ページに表示する方法を紹介します。
2. プロジェクト フォルダーにあるアプリケーションの Web.config ファイルを開き、ファイル末尾の終了 `</configuration>` 要素の直前に `<system.diagnostics>` 要素を追加します。

          <system.diagnostics>
            <trace>
              <listeners>
                <add name="WebPageTraceListener"
                    type="System.Web.WebPageTraceListener,
                    System.Web,
                    Version=4.0.0.0,
                    Culture=neutral,
                    PublicKeyToken=b03f5f7f11d50a3a" />
              </listeners>
            </trace>
          </system.diagnostics>

    `WebPageTraceListener` を使用すると、ブラウザーから `/trace.axd` にアクセスすることでトレース出力を表示できます。
3. Web.config ファイルの `<system.web>` に、次のような <a href="http://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">trace 要素</a>を追加します。

        <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
4. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。
5. ブラウザー ウィンドウのアドレス バーで、URL に続けて「*trace.axd*」と入力し、Enter キーを押します (例: http://localhost:53370/trace.axd)。
6. **[アプリケーション トレース]** ページの最初の行 (BrowserLink の行とは異なる) で、**[詳細の表示]** をクリックします。

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    **[要求の詳細]** ページが表示され、`Index` メソッドに追加したトレース ステートメントからの出力が **[トレース情報]** セクションに表示されます。

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    既定では、`trace.axd` の使用はローカルに限られます。 *Web.config* ファイルで `trace` 要素に `localOnly="false"` を追加すると、リモート Web アプリからも利用できるようになります。その例を次に示します。

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    ただしセキュリティ上の理由から、運用 Web アプリで `trace.axd` を有効にすることは、通常お勧めできません。もっと簡単に Azure の Web アプリのトレース ログを確認する方法を以降のセクションで説明します。

### <a name="view-the-tracing-output-in-azure"></a>Azure でのトレース出力の確認
1. **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、**[発行]** をクリックします。
2. **[Web の発行]** ダイアログ ボックスの **[発行]** をクリックします。

    更新したプロジェクトを発行すると、ブラウザー ウィンドウが起動して自分のホーム ページが表示されます (ただし、**[接続]** タブの **[宛先 URL]** をクリアした場合を除く)。
3. **サーバー エクスプローラー**で Web アプリを右クリックし、**[ストリーミング ログを表示]** を選択します。

    ![コンテキスト メニューからストリーミング ログを表示](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    ログ ストリーミング サービスに接続されたことを示すメッセージが**出力**ウィンドウに表示されます。ログが出力されないまま 1 分が経過すると、その都度、1 行の通知が追加されます。

    ![コンテキスト メニューからストリーミング ログを表示](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. アプリケーションのホーム ページが表示されているブラウザー ウィンドウで、 **[Contact]**をクリックします。

    数秒すると、 `Contact` メソッドに追加したエラー レベルのトレースが **出力** ウィンドウに表示されます。

    ![出力ウィンドウのエラー トレース](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio にエラー レベルのトレースしか表示されていないのは、それが、ログ監視サービスを有効にしたときの既定の設定であるためです。 先ほどの設定ページを開いたときに見たように、新しい Azure の Web アプリケーションを作成したときは、すべてのログが既定で無効となります。

    ![アプリケーション ログ記録がオフ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    ただし、**[ストリーミング ログを表示]** を選択すると、**[アプリケーション ログ記録 (ファイル システム)]** が自動的に **[エラー]** に変更されます。これは、エラー レベルのログが報告されることを意味します。 すべてのトレース ログを表示するためには、この設定を **[詳細]** に変更する必要があります。 "エラー" よりも低い重大度レベルを選択すると、その重大度を超えるログがすべて報告されます。 つまり [詳細] を選択した場合は、"情報"、"警告"、"エラー" に該当するログが表示されます。  

1. 先ほどと同じように、**サーバー エクスプローラー**で Web アプリを右クリックし、**[設定の表示]** をクリックします。
2. **[アプリケーション ログ記録 (ファイル システム)]** を **[詳細]** に変更し、**[保存]** をクリックします。

    ![トレース レベルを [詳細] に設定](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
3. **[連絡先]** ページが表示されているブラウザー ウィンドウで、**[ホーム]**、**[バージョン情報]**、**[連絡先]** を順にクリックします。

    数秒すると、 **出力** ウィンドウにすべてのトレース出力が表示されます。

    ![詳細レベルのトレース出力](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    このセクションでは、ログの有効と無効の切り替えを Azure の Web アプリケーションの設定で行いました。 トレース リスナーの有効と無効は、Web.config ファイルで設定することもできます。 ただし、Web.config ファイルに変更を加えると、アプリケーション ドメインの再利用処理が実行されます。一方、Web アプリケーションの構成を介してログを有効にした場合は、そのようにはなりません。 問題の再現に時間がかかる場合や、発生のタイミングが散発的である場合、アプリケーション ドメインの再利用処理で問題が "解消" し、再発までしばらく待たなければならなくなります。 Azure で診断を有効にすると、そのようなことにはならず、すぐにエラー情報を収集することができます。

### <a name="output-window-features"></a>出力ウィンドウの機能
**出力**ウィンドウの **[Azure ログ]** タブには、いくつかのボタンと 1 つのテキスト ボックスが表示されます。

![[ログ] タブのボタン](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-icons.png)

それぞれの機能を次に示します。

* **出力** ウィンドウをクリアする。
* 右端での折り返しを有効または無効にする。
* ログの監視を開始または停止する。
* 監視するログを指定する。
* ログをダウンロードする。
* 検索文字列または正規表現によるフィルターをログに適用する。
* **出力** ウィンドウを閉じる。

検索文字列または正規表現を入力した場合、ログ情報は、Visual Studio によってクライアント側でフィルタリングされます。 つまり、フィルターの条件は、**出力**ウィンドウにログが表示された後に入力できます。フィルターの条件を変更するためにログを生成し直す必要はありません。

## <a name="webserverlogs"></a>Web サーバーのログの表示
Web サーバーのログには、Web アプリケーション上の HTTP アクティビティがすべて記録されます。 それらの HTTP アクティビティを **出力** ウィンドウに表示するには、Web アプリに対してその機能を有効にしたうえで、HTTP アクティビティを監視するための指定を Visual Studio に対して行う必要があります。

1. **サーバー エクスプローラー**から開いた **Azure Web アプリの [構成]** タブで [Web サーバーのログ記録] を **[オン]** に変更し、**[保存]** をクリックします。

    ![Web サーバーのログ記録を有効にする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. **出力**ウィンドウの **[監視する Azure ログの指定]** をクリックします。

    ![[監視する Azure ログの指定]](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. **[Azure ログ オプション]** ダイアログ ボックスの **[Web サーバーのログ]** を選択し、**[OK]** をクリックします。

    ![Web サーバーのログを監視する](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. Web アプリを表示するブラウザー ウィンドウで、**[ホーム]**、**[バージョン情報]**、**[連絡先]** の順にクリックします。

    通常はアプリケーションのログが先に表示され、続けて Web サーバーのログが表示されます。 ログが表示されるまでにしばらく時間がかかる場合があります。

    ![出力ウィンドウの Web サーバーのログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Visual Studio で Web サーバーのログを初めて有効にしたとき、既定では、Azure によってファイル システムにログが書き込まれます。 代わりに、Azure ポータルを使用して、Web サーバーのログの書き込み先として、ストレージ アカウントの BLOB コンテナーを指定することもできます。

Azure ポータルを使用して、Azure ストレージ アカウントへの Web サーバーのログ記録を有効にした後、Visual Studio でログ記録を無効にした場合、Visual Studio でログ記録を再度有効にすると、ストレージ アカウントの設定が復元されます。

## <a name="detailederrorlogs"></a>詳細なエラー メッセージ ログの表示
詳細なエラー ログでは、エラー応答コード (400 以上) が返された HTTP 要求について、いくつかの詳しい情報が確認できます。 それらの HTTP 要求を **出力** ウィンドウに表示するには、Web アプリに対してその機能を有効にしたうえで、HTTP 要求を監視するための指定を Visual Studio に対して行う必要があります。

1. **サーバー エクスプローラー**から開いた **Azure Web アプリの [構成]** タブで **[詳細なエラー メッセージ]** を **[オン]** に変更し、**[保存]** をクリックします。

    ![詳細なエラー メッセージを有効にする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)
2. **出力**ウィンドウの **[監視する Azure ログの指定]** をクリックします。
3. **[Azure ログ オプション]** ダイアログ ボックスの **[すべてのログ]** をクリックし、**[OK]** をクリックします。

    ![すべてのログを監視する](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)
4. ブラウザー ウィンドウのアドレス バーで、404 エラーの原因となるような余分な文字を URL に追加し (例: `http://localhost:53370/Home/Contactx`)、Enter キーを押します。

    数秒後、Visual Studio の **出力** ウィンドウに詳細なエラー ログが表示されます。

    ![出力ウィンドウの詳細なエラー ログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Ctrl キーを押しながらリンクをクリックすると、次のような書式化されたログ出力がブラウザーに表示されます。

    ![ブラウザー ウィンドウの詳細なエラー ログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="downloadlogs"></a>ファイル システムのログのダウンロード
**出力** ウィンドウで監視できるすべてのログは *.zip* ファイルとしてダウンロードすることもできます。

1. **出力**ウィンドウの **[ストリーミング ログのダウンロード]** をクリックします。

    ![[ログ] タブのボタン](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    エクスプローラーが起動して *[ダウンロード]* フォルダーが開き、ダウンロード済みのファイルが選択状態で表示されます。

    ![ダウンロードされたファイル](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. この *.zip* ファイルを展開すると、次のフォルダー構造が確認できます。

    ![ダウンロードされたファイル](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * アプリケーション トレース ログは、*LogFiles\Application* フォルダーの *.txt* ファイルに記録されます。
   * Web サーバーのログは、*LogFiles\http\RawLogs* フォルダーの *.log* ファイルに記録されます。 これらのファイルの閲覧と操作は、 [Log Parser](http://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) などのツールを使って行うことができます。
   * 詳細なエラー メッセージのログは、*LogFiles\DetailedErrors* フォルダーの *.html* ファイルに記録されます。

     (*deployments* フォルダーは、ソース管理の発行によって作成されたファイルに使用されます。Visual Studio の発行に関連したファイルは保存されません。 *Git* フォルダーは、ログ ファイル ストリーミング サービスやソース管理の発行に関連したトレースに使用されます。)  

## <a name="storagelogs"></a>ストレージ ログの表示
アプリケーション トレース ログを Azure のストレージ アカウントに送って、Visual Studio で表示することもできます。 そのためには、ストレージ アカウントを作成してクラシック ポータルでストレージ ログを有効にし、**[Azure Web アプリ]** ウィンドウの **[ログ]** タブにストレージ ログを表示します。

次の 3 個の送信先のいずれかまたはすべてにログを送信することができます。

* ファイル システム。
* ストレージ アカウント テーブル。
* ストレージ アカウント BLOB。

送信先ごとに異なる重要度レベルを指定することもできます。

テーブル形式を使用すると、ログの詳細をオンラインで簡単に表示でき、またストリーミングもサポートされています。テーブル内にあるログを照会することや、作成されている新しいログを表示することもできます。 BLOB 形式を使用すると、ログをファイルにダウンロードし、HDInsight を使ってそれらのログを簡単に解析できるようになります。HDInsight は、BLOB ストレージを処理する方法を把握しているためです。 詳細については、[データ ストレージ オプション (Azure を使用した実際のクラウド アプリケーションの作成) に関するページ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options)の「**Hadoop and MapReduce**」を参照してください。

現在、ファイル システムのログは詳細レベルに設定されています。次の手順では、ストレージ アカウント テーブルに切り替える目的で情報レベルのログを設定する方法を説明します。 情報レベルを指定した場合、`Trace.TraceInformation`、`Trace.TraceWarning`、および `Trace.TraceError` の呼び出しで作成されたすべてのログが表示され、`Trace.WriteLine` で作成されたログは除外されます。

多くの場合、ストレージ アカウント形式はファイル システム形式と比較して、ログに対してより多くのストレージと、より長い期間にわたる保持を実現します。 アプリケーション トレース ログをストレージに送る別の利点は、ファイル システムのログでは得ることのできない詳しい情報をそれぞれのログと共に入手できることです。

1. Azure ノードの **[Storage]** を右クリックし、**[ストレージ アカウントの作成]** をクリックします。

![[ストレージ アカウントを作成]](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. **[ストレージ アカウントの作成]** ダイアログ ボックスで、ストレージ アカウントの名前を入力します。

    名前は一意である必要があります (Azure ストレージ アカウントで同じ名前は使用できません)。 入力した名前が既に使用されている場合、変更を求められます。

    ストレージ アカウントにアクセスするための URL は、 *{name}*.core.windows.net 形式になります。
2. **[リージョン/アフィニティ グループ]** ボックスの一覧で、現在の所在地に最も近いリージョンを選択します。

    この設定は、お使いのストレージ アカウントをホストしている Azure データセンターを指定します。 このチュートリアルでは、選択肢によって顕著な相違は生じませんが、運用 Web アプリケーションの場合は、待ち時間とデータ送信料金を最小限に抑えるために、Web サーバーとストレージ アカウントを同じリージョンに設定することが望まれます。 待ち時間を最小限にするには、Web アプリケーション (後で作成する) を Web アプリケーションにアクセスするブラウザーのできるだけ近くで実行します。
3. **[レプリケーション]** ボックスを **[ローカル冗長]** に設定します。
   
    Geo レプリケーションをストレージ アカウントに対して有効にすると、1 次拠点で重大な障害が発生した場合に備えて、保存したコンテンツは 2 次データセンターに複製されて、フェイルオーバーが可能になります。 Geo レプリケーションには追加費用が発生する場合があります。 また、テストおよび開発アカウントの場合は、一般的に Geo レプリケーションに対する課金は避けたいと考えるでしょう。 詳細については、「 [ストレージ アカウントの作成、管理、削除](../storage/storage-create-storage-account.md)」を参照してください。
4. **[作成]**をクリックします。

    ![新しいストレージ アカウント ](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. Visual Studio の **[Azure Web アプリ]** ウィンドウで、**[ログ]** タブをクリックし、**[管理ポータルでのログの構成]** をクリックします。

    <!-- todo:screenshot of new portal if the VS page link goes to new portal -->
    ![ログの構成](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    これにより、クラシック ポータルで Web アプリの **[構成]** タブが開きます。
6. クラシック ポータルの **[構成]** タブで、下へスクロールしてアプリケーション診断セクションを表示し、**[アプリケーション ログ記録 (テーブル ストレージ)]** を **[オン]** に変更します。
7. **[ログ レベル]** を **[情報]** に変更します。
8. **[テーブル ストレージの管理]**をクリックします。

    ![[テーブル ストレージの管理] をクリックします。](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    ストレージ アカウントが複数ある場合は、 **[アプリケーション診断用のテーブル ストレージの管理]** ボックスで目的のストレージ アカウントを選択できます。 新しいテーブルを作成するか、既存のテーブルを使用することができます。

    ![[テーブル ストレージの管理]](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. **[アプリケーション診断用のテーブル ストレージの管理]** ボックスのチェック マークをクリックしてこのボックスを閉じます。
10. クラシック ポータルの **[構成]** タブで、**[保存]** をクリックします。
11. アプリケーションの Web アプリを表示するブラウザー ウィンドウで、**[ホーム]**、**[バージョン情報]**、**[連絡先]** の順にクリックします。

     これらの Web ページを閲覧することによって生成されたログ情報がストレージ アカウントに書き込まれます。
12. Visual Studio の **[Azure Web アプリ]** ウィンドウの **[ログ]** タブで、**[診断の概要]** の下に表示される **[最新の情報に更新]** をクリックします。

     ![[最新の情報に更新] をクリックする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     既定では、直近 15 分間のログが **[診断の概要]** セクションに表示されます。 この時間を変更することで、表示するログの量を増やすことができます。

     ("テーブルが見つからない" という内容のエラーが表示された場合は、**[アプリケーション ログ記録 (Storage)]** を有効にした後と、**[保存]** をクリックした後に、トレース対象のページを参照したことを確認してください。)

     ![ストレージ ログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     この画面に **[プロセス ID]** と **[スレッド ID]** がログごとに表示されていることに注目してください。ファイル システムのログでは確認できない情報です。 Azure のストレージ テーブルを直接参照することで、さらに別のフィールドを確認できます。
13. **[すべてのアプリケーション ログの表示]**をクリックします。

     Azure のストレージ テーブル ビューアーにトレース ログ テーブルが表示されます。

     ("シーケンスに要素が含まれていません" というエラーが表示された場合は、**サーバー エクスプローラー**を開き、**Azure** ノードで、自分のストレージ アカウントに該当するノードを展開し、**[テーブル]** を右クリックして **[最新の情報に更新]** をクリックします。)

     ![テーブル形式で表示されたストレージ ログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     この画面には、他の画面では確認できないフィールドが表示されます。 専用のクエリ ビルダー UI を使用してクエリを作成し、ログにフィルターを適用することもできます。 詳細については、「 [サーバー エクスプローラーを使用したストレージ リソースの参照](http://msdn.microsoft.com/library/ff683677.aspx)」の「テーブル リソースの操作」にある「エンティティのフィルター処理」を参照してください。
14. 行の詳細を見るには、行の 1 つをダブルクリックします。

     ![サーバー エクスプローラーのトレース テーブル](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)

## <a name="failedrequestlogs"></a>失敗した要求トレース ログの表示
失敗した要求トレース ログは、URL の書き換えや認証の問題など、IIS による HTTP 要求の処理を詳しく把握する必要がある状況で活用できます。

Azure の Web アプリでは、同じ失敗した要求トレース機能が使用されています。この機能は、IIS 7.0 以降で利用できます。 ただし、ログに記録するエラーを指定するための IIS 設定にアクセスする必要はありません。 失敗した要求トレースを有効にすると、すべてのエラーがキャプチャされます。

失敗した要求トレースは Visual Studio を使用して有効にできますが、それらを Visual Studio で表示することはできません。 これらのログは XML ファイル形式になっています。 ストリーミング ログ サービスで監視されるのは、プレーンテキスト モードでの読み取りが可能と判断されたファイル (*.txt*、*.html*、*.log* の各ファイル) だけです。

失敗した要求トレース ログは、ブラウザーから FTP で直接表示できるほか、FTP ツールを使ってローカル コンピューターにダウンロードした後、ローカルで表示することもできます。 このセクションでは、ブラウザーで直接閲覧する方法を説明します。

1. **サーバー エクスプローラー**から開いた **[Azure Web アプリ]** ウィンドウの **[構成]** タブで、**[失敗した要求トレース]** を **[オン]** に変更し、**[保存]** をクリックします。

    ![失敗した要求トレースを有効にする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. Web アプリケーションが表示されているブラウザー ウィンドウのアドレス バーで、余分な文字を URL に追加し、Enter キーを押して 404 エラーを発生させます。

    これにより、失敗した要求トレース ログが作成されます。そのログを表示またはダウンロードする方法については、以降の手順で説明します。
3. Visual Studio で、**[Azure Web アプリ]** ウィンドウの **[構成]** タブにある **[管理ポータルで開く]** をクリックします。
4. Web アプリの [Azure Portal](https://portal.azure.com) の **[設定]** ブレードで **[デプロイ資格情報]** をクリックし、新しいユーザー名とパスワードを入力します。

    ![新しい FTP ユーザー名とパスワード](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    **ログインする際は、完全なユーザー名とその Web アプリ名プレフィックスを使用する必要があります。 たとえば、ユーザー名として「myid」と入力し、サイトが "myexample" の場合、"myexample\myid" としてログインします。
5. 新しいブラウザー ウィンドウで、Web アプリの **[Web アプリ]** ブレードの **[FTP ホスト名]** または **[FTPS ホスト名]** に表示されている URL に移動します。
6. 先ほど作成した FTP 資格情報を使用してログインします (ユーザー名の Web アプリケーション名プレフィックスを含めること)。

    Web アプリケーションのルート フォルダーがブラウザーに表示されます。
7. *LogFiles* フォルダーを開きます。

    ![LogFiles フォルダーを開く](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)
8. W3SVC に数値の付いた名前のフォルダーを開きます。

    ![W3SVC フォルダーを開く](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    このフォルダーには、失敗した要求トレースを有効にした後で記録されたすべてのエラーの XML ファイルに加え、ブラウザーで XML の体裁を設定するための XSL ファイルが格納されています。

    ![W3SVC フォルダー](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)
9. トレース情報を確認する失敗した要求の XML ファイルをクリックします。

    次の図は、サンプル エラーのトレース情報を部分的に示したものです。

    ![失敗した要求トレースをブラウザーで表示したところ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="nextsteps"></a>次のステップ
Azure の Web アプリケーションで作成されたログは Visual Studio を使って簡単に参照できることが確認できました。 次のセクションでは、関連トピックに関する他のリソースへのリンクを紹介します。

* Azure の Web アプリケーションのトラブルシューティング
* Visual Studio でのデバッグ
* Azure でのリモート デバッグ
* ASP.NET アプリケーションでのトレース
* Web サーバーのログの分析
* 失敗した要求トレース ログの分析
* Cloud Services のデバッグ

### <a name="azure-web-app-troubleshooting"></a>Azure の Web アプリケーションのトラブルシューティング
Azure App Service の Web アプリのトラブルシューティングの詳細については、以下のリソースを参照してください。

* [Web アプリを監視する方法](/manage/services/web-sites/how-to-monitor-websites/)
* [Visual Studio 2013 を使用した Azure の Web Apps でのメモリ リークの調査](http://blogs.msdn.com/b/visualstudioalm/archive/2013/12/20/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013.aspx)。 マネージされるメモリの問題の分析に役立つ Visual Studio の機能に関する Microsoft ALM のブログ記事
* [Azure web apps online tools you should know about (知っておくべき Azure Web アプリのオンライン ツール)](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/)。 Amit Apple によるブログの投稿です。

具体的なトラブルシューティングについての質問は、次のいずれかのフォーラムで投稿してください。

* [ASP.NET サイト上の Azure フォーラム](http://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET)。
* [MSDN 上の Azure フォーラム](http://social.msdn.microsoft.com/Forums/windowsazure/)。
* [StackOverflow.com](http://www.stackoverflow.com)。

### <a name="debugging-in-visual-studio"></a>Visual Studio でのデバッグ
Visual Studio のデバッグ モードの使い方については、MSDN のトピック「[Visual Studio でのデバッグ](http://msdn.microsoft.com/library/vstudio/sc65sadd.aspx)」と [Visual Studio 2010 でのデバッグのヒントに関するページ](http://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)を参照してください。

### <a name="remote-debugging-in-azure"></a>Azure でのリモート デバッグ
Azure の Web アプリと Web ジョブのリモート デバッグの詳細については、以下のリソースを参照してください。

* [Introduction to Remote Debugging Azure App Service Web Apps (Azure App Service Web Apps のリモート デバッグの概要)](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/)。
* [Introduction to Remote Debugging Azure App Service Web Apps part 2 - Inside Remote debugging (Azure App Service Web Apps のリモート デバッグの概要 2 - リモート デバッグの内部処理)](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduction to Remote Debugging on Azure App Service Web Apps part 3 - Multi-Instance environment and GIT (Azure App Service Web Apps のリモート デバッグの概要 3 - マルチインスタンス環境と GIT)](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs Debugging (Web ジョブのデバッグ) (ビデオ)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

Web アプリで Azure Web API または Mobile Services バックエンドを使用し、デバッグを実行する必要がある場合は [Visual Studio での .NET のデバッグに関するページ](http://blogs.msdn.com/b/azuremobile/archive/2014/03/14/debugging-net-backend-in-visual-studio.aspx)を参照してください。

### <a name="tracing-in-aspnet-applications"></a>ASP.NET アプリケーションでのトレース
ASP.NET トレースに関しては、最新かつ必要な情報をすべて網羅した解説がインターネットには存在しません。 そのため、過去に作成された入門者向けの資料を参考にするのが最善の方法となります。MVC がまだ存在していなかったために Web フォームを想定して書かれていますが、具体的な問題については、最新のブログで情報を補うことができます。 たとえば、以下のリソースが参考になります。

* [監視と利用統計情報 (Azure での実際のクラウド アプリケーションのビルド) に関するページ](http://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)。<br>
  Azure クラウド アプリケーションをトレースするためのベスト プラクティスを掲載した E-Book の章。
* [ASP.NET トレース](http://msdn.microsoft.com/library/ms972204.aspx)<br/>
  最新とは言えませんが、基本的な事柄がわかりやすくまとめられています。
* [トレース リスナー](http://msdn.microsoft.com/library/4y5y10s7.aspx)<br/>
  トレース リスナーについて書かれていますが、[WebPageTraceListener](http://msdn.microsoft.com/library/system.web.webpagetracelistener.aspx) には触れていません。
* [チュートリアル: ASP.NET トレースと System.Diagnostics トレースの統合](http://msdn.microsoft.com/library/b0ectfxd.aspx)<br/>
  これも古い情報ですが、入門記事では扱っていないような詳しい情報が記載されています。
* [ASP.NET MVC Razor ビューでのトレース](http://blogs.msdn.com/b/webdev/archive/2013/07/16/tracing-in-asp-net-mvc-razor-views.aspx)<br/>
  Razor ビューでのトレースに加え、MVC アプリケーションでハンドルされない例外をすべてログに記録するためのエラー フィルターの作成方法についても説明されています。 Web フォーム アプリケーションで、ハンドルされない例外をすべてログに記録する方法については、MSDN の「[エラー ハンドラーの完全なコード例](http://msdn.microsoft.com/library/bb397417.aspx)」で紹介されている Global.asax サンプルを参照してください。 MVC または Web フォームで、特定の例外をログに記録すると共に、既定のフレームワークの処理はそのまま活かしておく必要がある場合、例外を捕捉してから再スローする方法を利用できます。その例を次に示します。

        try
        {
           // Your code that might cause an exception to be thrown.
        }
        catch (Exception ex)
        {
            Trace.TraceError("Exception: " + ex.ToString());
            throw;
        }
* [Azure コマンド ラインからの診断トレース ログのストリーミングと Glimpse に関する情報](http://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  このチュートリアルで Visual Studio を使って行ったことをコマンド ラインで行う方法が解説されています。 [Glimpse](http://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) は、ASP.NET アプリケーションをデバッグするためのツールです。
* [Web Apps のログと診断の使用に関するページ - David Ebbo 作成](/documentation/videos/azure-web-site-logging-and-diagnostics/)、および [Web Apps からのログのストリーミングに関するページ - David Ebbo 作成](/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Scott Hanselman と David Ebbo によるビデオ。

エラーをログに記録する方法としては、独自のトレース コードを記述する以外にも、 [ELMAH](http://nuget.org/packages/elmah/)のようなオープン ソースのログ記録フレームワークを使う方法があります。 詳細については、 [Scott Hanselman が ELMAH についてまとめたブログ記事](http://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)を参照してください。

Azure からストリーミング ログを取得する必要がある場合、必ずしも ASP.NET や System.Diagnostics トレースを使う必要はありません。 Azure Web アプリのストリーミング ログ サービスは、*LogFiles* フォルダーに見つかったすべての *.txt* ファイル、*.html* ファイル、*.log* ファイルをストリーミングします。 したがって、Web アプリケーションのファイル システムに書き込む独自のログ記録システムを作成することもできます。必要なファイルが自動的にストリーミングされ、ダウンロードされます。 必要な作業は、*d:\home\logfiles* フォルダーにファイルを作成するアプリケーション コードを記述するだけです。

### <a name="analyzing-web-server-logs"></a>Web サーバーのログの分析
Web サーバーのログの分析の詳細については、次のリソースを参照してください。

* [LogParser](http://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Web サーバーのログ (*.log* ファイル) に記録されているデータを表示するためのツールです。
* [IIS のパフォーマンスの問題やアプリケーション エラーを LogParser でトラブルシューティングする](http://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Web サーバーのログを分析する際に活用できる Log Parser ツールについて基本的な事柄が説明されています。
* [LogParser の使用に関して Robert McMurray が執筆したブログ記事](http://blogs.msdn.com/b/robert_mcmurray/archive/tags/logparser/)<br/>
* [IIS 7.0、IIS 7.5、IIS 8.0 における HTTP 状態コード](http://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>失敗した要求トレース ログの分析
失敗した要求トレース ログの活用方法については、Microsoft TechNet Web サイトの「[Using Failed Request Tracing (失敗した要求トレースの使用)](http://www.iis.net/learn/troubleshoot/using-failed-request-tracing)」セクションなどが参考になります。 ただし、このドキュメントで扱う内容は、失敗した要求トレースを IIS で構成する作業が主体です。この作業を Azure Web Apps で行うことはできません。

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: websites-dotnet-webjobs-sdk.md

