---
title: チュートリアル:Azure Monitor を使用したトラブルシューティング
description: Azure Monitor と Log Analytics で App Service Web アプリを監視する方法について説明します。 Azure Monitor は、環境を監視するための包括的なソリューションを提供することで、可用性を最大限に高めます。
author: msangapu-msft
ms.author: msangapu
ms.topic: tutorial
ms.date: 06/20/2020
ms.openlocfilehash: d45a8b8f426df32b9f5ac6f64237107083e0f9ab
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "100586287"
---
# <a name="tutorial-troubleshoot-an-app-service-app-with-azure-monitor"></a>チュートリアル:Azure Monitor を使用した App Service アプリのトラブルシューティング

> [!NOTE]
> Azure Monitor と App Service の統合は[プレビュー](https://aka.ms/appsvcblog-azmon)段階です。
>

このチュートリアルでは、[Azure Monitor](overview.md) を使用して [App Service](../azure-monitor/overview.md) アプリのトラブルシューティングを行う方法について説明します。 このサンプル アプリには、メモリを使い果たして HTTP 500 エラーを引き起こすことを意図したコードが含まれているため、Azure Monitor を使用して問題を診断して修正できます。 完了すると、[Azure Monitor](../azure-monitor/overview.md) と統合された App Service on Linux でサンプル アプリが動作するようになります。

[Azure Monitor](../azure-monitor/overview.md) は、クラウドおよびオンプレミス環境からテレメトリを収集、分析し、それに対応する包括的なソリューションを提供することで、アプリケーションやサービスの可用性とパフォーマンスを最大限に高めます。

このチュートリアルでは、以下の内容を学習します。

> [!div class="checklist"]
> * Azure Monitor を使用して Web アプリを構成する
> * Log Analytics にコンソールのログを送信する
> * ログ クエリを使用して Web アプリのエラーを特定し、トラブルシューティングを行う

このチュートリアルの手順は、macOS、Linux、Windows で実行できます。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、次のものが必要です。

- [Azure サブスクリプション](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)
- [Azure CLI](/cli/azure/install-azure-cli)
- [Git](https://git-scm.com/)

## <a name="create-azure-resources"></a>Azure リソースを作成する

最初に、複数のコマンドをローカルで実行して、このチュートリアルで使用するサンプル アプリを設定します。 このコマンドでは、サンプル アプリをクローンし、Azure リソースを作成して、デプロイ ユーザーを作成し、そのアプリを Azure にデプロイします。 デプロイ ユーザーの作成の一環として指定したパスワードの入力が求められます。 

```bash
git clone https://github.com/Azure-Samples/App-Service-Troubleshoot-Azure-Monitor
az group create --name myResourceGroup --location "South Central US"
az webapp deployment user set --user-name <username> --password <password>
az appservice plan create --name myAppServicePlan --resource-group myResourceGroup --sku B1 --is-linux
az webapp create --resource-group myResourceGroup --plan myAppServicePlan --name <app-name> --runtime "PHP|7.3" --deployment-local-git
git remote add azure <url_from_previous_step>
git push azure main
```

## <a name="configure-azure-monitor-preview"></a>Azure Monitor を構成する (プレビュー)

### <a name="create-a-log-analytics-workspace"></a>Log Analytics ワークスペースを作成する

サンプル アプリを Azure App Service にデプロイしたら、問題の発生時にアプリのトラブルシューティングを行うよう監視機能を構成します。 Azure Monitor は、ログ データを Log Analytics ワークスペースに格納します。 ワークスペースは、データと構成情報が含まれるコンテナーです。

この手順では、Log Analytics ワークスペースを作成して、お使いのアプリで Azure Monitor を構成します。

```bash
az monitor log-analytics workspace create --resource-group myResourceGroup --workspace-name myMonitorWorkspace
```

> [!NOTE]
> [Azure Monitor Log Analytics では、データ インジェストとデータ保持について支払いが発生します。](https://azure.microsoft.com/pricing/details/monitor/)
>

### <a name="create-a-diagnostic-setting"></a>診断設定の作成

診断設定を使用すると、特定の Azure サービスのメトリックを Azure Monitor ログに収集し、ログ クエリを使用して、他の監視データと組み合わせて分析することができます。 このチュートリアルでは、Web サーバーと標準出力またはエラー ログを有効にします。 ログの種類と説明の完全な一覧については、「[サポートされるログの種類](./troubleshoot-diagnostic-logs.md#supported-log-types)」を参照してください。

次のコマンドを実行して、AppServiceConsoleLogs (標準出力またはエラー) と AppServiceHTTPLogs (Web サーバー ログ) の診断設定を作成します。 _\<app-name>_ と _\<workspace-name>_ を独自の値で置き換えます。 

> [!NOTE]
> 最初の 2 つのコマンド `resourceID` と `workspaceID` は、`az monitor diagnostic-settings create` コマンドで使用する変数です。 このコマンドの詳細については、「[Azure CLI を使用して診断設定を作成する](../azure-monitor/essentials/diagnostic-settings.md#create-using-azure-cli)」を参照してください。
>

```bash
resourceID=$(az webapp show -g myResourceGroup -n <app-name> --query id --output tsv)

workspaceID=$(az monitor log-analytics workspace show -g myResourceGroup --workspace-name <workspace-name> --query id --output tsv)

az monitor diagnostic-settings create --resource $resourceID \
 --workspace $workspaceID \
 -n myMonitorLogs \
 --logs '[{"category": "AppServiceConsoleLogs", "enabled": true},
  {"category": "AppServiceHTTPLogs", "enabled": true}]'
```

## <a name="troubleshoot-the-app"></a>アプリのトラブルシューティングを行う

`http://<app-name>.azurewebsites.net` を参照します。

サンプルアプリ ImageConverter は、含まれる画像を `JPG` から `PNG` に変換します。 このチュートリアルのコードには、バグが意図的に配置されています。 十分な数の画像を選択すると、アプリでは、画像の変換中に HTTP 500 エラーが発生します。 開発段階ではこのシナリオが考慮されなかったことを想像してください。 Azure Monitor を使用してこのエラーのトラブルシューティングを行います。

### <a name="verify-the-app-is-works"></a>アプリの動作を確認する

画像を変換するには、[`Tools`] をクリックし、[`Convert to PNG`] を選択します。

![[Tools] をクリックして [Convert to PNG] を選択する](./media/tutorial-azure-monitor/sample-monitor-app-tools-menu.png)

最初の 2 つの画像を選択し、[`convert`] をクリックします。 これで正常に変換されます。

![最初の 2 つの画像を選択する](./media/tutorial-azure-monitor/sample-monitor-app-convert-two-images.png)

### <a name="break-the-app"></a>アプリを中断する

2 つの画像を正常に変換してアプリを確認できたので、最初の 5 つの画像を変換してみます。

![最初の 5 つの画像を変換する](./media/tutorial-azure-monitor/sample-monitor-app-convert-five-images.png)

この操作は失敗し、開発段階でテストされなかった `HTTP 500` エラーが発生します。

![変換の結果として HTTP 500 エラーが発生する](./media/tutorial-azure-monitor/sample-monitor-app-http-500.png)

## <a name="use-log-query-to-view-azure-monitor-logs"></a>ログ クエリを使用して Azure Monitor ログを表示する

Log Analytics ワークスペースで使用できるログを確認してみましょう。 

この [Log Analytics ワークスペースのリンク](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.OperationalInsights%2Fworkspaces)をクリックして、Azure portal で自分のワークスペースにアクセスします。

Azure Portal で、Log Analytics ワークスペースを選びます。

### <a name="log-queries"></a>ログ クエリ

ログ クエリは、Azure Monitor ログ内に収集されたデータの価値を最大限に活用するのに役立ちます。 ログ クエリを使用して、AppServiceHTTPLogs と AppServiceConsoleLogs の両方でログを特定します。 ログ クエリの詳細については、[ログ クエリの概要](../azure-monitor/logs/log-query-overview.md)に関するページを参照してください。

### <a name="view-appservicehttplogs-with-log-query"></a>ログ クエリを使用して AppServiceHTTPLogs を確認する

アプリにアクセスしたところで、`AppServiceHTTPLogs` にある、HTTP 要求に関連付けられたデータを確認しましょう。

1. 左側のナビゲーションで [`Logs`] をクリックします。

![Log Analytics ワークスペースの [ログ]](./media/tutorial-azure-monitor/log-analytics-workspace-logs.png)

2. `appservice` を検索し、`AppServiceHTTPLogs` をダブルクリックします。

![Log Analytics ワークスペースの [テーブル]](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-tables.png)

3. [`Run`] をクリックします。

![Log Analytics ワークスペースの App Service HTTP ログ](./media/tutorial-azure-monitor/log-analytics-workspace-app-service-http-logs.png)

`AppServiceHTTPLogs` クエリでは、過去 24 時間の要求がすべて返されます。 `ScStatus` 列には、HTTP 状態が格納されます。 `HTTP 500` エラーを診断するには、次のように、`ScStatus` を 500 に制限してクエリを実行します。

```kusto
AppServiceHTTPLogs
| where ScStatus == 500
```

### <a name="view-appserviceconsolelogs-with-log-query"></a>ログ クエリを使用して AppServiceConsoleLogs を確認する

HTTP 500 を確認できたので、アプリの標準出力とエラーを見てみましょう。 これらのログは、"AppServiceConsoleLogs" にあります。

(1) `+` をクリックして新しいクエリを作成します。 

(2) `AppServiceConsoleLogs` テーブルをダブルクリックし、[`Run`] をクリックします。 

5 つの画像を変換するとサーバー エラーが発生するため、以下に示すように、`ResultDescription` にフィルターをかけてエラーを抽出することでアプリもエラーを書き込んでいるかどうかを確認できます。

```kusto
AppServiceConsoleLogs |
where ResultDescription  contains "error" 
```

`ResultDescription` 列に、次のエラーが表示されます。

```output
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

### <a name="join-appservicehttplogs-and-appserviceconsolelogs"></a>AppServiceHTTPLogs と AppServiceConsoleLogs を結合する

HTTP 500 と標準エラーの両方を特定したので、これらのメッセージの間に相関関係があるかどうかを確認する必要があります。 次に、タイム スタンプ `TimeGenerated` に基づいてテーブルを結合します。

> [!NOTE]
> 次の処理を行うクエリは用意されています。
>
> - HTTPLogs をフィルター処理して 500 エラーを抽出する
> - コンソールのログを照会する
> - `TimeGenerated` に基づいてテーブルを結合する
>

次のクエリを実行します。

```kusto
let myHttp = AppServiceHTTPLogs | where  ScStatus == 500 | project TimeGen=substring(TimeGenerated, 0, 19), CsUriStem, ScStatus;  

let myConsole = AppServiceConsoleLogs | project TimeGen=substring(TimeGenerated, 0, 19), ResultDescription;

myHttp | join myConsole on TimeGen | project TimeGen, CsUriStem, ScStatus, ResultDescription;
```

`ResultDescription` 列に、Web サーバー エラーと同時に次のエラーが表示されます。

```output
PHP Fatal error:  Allowed memory size of 134217728 bytes exhausted 
(tried to allocate 16384 bytes) in /home/site/wwwroot/process.php on line 20, 
referer: http://<app-name>.azurewebsites.net/
```

このメッセージは、`process.php` の 20 行目でメモリが使い果たされたことを示します。 これで、HTTP 500 エラーの発生中にアプリケーションでエラーが発生したことが確認されました。 問題を特定するコードを見てみましょう。

## <a name="identify-the-error"></a>エラーを特定する

ローカル ディレクトリで、`process.php` を開き、20 行目を確認します。 

```php
imagepng($imgArray[$x], $filename);
```

最初の引数 `$imgArray[$x]` は、変換を必要とするすべての JPG (インメモリ) を保持する変数です。 ただし、`imagepng` に必要なのは、すべての画像ではなく、変換される画像のみです。 画像の事前読み込みは必要ありません。メモリ不足の原因となり、HTTP 500 を引き起こす可能性があります。 必要に応じて画像を読み込むようコードを更新して、問題が解決されるかどうかを確認してみましょう。 次に、コードを改良してメモリの問題に対処します。

## <a name="fix-the-app"></a>アプリを修正する

### <a name="update-locally-and-redeploy-the-code"></a>コードをローカルで更新して再デプロイする

メモリ不足に対処するために、`process.php` に対して次の変更を行います。

```php
<?php

//Retrieve query parameters
$maxImages = $_GET['images'];
$imgNames  = explode(",",$_GET['imgNames']);

//Load JPEGs into an array (in memory)
for ($x=0; $x<$maxImages; $x++){
    $filename = './images/converted_' . substr($imgNames[$x],0,-4) . '.png';
    imagepng(imagecreatefromjpeg("./images/" . $imgNames[$x]), $filename);
}
```

Git で変更をコミットしてから、コード変更を Azure にプッシュします。

```bash
git commit -am "Load images on-demand in process.php"
git push azure main
```

### <a name="browse-to-the-azure-app"></a>Azure アプリを参照する

`http://<app-name>.azurewebsites.net` を参照します。 

画像を変換しても、HTTP 500 エラーが発生しなくなります。

![Azure App Service で実行される PHP アプリ](./media/tutorial-azure-monitor/sample-monitor-app-working.png)

[!INCLUDE [cli-samples-clean-up](../../includes/cli-samples-clean-up.md)]

次のコマンドを使用して、診断設定を削除します。

```bash
az monitor diagnostic-settings delete --resource $resourceID -n myMonitorLogs
```
ここで学習した内容は次のとおりです。

> [!div class="checklist"]
> * Azure Monitor を使用して Web アプリを構成しました
> * ログを Log Analytics に送信しました
> * ログ クエリを使用して Web アプリのエラーを特定し、トラブルシューティングを行いました

## <a name="next-steps"></a><a name="nextsteps"></a> 次のステップ
* [Azure Monitor でログにクエリを実行する](../azure-monitor/logs/log-query-overview.md)
* [Visual Studio での Azure App Service のトラブルシューティング](troubleshoot-dotnet-visual-studio.md)
* [HDInsight でのアプリ ログの分析](https://gallery.technet.microsoft.com/scriptcenter/Analyses-Windows-Azure-web-0b27d413)