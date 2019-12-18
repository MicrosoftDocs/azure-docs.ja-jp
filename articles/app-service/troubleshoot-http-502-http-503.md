---
title: HTTP 502 と HTTP 503 エラーを修正する
description: Azure App Service でホストされているアプリで発生するエラー "502 無効なゲートウェイ" と "503 サービス利用不可" のトラブルシューティングを行います。
tags: top-support-issue
keywords: 502 無効なゲートウェイ, 503 サービス利用不可, 503 エラー, 502 エラー
ms.assetid: 51cd331a-a3fa-438f-90ef-385e755e50d5
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 9345b6fb28aa282e85f1167f6f2531e5f990e3a2
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688325"
---
# <a name="troubleshoot-http-errors-of-502-bad-gateway-and-503-service-unavailable-in-azure-app-service"></a>Azure App Service での HTTP エラー "502 無効なゲートウェイ" と "503 サービス利用不可" のトラブルシューティング
"502 無効なゲートウェイ" と "503 サービス利用不可" は、[Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714) でホストされているアプリで発生する一般的なエラーです。 この記事は、これらのエラーのトラブルシューティングを行うために役立ちます。

この記事についてさらにヘルプが必要な場合は、いつでも [MSDN の Azure フォーラムとスタック オーバーフロー フォーラム](https://azure.microsoft.com/support/forums/)で Azure エキスパートに問い合わせることができます。 または、Azure サポート インシデントを送信できます。 その場合は、 [Azure サポートのサイト](https://azure.microsoft.com/support/options/) に移動して、 **[サポートの要求]** をクリックします。

## <a name="symptom"></a>症状
ブラウザーでアプリにアクセスすると、"502 無効なゲートウェイ" または "503 サービス利用不可" という HTTP エラーが返される。

## <a name="cause"></a>原因
この症状は多くの場合、アプリケーション レベルの問題が原因で発生します。その例を次に示します。

* 要求に時間がかかっている
* アプリケーションのメモリ/CPU 使用率が高い
* 例外が発生してアプリケーションがクラッシュする

## <a name="troubleshooting-steps-to-solve-502-bad-gateway-and-503-service-unavailable-errors"></a>"502 無効なゲートウェイ" エラーと "503 サービス利用不可" エラーを解決するトラブルシューティング手順
トラブルシューティングは、大きく次の 3 つのタスクに分けられます。この 3 つのタスクを上から順に行います。

1. [アプリケーションの動作を観察、監視する](#observe)
2. [データを収集する](#collect)
3. [問題を緩和する](#mitigate)

[App Service](overview.md) ではステップごとにさまざまなオプションを使用できます。

<a name="observe" />

### <a name="1-observe-and-monitor-application-behavior"></a>1.アプリケーションの動作を観察、監視する
#### <a name="track-service-health"></a>サービス正常性を追跡する
Microsoft Azure は、サービスの中断やパフォーマンスの低下があるたびに、毎回公表します。 サービスの正常性は、[Azure Portal](https://portal.azure.com/) で追跡できます。 詳細については、[サービスの正常性の追跡](../monitoring-and-diagnostics/insights-service-health.md)に関するページを参照してください。

#### <a name="monitor-your-app"></a>アプリを監視する
Web アプリに問題が発生しているかどうかは、アプリを監視することによって確認することができます。 アプリのブレードで **[要求とエラー]** タイルをクリックします。 **[メトリック]** ブレードには、追加できるすべてのメトリックが表示されます。

アプリに関しては、次のメトリックを監視するようお勧めします。

* 平均メモリ ワーキング セット
* 平均応答時間
* CPU 時間
* メモリ ワーキング セット
* Requests

![HTTP エラー "502 無効なゲートウェイ" と "503 サービス利用不可" の解決に向けてアプリを監視する](./media/app-service-web-troubleshoot-HTTP-502-503/1-monitor-metrics.png)

詳細については、次を参照してください。

* [Azure App Service のアプリの監視](web-sites-monitor.md)
* [アラート通知を受け取る](../monitoring-and-diagnostics/insights-receive-alert-notifications.md)

<a name="collect" />

### <a name="2-collect-data"></a>2.データを収集する
#### <a name="use-the-diagnostics-tool"></a>診断ツールの使用
App Service には、アプリのトラブルシューティングに役立つ構成不要のインテリジェントな対話型のエクスペリエンスが用意されています。 アプリに問題が発生した場合、診断ツールは問題点を指摘し、その問題のトラブルシューティングをすばやく簡単に行って解決するための適切な情報へとユーザーをガイドします。

App Service 診断にアクセスするには、[Azure Portal](https://portal.azure.com) の App Service アプリまたは App Service 環境に移動します。 左側のナビゲーションで、 **[問題の診断と解決]** をクリックします。

#### <a name="use-the-kudu-debug-console"></a>Kudu デバッグ コンソールを使用する
App Service には、ファイルのデバッグ、調査、アップロード用のデバッグ コンソールのほか、ご利用の環境についての情報を入手するための JSON エンドポイントが用意されています。 このコンソールは、アプリの *Kudu コンソール*または *SCM ダッシュボード*と呼ばれます。

ダッシュボードには、**https://&lt;アプリ名>.scm.azurewebsites.net/** リンクからアクセスできます。

Kudu には次のような機能があります。

* アプリケーションの環境設定
* ログ ストリーム
* 診断ダンプ
* デバッグ コンソール (Powershell のコマンドレットや基本的な DOS コマンドを実行可能)

Kudu にはもう 1 つ便利な機能があり、アプリケーションからファーストチャンス例外がスローされた場合に、Kudu と SysInternals ツール Procdump を使用してメモリ ダンプを作成することができます。 このメモリ ダンプはプロセスのスナップショットです。アプリに関して、通常より複雑な問題をトラブルシューティングできる場合も少なくありません。

Kudu で利用できる機能の詳細については、 [知っておくべき Azure Websites のオンライン ツール](https://azure.microsoft.com/blog/windows-azure-websites-online-tools-you-should-know-about/)に関するページを参照してください。

<a name="mitigate" />

### <a name="3-mitigate-the-issue"></a>3.問題を緩和する
#### <a name="scale-the-app"></a>アプリをスケーリングする
Azure App Service では、アプリケーションが実行されるスケールを調整することによって、パフォーマンスとスループットを高めることができます。 アプリのスケール アップには、2 つの関連する措置が伴います。1 つは、App Service プランの価格レベルを引き上げること、もう 1 つは、価格レベルを引き上げた後に特定の設定を構成することです。

スケーリングの詳細については、[Azure App Service でのアプリのスケール](manage-scale-up.md)に関する記事を参照してください。

加えて、アプリケーションを複数のインスタンスで実行することもできます。 処理能力がアップするだけでなく、ある程度の耐障害性を確保することができます。 この場合、1 つのインスタンスでプロセスがダウンしても、他のインスタンスが要求の処理を続行します。

スケーリングは、[手動] または [自動] に設定することができます。

#### <a name="use-autoheal"></a>AutoHeal を使用する
AutoHeal は、選択された設定 (構成の変更、要求、メモリに基づく制限、要求の実行に必要な時間など) に従って、アプリのワーカー プロセスをリサイクルします。 ほとんどの場合、問題を回復するための一番の近道は、プロセスをリサイクルすることです。 アプリはいつでも、Azure Portal 内から直接、再起動できますが、AutoHeal はユーザーの介入なしでそれを自動的に実行します。 必要な作業は、アプリのルート web.config にいくつかのトリガーを追加することだけです。 アプリケーションが .NET アプリ以外でも、これらの設定は同じように作用します。

詳細については、 [Azure Web Sites の自動復旧](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites/)に関するページを参照してください。

#### <a name="restart-the-app"></a>アプリの再起動
1 回限りの問題であれば、通常これが最も簡単な復旧方法です。 アプリを停止または再起動するためのオプションは、[Azure Portal](https://portal.azure.com/) のアプリ ブレードにあります。

 ![HTTP エラー "502 無効なゲートウェイ" と "503 サービス利用不可" を解決するためにアプリを再起動する](./media/app-service-web-troubleshoot-HTTP-502-503/2-restart.png)

アプリの管理には、Azure PowerShell を使用することもできます。 詳細については、 [リソース マネージャーでの Azure PowerShell の使用](../powershell-azure-resource-manager.md)をご覧ください。

