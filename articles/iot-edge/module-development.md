---
title: Azure IoT Edge のモジュールを開発する | Microsoft Docs
description: ランタイムおよび IoT Hub と通信できる Azure IoT Edge 用のカスタム モジュールを開発する
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/10/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a30b4b056d56e096f80b9494ab80a585fff76e66
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "103489929"
---
# <a name="develop-your-own-iot-edge-modules"></a>独自の IoT Edge モジュールを開発する

[!INCLUDE [iot-edge-version-201806-or-202011](../../includes/iot-edge-version-201806-or-202011.md)]

Azure IoT Edge モジュールでは、他の Azure サービスに接続し、大規模なクラウド データ パイプラインに関与することができます。 この記事では、IoT Edge ランタイムと IoT Hub、したがって Azure クラウドの残りの部分とも通信するモジュールを開発する方法について説明します。

## <a name="iot-edge-runtime-environment"></a>IoT Edge ランタイム環境

IoT Edge ランタイムでは、複数のIoT Edge モジュールの機能を統合し、IoT Edge デバイス上に展開するためのインフラストラクチャが提供されます。 ユーザーは任意のプログラムを IoT Edge モジュールとしてパッケージ化することができます。 IoT Edge の通信機能や管理機能を活用するため、モジュールで実行されているプログラムでは、Azure IoT Device SDK を使用してローカルの IoT Edge ハブに接続できます。
::: moniker range=">=iotedge-2020-11"
モジュールでは、任意の MQTT クライアントを使用して、ローカルの IoT Edge ハブ MQTT ブローカーに接続することもできます。
::: moniker-end

### <a name="packaging-your-program-as-an-iot-edge-module"></a>IoT Edge モジュールとしてプログラムをパッケージ化する

