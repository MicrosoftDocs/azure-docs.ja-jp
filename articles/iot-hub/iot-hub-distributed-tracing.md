---
title: 分散トレース (プレビュー) を使用して IoT メッセージに関連付け ID を追加する
description: 分散トレース機能を使用して、ソリューションで使用される Azure サービス全体の IoT メッセージをトレースする方法について説明します。
author: jlian
manager: briz
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: jlian
ms.custom:
- amqp
- mqtt
- fasttrack-edit
- iot
ms.openlocfilehash: adcbf4efc4dfaa7701c18440531327949640cb53
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "100581987"
---
# <a name="trace-azure-iot-device-to-cloud-messages-with-distributed-tracing-preview"></a>分散トレース (プレビュー) を使用して Azure IoT の cloud-to-device メッセージをトレースする

Microsoft Azure IoT Hub は現在、[プレビュー機能](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)として分散トレースをサポートしています。

IoT Hub は、分散トレースをサポートする最初の Azure サービスの 1 つです。 多くの Azure サービスが分散トレースをサポートしているため、ソリューションに関連する Azure サービス全体で IoT メッセージをトレースできます。 分散トレースの背景については、[分散トレース](../azure-monitor/app/distributed-tracing.md)に関連するページを参照してください。

IoT Hub に対して分散トレースを有効にすると、次のことを実行できるようになります。

