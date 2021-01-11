---
title: PowerShell を使用して Application Insights のアラートを設定する | Microsoft Docs
description: Application Insights の構成を自動化して、メトリックの変更に関する電子メールを受け取ります。
ms.topic: conceptual
ms.date: 07/23/2016
ms.openlocfilehash: 74d477b6660c0f7ec2ee32b34169bb85886936e5
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322467"
---
# <a name="use-powershell-to-set-alerts-in-application-insights"></a>PowerShell を使用して Application Insights のアラートを設定する

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[Application Insights](./app-insights-overview.md) では、[アラート](../platform/alerts-log.md)の構成を自動化できます。

さらに、[webhook を設定して、アラートへの対応を自動化](../platform/alerts-webhooks.md)することもできます。

> [!NOTE]
> リソースと警告を同時に作成したい場合は、[Azure Resource Manager テンプレートの使用](powershell.md)を検討してください。

## <a name="one-time-setup"></a>1 回限りのセットアップ
以前に Azure サブスクリプションで PowerShell を使用したことがない場合

スクリプトを実行するコンピューターに Azure PowerShell モジュールをインストールします。

* [Microsoft Web Platform Installer (v5 以上)](https://www.microsoft.com/web/downloads/platform.aspx)をインストールします。
* このインストーラーを使用して Microsoft Azure PowerShell をインストールする。

## <a name="connect-to-azure"></a>Azure に接続する
Azure PowerShell を起動して、 [サブスクリプションに接続](/powershell/azure/)します。

```azurepowershell
Add-AzAccount
```


## <a name="get-alerts"></a>アラートの取得

```azurepowershell
Get-AzAlertRule -ResourceGroup "Fabrikam" `
  [-Name "My rule"] `
  [-DetailedOutput]
```

## <a name="add-alert"></a>アラートの追加

```azurepowershell
Add-AzMetricAlertRule -Name "{ALERT NAME}" `
  -Description "{TEXT}" `
  -ResourceGroup "{GROUP NAME}" `
  -TargetResourceId "/subscriptions/{SUBSCRIPTION ID}/resourcegroups/{GROUP NAME}/providers/microsoft.insights/components/{APP RESOURCE NAME}" `
  -MetricName "{METRIC NAME}" `
  -Operator GreaterThan `
  -Threshold {NUMBER}  `
  -WindowSize {HH:MM:SS} `
  [-SendEmailToServiceOwners] `
  [-CustomEmails "EMAIL1@X.COM","EMAIL2@Y.COM"] `
  -Location "East US" // must be East US at present `
  -RuleType Metric
```


## <a name="example-1"></a>例 1
「HTTP 要求に対するサーバーの応答時間」の 5 分間の平均が 1 秒を超えた場合、電子メールで通知してください。 私の Application Insights リソースは IceCreamWebApp と呼ばれており、リソース グループ Fabrikam 内にあります。 私は Azure サブスクリプションの所有者です。

GUID は、サブスクリプション ID です (アプリケーションのインストルメンテーション キーではありません)。

```azurepowershell
Add-AzMetricAlertRule -Name "slow responses" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "request.duration" `
  -Operator GreaterThan `
  -Threshold 1 `
  -WindowSize 00:05:00 `
  -SendEmailToServiceOwners `
  -Location "East US" `
  -RuleType Metric
```

## <a name="example-2"></a>例 2
[TrackMetric()](./api-custom-events-metrics.md#trackmetric) を使用して "salesPerHour" という名前のメトリックを報告するアプリケーションがあります。 24 時間にわたる "salesPerHour" の平均が 100 を下回る場合は、私の同僚に電子メールを送信してください。

```azurepowershell
Add-AzMetricAlertRule -Name "poor sales" `
  -Description "slow sales alert" `
  -ResourceGroup "Fabrikam" `
  -TargetResourceId "/subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/Fabrikam/providers/microsoft.insights/components/IceCreamWebApp" `
  -MetricName "salesPerHour" `
  -Operator LessThan `
  -Threshold 100 `
  -WindowSize 24:00:00 `
  -CustomEmails "satish@fabrikam.com","lei@fabrikam.com" `
  -Location "East US" `
  -RuleType Metric
```

別の追跡呼び出しの [測定パラメーター](./api-custom-events-metrics.md#properties) (TrackEvent や trackPageView など) を使用して報告されるメトリックにも同じルールを使用できます。

## <a name="metric-names"></a>メトリックの名前
| メトリックの名前 | 画面の名前 | 説明 |
| --- | --- | --- |
| `basicExceptionBrowser.count` |ブラウザーの例外 |ブラウザーでスローされた、キャッチされない例外の数。 |
| `basicExceptionServer.count` |サーバーの例外 |アプリによってスローされた未処理の例外の数 |
| `clientPerformance.clientProcess.value` |クライアントの処理時間 |ドキュメントの最終バイトを受信してから、DOM が読み込まれるまでの時間。 非同期要求がまだ処理されている可能性があります。 |
| `clientPerformance.networkConnection.value` |ページ読み込みのネットワーク接続時間 |ブラウザーがネットワークに接続するために要する時間です。 キャッシュされている場合は、0 にすることができます。 |
| `clientPerformance.receiveRequest.value` |受信応答時間 |ブラウザーが要求を送信してから応答を受信し始めるまでの時間。 |
| `clientPerformance.sendRequest.value` |要求送信時間 |ブラウザーが要求を送信するのに要する時間。 |
| `clientPerformance.total.value` |ブラウザーのページ読み込み時間 |ユーザーが要求を出してから DOM、スタイル シート、スクリプト、およびイメージが読み込まれるまでの時間。 |
| `performanceCounter.available_bytes.value` |使用可能なメモリ |プロセスまたはシステムの用途で、すぐに利用できる物理メモリ。 |
| `performanceCounter.io_data_bytes_per_sec.value` |IO 処理速度 |ファイル、ネットワーク、およびデバイスに対する読み書きで 1 秒あたりに処理される合計バイト数。 |
| `performanceCounter.number_of_exceps_thrown_per_sec.value` |例外レート |1 秒あたりにスローされる例外。 |
| `performanceCounter.percentage_processor_time.value` |プロセス CPU |アプリケーション プロセスの実行命令に対してプロセッサが使用するすべてのプロセス スレッドの経過時間の割合。 |
| `performanceCounter.percentage_processor_total.value` |プロセッサ時間 |プロセッサが非アイドル スレッドで費やす時間の割合。 |
| `performanceCounter.process_private_bytes.value` |プロセスのプライベート バイト |監視対象のアプリケーション プロセスに対して専用に割り当てられるメモリ。 |
| `performanceCounter.request_execution_time.value` |ASP.NET 要求の実行時間 |最新の要求の実行時間。 |
| `performanceCounter.requests_in_application_queue.value` |実行キュー内の ASP.NET 要求 |アプリケーション要求キューの長さ。 |
| `performanceCounter.requests_per_sec.value` |ASP.NET 要求レート |ASP.NET からの 1 秒あたりのアプリケーションへのすべての要求のレート。 |
| `remoteDependencyFailed.durationMetric.count` |依存関係の障害 |サーバー アプリケーションによる外部リソースの呼び出しが失敗した回数。 |
| `request.duration` |サーバーの応答時間 |HTTP 要求を受信してから、応答の送信を終了するまでの時間。 |
| `request.rate` |要求レート |1 秒あたりのアプリケーションに出されるすべての要求のレート。 |
| `requestFailed.count` |失敗した要求 |400 またはこれより大きな応答コードを生じさせた HTTP 要求の数 |
| `view.count` |ページ ビュー |Web ページに対するクライアント ユーザーの要求数。 代理トラフィックはフィルターで除外されます。 |
| {カスタム メトリック名} |{メトリック名} |[TrackMetric](./api-custom-events-metrics.md#trackmetric) または[追跡呼び出しの測定パラメーター](./api-custom-events-metrics.md#properties)で報告されるメトリック値。 |

メトリックは、さまざまなテレメトリ モジュールによって送信されます。

| メトリック グループ | コレクター モジュール |
| --- | --- |
| basicExceptionBrowser、<br/>clientPerformance、<br/>view |[ブラウザーの JavaScript](./javascript.md) |
| performanceCounter |[パフォーマンス](./configuration-with-applicationinsights-config.md) |
| remoteDependencyFailed |[依存関係](./configuration-with-applicationinsights-config.md) |
| request、<br/>requestFailed |[サーバー要求](./configuration-with-applicationinsights-config.md) |

## <a name="webhooks"></a>Webhooks
[アラートへの対応を自動化](../platform/alerts-webhooks.md)できます。 アラートが発生すると、Azure は任意の Web アドレスを呼び出します。

## <a name="see-also"></a>参照
* [Application Insights を構成するスクリプト](./create-new-resource.md#creating-a-resource-automatically)
* [テンプレートから Application Insights と Web テスト リソースを作成する](powershell.md)
* [Microsoft Azure Diagnostics の Application Insights への結合を自動化する](powershell-azure-diagnostics.md)
* [アラートへの対応を自動化する](../platform/alerts-webhooks.md)

