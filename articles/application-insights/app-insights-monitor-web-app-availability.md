---
title: "Web サイトの可用性と応答性の監視 | Microsoft Docs"
description: "Application Insights で Web テストを設定します。 Web サイトが使用できなくなったり、応答速度が低下したりした場合に、アラートを受け取ります。"
services: application-insights
documentationcenter: 
author: SoubhagyaDash
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/25/2017
ms.author: bwren
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 74cd4dbf74afbf5e7e0e7d0b7aca49b5aa214b83
ms.contentlocale: ja-jp
ms.lasthandoff: 05/26/2017

---
# <a name="monitor-availability-and-responsiveness-of-any-web-site"></a>Web サイトの可用性と応答性の監視
いずれかのサーバーに Web アプリまたは Web サイトをデプロイした後、テストを設定して、その可用性と応答性を監視できます。 [ Application Insights](app-insights-overview.md) は、世界各地の複数のポイントから定期的にアプリケーションに Web 要求を送信します。 アプリケーションがまったく応答しなくなったりアプリケーションの応答が遅くなったりした場合は、Application Insights からその旨が通知されます。

可用性テストは、パブリック インターネットからアクセスできる任意の HTTP または HTTPS エンドポイントに対して設定できます。 テストする Web サイトには何も追加する必要はありません。 自社のサイトにも何も追加せずに、使用する REST API サービスをテストできます。

可用性テストには、次の 2 種類があります。

