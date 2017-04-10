---
title: "データが存在しない場合のトラブルシューティング - Application Insights for .NET"
description: "Auzre Application Insights にデータが表示されない場合は、 ここで解決してください。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: carmonm
ms.assetid: e231569f-1b38-48f8-a744-6329f41d91d3
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 03/14/2017
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: a087df444c5c88ee1dbcf8eb18abf883549a9024
ms.openlocfilehash: db8d7b0e117276dd63ee83a1c2337e69253184b8
ms.lasthandoff: 03/15/2017


---
# <a name="troubleshooting-no-data---application-insights-for-net"></a>データが存在しない場合のトラブルシューティング - Application Insights for .NET
## <a name="some-of-my-telemetry-is-missing"></a>テレメトリの一部が見つからない
*Application Insights で、アプリによって生成されているイベントのごく一部しか表示されません。*

* 同じ部分が常に表示される場合は、アダプティブ [サンプリング](app-insights-sampling.md)が原因である可能性があります。 これを確認するには、([概要] ブレードから) [検索] を開き、要求やその他のイベントのインスタンスを確認します。 プロパティ セクションの下部で、[...] をクリックし、すべてのプロパティの詳細を取得します。 要求数が > 1 の場合は、サンプリングが実行中です。 
* それ以外の場合、料金プランの [データ速度の上限](app-insights-pricing.md#limits-summary) に達してる可能性があります。 これらの制限は分単位で適用されます。

## <a name="no-data-from-my-server"></a>サーバーからデータを取得できない
*Web サーバーにアプリをインストールしたのですが、テレメトリがなにも表示されません。開発用コンピューターでは正常に機能していました。*

* おそらく、ファイアウォールの問題でしょう。 [Application Insights がデータを送信できるようにファイアウォールの例外を設定](app-insights-ip-addresses.md)してください。

*既存のアプリを監視するために、Web サーバーに [Status Monitor をインストール](app-insights-monitor-performance-live-website-now.md)したのですが、結果がまったく表示されません。*

* 「 [Status Monitor のトラブルシューティング](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)」を参照してください。 

## <a name="q01"></a>Visual Studio に [Application Insights の追加] オプションが表示されない
*ソリューション エクスプローラーで既存のプロジェクトを右クリックしたときに、Application Insights のオプションが表示されません。*

* このツールでは、一部の種類の .NET プロジェクトがサポートされません。 Web プロジェクトと WCF プロジェクトはサポートされます。 その他の種類のプロジェクト (デスクトップ アプリケーション、サービス アプリケーションなど) では、 [Application Insights SDK を手動でプロジェクトに追加](app-insights-windows-desktop.md)できます。
* [Visual Studio 2013 Update 3 以降](http://go.microsoft.com/fwlink/?LinkId=397827)を使用しているかご確認ください。 これは、Application Insights SDK を提供する Developer Analytics Tools と共に事前インストールされています。
* **[ツール]**、**[拡張機能と更新プログラム]** の順に選択し、**Developer Analytics Tools** がインストールされ、有効になっていることを確認します。 有効な場合は、 **[更新プログラム]** をクリックして更新プログラムが存在するかどうかを確認します。
* [新しいプロジェクト] ダイアログを開いて [ASP.NET Web アプリケーション] を選択します。 そこに Application Insights オプションが表示された場合、Application Insights Tools はインストールされています。 それ以外の場合は、アンインストールしてから Application Insights Tools を再インストールしてください。

## <a name="q02"></a>Application Insights の追加に失敗する
*Application Insights を既存のプロジェクトに追加しようとしたときに、エラー メッセージが表示されます。*

考えられる原因:

* Application Insights ポータルとの通信に失敗した場合。つまり、
* ご利用の Azure アカウントになんらかの問題があります。
* お客様は、 [新しいリソースを作成しようとしたサブスクリプションまたはグループに対する読み取りアクセス権](app-insights-resources-roles-access-control.md)しか持っていません。

解決策:

* 適切な Azure アカウントのサインイン資格情報を指定していることを確認してください。 
* ブラウザーで、 [Azure ポータル](https://portal.azure.com)に対するアクセス権があることを確認してください。 設定を開き、制限がないかどうか確認してください。
* [Application Insights を既存のプロジェクトに追加するには](app-insights-asp-net.md): ソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights の追加] を選択します。
* 解決しない場合は、 [手動の手順](app-insights-windows-services.md) を実行して、ポータルでリソースを追加してから、SDK をプロジェクトに追加してください。 

## <a name="emptykey"></a>エラー「インストルメンテーション キーは空にできません」が発生しました
Application Insights をインストールしているとき、またはログ アダプターをインストールしているときに、何かの問題が発生した可能性があります。

ソリューション エクスプローラーでプロジェクトを右クリックし、**[Application Insights]、[Application Insights の構成]** の順に選択します。 Azure へのサインインを促すダイアログが表示されます。または、Application Insights のリソースを作成するか、既存のリソースを再利用します。

## <a name="NuGetBuild"></a> ビルド サーバーに NuGet パッケージが見つからない
*開発コンピューターでデバッグするときはすべて問題なくビルドされるのに、ビルド サーバーでは NuGet エラーが発生します。*

[NuGet パッケージの復元](http://docs.nuget.org/Consume/Package-Restore)に関するページと[自動パッケージの復元](http://docs.nuget.org/Consume/package-restore/migrating-to-automatic-package-restore)に関するページをご覧ください。

## <a name="missing-menu-command-to-open-application-insights-from-visual-studio"></a>Visual Studio から Application Insights を開くためのメニュー コマンドがない
*ソリューション エクスプローラーでプロジェクトを右クリックしても、Application Insights のコマンドが見当たりません。[Application Insights を開く] コマンドも表示されません。*

考えられる原因:

* Application Insights リソースを手動で作成したか、Application Insights Tools ではサポートされない種類のプロジェクトである。
* Visual Studio で Developer Analytics Tools が無効にされている。 
* 使用している Visual Studio が 2013 Update 3 よりも古い。

解決策:

* Visual Studio のバージョンが 2013 Update 3 以降であることを確認してください。
* **[ツール]**、**[拡張機能と更新プログラム]** の順に選択し、**Developer Analytics Tools** がインストールされ、有効になっていることを確認します。 有効な場合は、 **[更新プログラム]** をクリックして更新プログラムが存在するかどうかを確認します。
* ソリューション エクスプローラーでプロジェクトを右クリックします。 **[Application Insights] > [Application Insights の構成]** コマンドが表示される場合は、そのコマンドを使用して Application Insights サービスのリソースにプロジェクトを接続します。

それ以外の場合、ご使用のプロジェクトは Application Insights Tools で直接サポートされたプロジェクト タイプではありません。 テレメトリを表示するには、 [Azure ポータル](https://portal.azure.com)にサインインし、左側のナビゲーション バーで [Application Insights] を選択して目的のアプリケーションを選択してください。

## <a name="access-denied-on-opening-application-insights-from-visual-studio"></a>Visual Studio から Application Insights を開くとアクセスが拒否される
*[Application Insights を開く] メニュー コマンドで Azure ポータルに誘導されるものの、アクセスが拒否されたことを示すエラーが表示されます。*

![](./media/app-insights-asp-net-troubleshoot-no-data/access-denied.png)

[Application Insights をアプリに追加したときに作成されたリソース](app-insights-asp-net.md)へのアクセス権が、既定のブラウザーで前回使用された Microsoft サインインにはありません。 次の&2; つの理由が考えられます。 

* Microsoft アカウントが複数存在する (仕事用と個人用など)。 前回既定のブラウザーで使用されたサインインが、 [Application Insights をプロジェクトに追加](app-insights-asp-net.md)するためのアクセス権を持ったアカウントと異なります。 
  
  * 解決策: ブラウザー ウィンドウの右上に表示される名前をクリックしてサインアウトします。 そのうえで、アクセス権のあるアカウントでサインインします。 左側のナビゲーション バーで [Application Insights] をクリックし、対象のアプリケーションを選択します。
* 自分以外のだれかが Application Insights をプロジェクトに追加し、作成先となった [リソース グループへのアクセス権](app-insights-resources-roles-access-control.md) をあなたに与えるのを忘れている。 
  
  * 解決策: その人物が組織アカウントを使用していた場合、そのチームに追加してもらいます。それ以外の場合は、リソース グループへのアクセス権を個別に付与してもらいます。

## <a name="asset-not-found-on-opening-application-insights-from-visual-studio"></a>Visual Studio から Application Insights を開こうとすると "資産が見つからない" という内容のメッセージが表示される
*[Application Insights を開く] メニュー コマンドで Azure ポータルに誘導されるものの、資産が見つからなかったことを示すエラーが表示されます。*

考えられる原因:

* アプリケーションの Application Insights リソースが削除されている。
* プロジェクト ファイルを更新せずに ApplicationInsights.config を直接編集することによってインストルメンテーション キーが設定または変更された。 

テレメトリがどこに送信されるかは、ApplicationInsights.config 内のインストルメンテーション キーによって制御されます。 Visual Studio のコマンドを使用したときにどのリソースが開くかは、プロジェクト ファイル内のコード行によって制御されます。 

解決策:

* ソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights]、[Application Insights の構成] の順に選択します。 テレメトリを既存のリソースに送信するか、または新しく作成するかをダイアログで選択できます。 または
* リソースを直接開きます。 [Azure ポータル](https://portal.azure.com)にサインインして左側のナビゲーション バーにある [Application Insights] をクリックし、目的のアプリを選択します。

## <a name="where-do-i-find-my-telemetry"></a>テレメトリはどこで確認できますか
*[Microsoft Azure Portal](https://portal.azure.com) にサインインし、Azure ホーム ダッシュボードを見ています。自分の Application Insights データはどこで確認できるのでしょうか。*

* 左側のナビゲーション バーで [Application Insights] をクリックし、目的のアプリの名前をクリックします。 プロジェクトがまったく表示されない場合は、 [Web プロジェクトに Application Insights を追加または構成](app-insights-asp-net.md)する必要があります。
  
    いくつかのサマリー グラフが表示されます。 それらをクリックしていくと、詳しい情報が表示されます。
* Visual Studio でアプリをデバッグしているときに、[Application Insights] ボタンをクリックします。

## <a name="q03"></a> サーバー データが表示されない (またはデータが一切表示されない)
*アプリを実行し、Microsoft Azure の Application Insights サービスを開きましたが、どのグラフでも、データの収集方法についての説明か、必要な構成が行われていないという内容のメッセージしか表示されません。* または、*ページ ビューとユーザー データだけが表示され、サーバー データが表示されません。*

* Visual Studio で F5 キーを押し、アプリケーションをデバッグ モードで実行します。 ある程度テレメトリを生成するために、アプリケーションを使用します。 記録されたイベントが Visual Studio の出力ウィンドウに表示されていることを確認します。 
  
    ![](./media/app-insights-asp-net-troubleshoot-no-data/output-window.png)
* Application Insights ポータルで [診断検索](app-insights-diagnostic-search.md)を開きます。 通常、まずここにデータが表示されます。
* [更新] ボタンをクリックします。 ブレードは周期的に自動で更新されますが、手動でも更新できます。 時間範囲が広いと、更新間隔は長くなります。
* インストルメンテーション キーが一致していることを確認します。 Application Insights ポータルのアプリのメイン ブレードで、**[要点]** ボックスの一覧に表示される **[インストルメンテーション キー]** を確認します。 次に、Visual Studio からプロジェクトの ApplicationInsights.config を開き、 `<instrumentationkey>`を探します。 2 つのキーが等しいことを確認してください。 等しくない場合は、次の作業を行います。
  
  * ポータルで [Application Insights] をクリックし、適切なキーのアプリ リソースを探します。または、
  * Visual Studio のソリューション エクスプローラーでプロジェクトを右クリックし、[Application Insights]、[構成] の順に選択します。 適切なリソースにテレメトリを送信するようにアプリをリセットします。
  * 一致するキーが見つからない場合は、ポータルにサインインするときと同じ資格情報を Visual Studio で使用していることを確認してください。
    
    ![](./media/app-insights-asp-net-troubleshoot-no-data/ikey-check.png)
* [Microsoft Azure ホーム ダッシュボード](https://portal.azure.com)で、サービス正常性マップを確認します。 アラート表示がある場合は、"OK" が表示されるまで待ってから、Application Insights アプリケーション ブレードをいったん閉じて開き直します。
* [状態ブログ](http://blogs.msdn.com/b/applicationinsights-status/)も参照してください。
* [サーバー側 SDK](app-insights-api-custom-events-metrics.md) で作成したコードによって、`TelemetryClient` インスタンス内または `TelemetryContext` 内のインストルメンテーション キーが変更されていないか確認します。 自分が記述した [フィルターやサンプリング構成](app-insights-api-filtering-sampling.md) によって、必要なデータまで排除されていないかも確認してください。
* ApplicationInsights.config を編集した場合は、 [TelemetryInitializers と TelemetryProcessors](app-insights-api-filtering-sampling.md)の構成を慎重に確認します。 不適切な名前が付けられた型またはパラメーターがあると、SDK によってデータが送信されない場合があります。

## <a name="q04"></a>ページ ビュー、ブラウザー、利用状況にデータが表示されない
*サーバー応答時間グラフとサーバー要求グラフにはデータが表示されますが、[ページ ビューの読み込み時間]、[ブラウザー] ブレードまたは [利用状況] ブレードにはデータが表示されません。*

データは、Web ページのスクリプトによって取得されます。 

* Application Insights を既存の Web プロジェクトに追加した場合は、 [スクリプトを手動で追加する](app-insights-javascript.md)必要があります。
* Internet Explorer がサイトを互換モードで表示していないかご確認ください。
* ブラウザーのデバッグ機能 (一部のブラウザーでは F12 キーを押した後 [ネットワーク] を選択) を使用して、データが `dc.services.visualstudio.com`に送信されていることを確認します。

## <a name="no-dependency-or-exception-data"></a>依存関係または例外のデータが得られない
[依存関係のテレメトリ](app-insights-asp-net-dependencies.md)に関する記事と[例外のテレメトリ](app-insights-asp-net-exceptions.md)に関する記事をご覧ください。

## <a name="no-performance-data"></a>パフォーマンス データが表示されない
パフォーマンス データ (CPU、IO レートなど) は、[Java Web サービス](app-insights-java-collectd.md)、[Windows デスクトップ アプリ](app-insights-windows-desktop.md)、[IIS Web アプリおよびサービス (Status Monitor がインストールされている場合)](app-insights-monitor-performance-live-website-now.md)、[Azure Cloud Services](app-insights-azure.md) で使用できます。 パフォーマンス データは、[設定] の [サーバー] の下にあります。

Azure Web サイトでは利用できません。

## <a name="no-server-data-since-i-published-the-app-to-my-server"></a>サーバーにアプリを発行して以来、(サーバー) データが得られない
* すべての Microsoft. ApplicationInsights DLL が Microsoft.Diagnostics.Instrumentation.Extensions.Intercept.dll と一緒にサーバーにコピーされたことを確認します。
* ファイアウォールで、[一部の TCP ポートを開く](app-insights-ip-addresses.md#data-access-api)ことが必要な場合があります。
* プロキシを使用して社内ネットワークの外に送信しなければならない場合は、Web.config に [defaultProxy](https://msdn.microsoft.com/library/aa903360.aspx) を設定します。
* Windows Server 2008: [KB2468871](https://support.microsoft.com/kb/2468871)、[KB2533523](https://support.microsoft.com/kb/2533523)、[KB2600217](https://support.microsoft.com/kb/2600217) の各更新プログラムがインストールされていることを確認します。

## <a name="i-used-to-see-data-but-it-has-stopped"></a>データが表示されていたのに停止しました。
* [状態ブログ](http://blogs.msdn.com/b/applicationinsights-status/)をご確認ください。
* データ ポイントの月間クォータに達していませんか? [設定]、[クォータと価格] の順に開いてご確認ください。 上限に達している場合は、プランをアップグレードするか、追加容量分を購入することができます。 「 [料金プラン](https://azure.microsoft.com/pricing/details/application-insights/)」をご覧ください。

## <a name="i-dont-see-all-the-data-im-expecting"></a>予期しているデータがすべて表示されません
アプリケーションが送信するデータ量が多く、Application Insights SDK for ASP.NET バージョン 2.0.0-beta3 以降を使用している場合は、 [アダプティブ サンプリング](app-insights-sampling.md) 機能が動作して、テレメトリの一定の割合のみが送信される可能性があります。 

その機能を無効にすることもできますがお勧めしません。 サンプリングは、関連するテレメトリが診断用途で正しく送信されるように設計されています。 

## <a name="wrong-geographical-data-in-user-telemetry"></a>ユーザー テレメトリの地理データに誤りがある
市区町村、地域、国の各ディメンションは IP アドレスから取得され、必ずしも正確であるとは限りません。

## <a name="exception-method-not-found-on-running-in-azure-cloud-services"></a>Azure Cloud Services で実行する際の例外「メソッドが見つかりません」
.NET 4.6 でビルドした場合 4.6 は Azure Cloud Services のロールでは自動的にサポートされていません。 アプリを実行する前に、[各ロールに 4.6 をインストール](../cloud-services/cloud-services-dotnet-install-dotnet.md)してください。

## <a name="still-not-working"></a>問題が解決しない場合
* [Application Insights フォーラム](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)


