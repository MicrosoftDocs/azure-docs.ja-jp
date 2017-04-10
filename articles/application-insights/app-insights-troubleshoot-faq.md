---
title: "Azure Application Insights に関するトラブルシューティングと Q & A | Microsoft Docs"
description: "Azure Application Insights について不明な点や問題点はありませんか。 ここで解決してください。"
services: application-insights
documentationcenter: .net
author: alancameronwills
manager: douge
ms.assetid: 0e3b103c-6e2a-4634-9e8c-8b85cf5e9c84
ms.service: application-insights
ms.workload: mobile
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 08/24/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 182e28e37eb56c547e28524f2a3e13f042238cb4
ms.openlocfilehash: e066a7fc671399ba44bec35a2ea860fccddb4cc5


---
# <a name="questions---application-insights-for-aspnet"></a>質問 - ASP.NET 向けの Application Insights
## <a name="configuration-problems"></a>構成の問題
*次のセットアップで問題が発生しています。*

* [.NET アプリ](app-insights-asp-net-troubleshoot-no-data.md)
* [既に実行中のアプリの監視](app-insights-monitor-performance-live-website-now.md#troubleshooting-runtime-configuration-of-application-insights)
* [Azure 診断](app-insights-azure-diagnostics.md)
* [Java Web アプリ](app-insights-java-troubleshoot.md)
* [その他のプラットフォーム](app-insights-platforms.md)

*サーバーからデータを取得できません。*

* [ファイアウォール例外の設定](app-insights-ip-addresses.md)
* [ASP.NET サーバーのセットアップ](app-insights-monitor-performance-live-website-now.md)
* [Java サーバーのセットアップ](app-insights-java-agent.md)

## <a name="can-i-use-application-insights-with-"></a>Application Insights と共に使用できるもの
[プラットフォームを確認][platforms]

## <a name="is-it-free"></a>これは無料ですか。

はい、試験段階用です。 Basic 価格プランでは、アプリケーションは毎月無料で一定のデータ許容量を送信できます。 無料許容量は、開発や、少数のユーザー向けにアプリを発行するのに十分な大きさです。 指定した以上のデータ量が処理されることを防ぐために上限を設定できます。

連続エクスポートなどの特定の機能を取得するには、エンタープライズ プランを使用する必要があります。 エンタープライズ プランを使用すると、1 日あたりの料金が発生します。

[価格プランを参照してください](https://azure.microsoft.com/pricing/details/application-insights/)。


## <a name="a-nameq14awhat-does-application-insights-modify-in-my-project"></a><a name="q14"></a>Application Insights によってどのような変更がプロジェクトに加えられますか?
詳細は、プロジェクトの種類によって異なります。 Web アプリケーションの場合:

* 次のファイルがプロジェクトに追加されます。

  * ApplicationInsights.config
  * ai.js
* Installs these NuGet packages:

  * *Application Insights API* - コア API
  * *Application Insights API for Web Applications* - サーバーからテレメトリを送信するために使用されます
  * *Application Insights API for JavaScript Applications* - クライアントからテレメトリを送信するために使用されます

    The packages include these assemblies:
  * Microsoft.ApplicationInsights
  * Microsoft.ApplicationInsights.Platform
* 次の項目を挿入します。

  * web.config
  * packages.config
* (新しいプロジェクトのみ。[Application Insights を既存のプロジェクトに追加している場合][start]は、手動でこの操作を行う必要があります)。これらを Application Insights リソース ID で初期化するためのスニペットを、クライアントとサーバーのコードに挿入します。 たとえば、MVC アプリでは、Views/Shared/_Layout.cshtml マスター ページにコードが挿入されます。

## <a name="how-do-i-upgrade-from-older-sdk-versions"></a>以前のバージョンの SDK からアップグレードする方法。
お使いのアプリケーションに適切な SDK については、「 [リリース ノート](app-insights-release-notes.md) 」をご覧ください。

## <a name="a-nameupdateahow-can-i-change-which-azure-resource-my-project-sends-data-to"></a><a name="update"></a>自分のプロジェクトがデータを送信する Azure のリソースを変更するにはどうすればいいですか?
ソリューション エクスプローラーで、 `ApplicationInsights.config` を右クリックし、[ **Application Insights の更新**] を選択します。 Azure の既存または新規のリソースにデータを送信できます。 更新ウィザードでは、サーバー SDK のデータの送信先を決定する、ApplicationInsights.config のインストルメンテーション キーを変更します。 [すべて更新] を選択解除している場合を除き、Web ページ内のキーが表示される場所でもキーが変更されます。

#### <a name="a-namedataahow-long-is-data-retained-in-the-portal-is-it-secure"></a><a name="data"></a>ポータルでのデータ保持期間はどのくらいですか? セキュリティで保護されていますか?
[データの保持とプライバシー][data]に関するページをご覧ください。

## <a name="logging"></a>ログの記録
#### <a name="a-namepostahow-do-i-see-post-data-in-diagnostic-search"></a><a name="post"></a>診断検索で POST データを表示する方法を教えてください。
POST データは自動ではログに記録されませんが、TrackTrace 呼び出しを使用してメッセージ パラメーターにデータを格納できます。 文字列プロパティの制限よりもサイズ制限は大きいですが、フィルター処理には使用できません。

## <a name="security"></a>セキュリティ
#### <a name="is-my-data-secure-in-the-portal-how-long-is-it-retained"></a>ポータルのデータはセキュリティで保護されていますか? 保持期間はどれくらいですか?
[データの保持とプライバシー][data]に関するページを参照してください。

## <a name="a-nameq17a-have-i-enabled-everything-in-application-insights"></a><a name="q17"></a> Application Insights の機能をすべて有効にしているでしょうか?
| 表示内容 | 表示方法 | 用途 |
| --- | --- | --- |
| 可用性グラフ |[Web テスト](app-insights-monitor-web-app-availability.md) |Web アプリが稼働しているか確認する |
| サーバー アプリ パフォーマンス: 応答時間、... |[Application Insights をプロジェクトに追加する](app-insights-asp-net.md)か、[AI Status Monitor をサーバーにインストールする](app-insights-monitor-performance-live-website-now.md) (または独自のコードを記述して[依存関係を追跡する](app-insights-api-custom-events-metrics.md#trackdependency)) |パフォーマンスの問題を検出する |
| 依存関係テレメトリ |[AI Status Monitor をサーバーにインストールする](app-insights-monitor-performance-live-website-now.md) |データベースや、その他の外部コンポーネントの問題を診断する |
| 例外からスタック トレースを取得する |[コード内に TrackException 呼び出しを挿入する](app-insights-search-diagnostic-logs.md#exceptions) (自動で報告されるものもある) |例外を検出して診断する |
| ログ トレースの検索 |[ログ アダプターを追加する](app-insights-search-diagnostic-logs.md) |例外、パフォーマンスの問題を診断する |
| クライアントの利用状況の基本情報: ページ ビュー、セッション、... |[Web ページの JavaScript の初期化子](app-insights-javascript.md) |Usage analytics |
| クライアントのカスタム メトリック |[Web ページでの呼び出しの追跡](app-insights-api-custom-events-metrics.md) |ユーザー エクスペリエンスを向上させる |
| サーバーのカスタム メトリック |[サーバーでの呼び出しの追跡](app-insights-api-custom-events-metrics.md) |Business intelligence |

## <a name="automation"></a>Automation
Application Insights リソースを作成および更新するための [PowerShell スクリプトを作成](app-insights-powershell.md) することができます。

## <a name="more-answers"></a>その他の回答
* [Application Insights フォーラム](https://social.msdn.microsoft.com/Forums/vstudio/en-US/home?forum=ApplicationInsights)

<!--Link references-->

[data]: app-insights-data-retention-privacy.md
[platforms]: app-insights-platforms.md
[start]: app-insights-overview.md
[windows]: app-insights-windows-get-started.md



<!--HONumber=Feb17_HO2-->


