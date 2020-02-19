---
title: Azure IoT Edge のモジュールを開発する | Microsoft Docs
description: ランタイムおよび IoT Hub と通信できる Azure IoT Edge 用のカスタム モジュールを開発する
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 96bd6b461a5374b5f5bc578c5f58dbcd09cd7087
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/23/2020
ms.locfileid: "76548630"
---
# <a name="develop-your-own-iot-edge-modules"></a>独自の IoT Edge モジュールを開発する

Azure IoT Edge モジュールでは、他の Azure サービスに接続し、大規模なクラウド データ パイプラインに関与することができます。 この記事では、IoT Edge ランタイムと IoT Hub、したがって Azure クラウドの残りの部分とも通信するモジュールを開発する方法について説明します。

## <a name="iot-edge-runtime-environment"></a>IoT Edge ランタイム環境

IoT Edge ランタイムでは、複数のIoT Edge モジュールの機能を統合し、IoT Edge デバイス上に展開するためのインフラストラクチャが提供されます。 ユーザーは任意のプログラムを IoT Edge モジュールとしてパッケージ化することができます。 IoT Edge の通信機能や管理機能を活用するため、モジュールで実行されているプログラムでは、Azure IoT Device SDK を使用してローカルの IoT Edge ハブに接続できます。

## <a name="using-the-iot-edge-hub"></a>IoT Edge ハブの使用

IoT Edge ハブでは、2 つの主要機能が提供されます。 IoT Hub へのプロキシと、ローカル通信です。

### <a name="iot-hub-primitives"></a>IoT Hub プリミティブ

IoT Hub では、次のような意味で、モジュール インスタンスがデバイスのようなものとして見なされます。

* [デバイス ツイン](../iot-hub/iot-hub-devguide-device-twins.md)、およびそのデバイスの他のモジュール ツインとは区別されて分離された、モジュール ツインがある。
* [device-to-cloud メッセージ](../iot-hub/iot-hub-devguide-messaging.md)を送信できる。
* その ID で明確に対象とされた[ダイレクト メソッド](../iot-hub/iot-hub-devguide-direct-methods.md)を受け取ることができる。

現在のところ、モジュールでは、クラウドからデバイスへのメッセージを受信したり、ファイルのアップロード機能を使用することはできません。

モジュールを作成するときに、デバイス アプリケーションで IoT Hub を使用する場合と同様に、[Azure IoT Device SDK](../iot-hub/iot-hub-devguide-sdks.md) 使用して IoT Edge ハブに接続し、上記の機能を使用することができます。 IoT Edge モジュールと IoT デバイス アプリケーションの唯一の違いは、デバイス ID の代わりにモジュール ID を参照する必要があることです。

### <a name="device-to-cloud-messages"></a>デバイスからクラウドへのメッセージ

デバイスからクラウドへのメッセージの複雑な処理を可能にするため、IoT Edge ハブでは、モジュール間、およびモジュールと IoT Hub 間での、メッセージの宣言型ルーティングが提供されます。 宣言型ルーティングによって、モジュールは他のモジュールによって送信されたメッセージをインターセプトして処理し、それらを複雑なパイプラインに伝達できるようになります。 詳細については、[モジュールのデプロイと IoT Edge へのルートの確立](module-composition.md)に関する記事をご覧ください。

通常の IoT Hub デバイス アプリケーションとは違い、IoT Edge モジュールでは、ローカルの IoT Edge ハブによってプロキシされている、デバイスからクラウドへのメッセージを受信し、それらを処理することができます。

IoT Edge ハブでは、[配置マニフェスト](module-composition.md)に関するページで説明されている宣言型のルートに基づいて、メッセージをモジュールに伝達します。 IoT Edge モジュールの開発時には、メッセージ ハンドラーを設定してこれらのメッセージを受信できます。

ルートの作成を簡略化するため、IoT Edge では、モジュールの*入力* エンドポイントと*出力* エンドポイントの概念が追加されています。 モジュールは、自身にルーティングされたすべての D2C メッセージ (デバイスからクラウドへのメッセージ) を、入力を指定することなく受信したり、出力を指定することなく、D2C メッセージ送信することができます。 ただし、明示的な入力と出力を使用したほうが、ルーティング ルールがわかりやすくなります。

最後に、Edge ハブによって処理された D2C メッセージには、次のシステム プロパティを使用してスタンプが付けられます。

| プロパティ | 説明 |
| -------- | ----------- |
| $connectionDeviceId | メッセージを送信したクライアントのデバイス ID |
| $connectionModuleId | メッセージを送信したモジュールのモジュール ID |
| $inputName | このメッセージを受信した入力。 空の場合もあります。 |
| $outputName | メッセージを送信するために使用された出力。 空の場合もあります。 |

### <a name="connecting-to-iot-edge-hub-from-a-module"></a>モジュールから IoT Edge ハブへの接続

モジュールからローカル IoT Edge ハブに接続するには、2 つの手順が必要です。

1. アプリケーションで ModuleClient インスタンスを作成します。
2. そのデバイスの IoT Edge ハブによって提示された証明書がアプリケーションによって受け入れられることを確認します。

ModuleClient インスタンスを作成し、デバイスで実行されている IoT Edge ハブにモジュールを接続します。これは、DeviceClient インスタンスで IoT デバイスを IoT Hub に接続する方法と似ています。 ModuleClient クラスとその通信方法に関する詳細については、優先する SDK 言語 ([C#](https://docs.microsoft.com/dotnet/api/microsoft.azure.devices.client.moduleclient?view=azure-dotnet)、[C](https://docs.microsoft.com/azure/iot-hub/iot-c-sdk-ref/iothub-module-client-h)、[Python](https://docs.microsoft.com/python/api/azure-iot-device/azure.iot.device.iothubmoduleclient?view=azure-python)、[Java](https://docs.microsoft.com/java/api/com.microsoft.azure.sdk.iot.device.moduleclient?view=azure-java-stable)、または [Node.js](https://docs.microsoft.com/javascript/api/azure-iot-device/moduleclient?view=azure-node-latest)) の API リファレンスを参照してください。

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
