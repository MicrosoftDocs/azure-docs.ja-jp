---
title: Azure Application Insights を使用して Visual Studio でデバッグする
description: デバッグ中および運用環境の Web アプリのパフォーマンス分析と診断。
ms.topic: conceptual
ms.date: 03/17/2017
ms.custom: vs-azure
ms.openlocfilehash: 2507dbf7bb8294c949f434d5fa96ccc0af9a7eb3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103563540"
---
# <a name="debug-your-applications-with-azure-application-insights-in-visual-studio"></a>Visual Studio の Azure Application Insights でアプリケーションをデバッグする
Visual Studio (2015 以降) では、[Azure Application Insights](./app-insights-overview.md) からのテレメトリを使用して、ASP.NET Web アプリのパフォーマンスの分析と問題の診断を、デバッグ中と運用環境の両方において実行できます。

Visual Studio 2017 以降を使用して ASP.NET Web アプリを作成した場合は、Application Insights SDK が既に含まれています。 この操作をまだ行っていない場合は、[アプリに Application Insights を追加](./asp-net.md)してください。

実際の運用環境でアプリを監視するには、通常、[Azure Portal](https://portal.azure.com) で Application Insights のテレメトリを確認します。Azure Portal では、アラートを設定し、強力な監視ツールを適用できます。 ただし、デバッグを行う場合は、Visual Studio でテレメトリを検索して分析することもできます。 Visual Studio を使用したテレメトリの分析は、運用サイトからも、開発用コンピューターでのデバッグの実行からも行うことができます。 後者の場合、Azure Portal にテレメトリを送信するよう SDK をまだ構成していない場合でも、デバッグの実行を分析できます。 

## <a name="debug-your-project"></a><a name="run"></a> プロジェクトのデバッグ
F5 キーを使用して、ローカル デバッグ モードで Web アプリを実行します。 ある程度のテレメトリを生成するために、複数のページを開きます。

Visual Studio では、プロジェクトの Application Insights モジュールによってログに記録されたイベント数を確認できます。

![Visual Studio では、Application Insights ボタンはデバッグ時に表示されます。](./media/visual-studio/appinsights-09eventcount.png)

このボタンをクリックして、テレメトリを検索します。 

## <a name="application-insights-search"></a>Application Insights での検索
[Application Insights の検索] ウィンドウには、ログに記録されたイベントが表示されます (Application Insights を設定する際に Azure にサインインした場合は、Azure Portal でも同じイベントを検索できます)。

![プロジェクトを右クリックし、[Application Insights]、[検索] を選択する](./media/visual-studio/34.png)

> [!NOTE] 
> フィルターを選択または選択解除した後、テキスト検索フィールドの末尾にある [検索] ボタンをクリックしてください。
>

フリー テキスト検索は、イベント内の任意のフィールドに使用できます。 たとえば、ページの URL の一部や、クライアントの市区町村などのプロパティ値、トレース ログの特定の単語などを検索できます。

イベントをクリックすると、その詳細なプロパティが表示されます。

Web アプリに対する要求では、クリックしてコードを表示できます。

![[要求の詳細] でクリックしてコードを表示する](./media/visual-studio/31.png)

関連する項目を開いて、失敗した要求や例外を診断することもできます。

![[要求の詳細] で下にスクロールし、関連する項目を表示する](./media/visual-studio/41.png)

## <a name="view-exceptions-and-failed-requests"></a>例外と失敗した要求を表示する
例外のレポートは、[検索] ウィンドウに表示されます (古い種類の ASP.NET アプリケーションの一部では、フレームワークによって処理される例外が表示されるように、[例外の監視を設定する](./asp-net-exceptions.md)する必要があります)。

スタック トレースを取得するには、例外をクリックします。 Visual Studio でアプリのコードが開かれている場合は、コードの該当する行をスタック トレースからクリックできます。

![スクリーンショットは、スタック トレース内の About オブジェクトを示しています。](./media/visual-studio/17.png)

## <a name="view-request-and-exception-summaries-in-the-code"></a>コードに要求と例外の概要を表示する
各ハンドラー メソッドの上にある CodeLens 行には、Application Insights によってログに記録された過去 24 時間の要求と例外の数が表示されます。

![スクリーンショットは、コンテキスト ダイアログ ボックスの例外を示しています。](./media/visual-studio/21.png)

> [!NOTE] 
> CodeLens に Application Insights のデータが表示されるのは、[テレメトリを Application Insights ポータルに送信するようアプリを構成した](./asp-net.md)場合のみです。
>

[CodeLens での Application Insights の詳細については、こちらを参照してください。](./visual-studio-codelens.md)

## <a name="local-monitoring"></a>ローカル監視
Visual Studio 2015 Update 2 以降、Application Insights ポータルにテレメトリを送信するように SDK を構成していない (ApplicationInsights.config にインストルメンテーション キーが存在しない) 場合、診断ウィンドウには、直近のデバッグ セッションからのテレメトリが表示されます。 

これは以前のバージョンのアプリを既に発行済みである場合に役立ちます。 デバッグ セッションから得られたテレメトリが、Application Insights ポータル上の発行済みアプリから得られたテレメトリと混同されるのは望ましくありません。

これは、ポータルにテレメトリを送信する前に、いくつかの [カスタム テレメトリ](./api-custom-events-metrics.md) をデバッグする場合にも役立ちます。

* *最初は、ポータルにテレメトリを送信するよう Application Insights を構成しましたが、今は、Visual Studio でテレメトリだけを表示したくなりました。*
  
  * アプリからポータルにテレメトリを送信している場合でも、[検索] ウィンドウの [設定] に用意されているオプションでローカルの診断を検索できます。
  * ポータルへのテレメトリの送信を中止するには、ApplicationInsights.config から `<instrumentationkey>...` 行をコメント アウトしてください。もう一度ポータルにテレメトリを送信する準備ができたら、コメント解除します。


## <a name="next-steps"></a>次のステップ

 * **[Application Insights ポータルの操作](./overview-dashboard.md)** 。 ダッシュボード、強力な診断および分析ツール、アラート、アプリケーションのリアルタイム依存関係マップ、エクスポートされたテレメトリ データを表示します。 

