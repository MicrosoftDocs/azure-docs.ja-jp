---
title: Azure IoT デバイス開発者向けの接続オプションについて説明します
description: Azure IoT デバイス開発者向けの一般的に使用されているデバイス接続オプションとツールについて説明します。
author: timlt
ms.author: timlt
ms.service: iot-develop
ms.topic: conceptual
ms.date: 02/11/2021
ms.openlocfilehash: 6bbd7d37418af68065daa194d4ff4bd80f6fd09c
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 02/18/2021
ms.locfileid: "100656384"
---
# <a name="overview-connection-options-for-azure-iot-device-developers"></a>概要: Azure IoT デバイス開発者向けの接続オプション
デバイスを扱う開発者向けに、Azure IoT デバイスの接続と管理のためのオプションがいくつか用意されています。 この記事では、デバイスの接続と管理に役立つ、最も一般的に使用されているオプションとツールの概要を示します。

デバイスの Azure IoT 接続オプションを評価する際には、次の項目を比較することをお勧めします。
- Azure IoT デバイス アプリケーション プラットフォーム
- デバイスの接続と管理のためのツール

## <a name="application-platforms-iot-central-and-iot-hub"></a>アプリケーション プラットフォーム: IoT Central と IoT Hub
Azure IoT には、デバイス対応クラウド アプリケーション用のプラットフォームである Azure IoT Central と Azure IoT Hub という 2 つのサービスが含まれています。 どちらを使用しても、IoT アプリケーションをホストし、デバイスを接続できます。
- [IoT Central](../iot-central/core/overview-iot-central.md) は、IoT ソリューションの使用に伴う複雑さとコストを削減するように設計されています。 これは、サービスとしてのソフトウェア (SaaS) アプリケーションであり、IoT アプリケーションをホストするための完全なプラットフォームを提供します。 IoT Central Web UI を使用すると、IoT アプリケーションの作成と管理のライフサイクル全体を効率化できます。 この Web UI を使用すると、アプリケーションを作成し、数台から数百万台ものデバイスを接続して管理するタスクが簡略化されます。 IoT Central は IoT Hub を使用してアプリケーションを作成および管理しますが、その詳細はユーザーには見えないようになっています。 一般に、IoT Central を使用すると、複雑さと開発の労力が軽減され、Web UI を通じてデバイスの管理が簡素化されます。
- [IoT Hub](../iot-hub/about-iot-hub.md) は、IoT アプリケーションと接続されたデバイス間の双方向通信用の中央メッセージ ハブとして機能します。 これは、サービスとしてのプラットフォーム (PaaS) アプリケーションであり、やはり IoT アプリケーションをホストするためのプラットフォームを提供します。 IoT Central と同様、数百万台ものデバイスをサポートするように拡張できます。 一般に、IoT Hub を使用すると、アプリケーションの設計をより詳細に制御およびカスタマイズできるようになり、サービスを操作するための開発者ツール オプションが増えますが、その分、開発と管理の複雑さが増します。

## <a name="tools-to-connect-and-manage-devices"></a>デバイスを接続および管理するためのツール
IoT アプリケーションをホストするために IoT Hub または IoT Central を選択した後は、いくつかの開発者ツールの中から選んで使用できます。 これらのツールを使用して、選択した IoT アプリケーション プラットフォームに接続し、アプリケーションとデバイスを作成および管理できます。 一般的なツールのオプションを次の表にまとめています。 

> [!NOTE]
> 次のツールに加えて、REST API、Azure SDK、または Azure Resource Manager テンプレートを使用して、IoT アプリケーションをプログラムで作成および管理することもできます。 詳細については、[IoT Hub](../iot-hub/about-iot-hub.md) と [IoT Central](../iot-central/core/overview-iot-central.md) サービスのドキュメントを参照してください。

|ツール  |サポートされる IoT プラットフォーム &nbsp; &nbsp; &nbsp; &nbsp; |ドキュメント  |説明  |
|---------|---------|---------|---------|
|Central Web UI     | Central | [Central のクイックスタート](../iot-central/core/quick-deploy-iot-central.md) | IoT Central 用のブラウザーベースのポータル。 |
|Azure portal     | Hub、Central      | [Azure portal を使用して IoT ハブを作成する](../iot-hub/iot-hub-create-through-portal.md)、[Azure portal から IoT Central を管理する](../iot-central/core/howto-manage-iot-central-from-portal.md)| IoT Hub とデバイス用のブラウザーベースのポータル。 IoT Central を含む、他の Azure リソースとも連携します。 |
|Azure CLI     | Hub、Central          | [CLI を使用して IoT ハブを作成する](../iot-hub/iot-hub-create-using-cli.md)、[Azure CLI から IoT Central を管理する](../iot-central/core/howto-manage-iot-central-from-cli.md) | IoT アプリケーションを作成および管理するためのコマンドライン インターフェイス。 |
|Azure PowerShell     | Hub、Central   | [PowerShell を使用して IoT ハブを作成する](../iot-hub/iot-hub-create-using-powershell.md)、[Azure PowerShell から IoT Central を管理する](../iot-central/core/howto-manage-iot-central-from-powershell.md) | IoT アプリケーションを作成および管理するための PowerShell インターフェイス |
|VS Code 用の Azure IoT Tools  | ハブ | [VS Code 用のツールを使用して IoT ハブを作成する](../iot-hub/iot-hub-create-use-iot-toolkit.md) | IoT Hub アプリケーションの VS Code 拡張機能。 |
|Azure IoT Explorer     | ハブ | [Azure IoT Explorer](https://github.com/Azure/azure-iot-explorer) | IoT ハブを作成することはできません。 既存の IoT ハブに接続してデバイスを管理します。 多くの場合、CLI またはポータルで使用されます。|

## <a name="next-steps"></a>次のステップ
デバイスを Azure IoT に接続するためのオプションの詳細については、次のクイックスタートを参照してください。
- IoT Central: [Azure IoT Central アプリケーションを作成する](../iot-central/core/quick-deploy-iot-central.md)
- IoT Hub: [デバイスから IoT ハブに利用統計情報を送信して Azure CLI で監視する](../iot-hub/quickstart-send-telemetry-cli.md)