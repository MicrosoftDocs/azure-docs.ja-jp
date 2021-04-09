---
title: デバイス シミュレーションで Protocol Buffers を使用する |Microsoft Docs
description: この攻略ガイドでは、Protocol Buffers を使用して、デバイス シミュレーション ソリューション アクセラレータから送信されたテレメトリをシリアル化する方法について説明します。
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.custom: mvc, amqp, devx-track-csharp
ms.date: 11/06/2018
ms.author: dobett
ms.openlocfilehash: 77a795b8f704084b612a2dcbd364b8d5be0a3333
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "96852152"
---
# <a name="serialize-telemetry-using-protocol-buffers"></a>Protocol Buffers を使用してテレメトリをシリアル化する

Protocol Buffers (Protobuf) は、構造化データ用のバイナリ シリアル化形式です。 Protobuf は、XML よりも優れたコンパクト性と高速性を実現するために、シンプル性とパフォーマンスに重点を置いて設計されています。

デバイス シミュレーションでは、プロトコル バッファー言語の **proto3** バージョンがサポートされています。

Protobuf では、データをシリアル化するためにコンパイル済みコードが必要とされるため、デバイス シミュレーションのカスタム バージョンを作成する必要があります。

この攻略ガイドの手順では、以下の方法を説明します。

1. 開発環境を準備する
1. デバイス モデルで Protobuf 形式を使用することを指定する
1. Protobuf 形式を定義する
1. Protobuf クラスを生成する
1. ローカルでテストする

## <a name="prerequisites"></a>前提条件

この攻略ガイドの手順を実行するには、次が必要です。

