---
title: チュートリアル:Azure Cosmos DB と Event Hubs で Java 関数を使用する
description: このチュートリアルでは、Java で記述された関数を使用して、Event Hubs からイベントを消費し、Azure Cosmos DB で更新を行う方法について説明します。
author: KarlErickson
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: karler
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0d5d285812aa61cf9fe684b268dc07718f6d1a1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "101708749"
---
# <a name="tutorial-create-a-function-in-java-with-an-event-hub-trigger-and-an-azure-cosmos-db-output-binding"></a>チュートリアル:イベント ハブ トリガーと Azure Cosmos DB 出力バインドを使用して Java で関数を作成する

このチュートリアルでは、Azure Functions を使用して、気温と気圧のデータの連続ストリームを分析する Java 関数を作成する方法について説明します。 センサーの測定値を表すイベント ハブ イベントにより、関数がトリガーされます。 関数によってイベント データが処理され、状態エントリが Azure Cosmos DB に追加されます。

このチュートリアルでは、次のことについて説明します。

> [!div class="checklist"]
> * Azure CLI を使用して Azure リソースを作成して構成する。
> * これらのリソースと対話する Java 関数を作成してテストする。
> * 関数を Azure にデプロイし、Application Insights を使用して監視する。

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>前提条件

このチュートリアルを完了するには、以下をインストールする必要があります。