* [URL の Ping テスト](#create): Azure Portal で作成できる簡単なテストです。
* [複数ステップ Web テスト](#multi-step-web-tests): Visual Studio Enterprise で作成してポータルにアップロードします。

アプリケーション リソースごとに最大 25 個の可用性テストを作成できます。

## <a name="create"></a>1.可用性テスト レポートのリソースを開く

Web アプリ用に**既に Application Insights を構成している場合**は、[Azure Portal](https://portal.azure.com) でその Web アプリ用の Application Insights リソースを開きます。

**新しいリソースでレポートを表示する場合**は、[Microsoft Azure](http://azure.com) にサインアップし、[Azure Portal](https://portal.azure.com) に移動して、Application Insights リソースを作成します。

![[新規] > [Application Insights]
](./media/app-insights-monitor-web-app-availability/11-new-app.png)

**[すべてのリソース]** をクリックして新しいリソースの概要ブレードを開きます。

## <a name="setup"></a>2.URL の Ping テストを作成する
[可用性] ブレードを開き、テストを追加します。

![少なくとも自分の Web サイトの URL を入力
](./media/app-insights-monitor-web-app-availability/13-availability.png)

* **[URL]** にはテストする任意の Web ページを指定できますが、パブリック インターネットからアクセス可能である必要があります。 URL にはクエリ文字列を含めることができます。 したがって、たとえば限られた範囲でデータベースを実行できます。 URL が解決されてリダイレクトする場合、それに続いて最大で 10 個リダイレクトを使用できます。
* **[従属要求の解析]**: このオプションをオンにしていると、テスト対象の Web ページの一部である画像、スクリプト、スタイル ファイル、その他のファイルがテストから要求されます。 記録される応答時間には、これらのファイルの取得にかかる時間が含まれます。 テスト全体のタイムアウト時間内にこれらすべてのリソースを正常にダウンロードできない場合、テストは失敗します。 

    このオプションがオンになっていない場合、テストからは指定した URL にあるファイルのみが要求されます。
* **[Web テストが失敗した場合に再試行を有効にします。]**: このオプションをオンにしていると、テストに失敗したとき、少し間を置いてテストを再試行します。 エラーは試行が 3 回連続で失敗した場合にのみ報告されます。 その後、後続のテストが通常のテスト間隔で実行されます。 再試行は、次の成功まで一時的に中断されます。 このルールがテスト場所ごとに独立して適用されます。 このオプションはオンにすることをお勧めします。 再試行の際に平均でエラーの約 80% がなくなります。
* **[テスト間隔]**: 各テストの場所からテストを実行する頻度を設定します。 間隔が 5 分で、テストの場所が 5 か所の場合、サイトは平均すると毎分テストされることになります。
* **テストの場所** とは、指定された URL にサーバーが Web 要求を送信する送信元の場所です。 Web サイトで発生している問題とネットワークの問題とを区別できるように、複数の場所を選択してください。 最大 16 個の場所を選択できます。
* **成功の基準**:

    **[テストのタイムアウト]**: この値を引き下げると、応答が遅い場合に警告されます。 テストは、この期間内にサイトから応答が返されない場合に、エラーとしてカウントされます。 **[従属要求の解析]**をオンにした場合、すべての画像、スタイル ファイル、スクリプト、その他依存するリソースがこの期間内に受信される必要があります。

    **[HTTP 応答]**: 成功としてカウントされる、返される状態コード。 200 は、通常の Web ページが返されたことを示すコードです。

    **[コンテンツの一致]**: "ようこそ!" のような、文字列。 それぞれの応答に大文字小文字を区別した完全一致があるかどうかをテストします。 文字列は、(ワイルドカードを含まない) プレーン文字列である必要があります。 ページ コンテンツが変更された場合は、この文字列も更新する必要がある可能性があることに注意してください。
* **アラート** は、既定では、エラーが 3 つの場所で 5 分を超えて存在する場合に送信されます。 エラーが 1 つの場所で発生している場合は、サイトでの問題ではなく、ネットワークの問題である可能性が高くなります。 ただし、しきい値は上げ下げでき、電子メールの送信先を変更することもできます。

    アラートが発生したときに呼び出される [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) を設定できます。 (ただし、現時点でクエリ パラメーターはプロパティとしては渡されないという点に注意してください)。

### <a name="test-more-urls"></a>他の URL のテスト
さらにテストを追加します。 たとえば、ホーム ページをテストするのに加えて、検索用の URL をテストしてデータベースが稼働していることを確認できます。


## <a name="monitor"></a>3.可用性テストの結果を表示する

数分後、**[更新]** をクリックしてテスト結果を表示します。 

![ホーム ブレード上の概要結果
](./media/app-insights-monitor-web-app-availability/14-availSummary-3.png)

散布図には、診断テスト手順の詳細が含まれたテスト結果のサンプルが表示されます。 テスト エンジンは、失敗したテストの診断の詳細を格納します。 成功したテストの場合、診断の詳細は実行のサブセットに対して格納されます。 緑色または赤色の点の上にポインターを置くと、テスト タイムスタンプ、テスト期間、場所、およびテスト名が表示されます。 散布図内の任意の点をクリックすると、テスト結果の詳細が表示されます。  

特定のテスト、場所を選択するか、または期間を短くすると、目的の期間に関するより詳細な結果が表示されます。 Search エクスプローラーを使用してすべての実行の結果を表示するか、または分析クエリを使用してこのデータに対してカスタム レポートを実行します。

生の結果に加えて、メトリックス エクスプローラーには次の 2 つの可用性メトリックがあります。 

1. 可用性: すべてのテスト実行にわたる、成功したテストの割合 (%)。 
2. テスト期間: すべてのテスト実行にわたる平均のテスト期間。

テスト名、場所にフィルターを適用すると、特定のテストまたは場所、あるいはその両方の傾向を分析できます。

## <a name="edit"></a> テストを確認または編集する

[概要] ページから、特定のテストを選択します。 指定したテストの結果の表示や編集ができるほか、一時的に無効にすることもできます。

![Web テストの編集または無効化
](./media/app-insights-monitor-web-app-availability/19-availEdit-3.png)

サービスに対するメンテナンスを実行している間、関連付けられた可用性テストまたはアラート ルールを無効にすることもできます。 

## <a name="failures"></a>エラーが発生する場合
赤い点をクリックします。

![赤い点をクリックします](./media/app-insights-monitor-web-app-availability/open-instance-3.png)


可用性テストの結果から、次のことを実行できます。

* サーバーから受信した応答を調べる。
* 失敗した要求インスタンスの処理中に、サーバー アプリによって送信されたテレメトリを開く。
* 懸案や作業の項目を Git または VSTS に記録して問題を追跡する。 バグには、このイベントへのリンクが含まれます。
* Visual Studio で Web テスト結果を開く。


*問題がないように見えるのに、エラーとして報告されました。* イメージ、スクリプト、スタイル シート、およびページによって読み込まれるその他のファイルすべてを確認してください。 これらのいずれかにエラーがある場合、メインの html ページの読み込みに問題がない場合でも、テストはエラーとして報告されます。

"*関連項目がない*" 場合は、 サーバー側のアプリケーションに対して Application Insights を設定している場合は、[サンプリング](app-insights-sampling.md)操作中のためである可能性があります。 

## <a name="multi-step-web-tests"></a>複数手順の Web テスト
URL の順序に関連するシナリオを監視することができます。 たとえば、販売 Web サイトを監視している場合は、ショッピング カートに商品を正しく追加できるかどうかをテストできます。

> [!NOTE] 
> 複数ステップ Web テストは課金対象です。 [価格の詳細](http://azure.microsoft.com/pricing/details/application-insights/)のページをご覧ください。
> 

複数手順のテストを作成するには、Visual Studio Enterprise を使用してシナリオを記録してから、その記録を Application Insights にアップロードします。 Application Insights は周期的にそのシナリオを再生し、応答を確認します。

> [!NOTE]
> テストでは、コード化された機能またはループは使用できません。 テストは .webtest スクリプトに完全に含まれている必要があります。 ただし、標準的なプラグインは使用できます。
>

#### <a name="1-record-a-scenario"></a>1.シナリオを記録する
Web セッションを記録するには、Visual Studio Enterprise を使用します。

1. Web パフォーマンス テストのプロジェクトを作成します。

    ![Visual Studio Enterprise Edition で、"Web パフォーマンスとロード テスト プロジェクト" テンプレートからプロジェクトを作成します。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-create.png)

 * "*"Web パフォーマンスとロード テスト プロジェクト" テンプレートが表示されない場合*" は、 Visual Studio Enterprise を閉じます。 **Visual Studio インストーラー**を開き、Visual Studio Enterprise のインストールを変更します。 **[個別のコンポーネント]** で **[Web パフォーマンスとロード テスト ツール]** を選択します。

2. .webtest ファイルを開き、記録を開始します。

    ![.webtest ファイルを開き、[記録] をクリックします。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-start.png)
3. テストでシミュレートする必要のあるユーザー操作を実行します。Web サイトを開いたり、買い物カゴに商品を追加したりしてください。 次に、テストを停止します。

    ![Internet Explorer で、Web テスト レコーダーが実行されます。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-record.png)

    長いシナリオは作成しないでください。 ここでは、100 ステップおよび 2 分が上限です。
4. テストを編集します。

   * 受信したテキストと応答コードを確認するための検証を追加します。
   * 余分な操作を削除します。 画像を求める依存要求、または広告あるいは追跡サイトへの依存要求を削除することも可能です。

     テスト スクリプトの編集が行えるだけです。カスタム コードの追加や他の Web テストの呼び出しはできません。 テストにループを挿入しないでください。 標準的な Web テスト プラグインを使用することができます。
5. 動作を確認するには、Visual Studio でテストを実行します。

    Web テスト ランナーは、web ブラウザーを開き、記録したアクションを繰り返します。 予想どおりに動作するかどうかを確認します。

    ![Visual Studio で .webtest ファイルを開き、[実行] をクリックします。](./media/app-insights-monitor-web-app-availability/appinsights-71webtest-multi-vs-run.png)

#### <a name="2-upload-the-web-test-to-application-insights"></a>2.Web テストを Application Insights にアップロードする
1. Application Insights ポータルで、Web テストを作成します。

    ![Web テスト ブレードで、[追加] を選択します。](./media/app-insights-monitor-web-app-availability/16-another-test.png)
2. 複数手順のテストを選択し、.webtest ファイルをアップロードします。

    ![複数手順の Web テストを選択します。](./media/app-insights-monitor-web-app-availability/appinsights-71webtestUpload.png)

    ping テストについても同様に、テストの場所、頻度、アラートのパラメーターを設定してください。

#### <a name="3-see-the-results"></a>3.結果を確認する

単一 URL のテストと同じように、テストの結果とエラーを表示します。

さらに、テスト結果をダウンロードして Visual Studio で確認することができます。

#### <a name="too-many-failures"></a>失敗が多すぎる場合

* 失敗の一般的な理由は、テストの実行時間が長すぎることです。 テストの実行は、2 分未満にする必要があります。

* テストが正常に完了するには、スクリプト、スタイル シート、画像など、ページのすべてのリソースが正しく読み込まれる必要があることに注意してください。

* Web テスト全体が .webtest スクリプトに含まれている必要があります。コード化された機能をテストで使用することはできません。

### <a name="plugging-time-and-random-numbers-into-your-multi-step-test"></a>複数手順のテストに対する時間とランダムな数の組み込み
外部からフィードされる株価など、時間に依存するデータを取得するツールをテストするとします。 Web テストを記録するときに、特定の時刻を使用する必要があります。ただし、その時刻は、StartTime と EndTime というテストのパラメーターとして設定しました。

![パラメーターを含む Web テスト。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-parameters.png)

テストを実行するときに、EndTime を現在の時刻に、StartTime を 15 分前に常に設定する必要があるとします。

時刻をパラメーター化するためには、Web テスト プラグインを使用します。

1. 目的の各変数のパラメーター値に対して Web テスト プラグインを追加します。 Web テスト ツールバーで、 **[Web テスト プラグインを追加]**を選択します。

    ![[Web テスト プラグインを追加] を選択し、種類を選択します。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugins.png)

    この例では、日時プラグインというインスタンスを 2 つ使用します。 1 つのインスタンスが "15 分前" 用、もう 1 つは "現在" 用です。
2. 各プラグインのプロパティを開きます。 名前を付け、現在の時刻を使用するように設定します。 いずれかのプロパティに、「Add Minutes = -15」を設定します。

    ![[名前]、[現在の時刻を使用する]、[分の追加] の各項目を設定します。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-parameters.png)
3. Web テスト パラメーターで、{{プラグイン名}} を使用して、プラグイン名を参照します。

    ![テスト パラメーターでは、{{プラグイン名}} を使用します。](./media/app-insights-monitor-web-app-availability/appinsights-72webtest-plugin-name.png)

ここでテストをポータルにアップロードします。 テストを実行するたびに動的な値を使用します。

## <a name="dealing-with-sign-in"></a>サインインの処理
ユーザーがアプリにサインインする場合、サインインの背後にあるページをテストできるように、サインインをシミュレートするためのさまざまなオプションがあります。 使用する方法は、アプリで提供されるセキュリティの種類によって異なります。

どの場合も、アプリケーションでテスト目的のみのアカウントを作成する必要があります。 可能であれば、実際のユーザーが Web テストの影響を受けないように、このテスト アカウントのアクセス許可を制限します。

### <a name="simple-username-and-password"></a>単純なユーザー名とパスワード
通常の方法で Web テストを記録します。 まず Cookie を削除します。

### <a name="saml-authentication"></a>SAML 認証
Web テストに使用できる SAML プラグインを使用します。

### <a name="client-secret"></a>クライアント シークレット
アプリにクライアント シークレットを含むサインイン ルートがある場合は、それを使用します。 クライアント シークレット サインインを提供するサービスの例として、Azure Active Directory (AAD) が挙げられます。 AAD の場合、クライアント シークレットはアプリ キーです。

アプリ キーを使用した Azure Web アプリのサンプル Web テストを次に示します。

![クライアント シークレットのサンプル](./media/app-insights-monitor-web-app-availability/110.png)

1. クライアント シークレット (AppKey) を使用して AAD からトークンを取得します。
2. 応答からベアラー トークンを抽出します。
3. 承認ヘッダーにベアラー トークンを使用して API を呼び出します。

Web テストが実際のクライアントであること、つまり、独自のアプリが AAD に登録されていることを確認し、clientId と appkey を使用します。 テスト対象のサービスの独自のアプリも AAD に登録されている場合: Web テストの "リソース" フィールドにはこのアプリの appID URI が反映されます。

### <a name="open-authentication"></a>オープン認証
オープン認証の例としては、Microsoft または Google アカウントを使用したサインインが挙げられます。 OAuth を使用する多くのアプリがクライアント シークレットに代わる機能を提供しているため、その可能性を調査することが最初の方法です。

テストで OAuth を使用してサインインする必要がある場合に、一般的な方法を次に示します。

* Fiddler などのツールを使用して、Web ブラウザー、認証サイト、アプリケーション間のトラフィックを調べます。
* 異なるコンピューターやブラウザーを使用するか、または長い間隔 (トークンを期限切れにさせる) で複数のサインインを実行します。
* 異なるセッションを比較して、認証サイトから返され、次にサインイン後にアプリケーション サーバーに渡されるトークンを識別します。
* Visual Studio を使用して Web テストを記録します。
* トークンをパラメーター化し、トークンが認証システムから返されたときに、パラメーターを設定し、サイトへのクエリでそれを使用します
  (Visual Studio は、テストのパラメーター化を試みますが、トークンを正しくパラメーター化しません)。


## <a name="performance-tests"></a>パフォーマンス テスト
Web サイトに対してロード テストを実行できます。 可用性テストと同じように、世界各地の複数のポイントから単純な要求または複数手順の要求を送信できます。 可用性テストとは異なり、複数の同時ユーザーをシミュレートするために、多数の要求が送信されます。

[概要] ブレードで、**[設定]**、**[Performance Tests (パフォーマンス テスト)]** の順に開きます。 テストを作成するときに、Visual Studio Team Services アカウントへの接続を求められるか、Visual Studio Team Services アカウントを作成します。

テストが完了すると、応答時間と成功率が表示されます。


![パフォーマンス テスト](./media/app-insights-monitor-web-app-availability/perf-test.png)

> [!TIP]
> パフォーマンス テストの影響を観察するには、[Live Stream](app-insights-live-stream.md) と [Profiler](app-insights-profiler.md) を使用します。
>

## <a name="automation"></a>Automation
* [PowerShell スクリプトを使用して、可用性テストを自動的に設定します](app-insights-powershell.md#add-an-availability-test)。
* アラートが発生したときに呼び出される [webhook](../monitoring-and-diagnostics/insights-webhooks-alerts.md) を設定する。

## <a name="qna"></a>疑問や 問題が発生した場合
* *Web テストからコードを呼び出すことはできますか。*

    いいえ。 テストの手順は、.webtest ファイルに含まれている必要があります。 他の Web テストの呼び出しまたはループの使用は許可されていません。 ただし、役に立つさまざまなプラグインがあります。
* *HTTPS はサポートされていますか。*

    TLS 1.1 と TLS 1.2 がサポートされています。
* *"Web テスト" と "可用性テスト" に違いはありますか。*

    この 2 つの用語は、同じ意味で参照されることがあります。 可用性テストは、複数ステップから成る Web テストに加えて単一の URL Ping テストも含む、より一般的な用語です。
* *ファイアウォールの内側で稼働している内部サーバーに対して可用性テストを使用したいと考えています。*

    解決策として、次の 2 つが考えられます。
    
    * [Web テスト エージェントの IP アドレス](app-insights-ip-addresses.md)からの受信要求を許可するようにファイアウォールを構成します。
    * 内部サーバーを定期的にテストする独自のコードを作成します。 このコードを、バック グラウンド プロセスとして、ファイアウォールの内側のテスト サーバーで実行します。 テスト プロセスは、コア SDK パッケージ内の [TrackAvailability()](https://docs.microsoft.com/dotnet/api/microsoft.applicationinsights.telemetryclient.trackavailability) API を使用して、その結果を Application Insights に送信できます。 この方法では、テスト サーバーに Application Insights のインジェスト エンドポイントへの送信アクセスが必要となりますが、受信要求を許可する代替方法と比べて、セキュリティのリスクは大幅に小さくなります。 結果は可用性 Web テストのブレードには表示されませんが、分析、検索、メトリックス エクスプローラーでの可用性の結果として表示されます。
* *複数手順の Web テストのアップロードが失敗します。*

    300 K のサイズ制限があります。

    ループはサポートされていません。

    他の Web テストへの参照はサポートされていません。

    データ ソースはサポートされていません。
* *複数手順のテストが完了しません。*

    1 テストあたりの要求は 100 個に制限されています。

    実行時間が 2 分を超えると、テストは停止します。
* *クライアント証明書でテストを実行するにはどうすればよいですか。*

    申し訳ありませんが、それはサポートされていません。


## <a name="next"></a>次のステップ
[診断ログを検索する][diagnostic]

[Troubleshooting][qna]

[Web テスト エージェントの IP アドレス](app-insights-ip-addresses.md)

<!--Link references-->

[azure-availability]: ../insights-create-web-tests.md
[diagnostic]: app-insights-diagnostic-search.md
[qna]: app-insights-troubleshoot-faq.md
[start]: app-insights-overview.md

