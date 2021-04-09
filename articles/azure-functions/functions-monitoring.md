---
title: Azure Functions を監視する
description: Azure Application Insights を Azure Functions とともに使用して、関数の実行を監視する方法を説明します。
ms.assetid: 501722c3-f2f7-4224-a220-6d59da08a320
ms.topic: conceptual
ms.date: 10/14/2020
ms.custom: devx-track-csharp, fasttrack-edit, contperf-fy21q2, devx-track-js
ms.openlocfilehash: 637f09c5ee52928631b965dfa6caea9368b44991
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99550761"
---
# <a name="monitor-azure-functions"></a>Azure Functions を監視する

[Azure Functions](functions-overview.md) には、関数を監視するための [Azure Application Insights](../azure-monitor/app/app-insights-overview.md) とのビルトイン統合機能が用意されています。 この記事では、Azure Functions を監視するために Azure によって提供されている監視機能の概要について説明します。

Application Insights を使用すると、ログ、パフォーマンス、およびエラー データを収集できます。 パフォーマンスの異常が自動的に検出されるほか、強力な分析ツールが特徴となっていて、より簡単に、問題を診断したり、関数がどのように使用されているかを理解したりすることができます。 これらのツールは、お使いの関数のパフォーマンスと使いやすさの継続的改善に役立つように設計されています。 ローカルFunction App  プロジェクトの開発中に Application Insights を使用することもできます。 詳細については、「[Application Insights とは何か](../azure-monitor/app/app-insights-overview.md)」を参照してください。

