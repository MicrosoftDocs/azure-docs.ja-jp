---
title: Azure Application Insights Profiler の設定ウィンドウを使用する | Microsoft Docs
description: Profiler の状態を確認して、プロファイリング セッションを開始します
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 9d9cc377ead0c297e8334d34255bd2c7c7cd39fc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86499411"
---
# <a name="configure-application-insights-profiler"></a>Application Insights Profiler を構成する

## <a name="updated-profiler-agent"></a>更新された Profiler エージェント
トリガー機能は、バージョン 2.6 以降のプロファイラー エージェントでのみ動作します。 Azure App Service を実行している場合、エージェントは自動的に更新されます。 Web サイトの Kudu URL にアクセスし、`https://yourwebsite.scm.azurewebsites.net/diagnosticservices` のように末尾に /diagnosticservices 追加すると、実行しているエージェントのバージョンを確認できます。 Application Insights Profiler の Web ジョブはバージョン 2.6 以降である必要があります。 Web アプリを再起動することで、強制的にアップグレードすることができます。 

プロファイラーを VM またはクラウド サービスで実行している場合は、Windows Azure Diagnostics (WAD) 拡張機能バージョン 16.0.4 以降がインストールされている必要があります。 WAD のバージョンを確認するには、VM にログオンし、次のディレクトリを参照します。C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.16.0.4。 ディレクトリ名が、インストールされている WAD のバージョンです。 新しいバージョンが利用可能になると、Azure VM エージェントは WAD を自動的に更新します。

## <a name="profiler-settings-page"></a>Profiler 設定ページ

Azure Application Insights Profiler の設定ウィンドウを開くには、Application Insights の [パフォーマンス] ウィンドウに移動して、 **[プロファイラーの構成]** ボタンを選択します。

![Profiler 設定ページを開くためのリンク][configure-profiler-entry]

次のようなページが開きます。

![Profiler 設定ページ][configure-profiler-page]

**[Application Insights Profiler の構成]** ページには以下の機能があります。

| 特徴量 | 説明 |
|-|-|
今すぐプロファイル | Application Insights のこのインスタンスにリンクされているすべてのアプリのプロファイリング セッションを開始します。
トリガー | プロファイラーを実行するトリガーを構成できます。 
最近のプロファイリング セッション | 過去のプロファイリング セッションに関する情報が表示されます。

## <a name="profile-now"></a>今すぐプロファイル
このオプションを使用すると、必要に応じてプロファイリング セッションを開始できます。 このリンクをクリックすると、この Application Insights インスタンスにデータを送信しているすべてのプロファイラー エージェントがプロファイルのキャプチャを開始します。 5 分から 10 分後に、プロファイル セッションが下の一覧に表示されます。

プロファイラー セッションをユーザーが手動でトリガーするためには、そのロールに最低でも、Application Insights コンポーネントの "書き込み" アクセス権が必要です。 ほとんどの場合、このアクセス権は自動的に与えられており、追加の作業は必要ありません。 問題が発生している場合、追加するサブスクリプション スコープ ロールは "Application Insights コンポーネント共同作成者" ロールになります。 [Azure Monitoring でのロール アクセス制御に関する詳細情報を参照してください](./resources-roles-access-control.md)。

## <a name="trigger-settings"></a>トリガーの設定
![[トリガーの設定] のポップアップ][trigger-settings-flyout]

メニュー バーの [トリガー] ボタンをクリックすると、[トリガーの設定] ボックスが開きます。 CPU またはメモリの使用率が設定したレベルに達したときに、プロファイルを開始するようにトリガーを設定できます。

| 設定 | 説明 |
|-|-|
オン/オフ ボタン | オン: このトリガーによってプロファイラーを開始できます。オフ: プロファイラーはこのトリガーによって開始されません。
メモリのしきい値 | メモリの使用率がこの割合になると、プロファイラーが開始されます。
Duration | プロファイラーがトリガーされたときに実行される時間の長さを設定します。
クールダウン | プロファイラーがトリガーされた後、メモリまたは CPU の使用率を再度確認するまでの待機時間を設定します。