- [トレース コンテキスト](https://github.com/w3c/trace-context)を使用して、IoT Hub を介した各メッセージの流れを正確に監視できます。 このトレース コンテキストには、あるコンポーネントからのイベントを別のコンポーネントからのイベントに関連付けることができる関連付け ID が含まれています。 これは、[デバイス ツイン](iot-hub-devguide-device-twins.md)を使用して、サブセットまたはすべての IoT デバイス メッセージに適用できます。
- トレース コンテキストを自動的に [Azure Monitor ログ](monitor-iot-hub.md)に記録できます。
- デバイスから IoT Hub およびルーティング エンドポイントまでのメッセージ フローと待ち時間を測定および把握できます。
- ご使用の IoT ソリューション内の Azure 以外のサービスに対して分散トレースをどのように実装するか、検討を開始できます。

この記事では、分散トレースが組み込まれた [C 用 Azure IoT device SDK](iot-hub-device-sdk-c-intro.md) を使用します。 分散トレースのサポートは、他の SDK では現在進行中です。

## <a name="prerequisites"></a>前提条件

- 分散トレースのプレビューは現在、次のリージョンで作成された IoT Hub に対してのみサポートされています。

  - **北ヨーロッパ**
  - **東南アジア**
  - **米国西部 2**

- この記事は、読者が IoT Hub へのテレメトリ メッセージの送信について理解していることを前提としています。 [テレメトリ C の送信のクイックスタート](quickstart-send-telemetry-c.md)に関するページを完了していることを確認してください。

- IoT Hub にデバイスを登録し (各クイックスタートで使用可能な手順)、接続文字列をメモします。

- 最新バージョンの [Git](https://git-scm.com/download/) をインストールします。

## <a name="configure-iot-hub"></a>IoT Hub を構成する

このセクションでは、分散トレース属性 (関連付け ID とタイムスタンプ) を記録するように IoT Hub を構成します。

1. [Azure portal](https://portal.azure.com/) で、IoT Hub に移動します。

1. IoT Hub の左側のウィンドウで、下にスクロールして **[監視]** セクションを表示し、 **[診断設定]** をクリックします。

1. **[診断設定の追加]** をクリックします。

1. **[名前]** フィールドに、新しい診断設定の名前を入力します。 たとえば、**DistributedTracingSettings** などです。

1. ログの送信先を決定するために、次のオプションを 1 つ以上選択します。

    - **[ストレージ アカウントへのアーカイブ]** :ログ情報を格納するようにストレージ アカウントを構成します。
    - **[イベント ハブへのストリーム]** :ログ情報を格納するようにイベント ハブを構成します。
    - **[Log Analytics への送信]** :ログ情報を格納するようにログ分析ワークスペースを構成します。

1. **[ログ]** セクションで、ログ情報が必要な操作を選択します。

    必ず **[DistributedTracing]** を含め、ログを保持する日数に対して **[リテンション期間]** を構成します。 ログのリテンション期間は、ストレージのコストに影響します。

    ![IoT の診断設定のための DistributedTracing カテゴリがどこにあるかを示すスクリーンショット](./media/iot-hub-distributed-tracing/diag-logs.png)

1. 新しい設定に対して **[保存]** をクリックします。

1. (省略可能) 異なる場所へのメッセージ フローを確認するために、[2 つ以上の異なるエンドポイントへのルーティング規則](iot-hub-devguide-messages-d2c.md)を設定します。

ログ記録を有効にすると、次の状況のいずれかで有効なトレース プロパティを含むメッセージが検出されたときに、IoT Hub によってログが記録されます。

- メッセージが IoT Hub のゲートウェイに到着する。
- メッセージが IoT Hub によって処理される。
- メッセージがカスタム エンドポイントにルーティングされる。 ルーティングを有効にする必要があります。

これらのログとそのスキーマの詳細については、[IoT Hub の監視](monitor-iot-hub.md)と [IoT Hub リソース ログの分散トレース](monitor-iot-hub-reference.md#distributed-tracing-preview)に関する記事を参照してください。

## <a name="set-up-device"></a>デバイスを設定する

このセクションでは、[Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) で使用する開発環境を準備します。 次に、サンプルの 1 つを変更して、デバイスのテレメトリ メッセージに対して分散トレースを有効にします。

これらの手順は、Windows でサンプルを構築するためのものです。 その他の環境では、[C SDK のコンパイル](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#compile)に関するページ、または[プラットフォーム固有の開発用のパッケージ済み C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/readme.md#prepackaged-c-sdk-for-platform-specific-development) に関するページを参照してください。

### <a name="clone-the-source-code-and-initialize"></a>ソース コードを複製し、初期化する

1. Visual Studio 2019 用の ["C++ によるデスクトップ開発" ワークロード](/cpp/build/vscpp-step-0-installation?view=vs-2019)をインストールします。 Visual Studio 2017 および 2015 もサポートされています。

1. [CMake](https://cmake.org/) をインストールします。 コマンド プロンプトから `cmake -version` と入力して、これが `PATH` に含まれていることを確認してください。

1. コマンド プロンプトまたは Git Bash シェルを開きます。 次のコマンドを実行して、最新リリースの [Azure IoT C SDK](https://github.com/Azure/azure-iot-sdk-c) GitHub リポジトリを複製します。

    ```cmd
    git clone -b public-preview https://github.com/Azure/azure-iot-sdk-c.git
    cd azure-iot-sdk-c
    git submodule update --init
    ```

    この操作は、完了するまでに数分かかります。

1. git リポジトリのルート ディレクトリに `cmake` サブディレクトリを作成し、そのフォルダーに移動します。 `azure-iot-sdk-c` ディレクトリから次のコマンドを実行します。

    ```cmd
    mkdir cmake
    cd cmake
    cmake ..
    ```

    `cmake` で C++ コンパイラが見つからない場合は、上記のコマンドの実行中にビルド エラーが発生している可能性があります。 これが発生した場合は、[Visual Studio コマンド プロンプト](/dotnet/framework/tools/developer-command-prompt-for-vs)でこのコマンドを実行してください。 

    ビルドが成功すると、最後のいくつかの出力行は次のようになります。

    ```cmd
    $ cmake ..
    -- Building for: Visual Studio 15 2017
    -- Selecting Windows SDK version 10.0.16299.0 to target Windows 10.0.17134.
    -- The C compiler identification is MSVC 19.12.25835.0
    -- The CXX compiler identification is MSVC 19.12.25835.0

    ...

    -- Configuring done
    -- Generating done
    -- Build files have been written to: E:/IoT Testing/azure-iot-sdk-c/cmake
    ```

### <a name="edit-the-send-telemetry-sample-to-enable-distributed-tracing"></a>送信テレメトリ サンプルを編集して分散トレースを有効にする

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/azure-iot-distributed-tracing-sample/blob/master/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c" target="_blank">GitHub でのサンプルの入手</a>

1. エディターを使用して、`azure-iot-sdk-c/iothub_client/samples/iothub_ll_telemetry_sample/iothub_ll_telemetry_sample.c` ソース ファイルを開きます。

1. 次の `connectionString` 定数の宣言を探します。

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=2)]

    `connectionString` 定数の値を、[送信テレメトリ C のクイックスタート](./quickstart-send-telemetry-c.md)に関するページの[デバイスの登録](./quickstart-send-telemetry-c.md#register-a-device)セクションでメモしたデバイス接続文字列に置き換えます。

1. `MESSAGE_COUNT` 定義を `5000` に変更します。

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_config&highlight=3)]

1. `IoTHubDeviceClient_LL_SetConnectionStatusCallback` を呼び出しているコード行を見つけ、送信メッセージのループ前に接続状態のコールバック関数を登録します。 デバイスの分散トレースを有効にする `IoTHubDeviceClient_LL_EnablePolicyConfiguration` を呼び出すコードを、以下に示されているその行の下に追加します。

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_tracing&highlight=5)]

    `IoTHubDeviceClient_LL_EnablePolicyConfiguration` 関数により、[デバイス ツイン](./iot-hub-devguide-device-twins.md)を介して構成される特定の IoT Hub 機能のポリシーが有効になります。 上記のコード行で `POLICY_CONFIGURATION_DISTRIBUTED_TRACING` が有効になると、デバイスのトレース動作は、デバイス ツインで行われた分散トレースの変更を反映します。

1. すべてのクォータを使い尽くさずにサンプル アプリを実行し続けるために、送信メッセージ ループの末尾に 1 秒の遅延を追加します。

    [!code-c[](~/samples-iot-distributed-tracing/iothub_ll_telemetry_sample-c/iothub_ll_telemetry_sample.c?name=snippet_sleep&highlight=8)]

### <a name="compile-and-run"></a>コンパイルと実行

1. 前に作成した CMake ディレクトリ (`azure-iot-sdk-c/cmake`) から *iothub_ll_telemetry_sample* プロジェクト ディレクトリに移動し、サンプルをコンパイルします。

    ```cmd
    cd iothub_client/samples/iothub_ll_telemetry_sample
    cmake --build . --target iothub_ll_telemetry_sample --config Debug
    ```

1. アプリケーションを実行します。 デバイスにより、分散トレースをサポートするテレメトリが送信されます。

    ```cmd
    Debug/iothub_ll_telemetry_sample.exe
    ```

1. そのままアプリを実行します。 必要に応じて、コンソール ウィンドウを調べることで、IoT Hub に送信されるメッセージを確認します。

<!-- For a client app that can receive sampling decisions from the cloud, check out [this sample](https://aka.ms/iottracingCsample).  -->

### <a name="workaround-for-third-party-clients"></a>サード パーティ製クライアントの回避策

C SDK を使用せずに分散トレース機能をプレビューするのは **簡単ではありません**。 したがって、このアプローチはお勧めできません。

最初に、開発ガイド「[IoT Hub メッセージを作成し、読み取る](iot-hub-devguide-messages-construct.md)」に従って、すべての IoT Hub プロトコル プリミティブをご自身のメッセージで実装する必要があります。 次に、MQTT/AMQP メッセージのプロトコル プロパティを編集して、`tracestate` を **システム プロパティ** として追加します。 具体的には次のとおりです。

* MQTT の場合は、`%24.tracestate=timestamp%3d1539243209` をメッセージ トピックに追加します。`1539243209` は、Unix タイムスタンプ形式のメッセージの作成時に置き換える必要があります。 例としては、[C SDK](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/iothubtransport_mqtt_common.c#L761) の実装を参照してください
* AMQP の場合は、`key("tracestate")` と `value("timestamp=1539243209")` をメッセージの注釈として追加します。 リファレンス実装については、[こちら](https://github.com/Azure/azure-iot-sdk-c/blob/6633c5b18710febf1af7713cf1a336fd38f623ed/iothub_client/src/uamqp_messaging.c#L527)を参照してください。

このプロパティを含むメッセージの割合を制御するには、ツインの更新など、クラウドによって開始されたイベントをリッスンするロジックを実装します。

## <a name="update-sampling-options"></a>サンプリング オプションを更新する 

クラウドからトレースするメッセージの割合を変更するには、デバイス ツインを更新する必要があります。 ポータルの JSON エディター、IoT Hub サービス SDK など、複数の方法でこれを実行できます。 次のサブセクションで例を示します。

### <a name="update-using-the-portal"></a>ポータルを使用して更新する

1. [Azure portal](https://portal.azure.com/) でご使用の IoT Hub に移動し、 **[IoT デバイス]** をクリックします。

1. ご使用のデバイスをクリックします。

1. **[Enable distributed tracing (preview)]\(分散トレース (プレビュー) を有効にする\)** を探し、 **[有効にする]** を選択します。

    ![Azure portal で分散トレースを有効にする](./media/iot-hub-distributed-tracing/azure-portal.png)

1. 0% と 100% の間で **[サンプリング レート]** を選択します。

1. **[保存]** をクリックします。

1. しばらく待ってから、 **[更新]** をクリックします。デバイスによって正常に認識されると、チェックマークの付いた同期アイコンが表示されます。

1. テレメトリ メッセージ アプリのコンソール ウィンドウに戻ります。 アプリケーション プロパティの `tracestate` で送信されるメッセージを確認できます。

    ![トレースの状態](./media/iot-hub-distributed-tracing/MicrosoftTeams-image.png)

1. (省略可能) サンプリング レートを別の値に変更して、アプリケーション プロパティに `tracestate` が含まれるメッセージの頻度の変化を監視します。

### <a name="update-using-azure-iot-hub-for-vs-code"></a>VS Code 用 Azure IoT Hub を使用して更新する

1. VS Code をインストールし、[ここ](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools)から VS Code 用 Azure IoT Hub の最新バージョンをインストールします。

1. VS Code を開き、[IoT Hub 接続文字列を設定](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit#user-content-prerequisites)します。

1. デバイスを展開し、 **[Distributed Tracing Setting (Preview)]\(分散トレース設定 (プレビュー)\)** を探します。 その下で、サブ ノードの **[Update Distributed Tracing Setting (Preview)]\(分散トレース設定 (プレビュー) の更新\)** をクリックします。

    ![Azure IoT Hub 拡張機能で分散トレースを有効にする](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-1.png)

1. ポップアップ ウィンドウで、 **[有効]** を選択し、Enter キーを押してサンプリング レートとして 100 を確定します。

    ![サンプリング モードを更新する](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-2.png)

    ![サンプリング レートを更新する](./media/iot-hub-distributed-tracing/update-distributed-tracing-setting-3.png)

### <a name="bulk-update-for-multiple-devices"></a>複数のデバイスの一括更新

複数のデバイスの分散トレース サンプリング構成を更新するには、[自動デバイス構成](./iot-hub-automatic-device-management.md)を使用します。 次のツイン スキーマに従う必要があります。

```json
{
    "properties": {
        "desired": {
            "azureiot*com^dtracing^1": {
                "sampling_mode": 1,
                "sampling_rate": 100
            }
        }
    }
}
```

| 要素名 | 必須 | Type | 説明 |
|-----------------|----------|---------|-----------------------------------------------------|
| `sampling_mode` | はい | Integer | サンプリングのオンとオフを切り替えるために、現在 2 つのモード値がサポートされています。 `1` がオンで、`2` がオフです。 |
| `sampling_rate` | はい | Integer | この値は、パーセンテージです。 `0` から `100` までの値 (両端を含む) のみ許可されます。  |

## <a name="query-and-visualize"></a>クエリと視覚化を実行する

IoT Hub によって記録されたすべてのトレースを表示するには、診断設定で選択したログ ストアに対してクエリを実行します。 このセクションでは、いくつかの異なるオプションについて説明します。

### <a name="query-using-log-analytics"></a>Log Analytics を使用してクエリを実行する

[リソース ログで Log Analytics](../azure-monitor/essentials/resource-logs.md#send-to-azure-storage) を設定した場合、`DistributedTracing` カテゴリでログを探すことでクエリを実行します。 たとえば、次のクエリでは、記録されたすべてのトレースが表示されます。

```Kusto
// All distributed traces 
AzureDiagnostics 
| where Category == "DistributedTracing" 
| project TimeGenerated, Category, OperationName, Level, CorrelationId, DurationMs, properties_s 
| order by TimeGenerated asc  
```

Log Analytics で表示されるログの例

| TimeGenerated | OperationName | カテゴリ | Level | CorrelationId | DurationMs | Properties |
|--------------------------|---------------|--------------------|---------------|---------------------------------------------------------|------------|------------------------------------------------------------------------------------------------------------------------------------------|
| 2018-02-22T03:28:28.633Z | DiagnosticIoTHubD2C | DistributedTracing | Informational | 00-8cd869a412459a25f5b4f31311223344-0144d2590aacd909-01 |  | {"deviceId":"AZ3166","messageSize":"96","callerLocalTimeUtc":"2018-02-22T03:27:28.633Z","calleeLocalTimeUtc":"2018-02-22T03:27:28.687Z"} |
| 2018-02-22T03:28:38.633Z | DiagnosticIoTHubIngress | DistributedTracing | Informational | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 20 | {"isRoutingEnabled":"false","parentSpanId":"0144d2590aacd909"} |
| 2018-02-22T03:28:48.633Z | DiagnosticIoTHubEgress | DistributedTracing | Informational | 00-8cd869a412459a25f5b4f31311223344-349810a9bbd28730-01 | 23 | {"endpointType":"EventHub","endpointName":"myEventHub", "parentSpanId":"0144d2590aacd909"} |

さまざまな種類のログについては、[Azure IoT Hub の分散トレース ログ](monitor-iot-hub-reference.md#distributed-tracing-preview)に関する記事を参照してください。

### <a name="application-map"></a>アプリケーション マップ

IoT メッセージのフローを可視化するために、アプリケーション マップのサンプル アプリを設定します。 サンプル アプリでは、Azure Function と Event Hub を使用して[アプリケーション マップ](../azure-monitor/app/app-map.md)に分散トレース ログが送信されます。

> [!div class="button"]
> <a href="https://github.com/Azure-Samples/e2e-diagnostic-provision-cli" target="_blank">GitHub でのサンプルの入手</a>

次の図は、3 つのルーティング エンドポイントがあるアプリケーション マップの分散トレースを示しています。

![アプリケーション マップでの IoT 分散トレース](./media/iot-hub-distributed-tracing/app-map.png)

## <a name="understand-azure-iot-distributed-tracing"></a>Azure IoT 分散トレースを理解する

### <a name="context"></a>Context

Microsoft 独自の[参照アーキテクチャ](/azure/architecture/reference-architectures/iot) (英語のみ) を含む多くの IoT ソリューションは、通常、[マイクロサービス アーキテクチャ](/azure/architecture/microservices/)のバリアントに準拠しています。 IoT ソリューションが複雑になるにつれて、数多くのマイクロサービスを使用することになります。 それらのマイクロサービスは、Azure のものである場合とそうでない場合があります。 IoT メッセージのドロップまたはスローダウンが発生している場所を正確に特定することは困難になる可能性があります。 たとえば、5 つの異なる Azure サービスと 1,500 台のアクティブ デバイスを使用する IoT ソリューションがあるとします。 device-to-cloud メッセージがデバイスごとに毎秒 10 件送信されますが (合計で毎秒 15,000 メッセージ)、Web アプリには毎秒 10,000 件のメッセージしか表示されません。 どこに問題がありますか。 どのようにして原因を見つけることができるのでしょうか。

### <a name="distributed-tracing-pattern-in-microservice-architecture"></a>マイクロサービス アーキテクチャでの分散トレース パターン

異なるサービス間での IoT メッセージのフローを再構築するには、各サービスで、メッセージを一意に識別する *関連付け ID* を伝達する必要があります。 一元管理されたシステムで収集されると、ユーザーは関連付け ID を使用してメッセージのフローを確認できます。 この方法は、[分散トレース パターン](/azure/architecture/microservices/logging-monitoring#distributed-tracing)と呼ばれます。

Microsoft は、分散トレースのより広範な採用をサポートするために、[分散トレースの W3C 標準の提案](https://w3c.github.io/trace-context/)に寄与しています。

### <a name="iot-hub-support"></a>IoT Hub のサポート

有効にすると、IoT Hub の分散トレース サポートは次のフローに従います。

1. IoT デバイスでメッセージが生成されます。
1. IoT デバイスは、このメッセージにトレース コンテキストを割り当てる必要があるかを (クラウドからサポートを受けて) 判断します。
1. SDK によって、メッセージ作成タイムスタンプを格納する `tracestate` がメッセージ プロパティに追加されます。
1. IoT デバイスによって、IoT Hub にメッセージが送信されます。
1. メッセージが IoT Hub ゲートウェイに到着します。
1. IoT Hub で、メッセージ プロパティ内の `tracestate` が調べられ、それが正しい形式であるかどうか確認されます。
1. そうである場合、IoT Hub によって、メッセージのグローバルに一意の `trace-id` ("ホップ" の場合は `span-id`) が生成され、それらが `DiagnosticIoTHubD2C` の操作で [IoT Hub 分散トレース ログ](monitor-iot-hub-reference.md#distributed-tracing-preview)に記録されます。
1. メッセージの処理が終了すると、IoT Hub によって別の `span-id` が生成され、`DiagnosticIoTHubIngress` の操作で既存の `trace-id` とともにそれが記録されます。
1. メッセージに対してルーティングが有効になっている場合、IoT Hub はそれをカスタム エンドポイントに書き込み、同じ `trace-id` を持つ別の `span-id` をカテゴリ `DiagnosticIoTHubEgress` に記録します。
1. 上記の手順は、生成されたメッセージごとに繰り返されます。

## <a name="public-preview-limits-and-considerations"></a>パブリック プレビューの制限と考慮事項

- W3C トレース コンテキスト標準の提案は、現在、草案の段階です。
- 現在、クライアント SDK でサポートされる開発言語は C のみです。
- cloud-to-device のツイン機能は、[IoT Hub の基本層](iot-hub-scaling.md#basic-and-standard-tiers)では使用できません。 ただし、IoT Hub は、適切に構成されたトレース コンテキスト ヘッダーを認識した場合には、Azure Monitor にログを記録します。
- 効率的な操作を確実にするために、IoT Hub は、分散トレースの一部として発生する可能性のあるログ記録のレートにスロットルを適用します。

## <a name="next-steps"></a>次のステップ

- マイクロサービスの一般的な分散トレース パターンについて詳しくは、[マイクロサービス アーキテクチャ パターン: 分散トレース](https://microservices.io/patterns/observability/distributed-tracing.html)に関するページを参照してください。
- 分散トレース設定を多数のデバイスに適用するための構成を設定するには、「[多数の IoT デバイスの構成と監視](./iot-hub-automatic-device-management.md)」を参照してください。
- Azure Monitor の詳細については、「[Azure Monitor とは](../azure-monitor/overview.md)」を参照してください。
- IoT Hub での Azure Monitor の使用の詳細については、[IoT Hub の監視](monitor-iot-hub.md)に関するページを参照してください