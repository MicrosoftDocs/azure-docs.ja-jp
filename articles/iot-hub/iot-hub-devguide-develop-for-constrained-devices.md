---
title: IoT Hub C SDK を使用した Azure IoT Hub の制約のあるデバイス向けの開発
description: 開発者ガイド - Azure IoT SDK を使用して制約のあるデバイス向けの開発を行う方法に関するガイダンス。
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 05/24/2018
ms.author: robinsh
ms.custom:
- amqp
- mqtt
ms.openlocfilehash: 5a43eb2537ebc09ffcb524a4426d7a8c9bec560b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96500004"
---
# <a name="develop-for-constrained-devices-using-azure-iot-c-sdk"></a>Azure IoT C SDK を使用した制約のあるデバイス向けの開発

Azure IoT Hub C SDK は ANSI C (C99) で記述されており、ディスクおよびメモリのフットプリントが小さいさまざまなプラットフォームを操作するのに最適です。 推奨されるメモリは 64 KB 以上ですが、正確なメモリ フットプリントは、使用するプロトコル、開かれる接続数、および対象とするプラットフォームに依存します。
> [!NOTE]
> * Azure IoT C SDK では、開発に役立つリソース消費量情報が定期的に発行されます。  Microsoft の [GitHub リポジトリ](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/c_sdk_resource_information.md)にアクセスし、最新のベンチマークを確認してください。
>

C SDK は apt-get、NuGet、および MBED からパッケージ形式で入手できます。 制約のあるデバイスを対象とするには、ターゲット プラットフォームに対して SDK をローカルにビルドすることをお勧めします。 このドキュメントでは、[cmake](https://cmake.org/) を使用することにより、特定の機能を削除して C SDK のフットプリントを縮小する方法を示します。 さらに、このドキュメントでは、制約のあるデバイスを操作するための最適なプログラミング モデルについても説明します。

## <a name="building-the-c-sdk-for-constrained-devices"></a>制約のあるデバイス向けの C SDK のビルド

制約のあるデバイス向けの C SDK をビルドします。

> [!NOTE]
> 埋め込み C SDK は、BYON (bring your own network) アプローチをサポートする、制約付きデバイスの代替手段です。 IoT 開発者はデバイス ソリューションを作成するために、任意の MQTT クライアント、TLS、ソケットを自由に使用できます。 [埋め込み C SDK の詳細については、こちらを参照してください](https://github.com/Azure/azure-sdk-for-c/tree/master/sdk/docs/iot)。

### <a name="prerequisites"></a>[前提条件]

こちらの [C SDK セットアップ ガイド](https://github.com/Azure/azure-iot-sdk-c/blob/master/doc/devbox_setup.md)に従って、C SDK をビルドするための開発環境を準備します。 cmake を使用したビルドの手順に進む前に、cmake フラグを呼び出して未使用の機能を削除することができます。

### <a name="remove-additional-protocol-libraries"></a>余分なプロトコル ライブラリを削除する

今日、C SDK では、MQTT、MQTT over WebSocket、AMQP、AMQP over WebSocket、HTTPS という 5 つのプロトコルがサポートされています。 ほとんどのシナリオでは、クライアント上で実行されているプロトコルのうちの 1 つ、2 つを必要とします。したがって、使用しないプロトコル ライブラリは SDK から削除することができます。 シナリオに適した通信プロトコルの選択方法の詳細については、[IoT Hub の通信プロトコルの選択](iot-hub-devguide-protocols.md)に関する記事を参照してください。 たとえば、MQTT は、制約のあるデバイスに適する場合が多い軽量なプロトコルです。

AMQP ライブラリと HTTP ライブラリは、次の cmake コマンドを使用して削除できます。

```
cmake -Duse_amqp=OFF -Duse_http=OFF <Path_to_cmake>
```

### <a name="remove-sdk-logging-capability"></a>SDK のログ機能を削除する

C SDK では、デバッグに役立つように、全体にわたって詳細なログを提供します。 運用環境のデバイス用のログ機能は、次の cmake コマンドを使用して削除することができます。

```
cmake -Dno_logging=OFF <Path_to_cmake>
```

### <a name="remove-upload-to-blob-capability"></a>BLOB へのアップロード機能を削除する

SDK の組み込み機能を使用することにより、大きなファイルを Azure Storage にアップロードすることができます。 Azure IoT Hub は、関連付けられた Azure Storage アカウントへのディスパッチャーとして機能します。 この機能を使用することで、メディア ファイル、大きなテレメトリ バッチ、およびログを送信することができます。 詳細情報については、「[IoT Hub を使用したファイルのアップロード](iot-hub-devguide-file-upload.md)」を参照してください。 この機能は、アプリケーションで必要ない場合、次の cmake コマンドを使用して削除することができます。

```
cmake -Ddont_use_uploadtoblob=ON <Path_to_cmake>
```

### <a name="running-strip-on-linux-environment"></a>Linux 環境でのストリップの実行

バイナリが Linux システム上で実行されている場合は、[strip コマンド](https://en.wikipedia.org/wiki/Strip_(Unix))を利用して、コンパイル後の最終的なアプリケーションのサイズを縮小することができます。

```
strip -s <Path_to_executable>
```

## <a name="programming-models-for-constrained-devices"></a>制約のあるデバイス用のプログラミング モデル

次に、制約のあるデバイス用のプログラミング モデルを調べます。

### <a name="avoid-using-the-serializer"></a>シリアライザーの使用を回避する

C SDK にはオプションとして [C SDK シリアライザー](https://github.com/Azure/azure-iot-sdk-c/tree/master/serializer)があります。このオプションでは、宣言型マッピング テーブルを使用して、メソッドとデバイス ツイン プロパティを定義することができます。 シリアライザーは開発の簡略化を目的として設計されていますが、オーバーヘッドが追加されるため、制約のあるデバイスに最適ではありません。 この場合は、プリミティブ クライアント API の使用を検討し、さらに [parson](https://github.com/kgabis/parson) などの軽量のパーサーを使用して JSON を解析します。

### <a name="use-the-lower-layer-_ll_"></a>下位のレイヤーを使用する (_LL_)

C SDK では 2 つのプログラミング モデルがサポートされています。 一方のセットには、下位レイヤーを意味する挿入辞 _LL_ を伴った API が含まれています。 この API セットは軽量であり、ワーカー スレッドを起動しません。このことは、ユーザーがスケジュール設定を手動で制御する必要があることを意味します。 たとえば、デバイス クライアント用の _LL_ API はこちらの [ヘッダー ファイル](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client_ll.h)で確認することができます。 

_LL_ インデックスを伴っていない、もう一方の API セットはコンビニエンス レイヤーと呼ばれています。この場合、ワーカー スレッドは自動的に起動します。 たとえば、デバイス クライアント用の便利なレイヤーの API はこちらの[IoT Device Client ヘッダー ファイル](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/inc/iothub_device_client.h)で確認できます。 余分な各スレッドによってシステム リソースの大部分が占有される可能性がある制約付きのデバイスの場合は、_LL_ API の使用を検討してください。

## <a name="next-steps"></a>次の手順

Azure IoT C SDK アーキテクチャの詳細について説明します。
-    [Azure IoT C SDK ソース コード](https://github.com/Azure/azure-iot-sdk-c/)
-    [Azure IoT device SDK for C の概要](iot-hub-device-sdk-c-intro.md)
