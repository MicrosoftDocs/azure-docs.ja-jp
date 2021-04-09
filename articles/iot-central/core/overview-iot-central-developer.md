---
title: Azure IoT Central 用デバイスの開発 | Microsoft Docs
description: Azure IoT Central は、IoT ソリューションの作成を簡単にする IoT アプリケーション プラットフォームです。 この記事では、IoT Central アプリケーションに接続するデバイスの開発について概要を説明します。 デバイスは、テレメトリを使用してストリーミング データとプロパティを送信することにより、その状態をレポートします。 IoT Central は、書き込み可能なプロパティを使用してデバイスの状態を設定したり、デバイスに対するコマンドを呼び出したりすることができます。
author: dominicbetts
ms.author: dobett
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.custom:
- mvc
- device-developer
ms.openlocfilehash: f69bbecfc2acc24cd63b87212197342b28723a9f
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/04/2021
ms.locfileid: "102043101"
---
# <a name="iot-central-device-development-guide"></a>IoT Central デバイスの開発ガイド

"*この記事は、デバイス開発者を対象としています。* "

IoT Central アプリケーションを使用すると、数百万台ものデバイスを、そのライフ サイクル全体にわたって監視および管理することができます。 このガイドは、IoT Central に接続されたデバイスで実行されるコードを実装するデバイス開発者を対象としています。

デバイスは、次のプリミティブを使用して IoT Central と対話します。

- "_テレメトリ_": デバイスが IoT Central に送信するデータ。 たとえば、オンボード センサーから送られてくる温度の値のストリーム。
- "_プロパティ_": デバイスが IoT Central に報告する状態の値。 たとえば、デバイスのファームウェアの現在のバージョン。 さらに、IoT Central がデバイスで更新できる書き込み可能なプロパティも使用できます (目標温度など)。
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

詳細については、「[Azure IoT Central に接続する](./concepts-get-connected.md)」および[ベスト プラクティス](concepts-best-practices.md)に関するページを参照してください。

### <a name="security"></a>Security

