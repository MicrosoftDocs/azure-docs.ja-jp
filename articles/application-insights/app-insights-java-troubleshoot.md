---
title: "Java Web プロジェクトでの Application Insights のトラブルシューティング"
description: "トラブルシューティング ガイド - Application Insights でライブ Java アプリケーションを監視します。"
services: application-insights
documentationcenter: java
author: alancameronwills
manager: douge
ms.assetid: ef602767-18f2-44d2-b7ef-42b404edd0e9
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 11/16/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 4fc4561516490b9b285220e7ae688bf97384fe6e
ms.openlocfilehash: 9164a93308a4a2f3eed4ff6262942239df029dae


---
# <a name="troubleshooting-and-q-and-a-for-application-insights-for-java"></a>Java 用 Application Insights のトラブルシューティングおよび Q&A
[Java 用 Azure Application Insights][java] について疑問または問題はありませんか。 ここでは、いくつかのヒントを紹介します。

## <a name="build-errors"></a>ビルド エラー
**Eclipse で、Maven または Gradle を使用して Application Insights SDK を追加すると、ビルドまたはチェックサムの検証エラーが発生します。**

* 依存 <version> 要素にワイルドカード文字を含むパターン (例: (Maven) `<version>[1.0,)</version>`、または (Gradle) `version:'1.0.+'`) を使用している場合、代わりに特定のバージョン (`1.0.2` など) を指定してみてください。 最新のバージョンの [リリース ノート](https://github.com/Microsoft/ApplicationInsights-Java#release-notes) を参照してください。

## <a name="no-data"></a>データが表示されない
**Application Insights が正常に追加された後でアプリケーションを実行したところ、ポータルにデータが表示されません。**

* 少し待ってから、[最新の情報に更新] をクリックします。 グラフは周期的に自動で更新されますが、手動で更新することもできます。 更新間隔は、グラフの時間範囲によって異なります。
* プロジェクトのリソース フォルダーにある ApplicationInsights.xml ファイル内で、インストルメンテーション キーが定義されていることをご確認ください。
* この xml ファイルに `<DisableTelemetry>true</DisableTelemetry>` ノードが存在しないことをご確認ください。
* ファイアウォールで、dc.services.visualstudio.com への送信トラフィック用に TCP ポート 80 と 443 を開くことが必要な場合があります。 最新のバージョンの [ファイアウォール例外の一覧に関する記事](app-insights-ip-addresses.md)
* Microsoft Azure のスタート ボードで、サービス状態マップをご確認ください。 アラート表示がある場合は、"OK" が表示されるまで待ってから、Application Insights アプリケーション ブレードをいったん閉じて開き直します。
* プロジェクトのリソース フォルダーにある ApplicationInsights.xml ファイル内で、ルート ノードの下に `<SDKLogger />` 要素を追加して IDE コンソール ウィンドウへのログを有効にし、[Error] から始まるエントリを調べます。
* 正しい ApplicationInsights.xml ファイルが Java SDK によって正常に読み込まれたことを確認します。そのためには、コンソールの出力メッセージに「構成ファイルが正常に検出されました」というメッセージがあるかどうかを確認します。
* 構成ファイルが見つからない場合、出力メッセージを確認して構成ファイルの検索範囲を確かめ、ApplicationInsights.xml がこれらの検索場所のいずれかに存在していることを確認します。 通例、構成ファイルは Application Insights SDK の JAR ファイルの近くに見つかります。 たとえば、Tomcat の場合は WEB-INF/lib フォルダーがこれに相当します。

#### <a name="i-used-to-see-data-but-it-has-stopped"></a>データが表示されていたのに停止しました。
* [状態ブログ](http://blogs.msdn.com/b/applicationinsights-status/)をご確認ください。
* データ ポイントの月間クォータに達していませんか? Open Settings/Quota and Pricing to find out. 上限に達している場合は、プランをアップグレードするか、追加容量分を購入することができます。 「 [料金プラン](https://azure.microsoft.com/pricing/details/application-insights/)」をご覧ください。

#### <a name="i-dont-see-all-the-data-im-expecting"></a>予期しているデータがすべて表示されません
* [Quotas and Pricing (クォータと価格)] ブレードを開き、[サンプリング](app-insights-sampling.md)が実行中かどうかを確認します  (転送率が 100% の場合、サンプリングは実行されていません)。Application Insights サービスは、アプリから到着したテレメトリの一部だけを受け入れるように設定できます。 これにより、テレメトリの月間クォータの上限を超えないようにすることができます。 

## <a name="no-usage-data"></a>使用状況データがない
**要求と応答時間についてのデータは表示されますが、ページ ビュー、ブラウザー、またはユーザーについてのデータが表示されません。**

サーバーからテレメトリを送信するためのアプリ設定は正常に行われています。 次の手順は、[Web ブラウザーからテレメトリを送信するように Web ページを設定する][usage]ことです。

または、使用するクライアントが[電話やその他のデバイス][platforms]内のアプリの場合、そのアプリからテレメトリを送信できます。 

クライアントおよびサーバー テレメトリの両方の設定に、同じインストルメンテーション キーを使用します。 データは同じ Application Insights リソース内に表示され、クライアントとサーバーのイベントを関連付けることができるようになります。


## <a name="disabling-telemetry"></a>テレメトリの無効化
**テレメトリの収集を無効にする方法を教えてください。**

コード内で以下のように指定します。

```Java

    TelemetryConfiguration config = TelemetryConfiguration.getActive();
    config.setTrackingIsDisabled(true);
```

**または** 

プロジェクトのリソース フォルダーにある ApplicationInsights.xml を更新します。 ルート ノードの下に次の内容を追加します。

```XML

    <DisableTelemetry>true</DisableTelemetry>
```

XML メソッドを使用するうえで、値を変更した場合はアプリケーションを再起動する必要があります。

## <a name="changing-the-target"></a>ターゲットの変更
**自分のプロジェクトがデータを送信する Azure のリソースを変更するにはどうすればいいですか?**

* [新しいリソースのインストルメンテーション キーを取得します。][java]
* Eclipse の Azure Toolkit を使用してプロジェクトに Application Insights を追加した場合、Web プロジェクトを右クリックし、**[Azure]**、**[Configure Application Insights]** の順に選択して、キーを変更します。
* それ以外の場合は、プロジェクトのリソース フォルダーにある ApplicationInsights.xml 内のキーを更新します。

## <a name="debug-data-from-the-sdk"></a>SDK からのデータをデバッグする

**SDK の動作を確認する方法について教えてください。**

API の仕組みの詳細を取得するには、ApplicationInsights.xml 構成ファイルのルート ノードの下に `<SDKLogger/>` を追加します。

ファイルに出力するようにロガーに指示することもできます。

```XML

    <SDKLogger type="File">
      <enabled>True</enabled>
      <uniqueprefix>JavaSDKLog</uniqueprefix>
    </SDKLogger>
```

これらのファイルは `%temp%\javasdklogs` で見つけることができます。


## <a name="the-azure-start-screen"></a>Azure のスタート画面
**[Azure Portal](https://portal.azure.com) を表示しています。このマップから、自分のアプリについて何か情報が得られるのでしょうか?**

いいえ、そのマップは世界中の Azure サーバーの正常性を表しています。

*Azure のスタート画面 (ホーム画面) から、アプリに関するデータを見つける方法を教えてください。*

[Application Insights 用にアプリを設定][java]してあると仮定します。[参照] をクリックし、[Application Insights] を選択して、アプリ用に作成したアプリ リソースを選択します。 次からその場所にすばやくアクセスできるように、スタート画面にアプリをピン留めすることができます。

## <a name="intranet-servers"></a>イントラネット サーバー
**イントラネット上のサーバーを監視できますか?**

はい、お使いのサーバーがパブリック インターネットを介して Application Insights ポータルにテレメトリを送信できるなら、可能です。 

ファイアウォールでは、dc.services.visualstudio.com と f5.services.visualstudio.com への発信トラフィック用に TCP ポート 80 と 443 を開く必要がある場合があります。

## <a name="data-retention"></a>データの保持
**ポータルでのデータ保持期間はどのくらいですか?セキュリティで保護されていますか?**

[データの保持とプライバシー][data]に関するページを参照してください。

## <a name="next-steps"></a>次のステップ
**Java サーバー アプリ用に Application Insights を設定しました。他には何ができるか教えてください。**

* [Web ページの可用性の監視][availability]
* [Web ページの使用状況の監視][usage]
* [デバイス アプリの使用状況を追跡し、問題を診断する][platforms]
* [アプリの使用状況を追跡するためのコードを記述する][track]
* [診断ログのキャプチャ][javalogs]

## <a name="get-help"></a>問い合わせ
* [スタック オーバーフロー](http://stackoverflow.com/questions/tagged/ms-application-insights)

<!--Link references-->

[availability]: app-insights-monitor-web-app-availability.md
[data]: app-insights-data-retention-privacy.md
[java]: app-insights-java-get-started.md
[javalogs]: app-insights-java-trace-logs.md
[platforms]: app-insights-platforms.md
[track]: app-insights-api-custom-events-metrics.md
[usage]: app-insights-web-track-usage.md




<!--HONumber=Feb17_HO3-->


