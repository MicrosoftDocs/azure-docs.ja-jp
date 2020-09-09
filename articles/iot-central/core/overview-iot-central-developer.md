---
title: Azure IoT Central 用デバイスの開発 | Microsoft Docs
description: Azure IoT Central は、IoT ソリューションの作成を簡単にする IoT アプリケーション プラットフォームです。 この記事では、IoT Central アプリケーションに接続するデバイスの開発について概要を説明します。
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: overview
ms.service: iot-central
services: iot-central
ms.custom: mvc
ms.openlocfilehash: aa442e15dbc95709ecf3c818f69301d2f02e9b5b
ms.sourcegitcommit: 8e5b4e2207daee21a60e6581528401a96bfd3184
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/04/2020
ms.locfileid: "84417023"
---
# <a name="iot-central-device-development-overview"></a>IoT Central デバイスの開発の概要

"*この記事は、デバイス開発者を対象としています。* "

IoT Central アプリケーションを使用すると、数百万台ものデバイスを、そのライフ サイクル全体にわたって監視および管理することができます。 この概要は、IoT Central に接続されたデバイスで実行されるコードを実装するデバイス開発者を対象としています。

デバイスは、次のプリミティブを使用して IoT Central と対話します。

- "_テレメトリ_": デバイスが IoT Central に送信するデータ。 たとえば、オンボード センサーから送られてくる温度の値のストリーム。
- "_プロパティ_": デバイスが IoT Central に報告する状態の値。 たとえば、デバイスのファームウェアの現在のバージョン。 さらに、IoT Central がデバイスで更新できる書き込み可能なプロパティも使用できます。
- "_コマンド_": デバイスの動作を制御するために IoT Central から呼び出されます。 たとえば、IoT Central アプリケーションは、デバイスを再起動するためにコマンドを呼び出す場合があります。

ソリューション ビルダーは、テレメトリの視覚化、プロパティの管理、コマンドの呼び出しを行うために、IoT Central の Web UI でダッシュボードとビューを構成する役割を担います。

## <a name="types-of-device"></a>デバイスの種類

以降のセクションでは、IoT Central アプリケーションに接続可能なデバイスの主な種類について説明します。

### <a name="standalone-device"></a>スタンドアロン デバイス

スタンドアロン デバイスは、IoT Central に直接接続します。 通常、スタンドアロン デバイスは、そのオンボード センサーまたは接続されているセンサーから IoT Central アプリケーションにテレメトリを送信します。 また、スタンドアロン デバイスは、プロパティ値の報告、書き込み可能なプロパティ値の受信、コマンドへの応答を行うこともできます。

### <a name="gateway-device"></a>ゲートウェイ デバイス

ゲートウェイ デバイスは、IoT Central アプリケーションに接続されている 1 台以上のダウンストリーム デバイスを管理します。 ダウンストリーム デバイスとゲートウェイ デバイスの関係を構成するには、IoT Central を使用します。 詳細については、「[Azure IoT Central アプリケーションで新しい種類の IoT ゲートウェイ デバイスを定義する](./tutorial-define-gateway-device-type.md)」を参照してください。

### <a name="edge-device"></a>エッジ デバイス

エッジ デバイスは、IoT Central に直接接続しますが、"_リーフ デバイス_" と呼ばれる他のデバイスの仲介役として機能します。 通常、エッジ デバイスは、それが仲介役となるリーフ デバイスの近くに配置されます。 エッジ デバイスを使用するシナリオには、次のようなものがあります。

- IoT Central に直接接続できないデバイスを、エッジ デバイスを介して接続できるようにする: たとえば、リーフ デバイスが Bluetooth を使用してエッジ デバイスに接続し、エッジ デバイスがインターネット経由で IoT Central に接続することができます。
- テレメトリを集約してから IoT Central に送信する: この方法は、データを IoT Central に送信するコストの削減に役立ちます。
- インターネットを介して IoT Central に接続する場合に生じる待ち時間を回避するために、リーフ デバイスをローカルで制御する:

エッジ デバイスは、自身のテレメトリの送信、そのプロパティの報告、書き込み可能なプロパティの更新やコマンドへの応答も行うことができます。

IoT Central では、エッジ デバイスに接続されているリーフ デバイスではなく、エッジ デバイスのみが認識されます。

詳細については、[Azure IoT Edge デバイスを Azure IoT Central アプリケーションに追加する](./tutorial-add-edge-as-leaf-device.md)に関するページを参照してください。

## <a name="connect-a-device"></a>デバイスの接続

Azure IoT Central では、すべてのデバイスの登録と接続の管理に [Azure IoT Hub Device Provisioning Service (DPS)](../../iot-dps/about-iot-dps.md) が使用されます。

DPS を使用すると、次のことが可能になります。

- IoT Central で、大量のデバイスのオンボーディングと接続をサポートできます。
- デバイスの資格情報を生成し、デバイスをオフラインで構成できます。IoT Central UI を通じてデバイスを登録する必要はありません。
- 独自のデバイス ID を使用して、IoT Central にデバイスを登録できます。 独自のデバイス ID を使用して、既存のバックオフィス システムとの統合を簡略化できます。
- 1 つの一貫した方法で IoT Central にデバイスを接続できます。

詳細については、「[Azure IoT Central に接続する](./concepts-get-connected.md)」を参照してください。

### <a name="security"></a>Security

デバイスと IoT Central アプリケーションの間の接続は、[共有アクセス署名](./concepts-get-connected.md#connect-devices-at-scale-using-sas)または業界標準の [X.509 証明書](./concepts-get-connected.md#connect-devices-using-x509-certificates)を使用してセキュリティ保護されます。

### <a name="communication-protocols"></a>通信プロトコル

デバイスが IoT Central に接続するために使用できる通信プロトコルとしては、MQTT、AMQP、HTTPS があります。 内部的には、IoT Central では、IoT ハブが使用され、デバイスとの接続が有効になります。 IoT Hub でデバイス接続のためにサポートされている通信プロトコルの詳細については、[通信プロトコルの選択](../../iot-hub/iot-hub-devguide-protocols.md)に関するページを参照してください。

## <a name="implement-the-device"></a>デバイスを実装する

デバイスの動作を実装するには、[Azure IoT device SDK](#languages-and-sdks) のいずれかを使用します。 コードでは、次の処理を行う必要があります。

- デバイスを DPS に登録し、DPS からの情報を使用して、お使いの IoT Central アプリケーション内の内部 IoT ハブに接続します。
- IoT Central 内のデバイス テンプレートで指定される形式でテレメトリを送信します。 IoT Central では、デバイス テンプレートを使用して、視覚化と分析のためのテレメトリの使用方法を決定します。
- デバイスと IoT Central の間でプロパティ値を同期させます。 デバイス テンプレートでは、IoT Central が情報を表示できるように、プロパティ名とデータの種類を指定します。
- デバイス テンプレートで指定するコマンドのコマンド ハンドラーを実装します。 デバイス テンプレートでは、デバイスで使用する必要があるコマンド名とパラメーターを指定します。

デバイス テンプレートの役割の詳細については、「[デバイス テンプレートとは](./concepts-device-templates.md)」を参照してください。

サンプル コードについては、[Node.js クライアント アプリケーションの作成と接続](./tutorial-connect-device-nodejs.md)に関するページ、または[Python クライアント アプリケーションの作成と接続](./tutorial-connect-device-python.md)に関するページを参照してください。

### <a name="languages-and-sdks"></a>言語と SDK

サポートされる言語と SDK の詳細については、[Azure IoT Hub device SDK の概要と使用方法](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

デバイス開発者がコードの内容を詳しく知りたい場合は、次の手順として、[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](./tutorial-connect-device-nodejs.md)ことをお勧めします。

IoT Central の詳細な使用方法については、次の手順として、クイックスタートの「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」から始めることをお勧めします。