Application Insights インストルメンテーションは Azure Functions に組み込まれているため、Function App を Application Insights リソースに接続するためには、有効なインストルメンテーション キーが必要です。 インストルメンテーション キーは、Azure 内にFunction App のリソースを作成するときに、アプリケーション設定に追加されます。 Function App にまだこのキーがない場合は、[手動で設定](configure-monitoring.md#enable-application-insights-integration)できます。  

## <a name="application-insights-pricing-and-limits"></a>Application Insights の価格と制限

Azure Functions との Application Insights の統合は、無料で試すことができます。これは、1 日に無料で処理されるデータ量に制限があることが特徴となっています。

開発中に Application Insights を有効にする場合は、テスト中にこの制限に達する可能性があります。 1 日あたりの上限に近づいた場合は、ポータルと電子メール通知でお知らせします。 これらのアラートに気が付かず、上限に達してしまった場合は、新しいログが Application Insights クエリに表示されません。 不要なトラブルシューティングに時間を費やさずにすむように、上限には気を付けてください。 詳細については、「[Application Insights での価格とデータ ボリュームの管理](../azure-monitor/app/pricing.md)」を参照してください。

> [!IMPORTANT]
> Application Insights には、負荷がピークのときに、完了した実行に関してテレメトリ データが生成されすぎないようにする[サンプリング](../azure-monitor/app/sampling.md)機能があります。 サンプリングは、既定で有効になっています。 データがないと思われる場合は、特定の監視シナリオに合わせてサンプリング設定を調整するだけで済みます。 詳細については、「[サンプリングを構成する](configure-monitoring.md#configure-sampling)」を参照してください。

Function App で使用できる Application Insights 機能の完全な一覧については、「[Azure Functions でサポートされる Application Insights の機能」 ](../azure-monitor/app/azure-functions-supported-features.md)を参照してください。

## <a name="application-insights-integration"></a>Application Insights の統合

一般に、Function App を作成するときに Application Insights インスタンスを作成します。 この場合、統合に必要なインストルメンテーション キーは、*APPINSIGHTS_INSTRUMENTATIONKEY* という名前のアプリケーション設定として既に設定されています。 何らかの理由でFunction App にインストルメンテーション キーが設定されていない場合は、[Application Insights の統合を有効にする](configure-monitoring.md#enable-application-insights-integration)必要があります。  

## <a name="collecting-telemetry-data"></a>利用統計情報の収集

Application Insights の統合が有効になっていると、利用統計情報は、接続されている Application Insights インスタンスに送信されます。 このデータには、Functions ホストによって生成されたログ、関数コードから書き込まれたトレース、およびパフォーマンス データが含まれています。 

>[!NOTE]
>関数と Functions ホストからのデータに加えて、[Functions スケール コントローラー](#scale-controller-logs)からデータを収集することもできます。   

### <a name="log-levels-and-categories"></a>ログのレベルとカテゴリ

アプリケーション コードからのトレースを記述するときには、トレースにログ レベルを割り当てる必要があります。 ログ レベルにより、トレースから収集されるデータの量を制限する手段が得られます。  

[!INCLUDE [functions-log-levels](../../includes/functions-log-levels.md)]

ログ レベルの詳細については、「[ログ レベルを構成する](configure-monitoring.md#configure-log-levels)」を参照してください。

ログに記録された項目をカテゴリに割り当てることで、Function App 内の特定のソースから生成されるテレメトリをより詳細に制御できます。 カテゴリを使用すると、収集されたデータに対する分析を実行しやすくなります。 関数コードから書き込まれるトレースは、関数名に基づいて個々のカテゴリに割り当てられます。 カテゴリの詳細については、「[カテゴリを構成する](configure-monitoring.md#configure-categories)」を参照してください。

### <a name="custom-telemetry-data"></a>カスタム利用統計情報

[C#](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions) と [JavaScript](functions-reference-node.md#log-custom-telemetry) では、Application Insights SDK を使用してカスタム利用統計情報を書き込むことができます。

### <a name="dependencies"></a>依存関係

バージョン 2.x 以降の Functions では、特定のクライアント SDK を使用するバインディングの依存関係に関するデータが、ランタイムによって自動的に収集されます。 Application Insights では、以下の依存関係に関するデータを収集します。

+ Azure Cosmos DB 
+ Azure Event Hubs
+ Azure Service Bus
+ Azure Storage サービス (Blob、Queue、Table)

`SqlClient` を使用する HTTP 要求とデータベース呼び出しもキャプチャされます。 Application Insights によってサポートされる依存関係の完全な一覧については、[自動追跡される依存関係](../azure-monitor/app/asp-net-dependencies.md#automatically-tracked-dependencies)に関するページを参照してください。

Application Insights では、収集された依存関係データの "_アプリケーション マップ_" が生成されます。 次に、Queue storage の出力バインドがある HTTP トリガー関数のアプリケーション マップの例を示します。  

![依存関係があるアプリケーション マップ](./media/functions-monitoring/app-map.png)

依存関係は、`Information` レベルで記述されます。 `Warning` 以上でフィルター処理すると、依存関係データは表示されなくなります。 また、依存関係の自動収集は、非ユーザー スコープで行われます。 依存関係データをキャプチャするには、ホストで、レベルが少なくともユーザー スコープ (`Function.<YOUR_FUNCTION_NAME>.User`) 外部の `Information` に設定されているようにします。

依存関係データの自動収集に加えて、言語固有の Application Insights SDK の 1 つを使用して、カスタム依存関係情報をログに書き込むこともできます。 カスタム依存関係を記述する方法の例については、次の言語固有の例のいずれかを参照してください。

+ [C# 関数でカスタム テレメトリをログに記録する](functions-dotnet-class-library.md#log-custom-telemetry-in-c-functions)
+ [JavaScript 関数でカスタム テレメトリをログに記録する](functions-reference-node.md#log-custom-telemetry) 

## <a name="writing-to-logs"></a>ログへの書き込み 

ログに書き込む方法と、使用する API は、Function App  プロジェクトの言語によって異なります。   
関数からのログの書き込みの詳細については、お使いの言語の開発者ガイドを参照してください。

+ [C# (.NET クラス ライブラリ)](functions-dotnet-class-library.md#logging)
+ [Java](functions-reference-java.md#logger)
+ [JavaScript](functions-reference-node.md#write-trace-output-to-logs) 
+ [PowerShell](functions-reference-powershell.md#logging)
+ [Python](functions-reference-python.md#logging)

## <a name="analyze-data"></a>データを分析する

既定では、関数アプリから収集されたデータは、Application Insights に格納されます。 [Azure portal](https://portal.azure.com) では、Application Insights によって、テレメトリ データの広範囲にわたるセットの視覚化が提供されます。 エラー ログ、クエリ イベント、メトリックの詳細を表示できます。 収集したデータを表示してクエリを実行する方法の基本的な例など、詳細については、「[Application Insights で Azure Functions のテレメトリを分析する](analyze-telemetry-data.md)」を参照してください。 

## <a name="streaming-logs"></a>ストリーミング ログ

アプリケーションの開発中、Azure 内での実行時にログに書き込まれている内容をほぼリアルタイムで確認する必要が生じることがよくあります。

関数の実行によって生成中のログ データのストリームを表示する方法は 2 つあります。

* **組み込みのログ ストリーミング**: App Service プラットフォームでは、アプリケーション ログ ファイルのストリームを表示できます。 このストリームは、[ローカル開発](functions-develop-local.md)中に関数をデバッグするときや、ポータルで **[テスト]** タブを使用するときに表示される出力と同等です。 すべてのログベース情報が表示されます。 詳しくは、[ログのストリーミング](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)に関する記事をご覧ください。 このストリーミング方法でサポートされるインスタンスは 1 つだけです。従量課金プランの Linux 上で実行されているアプリでは、この方法を使用できません。

* **Live Metrics Stream**: Function App が [Application Insights に接続されている](configure-monitoring.md#enable-application-insights-integration)ときには、[Live Metrics Stream](../azure-monitor/app/live-stream.md) を使用して、Azure portal でログ データやその他のメトリックをほぼリアルタイムで表示できます。 この方法は、複数のインスタンス上または従量課金プランの Linux 上で実行されている関数を監視する場合に使用します。 このメソッドでは、[サンプリングされたデータ](configure-monitoring.md#configure-sampling)が使用されます。

ログ ストリームは、ポータル内とほとんどのローカル開発環境内の両方で表示できます。 ログ ストリームを有効にする方法については、「[Azure Functions でストリーミング実行ログを有効にする](streaming-logs.md)」を参照してください。

## <a name="diagnostic-logs"></a>診断ログ

_この機能はプレビュー段階にあります。_ 

Application Insights を使用すると、利用統計情報を長期的ストレージまたはその他の分析サービスにエクスポートできます。  

関数も Azure Monitor と統合されるため、診断設定を使用しても、Azure Monitor ログを含むさまざまな送信先に利用統計情報を送信できます。 詳細については、「[Azure Monitor ログを使用した Azure Functions の監視](functions-monitor-log-analytics.md)」を参照してください。

## <a name="scale-controller-logs"></a>コントローラー ログをスケーリングする

_この機能はプレビュー段階にあります。_ 

[Azure Functions スケール コントローラー](./event-driven-scaling.md#runtime-scaling)は、アプリが実行されている Azure Functions ホストのインスタンスを監視します。 このコントローラーは、現在のパフォーマンスに基づいて、インスタンスを追加または削除するタイミングを決定します。 スケール コントローラーから Application Insights にログを出力させることで、Function App のためにスケール コントローラーで下されている決定をより詳細に理解できます。 生成されたログを、別のサービスによる分析のために BLOB ストレージに格納することもできます。 

この機能を有効にするには、`SCALE_CONTROLLER_LOGGING_ENABLED` という名前のアプリケーション設定を、ご利用の関数アプリの設定に追加します。 詳細については、「[スケール コントローラー ログを構成する](configure-monitoring.md#configure-scale-controller-logs)」を参照してください。

## <a name="report-issues"></a>レポートに関する問題

Functions での Application Insights 統合に関する問題をレポートしたり、提案や要求を行ったりするには、[GitHub で問題を作成します](https://github.com/Azure/Azure-Functions/issues/new)。

## <a name="next-steps"></a>次のステップ

詳細については、次のリソースを参照してください。

* [Application Insights](/azure/application-insights/)
* [ASP.NET Core のログ記録](/aspnet/core/fundamentals/logging/)