デバイスと IoT Central アプリケーションの間の接続は、[共有アクセス署名](./concepts-get-connected.md#sas-group-enrollment)または業界標準の [X.509 証明書](./concepts-get-connected.md#x509-group-enrollment)を使用してセキュリティ保護されます。

### <a name="communication-protocols"></a>通信プロトコル

デバイスが IoT Central に接続するために使用できる通信プロトコルとしては、MQTT、AMQP、HTTPS があります。 内部的には、IoT Central では、IoT ハブが使用され、デバイスとの接続が有効になります。 IoT Hub でデバイス接続のためにサポートされている通信プロトコルの詳細については、[通信プロトコルの選択](../../iot-hub/iot-hub-devguide-protocols.md)に関するページを参照してください。

## <a name="implement-the-device"></a>デバイスを実装する

IoT Central デバイス テンプレートには、その種類のデバイスで実装されるべき動作を指定する "_モデル_" が含まれています。 動作には、テレメトリ、プロパティ、コマンドが含まれます。

> [!TIP]
> モデルは、[Digital Twins Definition Language (DTDL) v2](https://github.com/Azure/opendigitaltwins-dtdl) JSON ファイルとして IoT Central からエクスポートできます。

各モデルには、一意の "_デバイス ツイン モデル識別子_" (DTMI) があります (例: `dtmi:com:example:Thermostat;1`)。 デバイスは、IoT Central に接続するときに、実装するモデルの DTMI を送信します。 これにより、IoT Central で正しいデバイス テンプレートをデバイスに関連付けることができます。

[IoT プラグ アンド プレイ](../../iot-pnp/overview-iot-plug-and-play.md)では、DTDL モデルの実装時にデバイスで遵守されるべき一連の規則を定義しています。

[Azure IoT device SDK](#languages-and-sdks) では、IoT プラグ アンド プレイ規則がサポートされています。

### <a name="device-model"></a>デバイスのモデル

デバイス モデルは、[DTDL](https://github.com/Azure/opendigitaltwins-dtdl) を使用して定義されます。 この言語では以下を定義できます。

- デバイスが送信するテレメトリ。 定義にはテレメトリの名前とデータ型が含まれます。 たとえば、デバイスは温度テレメトリを double として送信します。
- デバイスによって IoT Central にレポートされるプロパティ。 プロパティ定義には名前とデータ型が含まれます。 たとえば、デバイスはバルブの状態をブール値としてレポートします。
- デバイスが IoT Central から受け取ることができるプロパティ。 必要に応じて、プロパティを書き込み可能としてマークできます。 たとえば、IoT Central は目標温度を double としてデバイスに送信します。
- デバイスが応答するコマンド。 定義には、コマンドの名前、およびパラメーターの名前とデータ型が含まれます。 たとえば、デバイスは、再起動するまでの待機時間 (秒数) を指定した再起動コマンドに応答します。

DTDL モデルは、"_コンポーネントなし_" または "_複数コンポーネント_" のモデルにすることができます。

- コンポーネントなしモデル: 単純なモデルでは、埋め込みコンポーネントまたはカスケードされたコンポーネントは使用されません。 すべてのテレメトリ、プロパティ、コマンドは、1 つの "_既定のコンポーネント_" として定義されます。 例については、[Thermostat](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/Thermostat.json) モデルを参照してください。
- 複数コンポーネント モデル: 2 つ以上のコンポーネントを含むより複雑なモデル。 これらのコンポーネントには、1 つの既定のコンポーネントと、入れ子になった 1 つ以上の追加コンポーネントが含まれます。 例については、[Temperature Controller](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/samples/TemperatureController.json) モデルを参照してください。

詳細については、「[モデル内の IoT プラグ アンド プレイ コンポーネント](../../iot-pnp/concepts-components.md)」を参照してください。

### <a name="conventions"></a>規約

デバイスは、IoT Central とデータを交換するときに、IoT プラグ アンド プレイ規則に従う必要があります。 規則は次のとおりです。

- IoT Central に接続するときに DTMI を送信する。
- 正しく書式設定された JSON ペイロードとメタデータを IoT Central に送信する。
- IoT Central からの書き込み可能なプロパティとコマンドに正しく応答する。
- コンポーネントのコマンドの名前付け規則に従う。

> [!NOTE]
> 現在、IoT Central では、DTDL の **Array** および **Geospatial** データ型は完全にはサポートされていません。

デバイスが IoT Central と交換する JSON メッセージの形式の詳細については、「[テレメトリ、プロパティ、およびコマンドのペイロード](concepts-telemetry-properties-commands.md)」を参照してください。

IoT プラグ アンド プレイ規則の詳細については、「[IoT プラグ アンド プレイ規則](../../iot-pnp/concepts-convention.md)」を参照してください。

### <a name="device-sdks"></a>デバイスの SDK

デバイスの動作を実装するには、[Azure IoT device SDK](#languages-and-sdks) のいずれかを使用します。 コードでは、次の処理を行う必要があります。

- デバイスを DPS に登録し、DPS からの情報を使用して、お使いの IoT Central アプリケーション内の内部 IoT ハブに接続します。
- デバイスで実装されるモデルの DTMI を通知します。
- デバイス モデルで指定された形式でテレメトリを送信します。 IoT Central では、デバイス テンプレート内のモデルを使用して、視覚化と分析にテレメトリを使用する方法を決定します。
- デバイスと IoT Central の間でプロパティ値を同期させます。 モデルでは、IoT Central が情報を表示できるように、プロパティ名とデータ型を指定します。
- モデルで指定されたコマンドのコマンド ハンドラーを実装します。 モデルでは、デバイスで使用する必要があるコマンド名とパラメーターを指定します。

デバイス テンプレートの役割の詳細については、「[デバイス テンプレートとは](./concepts-device-templates.md)」を参照してください。

サンプル コードについては、[クライアント アプリケーションの作成と接続](./tutorial-connect-device.md)に関するページを参照してください。

### <a name="languages-and-sdks"></a>言語と SDK

サポートされる言語と SDK の詳細については、[Azure IoT Hub device SDK の概要と使用方法](../../iot-hub/iot-hub-devguide-sdks.md#azure-iot-hub-device-sdks)に関するページを参照してください。

## <a name="next-steps"></a>次のステップ

デバイス開発者がコードの内容を詳しく知りたい場合は、次の手順として、[クライアント アプリケーションを作成して Azure IoT Central アプリケーションに接続する](./tutorial-connect-device.md)ことをお勧めします。

IoT Central の詳細な使用方法については、次の手順として、クイックスタートの「[Azure IoT Central アプリケーションの作成](./quick-deploy-iot-central.md)」から始めることをお勧めします。