- [Java Developer Kit](/azure/developer/java/fundamentals/java-jdk-long-term-support)、バージョン 8
- [Apache Maven](https://maven.apache.org)、バージョン 3.0 以降
- [Azure Functions Core Tools](https://www.npmjs.com/package/azure-functions-core-tools) バージョン 2.6.666 以降[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../includes/azure-cli-prepare-your-environment-no-header.md)]

> [!IMPORTANT]
> このチュートリアルを完了するには、`JAVA_HOME` 環境変数を JDK のインストール場所に設定する必要があります。

このチュートリアルのコードを直接使用する場合は、[java-functions-eventhub-cosmosdb](https://github.com/Azure-Samples/java-functions-eventhub-cosmosdb) サンプル リポジトリを参照してください。

## <a name="create-azure-resources"></a>Azure リソースを作成する

このチュートリアルでは、次のリソースが必要です。

* 他のリソースを格納するリソース グループ
* Event Hubs 名前空間、イベント ハブ、および承認規則
* Cosmos DB アカウント、データベース、およびコレクション
* 関数アプリとそれをホストするためのストレージ アカウント

次のセクションでは、Azure CLI を使用してこれらのリソースを作成する方法について説明します。

### <a name="set-environment-variables"></a>環境変数の設定

次に、作成するリソースの名前と場所の環境変数を作成します。 次のコマンドを使用して、`<value>` のプレースホルダーを選択した値に置き換えます。 値は、[Azure リソースの名前付け規則と制限事項](/azure/architecture/best-practices/resource-naming)に準拠している必要があります。 `LOCATION` 変数には、`az functionapp list-consumption-locations` コマンドによって生成された値のいずれかを使用します。

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
RESOURCE_GROUP=<value>
EVENT_HUB_NAMESPACE=<value>
EVENT_HUB_NAME=<value>
EVENT_HUB_AUTHORIZATION_RULE=<value>
COSMOS_DB_ACCOUNT=<value>
STORAGE_ACCOUNT=<value>
FUNCTION_APP=<value>
LOCATION=<value>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCE_GROUP=<value>
set EVENT_HUB_NAMESPACE=<value>
set EVENT_HUB_NAME=<value>
set EVENT_HUB_AUTHORIZATION_RULE=<value>
set COSMOS_DB_ACCOUNT=<value>
set STORAGE_ACCOUNT=<value>
set FUNCTION_APP=<value>
set LOCATION=<value>
```

---

このチュートリアルの残りの部分では、これらの変数を使用します。 これらの変数は、現在の Azure CLI または Cloud Shell セッションの間のみ持続することに注意してください。 別のローカル ターミナル ウィンドウを使用するか、Cloud Shell セッションがタイムアウトした場合は、これらのコマンドを再度実行する必要があります。

### <a name="create-a-resource-group"></a>リソース グループを作成する

Azure では、リソース グループを使ってアカウント内のすべての関連リソースが収集されます。 そのため、それらをユニットとして表示し、作業が完了したら 1 つのコマンドを使用してそれらを削除できます。

次のコマンドを使用して、リソース グループを作成します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az group create \
    --name $RESOURCE_GROUP \
    --location $LOCATION
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az group create ^
    --name %RESOURCE_GROUP% ^
    --location %LOCATION%
```

---

### <a name="create-an-event-hub"></a>イベント ハブの作成

次に、次のコマンドを使用して Azure Event Hubs 名前空間、イベント ハブ、および承認規則を作成します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az eventhubs namespace create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAMESPACE
az eventhubs eventhub create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --message-retention 1
az eventhubs eventhub authorization-rule create \
    --resource-group $RESOURCE_GROUP \
    --name $EVENT_HUB_AUTHORIZATION_RULE \
    --eventhub-name $EVENT_HUB_NAME \
    --namespace-name $EVENT_HUB_NAMESPACE \
    --rights Listen Send
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az eventhubs namespace create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_NAMESPACE%
az eventhubs eventhub create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_NAME% ^
    --namespace-name %EVENT_HUB_NAMESPACE% ^
    --message-retention 1
az eventhubs eventhub authorization-rule create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %EVENT_HUB_AUTHORIZATION_RULE% ^
    --eventhub-name %EVENT_HUB_NAME% ^
    --namespace-name %EVENT_HUB_NAMESPACE% ^
    --rights Listen Send
```

---

Event Hubs 名前空間には、実際のイベント ハブとその承認規則が含まれています。 関数にこの承認規則を使うと、ハブにメッセージを送信し、対応するイベントをリッスンできます。 1 つの関数からは、テレメトリ データを表すメッセージが送信されます。 もう 1 つの関数では、イベントのリッスン、イベント データの分析、Azure Cosmos DB への結果の格納を行います。

### <a name="create-an-azure-cosmos-db"></a>Azure Cosmos DB を作成する

次に、次のコマンドを使用して、Azure Cosmos DB アカウント、データベース、およびコレクションを作成します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az cosmosdb create \
    --resource-group $RESOURCE_GROUP \
    --name $COSMOS_DB_ACCOUNT
az cosmosdb sql database create \
    --resource-group $RESOURCE_GROUP \
    --account-name $COSMOS_DB_ACCOUNT \
    --name TelemetryDb
az cosmosdb sql container create \
    --resource-group $RESOURCE_GROUP \
    --account-name $COSMOS_DB_ACCOUNT \
    --database-name TelemetryDb \
    --name TelemetryInfo \
    --partition-key-path '/temperatureStatus'
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az cosmosdb create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %COSMOS_DB_ACCOUNT%
az cosmosdb sql database create ^
    --resource-group %RESOURCE_GROUP% ^
    --account-name %COSMOS_DB_ACCOUNT% ^
    --name TelemetryDb
az cosmosdb sql container create ^
    --resource-group %RESOURCE_GROUP% ^
    --account-name %COSMOS_DB_ACCOUNT% ^
    --database-name TelemetryDb ^
    --name TelemetryInfo ^
    --partition-key-path "/temperatureStatus"
```

---

`partition-key-path` 値によって、各項目の `temperatureStatus` 値に基づいてデータが分割されます。 パーティション キーを使い、データを独立してアクセスできる個別のサブセットに分割することで、Cosmos DB のパフォーマンスを向上させることができます。

### <a name="create-a-storage-account-and-function-app"></a>ストレージ アカウントと関数アプリを作成する

次に、Azure Functions に必要な Azure Storage アカウントを作成してから、関数アプリを作成します。 次のコマンドを使用します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az storage account create \
    --resource-group $RESOURCE_GROUP \
    --name $STORAGE_ACCOUNT \
    --sku Standard_LRS
az functionapp create \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --storage-account $STORAGE_ACCOUNT \
    --consumption-plan-location $LOCATION \
    --runtime java \
    --functions-version 2
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az storage account create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %STORAGE_ACCOUNT% ^
    --sku Standard_LRS
az functionapp create ^
    --resource-group %RESOURCE_GROUP% ^
    --name %FUNCTION_APP% ^
    --storage-account %STORAGE_ACCOUNT% ^
    --consumption-plan-location %LOCATION% ^
    --runtime java ^
    --functions-version 2
```

---

`az functionapp create` コマンドを使って関数アプリを作成すると、同じ名前の Application Insights リソースも作成されます。 Application Insights に接続する `APPINSIGHTS_INSTRUMENTATIONKEY` という名前の設定で、関数アプリが自動的に構成されます。 このチュートリアルで後述するように、Azure に関数をデプロイすると、アプリのテレメトリを表示できます。

## <a name="configure-your-function-app"></a>関数アプリを構成する

正常に機能するには、関数アプリから他のリソースにアクセスする必要があります。 以下のセクションでは、ローカル コンピューター上で実行できるように関数アプリを構成する方法について説明します。

### <a name="retrieve-resource-connection-strings"></a>リソースの接続文字列を取得する

次のコマンドを使用して、ストレージ、イベント ハブ、および Cosmos DB の接続文字列を取得し、環境変数に保存します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
AZURE_WEB_JOBS_STORAGE=$( \
    az storage account show-connection-string \
        --name $STORAGE_ACCOUNT \
        --query connectionString \
        --output tsv)
echo $AZURE_WEB_JOBS_STORAGE
EVENT_HUB_CONNECTION_STRING=$( \
    az eventhubs eventhub authorization-rule keys list \
        --resource-group $RESOURCE_GROUP \
        --name $EVENT_HUB_AUTHORIZATION_RULE \
        --eventhub-name $EVENT_HUB_NAME \
        --namespace-name $EVENT_HUB_NAMESPACE \
        --query primaryConnectionString \
        --output tsv)
echo $EVENT_HUB_CONNECTION_STRING
COSMOS_DB_CONNECTION_STRING=$( \
    az cosmosdb keys list \
        --resource-group $RESOURCE_GROUP \
        --name $COSMOS_DB_ACCOUNT \
        --type connection-strings \
        --query 'connectionStrings[0].connectionString' \
        --output tsv)
echo $COSMOS_DB_CONNECTION_STRING
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
FOR /F "delims=" %X IN (' ^
    az storage account show-connection-string ^
        --name %STORAGE_ACCOUNT% ^
        --query connectionString ^
        --output tsv') DO SET AZURE_WEB_JOBS_STORAGE=%X
FOR /F "delims=" %X IN (' ^
    az eventhubs eventhub authorization-rule keys list ^
        --resource-group %RESOURCE_GROUP% ^
        --name %EVENT_HUB_AUTHORIZATION_RULE% ^
        --eventhub-name %EVENT_HUB_NAME% ^
        --namespace-name %EVENT_HUB_NAMESPACE% ^
        --query primaryConnectionString ^
        --output tsv') DO SET EVENT_HUB_CONNECTION_STRING=%X
FOR /F "delims=" %X IN (' ^
    az cosmosdb keys list ^
        --resource-group %RESOURCE_GROUP% ^
        --name %COSMOS_DB_ACCOUNT% ^
        --type connection-strings ^
        --query connectionStrings[0].connectionString ^
        --output tsv') DO SET COSMOS_DB_CONNECTION_STRING=%X
```

---

これらの変数は、Azure CLI コマンドから取得した値に設定されます。 各コマンドには、返された JSON ペイロードから接続文字列を抽出するために JMESPath クエリが使用されます。 また、`echo` を使用すると接続文字列が表示されるため、正常に取得されたことを確認できます。

### <a name="update-your-function-app-settings"></a>関数アプリの設定を更新する

次に、次のコマンドを使用して、接続文字列の値を Azure Functions アカウントのアプリ設定に転送します。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az functionapp config appsettings set \
    --resource-group $RESOURCE_GROUP \
    --name $FUNCTION_APP \
    --settings \
        AzureWebJobsStorage=$AZURE_WEB_JOBS_STORAGE \
        EventHubConnectionString=$EVENT_HUB_CONNECTION_STRING \
        CosmosDBConnectionString=$COSMOS_DB_CONNECTION_STRING
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az functionapp config appsettings set ^
    --resource-group %RESOURCE_GROUP% ^
    --name %FUNCTION_APP% ^
    --settings ^
        AzureWebJobsStorage=%AZURE_WEB_JOBS_STORAGE% ^
        EventHubConnectionString=%EVENT_HUB_CONNECTION_STRING% ^
        CosmosDBConnectionString=%COSMOS_DB_CONNECTION_STRING%
```

---

これで Azure リソースが作成され、適切に連携するように構成されます。

## <a name="create-and-test-your-functions"></a>関数を作成してテストする

次に、ローカル コンピューターにプロジェクトを作成し、Java コードを追加してテストします。 Maven 用 Azure Functions プラグインおよび Azure Functions Core Tools で動作するコマンドを使用します。 関数はローカルで実行されますが、作成したクラウドベースのリソースが使用されます。 関数をローカルで実行した後は、Maven を使用してそれらをクラウドにデプロイし、データと分析が蓄積されていることを確認できます。

Cloud Shell を使用してリソースを作成した場合、ローカルで Azure に接続することはできません。 この場合は、`az login` コマンドを使用してブラウザーベースのログイン プロセスを起動します。 必要に応じて、`az account set --subscription` に続けてサブスクリプション ID を指定して既定のサブスクリプションを設定します。 最後に、次のコマンドを実行して、ローカル コンピューター上でいくつかの環境変数を再作成します。 `<value>` プレースホルダーを以前に使用したものと同じ値に置き換えます。

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
RESOURCE_GROUP=<value>
FUNCTION_APP=<value>
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
set RESOURCE_GROUP=<value>
set FUNCTION_APP=<value>
```

---

### <a name="create-a-local-functions-project"></a>ローカル関数プロジェクトを作成する

次の Maven コマンドを使用して関数プロジェクトを作成し、必要な依存関係を追加します。

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn archetype:generate --batch-mode \
    -DarchetypeGroupId=com.microsoft.azure \
    -DarchetypeArtifactId=azure-functions-archetype \
    -DappName=$FUNCTION_APP \
    -DresourceGroup=$RESOURCE_GROUP \
    -DappRegion=$LOCATION \
    -DgroupId=com.example \
    -DartifactId=telemetry-functions
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn archetype:generate --batch-mode ^
    -DarchetypeGroupId=com.microsoft.azure ^
    -DarchetypeArtifactId=azure-functions-archetype ^
    -DappName=%FUNCTION_APP% ^
    -DresourceGroup=%RESOURCE_GROUP% ^
    -DappRegion=%LOCATION% ^
    -DgroupId=com.example ^
    -DartifactId=telemetry-functions
```

---

このコマンドによって、`telemetry-functions` フォルダー内にいくつかのファイルが生成されます。

* Maven に使用するための `pom.xml` ファイル
* ローカル テスト用のアプリ設定を保持する `local.settings.json` ファイル
* データ分析関数の Cosmos DB 出力バインドに必要な Azure Functions Extension Bundle を有効にする `host.json` ファイル
* 既定の関数実装が含まれている `Function.java` ファイル
* このチュートリアルには不要ないくつかのテスト ファイル

コンパイル エラーを回避するには、テスト ファイルを削除する必要があります。 次のコマンドを実行して、新しいプロジェクト フォルダーに移動し、テスト フォルダーを削除します。

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
cd telemetry-functions
rm -r src/test
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
cd telemetry-functions
rmdir /s /q src\test
```

---

### <a name="retrieve-your-function-app-settings-for-local-use"></a>ローカルで使用するために関数アプリの設定を取得する

ローカル テストの場合、関数プロジェクトには、このチュートリアルの前半で Azure Functions アプリに追加した接続文字列が必要です。 次の Azure Functions Core Tools コマンドを使用します。これにより、クラウドに保存されているすべての関数アプリ設定が取得され、それらが `local.settings.json` ファイルに追加されます。

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
func azure functionapp fetch-app-settings $FUNCTION_APP
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
func azure functionapp fetch-app-settings %FUNCTION_APP%
```

---

### <a name="add-java-code"></a>Java コードを追加する

次に、`Function.java` ファイルを開き、その内容を次のコードに置き換えます。

```java
package com.example;

import com.example.TelemetryItem.status;
import com.microsoft.azure.functions.annotation.Cardinality;
import com.microsoft.azure.functions.annotation.CosmosDBOutput;
import com.microsoft.azure.functions.annotation.EventHubOutput;
import com.microsoft.azure.functions.annotation.EventHubTrigger;
import com.microsoft.azure.functions.annotation.FunctionName;
import com.microsoft.azure.functions.annotation.TimerTrigger;
import com.microsoft.azure.functions.ExecutionContext;
import com.microsoft.azure.functions.OutputBinding;

public class Function {

    @FunctionName("generateSensorData")
    @EventHubOutput(
        name = "event",
        eventHubName = "", // blank because the value is included in the connection string
        connection = "EventHubConnectionString")
    public TelemetryItem generateSensorData(
        @TimerTrigger(
            name = "timerInfo",
            schedule = "*/10 * * * * *") // every 10 seconds
            String timerInfo,
        final ExecutionContext context) {

        context.getLogger().info("Java Timer trigger function executed at: "
            + java.time.LocalDateTime.now());
        double temperature = Math.random() * 100;
        double pressure = Math.random() * 50;
        return new TelemetryItem(temperature, pressure);
    }

    @FunctionName("processSensorData")
    public void processSensorData(
        @EventHubTrigger(
            name = "msg",
            eventHubName = "", // blank because the value is included in the connection string
            cardinality = Cardinality.ONE,
            connection = "EventHubConnectionString")
            TelemetryItem item,
        @CosmosDBOutput(
            name = "databaseOutput",
            databaseName = "TelemetryDb",
            collectionName = "TelemetryInfo",
            connectionStringSetting = "CosmosDBConnectionString")
            OutputBinding<TelemetryItem> document,
        final ExecutionContext context) {

        context.getLogger().info("Event hub message received: " + item.toString());

        if (item.getPressure() > 30) {
            item.setNormalPressure(false);
        } else {
            item.setNormalPressure(true);
        }

        if (item.getTemperature() < 40) {
            item.setTemperatureStatus(status.COOL);
        } else if (item.getTemperature() > 90) {
            item.setTemperatureStatus(status.HOT);
        } else {
            item.setTemperatureStatus(status.WARM);
        }

        document.setValue(item);
    }
}
```

ご覧のように、このファイルには `generateSensorData` と `processSensorData` の 2 つの関数が含まれています。 `generateSensorData` 関数を使うと、イベント ハブに気温と気圧の測定値を送信するセンサーをシミュレートできます。 タイマー トリガーによって 10 秒ごとに関数が実行され、イベント ハブの出力バインドから戻り値がイベント ハブに送信されます。

イベント ハブでは、メッセージを受け取ると、イベントが生成されます。 イベントを受け取ると、`processSensorData` 関数が実行されます。 次に、イベント データが処理され、Azure Cosmos DB の出力バインドを使って結果が Azure Cosmos DB に送信されます。

これらの関数に使用されるデータは、実装が必要な `TelemetryItem` というクラスを使用して保存されます。 `Function.java` と同じ場所に `TelemetryItem.java` という新しいファイルを作成し、次のコードを追加します。

```java
package com.example;

public class TelemetryItem {

    private String id;
    private double temperature;
    private double pressure;
    private boolean isNormalPressure;
    private status temperatureStatus;
    static enum status {
        COOL,
        WARM,
        HOT
    }

    public TelemetryItem(double temperature, double pressure) {
        this.temperature = temperature;
        this.pressure = pressure;
    }

    public String getId() {
        return id;
    }

    public double getTemperature() {
        return temperature;
    }

    public double getPressure() {
        return pressure;
    }

    @Override
    public String toString() {
        return "TelemetryItem={id=" + id + ",temperature="
            + temperature + ",pressure=" + pressure + "}";
    }

    public boolean isNormalPressure() {
        return isNormalPressure;
    }

    public void setNormalPressure(boolean isNormal) {
        this.isNormalPressure = isNormal;
    }

    public status getTemperatureStatus() {
        return temperatureStatus;
    }

    public void setTemperatureStatus(status temperatureStatus) {
        this.temperatureStatus = temperatureStatus;
    }
}
```

### <a name="run-locally"></a>ローカルで実行する

関数をローカルでビルドして実行し、Azure Cosmos DB にデータが表示されるようになりました。

次の Maven コマンドを使用して、関数をビルドして実行します。

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn clean package
mvn azure-functions:run
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn clean package
mvn azure-functions:run
```

---

いくつかのビルドおよび起動メッセージの後、関数が実行されるたびに次の例のような出力が表示されます。

```output
[10/22/19 4:01:30 AM] Executing 'Functions.generateSensorData' (Reason='Timer fired at 2019-10-21T21:01:30.0016769-07:00', Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Java Timer trigger function executed at: 2019-10-21T21:01:30.015
[10/22/19 4:01:30 AM] Function "generateSensorData" (Id: c1927c7f-4f70-4a78-83eb-bc077d838410) invoked by Java Worker
[10/22/19 4:01:30 AM] Executed 'Functions.generateSensorData' (Succeeded, Id=c1927c7f-4f70-4a78-83eb-bc077d838410)
[10/22/19 4:01:30 AM] Executing 'Functions.processSensorData' (Reason='', Id=f4c3b4d7-9576-45d0-9c6e-85646bb52122)
[10/22/19 4:01:30 AM] Event hub message received: TelemetryItem={id=null,temperature=32.728691307527015,pressure=10.122563042388165}
[10/22/19 4:01:30 AM] Function "processSensorData" (Id: f4c3b4d7-9576-45d0-9c6e-85646bb52122) invoked by Java Worker
[10/22/19 4:01:38 AM] Executed 'Functions.processSensorData' (Succeeded, Id=1cf0382b-0c98-4cc8-9240-ee2a2f71800d)
```

次に [Azure portal](https://portal.azure.com) に移動し、Azure Cosmos DB アカウントに移動できます。 データを受け取ったら、**[データ エクスプローラー]** を選択し、**TelemetryInfo** を展開してから、**[項目]** を選択して表示します。

![Cosmos DB Data Explorer](media/functions-event-hub-cosmos-db/data-explorer.png)

## <a name="deploy-to-azure-and-view-app-telemetry"></a>Azure にデプロイしてアプリのテレメトリを表示する

最後に、アプリを Azure にデプロイし、引き続きローカルと同様に機能することを確認できます。

次のコマンドを使用して、プロジェクトを Azure にデプロイします。

# <a name="bash"></a>[Bash](#tab/bash)

```Bash
mvn azure-functions:deploy
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```cmd
mvn azure-functions:deploy
```

---

関数は Azure で実行されるようになります。データは引き続き Azure Cosmos DB に蓄積します。 次のスクリーンショットに示すように、デプロイされた関数アプリを Azure portal で表示し、接続された Application Insights リソースを介してアプリのテレメトリを表示できます。

**Live Metrics Stream:**

![Application Insights Live Metrics Stream](media/functions-event-hub-cosmos-db/application-insights-live-metrics-stream.png)

**パフォーマンス:**

![Application Insights の [パフォーマンス] ブレード](media/functions-event-hub-cosmos-db/application-insights-performance.png)

## <a name="clean-up-resources"></a>リソースをクリーンアップする

このチュートリアルで作成した Azure リソースの使用が完了したら、次のコマンドを使用して削除できます。

# <a name="bash"></a>[Bash](#tab/bash)

```azurecli-interactive
az group delete --name $RESOURCE_GROUP
```

# <a name="cmd"></a>[Cmd](#tab/cmd)

```azurecli
az group delete --name %RESOURCE_GROUP%
```

---

## <a name="next-steps"></a>次のステップ

このチュートリアルでは、イベント ハブ イベントを処理し、Cosmos DB を更新する Azure 関数を作成する方法を学習しました。 詳細については、「[Azure Functions の Java 開発者向けガイド](./functions-reference-java.md)」を参照してください。 使用する注釈の詳細については、[com.microsoft.azure.functions.annotation](/java/api/com.microsoft.azure.functions.annotation) のリファレンスを参照してください。

このチュートリアルでは、環境変数とアプリケーション設定を使用して、接続文字列などのシークレットを保存しました。 これらのシークレットを Azure Key Vault に保存する方法については、「[App Service と Azure Functions の Key Vault 参照を使用する](../app-service/app-service-key-vault-references.md)」を参照してください。

次に、Azure Pipelines CI/CD を使用して自動デプロイを行う方法を学習します。

> [!div class="nextstepaction"]
> [Java をビルドして Azure Functions にデプロイする](/azure/devops/pipelines/ecosystems/java-function)
