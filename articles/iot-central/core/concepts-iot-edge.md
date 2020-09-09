---
title: Azure IoT Edge および Azure IoT Central | Microsoft Docs
description: IoT Central アプリケーションで Azure IoT Edge を使用する方法について説明します。
author: dominicbetts
ms.author: dobett
ms.date: 12/12/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: f8660cb18f5e7ac5f3695eb15475570b107bf25c
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/28/2020
ms.locfileid: "87337077"
---
# <a name="connect-azure-iot-edge-devices-to-an-azure-iot-central-application"></a>Azure IoT Edge デバイスを Azure IoT Central アプリケーションに接続する

"*この記事は、ソリューション ビルダーおよびデバイス開発者を対象としています。* "

IoT Edge は、次の 3 つのコンポーネントで構成されます。

* **IoT Edge モジュール**: Azure サービス、パートナー サービス、またはカスタム コードを実行するコンテナーです。 モジュールは、IoT Edge デバイスにデプロイされ、そのデバイスでローカルに実行されます。
* **IoT Edge ランタイム**: 個々の IoT Edge デバイス上で動作し、各デバイスにデプロイされたモジュールを管理します。
* **クラウドベースのインターフェイス**: IoT Edge デバイスをリモートから監視して管理します。 IoT Central がクラウド インターフェイスです。

**Azure IoT Edge** デバイスは、IoT Edge デバイスに接続するダウンストリーム デバイスがあるゲートウェイ デバイスにすることができます。 この記事では、ダウンストリーム デバイスの接続パターンに関する詳細情報を共有します。

**デバイス テンプレート**により、デバイスと IoT Edge のモジュールの機能が定義されます。 機能には、モジュールが送信するテレメトリ、モジュール プロパティ、モジュールが応答するコマンドが含まれます。

## <a name="downstream-device-relationships-with-a-gateway-and-modules"></a>ゲートウェイおよびモジュールとのダウンストリーム デバイスのリレーションシップ

ダウンストリーム デバイスは、`$edgeHub` モジュールを使用して IoT Edge ゲートウェイ デバイスに接続できます。 このシナリオでは、この IoT Edge デバイスは透過的なゲートウェイになります。

![透過的なゲートウェイの図](./media/concepts-iot-edge/gateway-transparent.png)

ダウンストリーム デバイスは、カスタム モジュールを使用して IoT Edge ゲートウェイ デバイスに接続することもできます。 次のシナリオでは、ダウンストリーム デバイスは Modbus カスタム モジュールを使用して接続します。

![カスタム モジュール接続の図](./media/concepts-iot-edge/gateway-module.png)

次の図は、両方の種類のモジュール (カスタムおよび `$edgeHub`) を使用した IoT Edge ゲートウェイ デバイスへの接続を示しています。  

![両方の接続モジュールを使用した接続の図](./media/concepts-iot-edge/gateway-module-transparent.png)

さらに、ダウンストリーム デバイスは、複数のカスタム モジュールを使用して IoT Edge ゲートウェイ デバイスに接続できます。 次の図は、Modbus カスタム モジュール、BLE カスタム モジュール、および `$edgeHub` モジュールを使用したダウンストリーム デバイス接続を示しています。 

![複数のカスタム モジュールを使用した接続の図](./media/concepts-iot-edge/gateway-module2-transparent.png)

## <a name="deployment-manifests-and-device-templates"></a>配置マニフェストとデバイス テンプレート

IoT Edge では、ビジネス ロジックをモジュールの形式でデプロイして管理できます。 IoT Edge モジュールは、IoT Edge によって管理される最小の計算単位であり、Azure Stream Analytics などの Azure サービスや独自のソリューション固有のコードを含めることができます。 モジュールがどのように開発、デプロイ、維持されるかについては、[IoT Edge モジュール](../../iot-edge/iot-edge-modules.md)に関する記事を参照してください。

大まかに言えば、配置マニフェストとは、必要なプロパティを使用して構成されたモジュール ツインのリストです。 インストールするモジュールとその構成方法は、配置マニフェストから IoT Edge デバイス (1 つまたは複数のデバイス) に伝えられます。 配置マニフェストには、各モジュール ツインの必要なプロパティが含まれています。 IoT Edge デバイスは、各モジュールの報告されたプロパティを返します。

Visual Studio Code を使用して配置マニフェストを作成します。 詳細については、「[Visual Studio Code 用の Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge)」を参照してください。

Azure IoT Central では、配置マニフェストをインポートしてデバイス テンプレートを作成できます。 次のフローチャートは、IoT Central での配置マニフェストのライフ サイクルを示しています。

![配置マニフェストのライフ サイクルのフローチャート](./media/concepts-iot-edge/dmflow.png)

IoT Central は、IoT Edge デバイスを次のようにモデル化します。

* すべての IoT Edge デバイス テンプレートに、デバイス機能モデルがあります。
* 配置マニフェストに一覧表示されているすべてのカスタム モジュールに対して、モジュール機能モデルが生成されます。
* 各モジュール機能モデルとデバイス機能モデルの間にリレーションシップが確立されます。
* モジュール機能モデルは、モジュール インターフェイスを実装します。
* 各モジュール インターフェイスには、テレメトリ、プロパティ、およびコマンドが含まれます。

![IoT Edge モデリングの図](./media/concepts-iot-edge/edgemodelling.png)

## <a name="iot-edge-gateway-devices"></a>IoT Edge ゲートウェイ デバイス

IoT Edge デバイスをゲートウェイ デバイスにするよう選択した場合は、ゲートウェイ デバイスに接続するデバイスのデバイス機能モデルにダウンストリーム リレーションシップを追加できます。

## <a name="next-steps"></a>次のステップ

デバイス開発者の場合は、次のステップとして [IoT Central のゲートウェイ デバイスの種類](./tutorial-define-gateway-device-type.md)について学習することをお勧めします。
