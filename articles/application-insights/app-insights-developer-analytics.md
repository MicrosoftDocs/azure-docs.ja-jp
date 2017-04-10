---
title: "Web 開発者向けのパフォーマンスと使用状況の分析 - Azure Application Insights | Microsoft Docs"
description: "Visual Studio、Application Insights、HockeyApp を使用した DevOps"
author: alancameronwills
services: application-insights
documentationcenter: 
manager: douge
ms.assetid: 1f1207a5-9019-451f-bff0-c67c4685ba32
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 05/18/2016
ms.author: awills
translationtype: Human Translation
ms.sourcegitcommit: 08ce387dd37ef2fec8f4dded23c20217a36e9966
ms.openlocfilehash: 06d01f6fb044c4ad3b3a4d95a11a101ef01ca121


---
# <a name="developer-analytics-with-application-insights-and-hockeyapp"></a>Application Insights と HockeyApp による開発者分析


多くのプロジェクトでは、迅速な [DevOps](https://en.wikipedia.org/wiki/DevOps) サイクルを展開しています。 アプリケーションを作成して配布し、そのパフォーマンスやユーザーの操作に関するフィードバックを入手した後、その知識を利用して将来の開発サイクルの計画を立てます。 

使用状況とパフォーマンスを監視するには、ユーザー自身によるフィードバックだけでなく、ライブ アプリケーションからのテレメトリを収集することが重要です。 

多くのシステムは複数のコンポーネントで構成されており、これには、Web サービス、バックエンド プロセッサまたはデータ ストア、ユーザーのブラウザーで実行されているクライアント ソフトウェアや携帯電話などのデバイスでアプリとして実行されているクライアント ソフトウェアが含まれます。 このようにさまざまなコンポーネントからのテレメトリは、ひとまとめにする必要があります。

リリースによっては、指定したテスト担当者に配布が制限される場合があります。そのため、Microsoft では、フライティング (限定した対象ユーザーによる新機能のテスト) や A/B テスト (代替 UI の並列テスト) も用意しました。

複数のクライアント コンポーネントとサーバー コンポーネント全体で配布を管理して監視を統合することは簡単なタスクではありません。 このプロセスは、アプリケーションのアーキテクチャの重要な部分です。この種のシステムを作成するには、反復的な開発サイクルと優れた監視ツールが必要不可欠です。

この記事では、devOps サイクルの監視の側面がプロセスの他の部分にどのように適合しているかについて説明します。 

具体的な例については、複数のクライアント コンポーネントとサーバー コンポーネントを使用した [興味深い事例](http://aka.ms/mydrivingdocs) があります。

## <a name="a-devops-cycle"></a>DevOps サイクル
Visual Studio と開発者分析ツールによって、十分に統合された devOps エクスペリエンスが実現します。 たとえば、Web アプリケーション (Java、Node.js、または ASP.NET) の典型的なサイクルを次に示します。

![Web app devops cycle](./media/app-insights-developer-analytics/040.png)

* 開発者は、コード リポジトリにチェックインするか、またはメイン ブランチにマージします。 この図では、リポジトリに Git を使用していますが、同様に [Team Foundation バージョン管理](https://www.visualstudio.com/docs/tfvc/overview)も使用できます。
* 変更により、ビルドと単体テストがトリガーされます。 ビルド サービスは、 [Visual Studio Team Services か、それと同等のオンプレミスの Team Foundation Server](https://www.visualstudio.com/docs/vsts-tfs-overview)にあります。 
* ビルドと単体テストが成功すると、 [自動デプロイをトリガー](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition)できます。 Web アプリのホストには、独自の Web サーバーまたは Microsoft Azure を指定できます。 
* ライブ アプリからのテレメトリが、サーバーと[クライアント ブラウザー](app-insights-javascript.md)の両方から [Application Insights](app-insights-overview.md) に送信されます。 これで、アプリのパフォーマンスと使用パターンの両方を分析できます。 強力な [検索ツール](app-insights-analytics.md) を使用すると、問題の診断に役立ちます。 [アラート](app-insights-alerts.md) を使用すると、問題が発生するとすぐにそれについて把握できるようになります。 
* 次の開発サイクルには、ライブ テレメトリの分析から通知されます。

### <a name="device-and-desktop-apps"></a>デバイス アプリとデスクトップ アプリ
デバイス アプリとデスクトップ アプリでは、サイクルの配布部分が若干異なります。これは、アップロード先のサーバーが&1; つや&2; つではないためです。 代わりに、ビルドと単体テストが成功すると、[HockeyApp へのアップロードをトリガー](https://support.hockeyapp.net/kb/third-party-bug-trackers-services-and-webhooks/how-to-use-hockeyapp-with-visual-studio-team-services-vsts-or-team-foundation-server-tfs)できます。 HockeyApp は、テスト ユーザーのチーム (または必要に応じて一般ユーザー) への配布を管理します。 

![Device devops cycle](./media/app-insights-developer-analytics/030.png)

また、HockeyApp は、次の形式でパフォーマンスと使用状況データを収集します。

* スクリーンショット付きのユーザーからのフィードバック
* クラッシュ レポート
* 開発者がコード化したカスタム テレメトリ

繰り返しますが、入手したフィードバックを考慮して将来の開発計画を立てるように devOps サイクルは作成されています。

## <a name="setting-up-developer-analytics"></a>開発者分析の設定
モバイル、Web、デスクトップを問わず、アプリケーションの各コンポーネントでは、基本的に手順は同じです。 多くの種類のアプリでは、これらの手順の一部が Visual Studio によって自動的に実行されます。

1. 適切な SDK をアプリに追加します。 デバイス アプリの場合は HockeyApp、Web サービスの場合は Application Insights です。 それぞれ、プラットフォーム応じていくつかのバリエーションがあります  (デスクトップ アプリではどちらの SDK も使用できますが、HockeyApp をお勧めします)。
2. 使用した SDK に応じて、Application Insights または HockeyApp ポータルにアプリを登録します。 ここには、ライブ アプリによる分析が表示されます。 構成したインストルメンテーション キーまたは ID をアプリに取り込むと、SDK ではテレメトリの送信先が認識されます。
3. カスタム コード (必要な場合) を追加して、イベントやメトリックをログに記録します。これは、診断やパフォーマンスまたは使用状況の分析に役立ちます。 多数の監視機能が組み込まれているため、最初のサイクルではこれは必要ありません。
4. デバイス アプリの場合:
   * デバッグ ビルドを HockeyApp にアップロードします。 そこから、テスト ユーザーのチームに配布できます。 それ以降、ビルドをアップロードするたびに、チームに通知されます。
   * 継続的なビルド サービスを設定する際は、プラグインの手順を使用して HockeyApp にアップロードするリリース定義を作成します。

### <a name="analytics-and-export-for-hockeyapp-telemetry"></a>HockeyApp テレメトリの分析とエクスポート
[ブリッジを設定](app-insights-hockeyapp-bridge-app.md)すると、Application Insights の Analytics 機能と連続エクスポート機能を使用して HockeyApp のカスタムおよびログ テレメトリを調査することができます。

## <a name="next-steps"></a>次のステップ
アプリの種類ごとの詳細な手順については、以下のリンク先を参照してください。

* [ASP.NET Web アプリ](app-insights-asp-net.md) 
* [Java Web アプリ](app-insights-java-get-started.md)
* [Node.js Web アプリ](https://github.com/Microsoft/ApplicationInsights-node.js)
* [iOS アプリ](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-ios)
* [Mac OS X アプリ](https://support.hockeyapp.net/kb/client-integration-ios-mac-os-x-tvos/hockeyapp-for-mac-os-x)
* [Android アプリ](https://support.hockeyapp.net/kb/client-integration-android/hockeyapp-for-android-sdk)
* [ユニバーサル Windows アプリ](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/how-to-create-an-app-for-uwp)
* [Windows Phone 8 および 8.1 アプリ](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-phone-silverlight-apps-80-and-81)
* [Windows Presentation Foundation アプリ](https://support.hockeyapp.net/kb/client-integration-windows-and-windows-phone/hockeyapp-for-windows-wpf-apps)




<!--HONumber=Jan17_HO4-->


