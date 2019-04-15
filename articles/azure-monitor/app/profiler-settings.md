---
title: Azure Application Insights Profiler の設定ウィンドウを使用する | Microsoft Docs
description: Profiler の状態を確認して、プロファイリング セッションを開始します
services: application-insights
documentationcenter: ''
author: cweining
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: mbullwin
ms.date: 08/06/2018
ms.author: cweining
ms.openlocfilehash: 9603c45443c6339a127f977600eeff2ba57a283f
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/03/2019
ms.locfileid: "58884161"
---
# <a name="configure-application-insights-profiler"></a>Application Insights Profiler を構成する

## <a name="profiler-settings-pane"></a>Profiler の設定ウィンドウ

Azure Application Insights Profiler の設定ウィンドウを開くには、Application Insights の [パフォーマンス] ウィンドウに移動して、**[Profiler]** ボタンを選択します。

![Profiler の構成ウィンドウ][configure-profiler-entry]

**[Application Insights Profiler の構成]** ウィンドウには 4 つの機能が含まれています。 
* **[今すぐプロファイル]**:Application Insights のこのインスタンスにリンクされているすべてのアプリのプロファイリング セッションを開始します。
* **[リンクされたアプリ]**:この Application Insights リソースにプロファイリング データを送信しているアプリケーションの一覧が表示されます。
* **[セッションは進行中です]**:**[今すぐプロファイル]** を選択したときのセッションの状態が表示されます。 
* **[最近のプロファイリング セッション]**:過去のプロファイリング セッションに関する情報が表示されます。

![オンデマンドでのプロファイラー][profiler-on-demand]

## <a name="app-service-environment"></a>App Service 環境
Azure App Service Environment の構成によっては、エージェントの状態を確認するための呼び出しがブロックされます。 エージェントが実行されている場合でも、ウィンドウにはエージェントが実行されていないというメッセージが表示される場合があります。 そうかどうかを確認するには、アプリケーションの webjob を調べます。 すべてのアプリ設定値が正しく、アプリケーションに Application Insights サイト拡張機能がインストールされている場合は、Profiler が実行されています。 アプリケーションが十分なトラフィックを受信している場合は、最近のプロファイリング セッションが一覧に表示されます。

## <a id="profileondemand"></a> Profiler を手動でトリガーする

