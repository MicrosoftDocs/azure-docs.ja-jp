---
title: IoT Edge 向け Defender for IoT Defender-IoT-micro-agent
description: IoT Edge 向け Azure Defender for IoT Defender-IoT-micro-agent のアーキテクチャと機能を理解します。
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 81eb8816e1bcf74a9e27e34d14465102599c7d5d
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104782658"
---
# <a name="azure-defender-for-iot-edge-defender-iot-micro-agent"></a>Azure Defender for IoT Edge Defender-IoT-micro-agent

[Azure IoT Edge](../iot-edge/index.yml) では、エッジでのビジネス ワークフローを管理および実行するための強力な機能が提供されます。
IoT Edge は IoT 環境内で重要な役割を果たしているため、悪意のあるアクターの注意を特に引きます。

Defender for IoT Defender-IoT-micro-agent により、IoT Edge デバイスの包括的なセキュリティ ソリューションが提供されます。
Defender for IoT モジュールでは、オペレーティング システムおよびコンテナー システムから未加工のセキュリティ データが収集、集約、分析されて、実行可能なセキュリティ推奨事項とアラートに変換されます。

IoT デバイスに対する Defender for IoT セキュリティ エージェントと同様に、Defender for IoT Edge モジュールもそのモジュール ツインによって高度なカスタマイズが可能です。
詳しくは、[エージェントの構成](how-to-agent-configuration.md)に関する記事をご覧ください。

IoT Edge 用の Defender for IoT Defender-IoT-micro-agent からは、次の機能が提供されます。

- 基になるオペレーティング システム (Linux) および IoT Edge コンテナー システムから、生のセキュリティ イベントを収集します。

  使用可能なセキュリティ データ コレクターの詳細については、[Defender for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

- IoT Edge のデプロイ マニフェストを分析します。

- [IoT Edge ハブ](../iot-edge/iot-edge-runtime.md#iot-edge-hub)経由で送信されるメッセージに、未加工のセキュリティ イベントを集約します。

- Defender-IoT-micro-agent ツインを使用することで構成が削除されます。

  詳細については、[Defender for IoT エージェントの構成](how-to-agent-configuration.md)に関するページを参照してください。

IoT Edge 用の Defender for IoT Defender-IoT-micro-agent は、IoT Edge において特権モードで実行されます。
モジュールでオペレーティング システムおよび他の IoT Edge モジュールを監視できるためには、特権モードが必要です。

## <a name="module-supported-platforms"></a>モジュールのサポート対象プラットフォーム

現在、IoT Edge 用の Defender for IoT Defender-IoT-micro-agent は、Linux でのみ利用できます。

## <a name="next-steps"></a>次のステップ

この記事では、IoT Edge 用の Defender for IoT Defender-IoT-micro-agent のアーキテクチャと機能について説明しました。

Defender for IoT のデプロイの概要について引き続き学習するには、次の記事を参照してください。

- [IoT Edge 向け Defender-IoT-micro-agent](how-to-deploy-edge.md) を展開する
- [Defender-IoT-micro-agent を構成する](how-to-agent-configuration.md)方法を学習する
- Defender for IoT の [[Defender for IoT Horizon]\(Defender for IoT Horizon\)](resources-manage-proprietary-protocols.md) を確認する
- [IoT Hub で Defender for IoT サービスを有効にする](quickstart-onboard-iot-hub.md)方法について確認する
- [Defender for IoT についてよく寄せられる質問](resources-frequently-asked-questions.md)からサービスの詳細について確認する