---
title: Visual Studio を使用したトラブルシューティング
description: Visual Studio 2013 に組み込まれているリモート デバッグ、トレース、ログ記録のツールを使用して、App Service アプリのトラブルシューティングを行う方法について説明します。
ms.assetid: def8e481-7803-4371-aa55-64025d116c97
ms.devlang: dotnet
ms.topic: article
ms.date: 08/29/2016
ms.custom: seodec18
ms.openlocfilehash: 516c7f50f7ff9fe947475b12120a527fc69353bc
ms.sourcegitcommit: a6d477eb3cb9faebb15ed1bf7334ed0611c72053
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2020
ms.locfileid: "82926852"
---
# <a name="troubleshoot-an-app-in-azure-app-service-using-visual-studio"></a>Visual Studio を使用した Azure App Service のアプリのトラブルシューティング
## <a name="overview"></a>概要
このチュートリアルでは、Visual Studio Tools を活用し、[App Service](https://go.microsoft.com/fwlink/?LinkId=529714) のアプリを[デバッグ モード](https://docs.microsoft.com/visualstudio/debugger/)でリモートから実行するか、アプリケーションのログと Web サーバーのログを参照することによってデバッグする方法を説明します。

学習内容:

* Visual Studio から利用できるアプリ管理機能。
* Visual Studio のリモート ビューを使用して、リモート アプリをすばやく変更する方法。
* Azure でプロジェクトが実行されているときに、アプリと WebJobs の両方をリモートからデバッグ モードで実行する方法。
* アプリケーションのトレース ログを作成する方法と、ログが作成されている最中にそれらを確認する方法。
* Web サーバーのログ (詳細なエラー メッセージ、失敗した要求トレースを含む) を確認する方法。
* Azure のストレージ アカウントに診断ログを送り、そこでログを確認する方法。

Visual Studio Ultimate がある場合は、デバッグに [IntelliTrace](/visualstudio/debugger/intellitrace) を使用することもできます。 IntelliTrace については、このチュートリアルでは説明しません。

## <a name="prerequisites"></a><a name="prerequisites"></a>前提条件
このチュートリアルでは、[Azure App Service での ASP.NET アプリの作成](app-service-web-get-started-dotnet-framework.md)に関するページで設定した開発環境、Web プロジェクト、および App Service アプリを使用します。 WebJobs のセクションでは、[Azure WebJobs SDK の使用][GetStartedWJ]に関するページで作成したアプリケーションが必要です。

このチュートリアルで示すコード サンプルは、C# MVC Web アプリケーションに対応していますが、トラブルシューティング手順は Visual Basic および Web フォームの各アプリケーションでも同じです。

このチュートリアルは、Visual Studio 2019 の使用を前提としています。 

ストリーミング ログ機能は、.NET Framework 4 以降を対象とするアプリケーションでのみ動作します。

## <a name="app-configuration-and-management"></a><a name="sitemanagement"></a>アプリの構成と管理
Visual Studio は、[Azure portal](https://go.microsoft.com/fwlink/?LinkId=529715) で利用できるアプリ管理機能や構成設定に一部アクセスできるようになっています。 このセクションでは、**サーバー エクスプローラー**を使用することで、その対象となる機能や設定について取り上げます。 最新の Azure の統合機能を確認するために、 **Cloud Explorer** もお試しください。 どちらのウィンドウも **[表示]** メニューから開くことができます。

1. まだ Visual Studio で Azure にサインインしていない場合は、**サーバー エクスプ ローラー**で、 **[Azure]** を右クリックし、**Microsoft Azure サブスクリプション**への接続を選択します。

    または、アカウントへのアクセスを可能にする管理証明書をインストールします。 証明書をインストールする方針を選択した場合は、**サーバー エクスプローラー**で、 **[Azure]** ノードを右クリックし、コンテキスト メニューの **[サブスクリプションの管理およびフィルター]** を選択します。 **[Microsoft Azure サブスクリプションの管理]** ダイアログ ボックスで、 **[証明書]** タブをクリックし、 **[インポート]** をクリックします。 操作手順に従い、Azure アカウント用のサブスクリプション ファイル ( *.publishsettings* ファイル) をダウンロードしてインポートします。

   > [!NOTE]
   > サブスクリプション ファイルをダウンロードする場合は、ソース コード ディレクトリの外にあるフォルダー (Downloads フォルダーなど) に保存し、インポートが完了したらそのファイルを削除します。 悪意のあるユーザーがサブスクリプション ファイルへのアクセス許可を取得すると、Azure サービスを編集、作成、削除できるためです。
   >
   >

    Visual Studio から Azure リソースへの接続の詳細については、「 [アカウント、サブスクリプション、管理ロールの管理](https://go.microsoft.com/fwlink/?LinkId=324796#BKMK_AccountVCert)」を参照してください。
2. **サーバー エクスプローラー**で **[Azure]** を展開し、 **[App Service]** を展開します。
3. [Azure App Service での ASP.NET Web アプリの作成](app-service-web-get-started-dotnet-framework.md)に関するページで作成したアプリを含むリソース グループを展開し、アプリ ノードを右クリックして、 **[設定の表示]** をクリックします。

    ![サーバー エクスプローラーの [設定の表示]](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewsettings.png)

    **[Azure Web アプリ]** タブが表示され、Visual Studio から利用できるアプリ管理タスクや構成タスクが確認できます。

    ![Azure Web アプリ ウィンドウ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configtab.png)

    このチュートリアルでは、ログとトレースのドロップダウンを使用します。 また、リモート デバッグも使用し、さまざまな方法でこの機能を有効にします。

    このウィンドウの [アプリ設定] ボックスの一覧と [接続文字列] ボックスの一覧については、「[Azure App Service:How Application Strings and Connection Strings Work (アプリケーション文字列と接続文字列の動作)](https://azure.microsoft.com/blog/windows-azure-web-sites-how-application-strings-and-connection-strings-work/)」をご覧ください。

    このウィンドウでは実行できないアプリ管理タスクを行う場合は、 **[管理ポータルで開く]** をクリックし、ブラウザー ウィンドウを開いて Azure portal にアクセスします。

## <a name="access-app-files-in-server-explorer"></a><a name="remoteview"></a>サーバー エクスプローラーでのアプリ ファイルへのアクセス
通常は、Web.config ファイルで `customErrors` フラグを `On` または `RemoteOnly` に設定して Web プロジェクトをデプロイします。これは、何か問題が発生したときに、役に立つエラー メッセージを表示しないことを意味します。 表示されるエラーの多くは、次のいずれかのようなページになります。

**'/' アプリケーションのサーバー エラー:**

![あまり役に立たないエラー ページ](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror.png)

**エラーが発生しました:**

![あまり役に立たないエラー ページ](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror1.png)

**Web サイト側でページを表示できません**

![あまり役に立たないエラー ページ](./media/web-sites-dotnet-troubleshoot-visual-studio/genericerror2.png)

多くの場合、エラーの原因を最も簡単に見つける方法は、詳細なエラー メッセージを有効にすることです。その結果、既に示した最初のスクリーンショットのように、対応方法が表示されます。 ここでは、デプロイした Web.config ファイルの変更が必要です。 プロジェクト内の *Web.config* ファイルを編集し、プロジェクトを再デプロイすること、または [Web.config の変換](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations)を作成し、デバッグ ビルドをデプロイすることもできますが、より簡単な方法があります。**ソリューション エクスプローラー**で、*リモート ビュー*機能を使用して、リモート アプリからファイルを直接表示し、編集することができます。

1. **サーバー エクスプローラー**で、 **[Azure]** 、 **[App Service]** 、アプリがあるリソース グループ、アプリのノードの順に展開します。

    アプリのコンテンツ ファイルとログ ファイルへのアクセス許可を付与するノードが表示されます。
2. **[ファイル]** ノードを展開し、 *[Web.config]* ファイルをダブルクリックします。

    ![[Web.config] を開く](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfig.png)

    Visual Studio は、リモート アプリから Web.config ファイルを開き、タイトル バーのファイル名 の横に [リモート] を表示します。
3. 次の行を `system.web` 要素に追加します。

    `<customErrors mode="Off"></customErrors>`

    ![Web.config の編集](./media/web-sites-dotnet-troubleshoot-visual-studio/webconfigedit.png)
4. あまり役に立たないエラー メッセージを示しているブラウザーを最新の情報に更新すると、次の例のような詳細なエラー メッセージが表示されます。

    ![詳細なエラー メッセージ](./media/web-sites-dotnet-troubleshoot-visual-studio/detailederror.png)

    (ここで示されているエラーは、赤く表示されている行を *Views\Home\Index.cshtml* に追加することによって作成したものです。)

Web.config ファイルの編集は、App Service アプリにあるファイルの読み取りと編集を可能にしてトラブルシューティングを容易にするシナリオの一例にすぎません。

## <a name="remote-debugging-apps"></a><a name="remotedebug"></a>アプリのリモート デバッグ
詳細なエラー メッセージで十分な情報が表示されておらず、エラーをローカルで再現できない場合は、トラブルシューティングの別の方法として、リモートでデバッグ モードを実行することができます。 ブレークポイントの設定、メモリの直接操作、コードのステップ実行、さらにコード パスの変更を実行できます。

リモート デバッグは、Visual Studio の各 Express Edition では機能しません。

ここでは、[Azure App Service での ASP.NET アプリの作成](app-service-web-get-started-dotnet-framework.md)に関するページで作成したプロジェクトを使用して、リモートでデバッグする方法について説明します。

1. [Azure App Service での ASP.NET アプリの作成](app-service-web-get-started-dotnet-framework.md)に関するページで作成した Web プロジェクトを開きます。

1. *Controllers\HomeController.cs* を開きます。

1. `About()` メソッドを削除し、その位置に次のコードを挿入します。

    ```csharp
    public ActionResult About()
    {
        string currentTime = DateTime.Now.ToLongTimeString();
        ViewBag.Message = "The current time is " + currentTime;
        return View();
    }
    ```

1. `ViewBag.Message` の行に[ブレークポイントを設定](https://docs.microsoft.com/visualstudio/debugger/)します。

1. **ソリューション エクスプローラー**で目的のプロジェクトを右クリックし、 **[発行]** をクリックします。

1. **[プロファイル]** ドロップダウン リストで、[Azure App Service での ASP.NET アプリの作成](app-service-web-get-started-dotnet-framework.md)に関するページで使用した同じプロファイルを選択します。 次に、[設定] をクリックします。

1. **[発行]** ダイアログ ボックスで、 **[設定]** タブをクリックし、 **[構成]** を **[デバッグ]** に変更し、 **[保存]** をクリックします。

    ![デバッグ モードでの発行](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-publishdebug.png)

1. **[発行]** をクリックします。 デプロイが完了し、ブラウザーが起動してアプリの Azure URL が表示されたら、ブラウザーを閉じます。

1. **サーバー エクスプローラー**で、アプリを右クリックしてから **[デバッガーの接続]** をクリックします。

    ![[デバッガーの接続]](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-attachdebugger.png)

    ブラウザーが自動的に起動し、Azure で実行されているホーム ページが表示されます。 デバッグに必要な設定を Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。 この待ち時間が生じるのは、48 時間以内にアプリでのデバッグ モードを実行する初回に限られます。 同じ期間に再度デバッグを開始しても、遅延はありません。

    > [!NOTE] 
    > デバッガーの起動時に問題がある場合、**サーバー エクスプローラー**ではなく**クラウド エクスプローラー**を使用して起動してみてください。
    >

1. メニューの **[About]** をクリックします。

    Visual Studio がブレークポイントで停止します。コードが実行されている場所は、ローカル コンピューターではなく Azure 上です。

1. `currentTime` 変数にマウスを合わせて、時刻値を表示します。

    ![Azure で実行されているコード内の変数をデバッグ モードで表示](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugviewinwa.png)

    表示される時刻は、Azure サーバーの時刻です。ローカル コンピューターとはタイム ゾーンが異なります。

1. `currentTime` 変数に新しい値 ("Now running in Azure" など) を入力します。

1. F5 キーを押して実行を継続します。

     Azure で実行中の [About] ページに、先ほど currentTime 変数に対して入力した新しい値が表示されます。

     ![[About] ページ (値の変更後)](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugchangeinwa.png)

## <a name="remote-debugging-webjobs"></a><a name="remotedebugwj"></a> WebJobs のリモート デバッグ
このセクションでは、[Azure WebJobs SDK の概要](https://github.com/Azure/azure-webjobs-sdk/wiki)に関するページで作成したプロジェクトとアプリを使用してリモートでデバッグする方法を示します。

このセクションで示す機能は、Visual Studio 2013 Update 4 以降でのみ使用できます。

リモート デバッグは、継続的な WebJobs でのみ動作します。 スケジュールされたオンデマンドの WebJobs では、デバッグはサポートされていません。

1. [Azure WebJobs SDK の使用][GetStartedWJ]に関するページで作成した Web プロジェクトを開きます。

2. ContosoAdsWebJob プロジェクトで、 *Functions.cs*を開きます。

3. `GnerateThumbnail` メソッドの最初のステートメントに[ブレークポイントを設定](https://docs.microsoft.com/visualstudio/debugger/)します。

    ![ブレークポイントの設定](./media/web-sites-dotnet-troubleshoot-visual-studio/wjbreakpoint.png)

4. **ソリューション エクスプローラー**で Web プロジェクト (WebJobs プロジェクトではない) を右クリックし、 **[発行]** をクリックします。

5. **[プロファイル]** ボックスの一覧から、「 [Azure WebJobs SDK の使用](https://github.com/Azure/azure-webjobs-sdk/wiki)」で使用したものと同じプロファイルを選択します。

6. **[設定]** タブをクリックして **[構成]** を **[デバッグ]** に変更し、 **[発行]** をクリックします。

    Visual Studio によって Web プロジェクトと WebJobs プロジェクトがデプロイされ、ブラウザーでアプリの Azure URL が表示されます。

7. **サーバー エクスプローラー**で、 **[Azure]、[App Service]、使用するリソース グループ、使用するアプリ、[WebJobs]、[継続]** の順に展開し、 **[ContosoAdsWebJob]** を右クリックします。

8. **[デバッガーの接続]** をクリックします。

    ![[デバッガーの接続]](./media/web-sites-dotnet-troubleshoot-visual-studio/wjattach.png)

    ブラウザーが自動的に起動し、Azure で実行されているホーム ページが表示されます。 デバッグに必要な設定を Azure がサーバーに対して行う間、20 秒ほどの待ち時間が生じることがあります。 この待ち時間が生じるのは、48 時間以内にアプリでのデバッグ モードを実行する初回に限られます。 同じ期間に再度デバッグを開始しても、遅延はありません。

9. Contoso Ads ホーム ページを表示している Web ブラウザーで、新しい広告を作成します。

    広告を作成すると、キュー メッセージが作成されます。キュー メッセージは WebJobs によって取得され、処理されます。 WebJobs SDK がキュー メッセージを処理する関数を呼び出すと、コードがブレークポイントにヒットします。

10. デバッガーがブレークポイントで停止すると、プログラムがクラウドを実行している間に、変数の値を確認、変更することができます。 次の図では、デバッガーは、`GenerateThumbnail` メソッドに渡された blobInfo オブジェクトの内容を示しています。

     ![デバッガーの blobInfo オブジェクト](./media/web-sites-dotnet-troubleshoot-visual-studio/blobinfo.png)

11. F5 キーを押して実行を継続します。

     `GenerateThumbnail` メソッドはサムネイルの作成を完了します。

12. ブラウザーで、[インデックス] ページを更新して、サムネイルを表示します。

13. Visual Studio で、Shift キーを押しながら F5 キーを押すと、デバッグは停止します。

14. **サーバー エクスプローラー**で、ContosoAdsWebJob ノードを右クリックし、 **[ダッシュボードの表示]** をクリックします。

15. Azure の資格情報を使用してサインインし、WebJobs 名をクリックして、WebJobs のページに移動します。

     ![ContosoAdsWebJob をクリック](./media/web-sites-dotnet-troubleshoot-visual-studio/clickcaw.png)

     `GenerateThumbnail` 関数が最近実行されたことが、ダッシュボードに示されます。

     (次回、 **[ダッシュボードの表示]** をクリックするときには、サインインする必要はありません。ブラウザーが WebJobs のページに直接移動します。)

16. 関数名をクリックすると、関数の実行について詳細が表示されます。

     ![機能の詳細](./media/web-sites-dotnet-troubleshoot-visual-studio/funcdetails.png)

関数が [ログを書き込んだ](https://github.com/Azure/azure-webjobs-sdk/wiki)場合は、 **[ToggleOutput]** をクリックするとログを表示できます。

## <a name="notes-about-remote-debugging"></a>リモート デバッグに関する注意

* 運用環境におけるデバッグ モードの実行はお勧めできません。 運用環境のアプリが複数のサーバー インスタンスにスケールアウトされていない場合、デバッグを行うと、Web サーバーが他の要求に応答できなくなります。 しかし、Web サーバーのインスタンスが複数存在する場合、デバッガーのアタッチ先となるインスタンスは無作為に決定されるため、同じインスタンスに後続のブラウザーの要求を確実に渡すことができません。 また、運用環境にデバッグ ビルドをデプロイすることも一般的ではありません。リリース ビルドに対してはコンパイラが最適化を行うため、ソース コードの状況を行レベルで把握することは不可能です。 運用環境の問題をトラブルシューティングするのに最も適しているリソースは、アプリケーション トレースと Web サーバーのログです。
* リモート デバッグ時、ブレークポイントで長時間停止させることは避けてください。 数分以上停止しているプロセスは、応答していないプロセスと見なされ、Azure によりシャットダウンされます。
* デバッグ中は、サーバーから Visual Studio にデータが送信されるため、帯域幅の使用料に影響が及ぶ可能性があります。 帯域幅使用料については、 [Azure 料金計算ツール](https://azure.microsoft.com/pricing/calculator/)を参照してください。
* *Web.config* ファイルの `compilation` 要素の `debug` 属性が true に設定されていることを確認します。 デバッグ ビルド構成で発行するときは、true が既定値です。

    ```xml
    <system.web>
      <compilation debug="true" targetFramework="4.5" />
      <httpRuntime targetFramework="4.5" />
    </system.web>
    ```
* デバッグ対象となるコードにデバッガーがステップ インしない場合、[マイ コードのみ] の設定を変更してみてください。  詳しくは、「[Specify whether to debug only user code using Just My Code in Visual Studio (Visual Studio で [マイコードのみ] を使用してユーザー コードのみをデバッグするかどうかを指定する)](https://docs.microsoft.com/visualstudio/debugger/just-my-code)」をご覧ください。
* リモート デバッグ機能を有効にしたときに、サーバー上でタイマーが開始され、48 時間後にこの機能が自動的に無効になります。 この 48 時間の上限はセキュリティとパフォーマンス上の理由で設定されています。 必要に応じて、この機能を何回でも簡単に有効に戻すことができます。 積極的にデバッグを実行している場合以外は、この機能を無効にしたままにすることをお勧めします。
* 手動でデバッガーをアプリ プロセス (w3wp.exe) だけでなく、任意のプロセスに接続できます。 Visual Studio のデバッグ モードの使い方の詳細については、MSDN のトピック「 [Visual Studio でのデバッグ](/visualstudio/debugger/debugging-in-visual-studio)」を参照してください。

## <a name="diagnostic-logs-overview"></a><a name="logsoverview"></a>診断ログの概要
App Service アプリで実行される ASP.NET アプリケーションは、次の種類のログを作成できます。

* **アプリケーション トレース ログ**<br/>
  アプリケーションが [System.Diagnostics.Trace](/dotnet/api/system.diagnostics.trace) クラスのメソッドを呼び出すことによって作成されます。
* **Web サーバー ログ**<br/>
  Web サーバーは、アプリに届くすべての HTTP 要求について、それぞれログ エントリを作成します。
* **詳細なエラー メッセージ ログ**<br/>
  失敗した HTTP 要求 (状態コード 400 以上の要求) について、より詳しい情報を記した HTML ページが Web サーバーによって作成されます。
* **失敗した要求トレース ログ**<br/>
  失敗した HTTP 要求についての詳しいトレース情報を記録した XML ファイルが Web サーバーによって作成されます。 また、ブラウザーで XML の体裁を設定するための XSL ファイルも作成されます。

ログ出力はアプリのパフォーマンスに影響を及ぼすため、Azure では、必要に応じてログの種類ごとにその有効と無効を切り替えることができるようになっています。 アプリケーション ログについては、特定の重大度レベルを超えるログだけを記録するように指定できます。 新しいアプリを作成した時点ではすべてのログが既定で無効になります。

ログは、アプリのファイル システムにあり、FTP 経由でアクセス可能な *LogFiles* フォルダー内のファイルに書き込まれます。 Web サーバーのログとアプリケーションのログは、Azure のストレージ アカウントに出力することもできます。 ストレージ アカウントには、ファイル システムよりも大量のログを保持することができます。 ファイル システムを使用した場合、保存できるログの上限は 100 MB です。 (ファイル システムのログは、短期間のみ保持されます。 上限に達すると、古いログ ファイルは削除され、新しいログ ファイルのための領域が確保されます。)  

## <a name="create-and-view-application-trace-logs"></a><a name="apptracelogs"></a>アプリケーションのトレース ログの作成と表示
このセクションでは、次のタスクを実行します。

* [Azure と ASP.NET の使用](app-service-web-get-started-dotnet-framework.md)に関するページで作成した Web プロジェクトに、トレース ステートメントを追加します。
* プロジェクトをローカル実行したときのログを確認します。
* Azure で実行中のアプリケーションによって生成されたログを確認します。

WebJobs でアプリケーション ログを作成する方法については、「 [WebJobs SDK を使用して Azure キュー ストレージを操作する方法 - ログの記述方法](https://github.com/Azure/azure-webjobs-sdk/wiki)」を参照してください。 ログの表示とログを Azure に格納する方法の制御に関する次の手順は、WebJobs によって作成されたアプリケーション ログにも適用されます。

### <a name="add-tracing-statements-to-the-application"></a>アプリケーションへのトレース ステートメントの追加
1. `System.Diagnostics` の `Trace` ステートメントと `using` ステートメントを追加するために、*Controllers\HomeController.cs* を開き、`Index`、`About`、`Contact` のメソッドを次のコードで置き換えます。

    ```csharp
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
    ```

1. `using System.Diagnostics;` ステートメントをファイルの先頭に追加します。

### <a name="view-the-tracing-output-locally"></a>ローカルでのトレース出力の表示
1. F5 キーを押してデバッグ モードでアプリケーションを実行します。

    既定のトレース リスナーは、すべてのトレース出力を他のデバッグ出力と一緒に **出力** ウィンドウに書き込みます。 次の図は、 `Index` メソッドに追加したトレース ステートメントからの出力結果を示したものです。

    ![[デバッグ] ウィンドウでのトレース](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-debugtracing.png)

    以降の手順では、コンパイルせずにデバッグ モードで、トレース出力を Web ページに表示する方法を紹介します。
1. プロジェクト フォルダーにあるアプリケーションの Web.config ファイルを開き、ファイル末尾の終了 `</configuration>` 要素の直前に `<system.diagnostics>` 要素を追加します。

    ``` xml
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
    ```

`WebPageTraceListener` を使用すると、ブラウザーから `/trace.axd` にアクセスすることでトレース出力を表示できます。
1. Web.config ファイルの `<system.web>` に、次のような <a href="https://msdn.microsoft.com/library/vstudio/6915t83k(v=vs.100).aspx">trace 要素</a>を追加します。

    ``` xml
    <trace enabled="true" writeToDiagnosticsTrace="true" mostRecent="true" pageOutput="false" />
    ```

1. Ctrl キーを押しながら F5 キーを押してアプリケーションを実行します。
1. ブラウザー ウィンドウのアドレス バーで、URL に続けて「*trace.axd*」と入力し、Enter キーを押します (例: `http://localhost:53370/trace.axd`)。
1. **[アプリケーション トレース]** ページの最初の行 (BrowserLink の行とは異なる) で、 **[詳細の表示]** をクリックします。

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd1.png)

    **[要求の詳細]** ページが表示され、`Index` メソッドに追加したトレース ステートメントからの出力が **[トレース情報]** セクションに表示されます。

    ![trace.axd](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-traceaxd2.png)

    既定では、`trace.axd` の使用はローカルに限られます。 *Web.config* ファイルで `trace` 要素に `localOnly="false"` を追加すると、リモート アプリからも利用できるようになります。その例を次に示します。

        <trace enabled="true" writeToDiagnosticsTrace="true" localOnly="false" mostRecent="true" pageOutput="false" />

    ただし、運用アプリで `trace.axd` を有効にすることはセキュリティ上の理由でお勧めできません。 次のセクションでは、App Service アプリでトレース ログを読み取る簡単な方法を示します。

### <a name="view-the-tracing-output-in-azure"></a>Azure でのトレース出力の確認
1. **ソリューション エクスプローラー**で Web プロジェクトを右クリックし、 **[発行]** をクリックします。
2. **[Web の発行]** ダイアログ ボックスの **[発行]** をクリックします。

    更新したプロジェクトを発行すると、ブラウザー ウィンドウが起動して自分のホーム ページが表示されます (ただし、 **[接続]** タブの **[宛先 URL]** をクリアした場合を除く)。
3. **サーバー エクスプローラー**でアプリを右クリックし、 **[ストリーミング ログの表示]** を選択します。

    ![コンテキスト メニューからストリーミング ログを表示](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-viewlogsmenu.png)

    ログ ストリーミング サービスに接続されたことを示すメッセージが**出力**ウィンドウに表示されます。ログが出力されないまま 1 分が経過すると、その都度、1 行の通知が追加されます。

    ![コンテキスト メニューからストリーミング ログを表示](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-nologsyet.png)
4. アプリケーションのホーム ページが表示されているブラウザー ウィンドウで、 **[Contact]** をクリックします。

    数秒すると、`Contact` メソッドに追加したエラー レベルのトレースが**出力**ウィンドウに表示されます。

    ![出力ウィンドウのエラー トレース](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-errortrace.png)

    Visual Studio にエラー レベルのトレースしか表示されていないのは、それが、ログ監視サービスを有効にしたときの既定の設定であるためです。 先ほどの設定ページを開いたときに見たように、新しい App Service アプリを作成したときは、すべてのログが既定で無効となります。

    ![アプリケーション ログ記録がオフ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-apploggingoff.png)

    ただし、 **[ストリーミング ログを表示]** を選択すると、 **[アプリケーション ログ記録 (ファイル システム)]** が自動的に **[エラー]** に変更されます。これは、エラー レベルのログが報告されることを意味します。 すべてのトレース ログを表示するためには、この設定を **[詳細]** に変更する必要があります。 "エラー" よりも低い重大度レベルを選択すると、その重大度を超えるログがすべて報告されます。 つまり [詳細] を選択した場合は、"情報"、"警告"、"エラー" に該当するログが表示されます。  

5. 先ほどと同じように、**サーバー エクスプローラー**でアプリを右クリックし、 **[表示の設定]** をクリックします。
6. **[アプリケーション ログ記録 (ファイル システム)]** を **[詳細]** に変更し、 **[保存]** をクリックします。

    ![トレース レベルを [詳細] に設定](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-applogverbose.png)
7. **[連絡先]** ページが表示されているブラウザー ウィンドウで、 **[ホーム]** 、 **[バージョン情報]** 、 **[連絡先]** を順にクリックします。

    数秒すると、 **出力** ウィンドウにすべてのトレース出力が表示されます。

    ![詳細レベルのトレース出力](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-verbosetraces.png)

    このセクションでは、ログの有効と無効の切り替えをアプリの設定で行いました。 トレース リスナーの有効と無効は、Web.config ファイルで設定することもできます。 ただし、Web.config ファイルに変更を加えると、アプリケーション ドメインの再利用処理が実行されます。一方、アプリの構成を介してログを有効にした場合は、そのようにはなりません。 問題の再現に時間がかかる場合や、発生のタイミングが散発的である場合、アプリケーション ドメインの再利用処理で問題が "解消" し、再発までしばらく待たなければならなくなります。 Azure で診断を有効にすると、アプリケーション ドメインの再利用処理は行われず、すぐにエラー情報を収集することができます。

### <a name="output-window-features"></a>出力ウィンドウの機能
**出力**ウィンドウの **[Microsoft Azure ログ]** タブには、いくつかのボタンと 1 つのテキスト ボックスが表示されます。

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

## <a name="view-web-server-logs"></a><a name="webserverlogs"></a>Web サーバーのログの表示
Web サーバーのログには、アプリの HTTP アクティビティがすべて記録されます。 それらの HTTP 要求を**出力**ウィンドウに表示するには、アプリに対してその機能を有効にしたうえで、HTTP 要求を監視するための指定を Visual Studio に対して行う必要があります。

1. **サーバー エクスプローラー**から開いた **Azure Web アプリの [構成]** タブで [Web サーバーのログ記録] を **[オン]** に変更し、 **[保存]** をクリックします。

    ![Web サーバーのログ記録を有効にする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-webserverloggingon.png)
2. **出力**ウィンドウの **[監視する Microsoft Azure ログを指定する]** をクリックします。

    ![[監視する Azure ログの指定]](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-specifylogs.png)
3. **[Microsoft Azure ログ オプション]** ダイアログ ボックスの **[Web サーバーのログ]** を選択し、 **[OK]** をクリックします。

    ![Web サーバーのログを監視する](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorwslogson.png)
4. アプリを表示するブラウザー ウィンドウで、 **[ホーム]** 、 **[バージョン情報]** 、 **[連絡先]** の順にクリックします。

    通常はアプリケーションのログが先に表示され、続けて Web サーバーのログが表示されます。 ログが表示されるまでにしばらく時間がかかる場合があります。

    ![出力ウィンドウの Web サーバーのログ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-wslogs.png)

Visual Studio で Web サーバーのログを初めて有効にしたとき、既定では、Azure によってファイル システムにログが書き込まれます。 代わりに、Azure ポータルを使用して、Web サーバーのログの書き込み先として、ストレージ アカウントの BLOB コンテナーを指定することもできます。

Azure ポータルを使用して、Azure ストレージ アカウントへの Web サーバーのログ記録を有効にした後、Visual Studio でログ記録を無効にした場合、Visual Studio でログ記録を再度有効にすると、ストレージ アカウントの設定が復元されます。

## <a name="view-detailed-error-message-logs"></a><a name="detailederrorlogs"></a>詳細なエラー メッセージ ログの表示
詳細なエラー ログでは、エラー応答コード (400 以上) が返された HTTP 要求について、いくつかの詳しい情報が確認できます。 それらの HTTP 要求を**出力**ウィンドウに表示するには、アプリに対してその機能を有効にしたうえで、HTTP 要求を監視するための指定を Visual Studio に対して行う必要があります。

1. **サーバー エクスプローラー**から開いた **Azure Web アプリの [構成]** タブで **[詳細なエラー メッセージ]** を **[オン]** に変更し、 **[保存]** をクリックします。

    ![詳細なエラー メッセージを有効にする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailedlogson.png)

2. **出力**ウィンドウの **[監視する Microsoft Azure ログを指定する]** をクリックします。

3. **[Microsoft Azure ログ オプション]** ダイアログ ボックスの **[すべてのログ]** を選択し、 **[OK]** をクリックします。

    ![すべてのログを監視する](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-monitorall.png)

4. ブラウザー ウィンドウのアドレス バーで、404 エラーの原因となるような余分な文字を URL に追加し (例: `http://localhost:53370/Home/Contactx`)、Enter キーを押します。

    数秒後、Visual Studio の **出力** ウィンドウに詳細なエラー ログが表示されます。

    ![詳細なエラー ログ - 出力ウィンドウ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorlog.png)

    Ctrl キーを押しながらリンクをクリックすると、次のような書式化されたログ出力がブラウザーに表示されます。

    ![詳細なエラー ログ - ブラウザー ウィンドウ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-detailederrorloginbrowser.png)

## <a name="download-file-system-logs"></a><a name="downloadlogs"></a>ファイル システムのログのダウンロード
**出力** ウィンドウで監視できるすべてのログは *.zip* ファイルとしてダウンロードすることもできます。

1. **出力**ウィンドウの **[ストリーミング ログのダウンロード]** をクリックします。

    ![[ログ] タブのボタン](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadicon.png)

    エクスプローラーが起動して *[ダウンロード]* フォルダーが開き、ダウンロード済みのファイルが選択状態で表示されます。

    ![ダウンロードされたファイル](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-downloadedfile.png)
2. この *.zip* ファイルを展開すると、次のフォルダー構造が確認できます。

    ![ダウンロードされたファイル](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilefolders.png)

   * アプリケーション トレース ログは、*LogFiles\Application* フォルダーの *.txt* ファイルに記録されます。
   * Web サーバーのログは、*LogFiles\http\RawLogs* フォルダーの *.log* ファイルに記録されます。 これらのファイルの閲覧と操作は、 [Log Parser](https://www.microsoft.com/download/details.aspx?displaylang=en&id=24659) などのツールを使って行うことができます。
   * 詳細なエラー メッセージのログは、*LogFiles\DetailedErrors* フォルダーの *.html* ファイルに記録されます。

     (*deployments* フォルダーは、ソース管理の発行によって作成されたファイルに使用されます。Visual Studio の発行に関連したファイルは保存されません。 *Git* フォルダーは、ログ ファイル ストリーミング サービスやソース管理の発行に関連したトレースに使用されます。)  

<!-- ## <a name="storagelogs"></a>View storage logs
Application tracing logs can also be sent to an Azure storage account, and you can view them in Visual Studio. To do that you'll create a storage account, enable storage logs in the Azure portal, and view them in the **Logs** tab of the **Azure Web App** window.

You can send logs to any or all of three destinations:

* The file system.
* Storage account tables.
* Storage account blobs.

You can specify a different severity level for each destination.

Tables make it easy to view details of logs online, and they support streaming; you can query logs in tables and see new logs as they are being created. Blobs make it easy to download logs in files and to analyze them using HDInsight, because HDInsight knows how to work with blob storage. For more information, see **Hadoop and MapReduce** in [Data Storage Options (Building Real-World Cloud Apps with Azure)](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/data-storage-options).

You currently have file system logs set to verbose level; the following steps walk you through setting up information level logs to go to storage account tables. Information level means all logs created by calling `Trace.TraceInformation`, `Trace.TraceWarning`, and `Trace.TraceError` will be displayed, but not logs created by calling `Trace.WriteLine`.

Storage accounts offer more storage and longer-lasting retention for logs compared to the file system. Another advantage of sending application tracing logs to storage is that you get some additional information with each log that you don't get from file system logs.

1. Right-click **Storage** under the Azure node, and then click **Create Storage Account**.

![Create Storage Account](./media/web-sites-dotnet-troubleshoot-visual-studio/createstor.png)

1. In the **Create Storage Account** dialog, enter a name for the storage account.

    The name must be must be unique (no other Azure storage account can have the same name). If the name you enter is already in use you'll get a chance to change it.

    The URL to access your storage account will be *{name}*.core.windows.net.
2. Set the **Region or Affinity Group** drop-down list to the region closest to you.

    This setting specifies which Azure datacenter will host your storage account. For this tutorial your choice won't make a noticeable difference, but for a production web app you want your web server and your storage account to be in the same region to minimize latency and data egress charges. The web app (which you'll create later) should run in a region as close as possible to the browsers accessing your web app in order to minimize latency.
3. Set the **Replication** drop-down list to **Locally redundant**.
   
    When geo-replication is enabled for a storage account, the stored content is replicated to a secondary datacenter to enable failover to that location in case of a major disaster in the primary location. Geo-replication can incur additional costs. For test and development accounts, you generally don't want to pay for geo-replication. For more information, see [Create, manage, or delete a storage account](../storage/common/storage-create-storage-account.md).
4. Click **Create**.

    ![New storage account](./media/web-sites-dotnet-troubleshoot-visual-studio/newstorage.png)    
5. In the Visual Studio **Azure Web App** window, click the **Logs** tab, and then click **Configure Logging in Management Portal**.

     <!-- todo:screenshot of new portal if the VS page link goes to new portal -- >
    ![Configure logging](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-configlogging.png)

    This opens the **Configure** tab in the portal for your web app.
6. In the portal's **Configure** tab, scroll down to the application diagnostics section, and then change **Application Logging (Table Storage)** to **On**.
7. Change **Logging Level** to **Information**.
8. Click **Manage Table Storage**.

    ![Click Manage TableStorage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-stgsettingsmgmtportal.png)

    In the **Manage table storage for application diagnostics** box, you can choose your storage account if you have more than one. You can create a new table or use an existing one.

    ![Manage table storage](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-choosestorageacct.png)
9. In the **Manage table storage for application diagnostics** box, click the check mark to close the box.
10. In the portal's **Configure** tab, click **Save**.
11. In the browser window that displays the application web app, click **Home**, then click **About**, and then click **Contact**.

     The logging information produced by browsing these web pages is written to the storage account.
12. In the **Logs** tab of the **Azure Web App** window in Visual Studio, click **Refresh** under **Diagnostic Summary**.

     ![Click Refresh](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-refreshstorage.png)

     The **Diagnostic Summary** section shows logs for the last 15 minutes by default. You can change the period to see more logs.

     (If you get a "table not found" error, verify that you browsed to the pages that do the tracing after you enabled **Application Logging (Storage)** and after you clicked **Save**.)

     ![Storage logs](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-storagelogs.png)

     Notice that in this view you see **Process ID** and **Thread ID** for each log, which you don't get in the file system logs. You can see additional fields by viewing the Azure storage table directly.
13. Click **View all application logs**.

     The trace log table appears in the Azure storage table viewer.

     (If you get a "sequence contains no elements" error, open **Server Explorer**, expand the node for your storage account under the **Azure** node, and then right-click **Tables** and click **Refresh**.)

     ![Storage logs in table view](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracelogtableview.png)

     This view shows additional fields you don't see in any other views. This view also enables you to filter logs by using special Query Builder UI for constructing a query. For more information, see Working with Table Resources - Filtering Entities in [Browsing Storage Resources with Server Explorer](https://msdn.microsoft.com/library/ff683677.aspx).
14. To look at the details for a single row, double-click one of the rows.

     ![Trace table in Server Explorer](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-tracetablerow.png)
 -->
## <a name="view-failed-request-tracing-logs"></a><a name="failedrequestlogs"></a>失敗した要求トレース ログの表示
失敗した要求トレース ログは、URL の書き換えや認証の問題など、IIS による HTTP 要求の処理を詳しく把握する必要がある状況で活用できます。

App Service アプリでは、同じ失敗した要求トレース機能が使用されています。この機能は、IIS 7.0 以降で利用できます。 ただし、ログに記録するエラーを指定するための IIS 設定にアクセスする必要はありません。 失敗した要求トレースを有効にすると、すべてのエラーがキャプチャされます。

失敗した要求トレースは Visual Studio を使用して有効にできますが、それらを Visual Studio で表示することはできません。 これらのログは XML ファイル形式になっています。 ストリーミング ログ サービスで監視されるのは、プレーンテキスト モードでの読み取りが可能と判断されたファイル ( *.txt*、 *.html*、 *.log* の各ファイル) だけです。

失敗した要求トレース ログは、ブラウザーから FTP で直接表示できるほか、FTP ツールを使ってローカル コンピューターにダウンロードした後、ローカルで表示することもできます。 このセクションでは、ブラウザーで直接閲覧する方法を説明します。

1. **サーバー エクスプローラー**から開いた **[Azure Web アプリ]** ウィンドウの **[構成]** タブで、 **[失敗した要求トレース]** を **[オン]** に変更し、 **[保存]** をクリックします。

    ![失敗した要求トレースを有効にする](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequeston.png)
2. アプリが表示されているブラウザー ウィンドウのアドレス バーで、余分な文字を URL に追加し、Enter キーを押して 404 エラーを発生させます。

    これにより、失敗した要求トレース ログが作成されます。そのログを表示またはダウンロードする方法については、以降の手順で説明します。

3. Visual Studio で、 **[Azure Web アプリ]** ウィンドウの **[構成]** タブにある **[管理ポータルで開く]** をクリックします。

4. [Azure portal](https://portal.azure.com) のアプリの **[設定]** ページで、 **[デプロイ資格情報]** をクリックし、新しいユーザー名とパスワードを入力します。

    ![新しい FTP ユーザー名とパスワード](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-enterftpcredentials.png)

    > [!NOTE]
    > ログインするときは、完全なユーザー名とそのアプリ名プレフィックスを使用する必要があります。 たとえば、ユーザー名として「myid」と入力し、サイトが "myexample" の場合、"myexample\myid" としてログインします。
    >

5. 新しいブラウザー ウィンドウで、アプリの **[概要]** ページの **[FTP ホスト名]** または **[FTPS ホスト名]** に表示されている URL に移動します。

6. 先ほど作成した FTP 資格情報を使用してサインインします (ユーザー名のアプリ名プレフィックスを含めること)。

    アプリのルート フォルダーがブラウザーに表示されます。

7. *LogFiles* フォルダーを開きます。

    ![LogFiles フォルダーを開く](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-logfilesfolder.png)

8. W3SVC に数値の付いた名前のフォルダーを開きます。

    ![W3SVC フォルダーを開く](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfolder.png)

    このフォルダーには、失敗した要求トレースを有効にした後で記録されたすべてのエラーの XML ファイルに加え、ブラウザーで XML の体裁を設定するための XSL ファイルが格納されています。

    ![W3SVC フォルダー](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-w3svcfoldercontents.png)

9. トレース情報を確認する失敗した要求の XML ファイルをクリックします。

    次の図は、サンプル エラーのトレース情報を部分的に示したものです。

    ![失敗した要求トレースをブラウザーで表示したところ](./media/web-sites-dotnet-troubleshoot-visual-studio/tws-failedrequestinbrowser.png)

## <a name="next-steps"></a><a name="nextsteps"></a>次のステップ
App Service アプリで作成されたログは Visual Studio を使って簡単に参照できることが確認できました。 次のセクションでは、関連トピックに関する他のリソースへのリンクを紹介します。

* App Service のトラブルシューティング
* Visual Studio でのデバッグ
* Azure でのリモート デバッグ
* ASP.NET アプリケーションでのトレース
* Web サーバーのログの分析
* 失敗した要求トレース ログの分析
* Cloud Services のデバッグ

### <a name="app-service-troubleshooting"></a>App Service のトラブルシューティング
Azure App Service のアプリのトラブルシューティングの詳細については、以下のリソースを参照してください。

* [How to monitor apps (アプリの監視方法)](web-sites-monitor.md)
* [Investigating Memory Leaks in Azure App Service with Visual Studio 2013 (Visual Studio 2013 を使用した Azure App Service でのメモリ リークの調査)](https://devblogs.microsoft.com/devops/investigating-memory-leaks-in-azure-web-sites-with-visual-studio-2013/)。 マネージド メモリの問題の分析に役立つ Visual Studio の機能に関する Microsoft ALM のブログ記事
* [Azure App Service online tools you should know about (知っておくべき Azure App Service のオンライン ツール)](https://azure.microsoft.com/blog/2014/03/28/windows-azure-websites-online-tools-you-should-know-about-2/)。 Amit Apple によるブログの投稿です。

具体的なトラブルシューティングについての質問は、次のいずれかのフォーラムで投稿してください。

* [ASP.NET サイト上の Azure フォーラム](https://forums.asp.net/1247.aspx/1?Azure+and+ASP+NET)。
* [Microsoft Q&A での Azure フォーラム](https://docs.microsoft.com/answers/topics/azure-webapps.html)。
* [StackOverflow.com](https://www.stackoverflow.com)。

### <a name="debugging-in-visual-studio"></a>Visual Studio でのデバッグ
Visual Studio のデバッグ モードの使い方については、「[Visual Studio でのデバッグ](/visualstudio/debugger/debugging-in-visual-studio)」と [Visual Studio 2010 でのデバッグのヒントに関するページ](https://weblogs.asp.net/scottgu/archive/2010/08/18/debugging-tips-with-visual-studio-2010.aspx)を参照してください。

### <a name="remote-debugging-in-azure"></a>Azure でのリモート デバッグ
App Service アプリと WebJobs のリモート デバッグの詳細については、以下のリソースを参照してください。

* [Introduction to Remote Debugging Azure App Service (Azure App Service のリモート デバッグの概要)](https://azure.microsoft.com/blog/2014/05/06/introduction-to-remote-debugging-on-azure-web-sites/)。
* [Introduction to Remote Debugging Azure App Service part 2 - Inside Remote debugging (Azure App Service のリモート デバッグの概要 2 - リモート デバッグの内部処理)](https://azure.microsoft.com/blog/2014/05/07/introduction-to-remote-debugging-azure-web-sites-part-2-inside-remote-debugging/)
* [Introduction to Remote Debugging on Azure App Service part 3 - Multi-Instance environment and GIT (Azure App Service のリモート デバッグの概要 3 - マルチインスタンス環境と GIT)](https://azure.microsoft.com/blog/2014/05/08/introduction-to-remote-debugging-on-azure-web-sites-part-3-multi-instance-environment-and-git/)
* [WebJobs Debugging (WebJobs のデバッグ) (ビデオ)](https://www.youtube.com/watch?v=ncQm9q5ZFZs&list=UU_SjTh-ZltPmTYzAybypB-g&index=1)

アプリで Azure Web API または Mobile Services バックエンドを使用し、デバッグを実行する必要がある場合は、[Visual Studio での .NET のデバッグ](/archive/blogs/azuremobile/debugging-net-backend-in-visual-studio)に関するページを参照してください。

### <a name="tracing-in-aspnet-applications"></a>ASP.NET アプリケーションでのトレース
ASP.NET トレースに関しては、最新かつ必要な情報をすべて網羅した解説がインターネットには存在しません。 そのため、過去に作成された入門者向けの資料を参考にするのが最善の方法となります。MVC がまだ存在していなかったために Web フォームを想定して書かれていますが、具体的な問題については、最新のブログで情報を補うことができます。 たとえば、以下のリソースが参考になります。

* [監視と利用統計情報 (Azure での実際のクラウド アプリケーションのビルド) に関するページ](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry)。<br>
  Azure クラウド アプリケーションをトレースするためのベスト プラクティスを掲載した E-Book の章。
* [ASP.NET トレース](/previous-versions/dotnet/articles/ms972204(v=msdn.10))<br/>
  最新とは言えませんが、基本的な事柄がわかりやすくまとめられています。
* [トレース リスナー](/dotnet/framework/debug-trace-profile/trace-listeners)<br/>
  トレース リスナーについて書かれていますが、[WebPageTraceListener](/dotnet/api/system.web.webpagetracelistener) には触れていません。
* [チュートリアル:Integrating ASP.NET Tracing with System.Diagnostics Tracing (ASP.NET トレースと System.Diagnostics トレースの統合)](/previous-versions/b0ectfxd(v=vs.140))<br/>
  この記事も古い情報ですが、入門記事では扱っていないような詳しい情報が記載されています。
* [ASP.NET MVC Razor ビューでのトレース](https://devblogs.microsoft.com/aspnet/tracing-in-asp-net-mvc-razor-views/)<br/>
  Razor ビューでのトレースに加え、MVC アプリケーションでハンドルされない例外をすべてログに記録するためのエラー フィルターの作成方法についても説明されています。 Web フォーム アプリケーションで、ハンドルされない例外をすべてログに記録する方法については、MSDN の「[エラー ハンドラーの完全なコード例](/previous-versions/bb397417(v=vs.140))」で紹介されている Global.asax サンプルを参照してください。 MVC または Web フォームで、特定の例外をログに記録すると共に、既定のフレームワークの処理はそのまま活かしておく必要がある場合、例外を捕捉してから再スローする方法を利用できます。その例を次に示します。

    ```csharp
    try
    {
       // Your code that might cause an exception to be thrown.
    }
    catch (Exception ex)
    {
        Trace.TraceError("Exception: " + ex.ToString());
        throw;
    }
    ```

* [Azure コマンド ラインからの診断トレース ログのストリーミングと Glimpse に関する情報](https://www.hanselman.com/blog/StreamingDiagnosticsTraceLoggingFromTheAzureCommandLinePlusGlimpse.aspx)<br/>
  このチュートリアルで Visual Studio を使って行ったことをコマンド ラインで行う方法が解説されています。 [Glimpse](https://www.hanselman.com/blog/IfYoureNotUsingGlimpseWithASPNETForDebuggingAndProfilingYoureMissingOut.aspx) は、ASP.NET アプリケーションをデバッグするためのツールです。
* [Web Apps のログと診断の使用に関するページ - David Ebbo 作成](https://azure.microsoft.com/documentation/videos/azure-web-site-logging-and-diagnostics/)、および [Web Apps からのログのストリーミングに関するページ - David Ebbo 作成](https://azure.microsoft.com/documentation/videos/log-streaming-with-azure-web-sites/)<br>
  Scott Hanselman と David Ebbo によるビデオ。

エラーをログに記録する方法としては、独自のトレース コードを記述する以外にも、 [ELMAH](https://nuget.org/packages/elmah/)のようなオープン ソースのログ記録フレームワークを使う方法があります。 詳細については、 [Scott Hanselman が ELMAH についてまとめたブログ記事](https://www.hanselman.com/blog/NuGetPackageOfTheWeek7ELMAHErrorLoggingModulesAndHandlersWithSQLServerCompact.aspx)を参照してください。

さらに、ASP.NET または `System.Diagnostics` トレースを使用して、Azure からストリーミング ログを取得する必要はありません。 App Service アプリのストリーミング ログ サービスは、*LogFiles* フォルダーに見つかったすべての *.txt* ファイル、 *.html* ファイル、 *.log* ファイルをストリーミングします。 したがって、アプリのファイル システムに書き込む独自のログ記録システムを作成することもできます。必要なファイルが自動的にストリーミングされ、ダウンロードされます。 必要な作業は、*d:\home\logfiles* フォルダーにファイルを作成するアプリケーション コードを記述するだけです。

### <a name="analyzing-web-server-logs"></a>Web サーバーのログの分析
Web サーバーのログの分析の詳細については、次のリソースを参照してください。

* [LogParser](https://www.microsoft.com/download/details.aspx?id=24659)<br/>
  Web サーバーのログ ( *.log* ファイル) に記録されているデータを表示するためのツールです。
* [IIS のパフォーマンスの問題やアプリケーション エラーを LogParser でトラブルシューティングする](https://www.iis.net/learn/troubleshoot/performance-issues/troubleshooting-iis-performance-issues-or-application-errors-using-logparser)<br/>
  Web サーバーのログを分析する際に活用できる Log Parser ツールについて基本的な事柄が説明されています。
* [LogParser の使用に関して Robert McMurray が執筆したブログ記事](https://docs.microsoft.com/archive/blogs/robert_mcmurray/using-logparser-with-ftp-7-x-sessions)<br/>
* [IIS 7.0、IIS 7.5、IIS 8.0 における HTTP 状態コード](https://support.microsoft.com/kb/943891)

### <a name="analyzing-failed-request-tracing-logs"></a>失敗した要求トレース ログの分析
失敗した要求トレース ログの活用方法については、Microsoft TechNet Web サイトの「[Using Failed Request Tracing](https://www.iis.net/learn/troubleshoot/using-failed-request-tracing)」 (失敗した要求トレースの使用) セクションなどが参考になります。 ただし、このドキュメントで扱う内容は、失敗した要求トレースを IIS で構成する作業が主体です。この作業を Azure App Service で行うことはできません。

[GetStarted]: app-service-web-get-started-dotnet.md
[GetStartedWJ]: https://github.com/Azure/azure-webjobs-sdk/wiki
