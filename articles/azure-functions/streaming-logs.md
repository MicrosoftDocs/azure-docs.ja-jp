---
title: Azure Functions で実行ログをストリーミングする
description: スペースを含めて 115 - 145 文字。 この要約は、検索結果に表示されます。
ms.date: 9/1/2020
ms.topic: how-to
ms.custom: contperf-fy21q2, devx-track-azurecli
ms.openlocfilehash: 085849386fce929ceaec4536844cf31b94fe7539
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "97033053"
---
# <a name="enable-streaming-execution-logs-in-azure-functions"></a>Azure Functions で実行ログのストリーミングを有効にする

アプリケーションの開発中、Azure 内での実行時にログに書き込まれている内容をほぼリアルタイムで確認する必要が生じることがよくあります。

関数実行によって生成されているログ ファイルのストリームを表示する方法は 2 つあります。

* **組み込みのログ ストリーミング**: App Service プラットフォームでは、アプリケーション ログ ファイルのストリームを表示できます。 これは、[ローカル開発](functions-develop-local.md)中に関数をデバッグするときや、ポータル内の **[テスト]** タブを使用するときに見られる出力と同等です。 すべてのログベース情報が表示されます。 詳しくは、[ログのストリーミング](../app-service/troubleshoot-diagnostic-logs.md#stream-logs)に関する記事をご覧ください。 このストリーミング方法でサポートされるインスタンスは 1 つだけです。従量課金プランの Linux 上で実行されているアプリでは、この方法を使用できません。

* **Live Metrics Stream**: 関数アプリが [Application Insights に接続されている](configure-monitoring.md#enable-application-insights-integration)場合、[Live Metrics Stream](../azure-monitor/app/live-stream.md) を使用して Azure portal 内でログ データやその他のメトリックをほぼリアルタイムで表示できます。 この方法は、複数のインスタンス上または従量課金プランの Linux 上で実行されている関数を監視する場合に使用します。 このメソッドでは、[サンプリングされたデータ](configure-monitoring.md#configure-sampling)が使用されます。

ログ ストリームは、ポータル内とほとんどのローカル開発環境内の両方で表示できます。 

## <a name="portal"></a>ポータル

ポータル上では、両方の種類のログ ストリームを表示できます。

### <a name="built-in-log-streaming"></a>組み込みのログ ストリーミング

ポータルでストリーミング ログを表示するには、関数アプリで **[プラットフォーム機能]** タブを選択します。 次に、 **[監視]** の **[ログ ストリーミング]** を選択します。

![ポータルでストリーミング ログを有効にする](./media/functions-monitoring/enable-streaming-logs-portal.png)

これにより、アプリがログ ストリーミング サービスに接続され、ウィンドウにアプリケーション ログが表示されます。 **[アプリケーション ログ]** と **[Web サーバー ログ]** を切り替えることができます。  

![ポータルでストリーミング ログを表示する](./media/functions-monitoring/streaming-logs-window.png)

### <a name="live-metrics-stream"></a>ライブ メトリック ストリーム

アプリの Live Metrics Stream を表示するには、関数アプリの **[概要]** タブを選択します。 Application Insights 有効にすると、 **[構成済みの機能]** の下に **[Application Insights]** リンクが表示されます。 このリンクをクリックすると、アプリの Application Insights ページに移動します。

Application Insights で、 **[Live Metrics Stream]** を選択します。 [サンプリングされたログ エントリ](configure-monitoring.md#configure-sampling)が、 **[Sample Telemetry]\(サンプル テレメトリ\)** の下に表示されます。

![ポータル上で Live Metrics Stream を表示する](./media/functions-monitoring/live-metrics-stream.png) 

## <a name="visual-studio-code"></a>Visual Studio Code

[!INCLUDE [functions-enable-log-stream-vs-code](../../includes/functions-enable-log-stream-vs-code.md)]

## <a name="core-tools"></a>Core Tools

[!INCLUDE [functions-streaming-logs-core-tools](../../includes/functions-streaming-logs-core-tools.md)]

## <a name="azure-cli"></a>Azure CLI

[Azure CLI](/cli/azure/install-azure-cli) を使用して、ストリーミング ログを有効にできます。 次のコマンドを使用してサインインし、サブスクリプションを選択し、ログ ファイルをストリーミングします。

```azurecli
az login
az account list
az account set --subscription <subscriptionNameOrId>
az webapp log tail --resource-group <RESOURCE_GROUP_NAME> --name <FUNCTION_APP_NAME>
```

## <a name="azure-powershell"></a>Azure PowerShell

[Azure PowerShell](/powershell/azure/) を使用して、ストリーミング ログを有効にすることができます。 PowerShell の場合は、次のスニペットに示すように、[Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) コマンドを使用して、関数アプリでのログ記録を有効にします。 

:::code language="powershell" source="~/powershell_scripts/app-service/monitor-with-logs/monitor-with-logs.ps1" range="19-20":::

詳細については、[完全なコード例](../app-service/scripts/powershell-monitor.md#sample-script)を参照してください。 

## <a name="next-steps"></a>次の手順

+ [Azure Functions を監視する](functions-monitoring.md)
+ [Application Insights で Azure Functions のテレメトリを分析する](analyze-telemetry-data.md)