* Visual Studio Code。 [Mac、Linux、および Windows 用の Visual Studio Code をダウンロード](https://code.visualstudio.com/download)できます。
* .NET Core。 [Mac、Linux、および Windows 用の .NET Core をダウンロード](https://www.microsoft.com/net/download)できます。
* Postman。 [Mac、Windows、または Linux 用の Postman をダウンロード](https://www.getpostman.com/apps)できます。
* [Azure サブスクリプションにデプロイされた IoT ハブ](../iot-hub/iot-hub-create-through-portal.md)。 このガイドの手順を完了するには、IoT ハブの接続文字列が必要です。 この接続文字列は、Azure portal から取得できます。
* SQL API を使用し、かつ[強力な一貫性](../cosmos-db/how-to-manage-database-account.md)を確保するように構成された、[Azure サブスクリプションにデプロイ済みの Cosmos DB データベース](../cosmos-db/create-sql-api-dotnet.md#create-account)。 このガイドの手順を完了するには、Cosmos DB データベースの接続文字列が必要です。 この接続文字列は、Azure portal から取得できます。
* [Azure サブスクリプションにデプロイされた Azure ストレージ アカウント](../storage/common/storage-account-create.md)。 このガイドの手順を完了するには、ストレージ アカウントの接続文字列が必要です。 この接続文字列は、Azure portal から取得できます。

## <a name="prepare-your-development-environment"></a>開発環境を準備する

開発環境を準備するには、次のタスクを実行します。

* デバイス シミュレーション マイクロサービスのソースをダウンロードします。
* ストレージ アダプター マイクロサービスのソースをダウンロードします。
* ストレージ アダプター マイクロサービスをローカルで実行します。

この記事の手順では、Windows の使用を想定しています。 別のオペレーティング システムを使用している場合は、環境に合わせてファイル パスとコマンドの一部を調整することが必要になる場合があります。

### <a name="download-the-microservices"></a>マイクロサービスをダウンロードする

[リモート監視マイクロサービス](https://github.com/Azure/remote-monitoring-services-dotnet/archive/master.zip)を GitHub からローカル コンピューター上の適切な場所にダウンロードし、解凍します。 このリポジトリには、このガイドの手順に必要なストレージ アダプター マイクロ サービスが含まれています。

[デバイス シミュレーション マイクロサービス](https://github.com/Azure/device-simulation-dotnet/archive/master.zip)を GitHub からローカル コンピューター上の適切な場所にダウンロードし、解凍します。

### <a name="run-the-storage-adapter-microservice"></a>ストレージ アダプター マイクロサービスを実行する

Visual Studio Code で **remote-monitoring-services-dotnet-master\storage-adapter** フォルダーを開きます。 **[復元]** ボタンをクリックして、未解決の依存関係を修正します。

**.vscode/launch.json** ファイルを開き、Cosmos DB 接続文字列を **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 環境変数に割り当てます。

> [!NOTE]
> マイクロサービスをローカル コンピューターで実行する際にも、Azure 内の Cosmos DB インスタンスは必要です (サービスを正常に機能させるため)。

ストレージ アダプター マイクロサービスをローカルで実行するために、 **[デバッグ] \> [デバッグの開始]** をクリックします。

Visual Studio Code の **ターミナル** ウィンドウに、実行中のマイクロサービスからの出力が表示されます。これに、Web サービスの正常性チェック用の URL (<http://127.0.0.1:9022/v1/status>) が含まれます。 このアドレスに移動すると、状態が "OK: Alive and well" と表示されます。

ストレージ アダプター マイクロサービスは、以下の手順を実行している間もこの Visual Studio Code のインスタンスで実行したままにしておきます。

## <a name="define-your-device-model"></a>デバイス モデルを定義する

Visual Studio Code の新しいインスタンスで、GitHub からダウンロードした **device-simulation-dotnet-master** フォルダーを開きます。 未解決の依存関係がある場合は、 **[復元]** ボタンをクリックして修正します。

この攻略ガイドでは、アセット トラッカー用の新しいデバイス モデルを作成します。

1. **Services\data\devicemodels** フォルダーに、**assettracker-01.json** という新しいデバイス モデル ファイルを作成します。

1. デバイス モデル ファイル **assettracker-01.json** で、デバイス機能を定義します。 Protobuf デバイス モデルのテレメトリ セクションでは、以下の条件を満たす必要があります。

   * デバイス用に生成する Protobuf クラスの名前を含める。 このクラスの生成方法については、次のセクションで説明します。
   * メッセージ形式として、Protobuf を指定する。

     ```json
     {
     "SchemaVersion": "1.0.0",
     "Id": "assettracker-01",
     "Version": "0.0.1",
     "Name": "Asset Tracker",
     "Description": "An asset tracker with location, temperature, and humidity",
     "Protocol": "AMQP",
     "Simulation": {
       "InitialState": {
         "online": true,
         "latitude": 47.445301,
         "longitude": -122.296307,
         "temperature": 38.0,
         "humidity": 62.0
       },
       "Interval": "00:01:00",
       "Scripts": [
         {
           "Type": "javascript",
           "Path": "assettracker-01-state.js"
         }
       ]
     },
     "Properties": {
       "Type": "AssetTracker",
       "Location": "Field",
       "Latitude": 47.445301,
       "Longitude": -122.296307
     },
     "Telemetry": [
       {
         "Interval": "00:00:10",
         "MessageTemplate": "{\"latitude\":${latitude},\"longitude\":${longitude},\"temperature\":${temperature},\"humidity\":${humidity}}",
         "MessageSchema": {
           "Name": "assettracker-sensors;v1",
           "ClassName": "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf.AssetTracker",
           "Format": "Protobuf",
           "Fields": {
             "latitude": "double",
             "longitude": "double",
             "temperature": "double",
             "humidity": "double"
           }
         }
       }
     ]
     }
     ```

### <a name="create-device-behaviors-script"></a>デバイス動作スクリプトを作成する

デバイスの動作を定義する動作スクリプトを記述します。 詳しくは、[高度なシミュレート デバイスの作成](iot-accelerators-device-simulation-advanced-device.md)に関する記事をご覧ください。

## <a name="define-your-protobuf-format"></a>Protobuf 形式を定義する

デバイス モデルを作成し、メッセージ形式を指定したら、次は **proto** ファイルを作成します。 **proto** ファイルには、次のものを追加します。

* デバイス モデル内の **ClassName** プロパティに一致する `csharp_namespace`。
* シリアル化する各データ構造用のメッセージ。
* メッセージ内の各フィールドの名前と型。

1. **Services\Models\Protobuf\proto** フォルダーに、**assettracker.proto** という新しいファイルを作成します。

1. **proto** ファイルで、構文、名前空間、およびメッセージ スキーマを次のように定義します。

    ```proto
    syntax = "proto3";

    option csharp_namespace = "Microsoft.Azure.IoTSolutions.DeviceSimulation.Services.Models.Protobuf";

    message AssetTracker {
      double latitude=1;
      double longitude=2;
      double temperature=5;
      double humidity=6;
    }
    ```

各要素の `=1`、`=2` のマーカーは、バイナリ エンコーディングでフィールドが使用する一意のタグを指定するものです。 1 から 15 の数値は、1 つ増えるごとにエンコードのサイズが 1 バイト増えることを意味します。

## <a name="generate-the-protobuf-class"></a>Protobuf クラスを生成する

**proto** ファイルを作成したら、次は、メッセージの読み書きに必要なクラスを生成します。 この手順を完了するには、**Protoc** Protobuf コンパイラが必要です。

1. [Protobuf コンパイラを GitHub からダウンロードします](https://github.com/protocolbuffers/protobuf/releases/download/v3.4.0/protoc-3.4.0-win32.zip)

1. ソース ディレクトリ、ターゲット ディレクトリ、および **proto** ファイルの名前を指定して、コンパイラを実行します。 次に例を示します。

    ```cmd
    protoc -I c:\temp\device-simulation-dotnet-master\Services\Models\Protobuf\proto --csharp_out=C:\temp\device-simulation-dotnet-master\Services\Models\Protobuf assettracker.proto
    ```

    このコマンドは、**Services\Models\Protobuf** フォルダーに **Assettracker.cs** ファイルを生成します。

## <a name="test-protobuf-locally"></a>Protobuf をローカルでテストする

このセクションでは、前のセクションで作成したアセット トラッカー デバイスをローカルでテストします。

### <a name="run-the-device-simulation-microservice"></a>デバイス シミュレーション マイクロサービスを実行する

**.vscode/launch.json** ファイルを開き、以下を割り当てます。

* **PC\_IOTHUB\_CONNSTRING** 環境変数に、IoT Hub 接続文字列を割り当てます。
* **PCS\_AZURE\_STORAGE\_ACCOUNT** 環境変数に、ストレージ アカウント接続文字列を割り当てます。
* **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 環境変数に、Cosmos DB 接続文字列を割り当てます。

**WebService/Properties/launchSettings.json** ファイルを開き、以下を割り当てます。

* **PC\_IOTHUB\_CONNSTRING** 環境変数に、IoT Hub 接続文字列を割り当てます。
* **PCS\_AZURE\_STORAGE\_ACCOUNT** 環境変数に、ストレージ アカウント接続文字列を割り当てます。
* **PCS\_STORAGEADAPTER\_DOCUMENTDB\_CONNSTRING** 環境変数に、Cosmos DB 接続文字列を割り当てます。

**WebService\appsettings.ini** ファイルを開き、次のように設定を変更します。

#### <a name="configure-the-solution-to-include-your-new-device-model-files"></a>新しいデバイス モデル ファイルを含めるようにソリューションを構成する

既定では、新しいデバイス モデルの JSON および JS ファイルは、ビルドされたソリューションにコピーされません。 明示的に含める必要があります。

含めるファイルごとに、**services\services.csproj** ファイルにエントリを追加します。 次に例を示します。

```xml
<None Update="data\devicemodels\assettracker-01.json">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
<None Update="data\devicemodels\scripts\assettracker-01-state.js">
    <CopyToOutputDirectory>Always</CopyToOutputDirectory>
</None>
```

マイクロサービスをローカルで実行するために、 **[デバッグ] \> [デバッグの開始]** をクリックします。

Visual Studio Code の **ターミナル** ウィンドウに、実行中のマイクロサービスからの出力が表示されます。

デバイス シミュレーション マイクロサービスは、次の手順を実行している間もこの Visual Studio Code のインスタンスで実行したままにしておきます。

### <a name="set-up-a-monitor-for-device-events"></a>デバイス イベントのモニターを設定する

このセクションでは、Azure CLI を使用して、IoT ハブに接続されたデバイスから送信されたテレメトリを表示するイベント モニターを設定します。

次のスクリプトでは、IoT ハブの名前を **device-simulation-test** と想定しています。

```azurecli-interactive
# Install the IoT extension if it's not already installed
az extension add --name azure-iot

# Monitor telemetry sent to your hub
az iot hub monitor-events --hub-name device-simulation-test
```

イベント モニターは、シミュレートされたデバイスをテストしている間も実行したままにしておきます。

### <a name="create-a-simulation-with-the-asset-tracker-device-type"></a>アセット トラッカー デバイスの種類を使用してシミュレーションを作成する

このセクションでは、Postman ツールを使用して、アセット トラッカー デバイスの種類を使ってシミュレーションを実行するようデバイス シミュレーション マイクロサービスに要求します。 Postman は、Web サービスに REST 要求を送信するためのツールです。

Postman を設定するには:

1. ローカル コンピューターで Postman を開きます。

1. **[File]\(ファイル\) \> [Import]\(インポート\)** をクリックします。 次に、 **[Choose Files]\(ファイルの選択\)** をクリックします。

1. **Azure IoT Device Simulation solution accelerator.postman\_collection** と **Azure IoT Device Simulation solution accelerator.postman\_environment** を選択し、 **[Open]\(開く\)** をクリックします。

1. **[Azure IoT Device Simulation solution accelerator]\(Azure IoT デバイス シミュレーション ソリューション アクセラレータ\)** を展開して、送信可能な要求を表示します。

1. **[No Environment]\(環境なし\)** をクリックし、 **[Azure IoT Device Simulation solution accelerator]\(Azure IoT デバイス シミュレーション ソリューション アクセラレータ\)** を選択します。

これで、デバイス シミュレーション マイクロサービスとやり取りするためのコレクションと環境が Postman ワークスペースに読み込まれました。

シミュレーションを構成および実行するには:

1. Postman コレクション内で、 **[Create asset tracker simulation]\(アセット トラッカー シミュレーションの作成\)** を選択し、 **[Send]\(送信\)** をクリックします。 この要求により、シミュレートされたアセット トラッカー デバイスの種類のインスタンスが 4 つ作成されます。

1. Azure CLI ウィンドウのイベント モニター出力に、シミュレートされたデバイスからのテレメトリが表示されます。

シミュレーションを停止するには、Postman で **[Stop simulation]\(シミュレーションの停止\)** 要求を選択し、 **[Send]\(送信\)** をクリックします。

### <a name="clean-up-resources"></a>リソースをクリーンアップする

ローカルで実行されている 2 つのマイクロサービスを Visual Studio Code インスタンスで停止することができます ( **[デバッグ] \> [デバッグの停止]** )。

IoT Hub および Cosmos DB のインスタンスが必要でなくなった場合は、不必要な課金を回避するために、Azure サブスクリプションからそれらを削除します。

## <a name="iot-hub-support"></a>IoT Hub のサポート

多くの IoT Hub 機能では、Protobuf やその他のバイナリ形式がネイティブにサポートされません。 たとえば、IoT Hub ではメッセージ ペイロードを処理することができないため、メッセージ ペイロードに基づいてルーティングを行うことはできません。 ただし、メッセージ ヘッダーに基づいてルーティングすることは可能です。

## <a name="next-steps"></a>次のステップ

ここでは、Protobuf を使用してテレメトリを送信するようにデバイス シミュレーションをカスタマイズする方法について学習しました。次の手順では、GitHub リポジトリにアクセスし、[デバイス シミュレーション](https://github.com/Azure/device-simulation-dotnet)の詳細について説明します。