## <a name="recent-profiling-sessions"></a>最近のプロファイリング セッション
ページのこのセクションには、最近のプロファイル セッションに関する情報が表示されます。 プロファイル セッションは、プロファイラー エージェントがアプリケーションをホストしているコンピューターの 1 つでプロファイルを取得していた期間を表します。 いずれかの行をクリックすると、セッションからプロファイルを開くことができます。 各セッションについて、以下が表示されます。

| 設定 | 説明 |
|-|-|
トリガー元 | トリガー、[今すぐプロファイル]、既定のサンプリングのいずれによってセッションが開始されたか。 
アプリケーション名 | プロファイルされたアプリケーションの名前。
マシン インスタンス | プロファイラー エージェントが実行されたマシンの名前。
Timestamp | プロファイルがキャプチャされた時刻。
Tracee (トレース) | 個々の要求にアタッチされたトレースの数。
CPU % | プロファイラーの実行中に使用されていた CPU の割合。
Memory % (メモリ %) | プロファイラーの実行中に使用されていた メモリ の割合。

## <a name="use-web-performance-tests-to-generate-traffic-to-your-application"></a><a id="profileondemand"></a> Web パフォーマンス テストを使用してアプリケーションへのトラフィックを生成する

シングル クリックで Profiler を手動でトリガーできます。 Web パフォーマンス テストを実行しているものとします。 Web アプリに負荷がかかった状態のパフォーマンスを把握するには、トレースが必要です。 ロード テストがいつ実行されるかがわかるので、トレースがキャプチャされるタイミングを制御できることは重要です。 しかし、ランダムなサンプリング間隔では失敗する可能性があります。

次のセクションでは、このシナリオがどのように機能するかについて説明します。

### <a name="step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>手順 1:Web パフォーマンス テストを開始して Web アプリへのトラフィックを生成する

Web アプリが既にトラフィックを受信している、または手動でトラフィックを生成する場合は、このセクションをスキップして手順 2 に進みます。

1. Application Insights ポータルで、 **[構成]**  >  **[パフォーマンス テスト]** を選択します。 

1. 新しいパフォーマンス テストを開始するには、 **[新規]** ボタンを選択します。

   ![新しいパフォーマンス テストを作成する][create-performance-test]

1. **[新しいパフォーマンス テスト]** ウィンドウで、テスト対象の URL を構成します。 すべての設定を既定値のままにし、 **[テストの実行]** を選択してロード テストの実行を開始します。

    ![ロード テストを構成する][configure-performance-test]

    新しいテストはまずキューに登録された後、"*進行中*" 状態になります。

    ![ロード テストが送信されてキューに登録される][load-test-queued]

    ![ロード テストの実行が行われている][load-test-in-progress]

### <a name="step-2-start-a-profiler-on-demand-session"></a>手順 2:オンデマンド セッションで Profiler を開始する

1. ロード テストが実行されたら、Profiler を開始し、負荷がかかっている間に Web アプリでトレースをキャプチャします。

1. **[Configure Profiler]\(Profiler の構成\)** ウィンドウに移動します。


### <a name="step-3-view-traces"></a>手順 3:トレースを表示する

Profiler の実行が完了したら、通知の指示に従って [パフォーマンス] ページに移動し、トレースを表示します。

## <a name="next-steps"></a>次のステップ
[Profiler を有効にしてトレースを表示する](profiler-overview.md?toc=/azure/azure-monitor/toc.json)

[profiler-on-demand]: ./media/profiler-settings/Profiler-on-demand.png
[configure-profiler-entry]: ./media/profiler-settings/configure-profiler-entry.png
[configure-profiler-page]: ./media/profiler-settings/configureBlade.png
[trigger-settings-flyout]: ./media/profiler-settings/CPUTrigger.png
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