### <a name="minimum-requirements"></a>最小要件 
プロファイラー セッションをユーザーが手動でトリガーするためには、そのロールに最低でも、Application Insights コンポーネントの "書き込み" アクセス権が必要です。 ほとんどの場合、このアクセス権は自動的に与えられており、追加の作業は必要ありません。 問題が発生している場合、追加するサブスクリプション スコープ ロールは "Application Insights コンポーネント共同作成者" ロールになります。 [Azure Monitoring でのロール アクセス制御に関する詳細情報を参照してください](https://docs.microsoft.com/en-us/azure/azure-monitor/app/resources-roles-access-control)。

シングル クリックで Profiler を手動でトリガーできます。 Web パフォーマンス テストを実行しているものとします。 Web アプリに負荷がかかった状態のパフォーマンスを把握するには、トレースが必要です。 ロード テストがいつ実行されるかがわかるので、トレースがキャプチャされるタイミングを制御できることは重要です。 しかし、ランダムなサンプリング間隔では失敗する可能性があります。

次のセクションでは、このシナリオがどのように機能するかについて説明します。

### <a name="step-1-optional-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>手順 1:(省略可能) Web パフォーマンス テストを開始して Web アプリへのトラフィックを生成する

Web アプリが既にトラフィックを受信している、または手動でトラフィックを生成する場合は、このセクションをスキップして手順 2 に進みます。

1. Application Insights ポータルで、**[構成]** > **[パフォーマンス テスト]** を選択します。 

1. 新しいパフォーマンス テストを開始するには、**[新規]** ボタンを選択します。

   ![新しいパフォーマンス テストを作成する][create-performance-test]

1. **[新しいパフォーマンス テスト]** ウィンドウで、テスト対象の URL を構成します。 すべての設定を既定値のままにし、**[テストの実行]** を選択してロード テストの実行を開始します。

    ![ロード テストを構成する][configure-performance-test]

    新しいテストはまずキューに登録された後、"*進行中*" 状態になります。

    ![ロード テストが送信されてキューに登録される][load-test-queued]

    ![ロード テストの実行が行われている][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>手順 2:オンデマンド セッションで Profiler を開始する

1. ロード テストが実行されたら、Profiler を開始し、負荷がかかっている間に Web アプリでトレースをキャプチャします。

1. **[Configure Profiler]\(Profiler の構成\)** ウィンドウに移動します。


### <a name="step-3-view-traces"></a>手順 3:トレースを表示する

Profiler の実行が完了したら、通知の指示に従って [パフォーマンス] ページに移動し、トレースを表示します。

## <a name="troubleshoot-the-profiler-on-demand-session"></a>Profiler のオンデマンド セッションをトラブルシューティングする

オンデマンド セッションの後、次のような Profiler タイムアウト エラー メッセージが表示されることがあります。

![Profiler のタイムアウト エラー][profiler-timeout]

このエラーは、次のいずれかの理由で受け取る可能性があります。

* オンデマンドでの Profiler セッションは成功したものの、Application Insights が収集したデータを処理するのに予想より時間がかかった。  

  データが 15 分以内に処理されない場合、ポータルにタイムアウト メッセージが表示されます。 ただし、しばらくすると Profiler のトレースが表示されます。 エラー メッセージが表示される場合は、とりあえず無視してください。 現在、この問題の修正に積極的に取り組んでいます。

* Web アプリでオンデマンド機能がない古いバージョンの Profiler エージェントが使用されている。  

  以前に Application Insights Profiler を有効にしたことがある場合、オンデマンド機能の使用を開始するには Profiler エージェントの更新が必要になることがあります。
  
App Services の **[アプリケーション設定]** ウィンドウに移動し、次の設定を確認します。
* **APPINSIGHTS_INSTRUMENTATIONKEY**:Application Insights 用の適切なインストルメンテーション キーに置き換えます。
* **APPINSIGHTS_PORTALINFO**:ASP.NET
* **APPINSIGHTS_PROFILERFEATURE_VERSION**:1.0.0

前述の値のいずれかが設定されていない場合は、次の手順に従って、最新のサイト拡張機能をインストールします。

1. App Services ポータルの **[Application Insights]** ウィンドウに移動します。

    ![App Services ポータルから Application Insights を有効にする][enable-app-insights]

1. **[Application Insights]** ウィンドウに **[更新]** ボタンが表示される場合は、それを選択して Application Insights サイト拡張機能を更新します。これにより、最新の Profiler エージェントがインストールされます。

    ![サイト拡張機能の更新][update-site-extension]

1. Profiler を確実に有効にするため、**[変更]** を選択し、**[OK]** を選択して、変更を保存します。

    ![App Insights を変更して保存する][change-and-save-appinsights]

1. App Service の **[アプリケーション設定]** ウィンドウに戻り、次の値が設定されていることを確認します。
   * **APPINSIGHTS_INSTRUMENTATIONKEY**:Application Insights 用の適切なインストルメンテーション キーに置き換えます。
   * **APPINSIGHTS_PORTALINFO**:ASP.NET 
   * **APPINSIGHTS_PROFILERFEATURE_VERSION**:1.0.0

     ![Profiler 用のアプリ設定][app-settings-for-profiler]

1. 必要に応じて、**[拡張機能]** を選択し、拡張機能のバージョンを確認して、更新プログラムを利用できるかどうかを判断します。

    ![拡張機能の更新プログラムを確認する][check-for-extension-update]

## <a name="next-steps"></a>次の手順
[Profiler を有効にしてトレースを表示する](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[create-performance-test]: ./media/profiler-settings/new-performance-test.png
[configure-performance-test]: ./media/profiler-settings/configure-performance-test.png
[load-test-queued]: ./media/profiler-settings/load-test-queued.png
[load-test-in-progress]: ./media/profiler-settings/load-test-inprogress.png
[enable-app-insights]: ./media/profiler-settings/enable-app-insights-blade-01.png
[update-site-extension]: ./media/profiler-settings/update-site-extension-01.png
[change-and-save-appinsights]: ./media/profiler-settings/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/profiler-settings/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/profiler-settings/check-extension-update-01.png
[profiler-timeout]: ./media/profiler-settings/profiler-timeout.png