IoT Edge デバイスにプログラムをデプロイするには、まず、Docker 互換エンジンで、コンテナー化して実行する必要があります。 IoT Edge では、Docker の背後にあるオープンソース プロジェクトである [Moby](https://github.com/moby/moby) が Docker 互換エンジンとして使用されます。 Docker で使用していた同じパラメーターを IoT Edge モジュールに渡すことができます。 詳細については、「[IoT Edge モジュールのコンテナー作成オプションを構成する方法](how-to-use-create-options.md)」を参照してください。

## <a name="using-the-iot-edge-hub"></a>IoT Edge ハブの使用

IoT Edge ハブでは、2 つの主要機能が提供されます。 IoT Hub へのプロキシと、ローカル通信です。

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>モジュールから IoT Edge ハブへの接続

モジュールからローカル IoT Edge ハブへの接続は、任意のクライアントの場合と同じ接続手順が必要です。 詳細については、[IoT Edge ハブ への接続](iot-edge-runtime.md#connecting-to-the-iot-edge-hub)に関するページを参照してください。

AMQP または MQTT 経由の IoT Edge ルーティングを使用するには、Azure IoT SDK の ModuleClient を使用できます。 ModuleClient インスタンスを作成し、デバイスで実行されている IoT Edge ハブにモジュールを接続します。これは、DeviceClient インスタンスで IoT デバイスを IoT Hub に接続する方法と似ています。 ModuleClient クラスとその通信方法に関する詳細については、優先する SDK 言語 ([C#](/dotnet/api/microsoft.azure.devices.client.moduleclient)、[C](/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、[Python](/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient)、[Java](/java/api/com.microsoft.azure.sdk.iot.device.moduleclient)、または [Node.js](/javascript/api/azure-iot-device/moduleclient)) の API リファレンスを参照してください。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

IoT Edge MQTT ブローカーを使用するには、独自の MQTT クライアントを持ち込み、IoT Edge デーモン ワークロード API から取得した情報を使用して、自身で接続を開始する必要があります。 <!--Need to add details here-->

MQTT ブローカーによるルーティングまたは発行/サブスクライブの選択の詳細については、「[ローカル通信](iot-edge-runtime.md#local-communication)」を参照してください。

### <a name="mqtt-broker-primitives"></a>MQTT ブローカー プリミティブ

#### <a name="send-a-message-on-a-user-defined-topic"></a>ユーザー定義のトピックのメッセージを送信する

IoT Edge MQTT ブローカーを使用すると、任意のユーザー定義のトピックのメッセージを発行できます。 これを行うには、モジュールが特定のトピックで発行することを認可してから、MQTT ブローカーへの接続時にパスワードとして使用するトークンをワークロード API から取得して、最後に任意の MQTT クライアントで認可されたトピックのメッセージを発行します。

#### <a name="receive-messages-on-a-user-defined-topic"></a>ユーザー定義のトピックのメッセージを受信する

IoT Edge MQTT ブローカーを使用した場合の、メッセージの受信も同様です。 まず、モジュールが特定のトピックをサブスクライブするように認可されていることを確認してから、MQTT ブローカーへの接続時にパスワードとして使用するトークンをワークロード API から取得して、最後に任意の MQTT クライアントで認可されたトピックのメッセージをサブスクライブします。

::: moniker-end

### <a name="iot-hub-primitives"></a>IoT Hub プリミティブ

IoT Hub では、次のような意味で、モジュール インスタンスがデバイスのようなものとして見なされます。

* [device-to-cloud メッセージ](../iot-hub/iot-hub-devguide-messaging.md)を送信できる。
* その ID で明確に対象とされた[ダイレクト メソッド](../iot-hub/iot-hub-devguide-direct-methods.md)を受け取ることができる。
* [デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)、およびそのデバイスの他のモジュール ツインとは区別されて分離された、モジュール ツインがある。

現在のところ、モジュールでは、クラウドからデバイスへのメッセージを受信したり、ファイルのアップロード機能を使用することはできません。

モジュールを作成するときに、デバイス アプリケーションで IoT Hub を使用する場合と同様に、IoT Edge ハブに接続し、IoT Hub プリミティブを使用することができます。 IoT Edge モジュールと IoT デバイス アプリケーションの唯一の違いは、デバイス ID の代わりにモジュール ID を参照する必要があることです。

#### <a name="device-to-cloud-messages"></a>デバイスからクラウドへのメッセージ

IoT Edge モジュールにより、ローカル ブローカーとして機能し、メッセージをクラウドに伝達する IoT Edge ハブ経由で、クラウドにメッセージを送信できます。 デバイスからクラウドへのメッセージの複雑な処理を可能にするために、IoT Edge モジュールでは、他のモジュールまたはデバイスからそのローカルの IoT Edge ハブに送信されたメッセージをインターセプトして処理し、処理済みのデータを含む新しいメッセージを送信することもできます。 そのため、IoT Edge モジュールのチェーンを作成して、ローカル処理パイプラインを構築できます。

ルーティングを使用してデバイスからクラウドへのテレメトリ メッセージを送信するには、Azure IoT SDK の ModuleClient を使用します。 Azure IoT SDK では、各モジュールにモジュール *入力* および *出力* エンドポイントの概念があり、それらは特別な MQTT トピックにマップされます。 `ModuleClient.sendMessageAsync` メソッドを使用すると、モジュールの出力エンドポイントでメッセージが送信されます。 次に、edgeHub でこの出力エンドポイントを IoT Hub に送信するルートを構成します。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT ブローカーでデバイスからクラウドへのテレメトリ メッセージを送信することは、ユーザー定義のトピックのメッセージの発行と似ていますが、モジュールの IoT Hub の特別なトピック `devices/<device_name>/<module_name>/messages/events` を使用します。 認可を適切に設定する必要があります。 このトピックのメッセージをクラウドに転送するように、MQTT ブリッジを構成する必要もあります。

::: moniker-end

ルーティングを使用してメッセージを処理するには、まず、別のエンドポイント (モジュールまたはデバイス) からの受信メッセージを、モジュールの入力エンドポイントに送信するルートを設定し、次にモジュールの入力エンドポイントでメッセージをリッスンします。 新しいメッセージが返されるたびに、Azure IoT SDK によってコールバック関数がトリガーされます。 このコールバック関数を使用してメッセージを処理し、必要に応じて、モジュール エンドポイント キューで新しいメッセージを送信します。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT ブローカーを使用してメッセージを処理することは、ユーザー定義のトピックのメッセージをサブスクライブすることと似ていますが、モジュールの出力キューの IoT Edge の特別なトピック `devices/<device_name>/<module_name>/messages/events` を使用します。 認可を適切に設定する必要があります。 必要に応じて、任意のトピックの新しいメッセージを送信できます。

::: moniker-end

#### <a name="twins"></a>ツイン

ツインは、IoT Hub によって提供されるプリミティブの 1 つです。 メタデータ、構成、条件などの状態情報を格納する JSON ドキュメントがあります。 各モジュールやデバイスには、独自のツインがあります。

Azure IoT SDK でモジュール ツインを取得するには、`ModuleClient.getTwin` メソッドを呼び出します。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT クライアントでモジュール ツインを取得するには、ツインの取得が一般的な MQTT パターンではないため、少し手間が伴います。 モジュールで、まず IoT Hub の特別なトピック `$iothub/twin/res/#` をサブスクライブする必要があります。 このトピック名は IoT Hub から継承され、すべてのデバイス/モジュールで同じトピックをサブスクライブする必要があります。 それは、デバイスが相互のツインを受け取るという意味ではありません。 IoT Hub と edgeHub では、すべてのデバイスで同じトピック名がリッスンされている場合でも、どのツインがどこに配信されるべきかを認識しています。 サブスクリプションが行われたら、モジュールでは、要求 ID を持つ IoT Hub の特別なトピック `$iothub/twin/GET/?$rid=1234` にメッセージを発行することによって、ツインを要求する必要があります。 この要求 ID は任意の ID (つまり、GUID) であり、IoT Hub によって、要求されたデータと共に返送されます。 これは、クライアントが要求を応答とペアにできる方法です。 結果コードは HTTP に似た状態コードであり、成功が 200 としてエンコードされます。

::: moniker-end

Azure IoT SDK によってモジュール ツイン パッチを受け取るには、コールバック関数を実装し、Azure IoT SDK の `ModuleClient.moduleTwinCallback` メソッドによってそれを登録します。これにより、ツイン パッチが提供されるたびにコールバック関数がトリガーされるようになります。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

任意の MQTT クライアントでモジュール ツイン パッチを受け取る場合、このプロセスは完全なツインの受け取りとよく似ています。クライアントでは、特別な IoT Hub のトピック `$iothub/twin/PATCH/properties/desired/#` をサブスクライブする必要があります。 サブスクリプションが行われた後に、IoT Hub によってツインの目的のセクションの変更が送信されると、クライアントがそれを受信します。

::: moniker-end

#### <a name="receive-direct-methods"></a>ダイレクト メソッドを受信する

Azure IoT SDK でダイレクト メソッドを受信するには、コールバック関数を実装し、Azure IoT SDK の `ModuleClient.methodCallback` メソッドによってそれを登録します。これにより、ダイレクト メソッドが提供されるたびにコールバック関数がトリガーされるようになります。

<!-- <1.2> -->
::: moniker range=">=iotedge-2020-11"

MQTT クライアントでダイレクト メソッドを受信する場合、プロセスはツイン パッチの受け取りとよく似ています。 クライアントでは、呼び出しを受信したことの確認を返す必要があり、同時にいくつかの情報を返信できます。 サブスクライブする特別な IoT Hub トピックは `$iothub/methods/POST/#` です。

::: moniker-end

## <a name="language-and-architecture-support"></a>言語とアーキテクチャのサポート

IoT Edge では複数のオペレーティング システム、デバイス アーキテクチャ、および開発言語をサポートしているため、ニーズに合うシナリオを構築できます。 ここでは、カスタム IoT Edge モジュールを開発する際の選択肢について説明します。 言語ごとのツールのサポートと要件の詳細については、「[IoT Edge のための開発およびテスト環境の準備](development-environment.md)」を参照してください。

### <a name="linux"></a>Linux

次の表のすべての言語について、IoT Edge では AMD64 および ARM32 Linux デバイスの開発をサポートしています。

| 開発言語 | 開発ツール |
| -------------------- | ----------------- |
| C | Visual Studio Code<br>Visual Studio 2017/2019 |
| C# | Visual Studio Code<br>Visual Studio 2017/2019 |
| Java | Visual Studio Code |
| Node.js | Visual Studio Code |
| Python | Visual Studio Code |

>[!NOTE]
>Linux ARM64 デバイスの開発とデバッグは、[パブリック プレビュー](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)でサポートされています。 詳細については、「[Visual Studio Code で ARM64 IoT Edge モジュールを開発してデバッグする (プレビュー)](https://devblogs.microsoft.com/iotdev/develop-and-debug-arm64-iot-edge-modules-in-visual-studio-code-preview)」を参照してください。

### <a name="windows"></a>Windows

次の表のすべての言語について、IoT Edge では AMD64 Windows デバイスの開発がサポートされています。

| 開発言語 | 開発ツール |
| -------------------- | ----------------- |
| C | Visual Studio 2017/2019 |
| C# | Visual Studio Code (デバッグ機能なし)<br>Visual Studio 2017/2019 |

## <a name="next-steps"></a>次のステップ

[IoT Edge のための開発およびテスト環境の準備](development-environment.md)

[Visual Studio を使用して IoT Edge 用の C# モジュールを開発する](how-to-visual-studio-develop-module.md)

[Visual Studio Code を使用して IoT Edge 用のモジュールを開発する](how-to-vs-code-develop-module.md)

[Azure IoT Hub SDK の概要と使用方法](../iot-hub/iot-hub-devguide-sdks.md)
