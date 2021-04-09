---
title: セキュリティ エージェント
description: IoT デバイスでの Azure Defender for IoT セキュリティ サービス エージェントについて理解を深め、構成、デプロイ、および使用を開始します。
ms.topic: conceptual
ms.date: 1/24/2021
ms.openlocfilehash: a1d74de9824b9e825f87754da21070d4e7f1ee33
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104783491"
---
# <a name="get-started-with-azure-defender-for-iot-device-micro-agents"></a>Azure Defender for IoT デバイス マイクロ エージェントの使用を開始する

Defender for IoT セキュリティ エージェントでは、オペレーティング システム構成のベスト プラクティスの監視など、強化されたセキュリティ機能が提供されています。 1つのサービスで、デバイス フィールドの脅威保護とセキュリティ体制を制御します。

Defenders for IoT セキュリティ エージェントでは、デバイスのオペレーティング システムからの未加工イベント収集、コストを削減するためのイベント集計、およびデバイス モジュール ツインを使用した構成が処理されます。 セキュリティ メッセージは、IoT Hub を介して Defender for IoT 分析サービスへ送信されます。

次のワークフローを使用して、Defender for IoT セキュリティ エージェントをデプロイしてテストします。

1. [IoT Hub で Defender for IoT サービスを有効にします](quickstart-onboard-iot-hub.md)。

1. IoT Hub に登録済みデバイスがない場合は、[新しいデバイスを登録](../iot-accelerators/iot-accelerators-device-simulation-overview.md)します。

1. デバイスの [DefenderIotMicroAgent モジュール ツインを作成](quickstart-create-micro-agent-module-twin.md)します。

1. 実際のデバイスにインストールするのではなく、Azure シミュレート デバイスにエージェントをインストールするには、使用可能なゾーンで[新しい Azure Virtual Machine (VM ) を作成](../virtual-machines/linux/quick-create-portal.md)します。

1. IoT デバイスまたは新しい VM 上に [Defender for IoT セキュリティ エージェントをデプロイ](how-to-deploy-linux-cs.md)します。

1. OS ベースライン イベントを実行するには、[trigger_events](https://aka.ms/iot-security-github-trigger-events) の手順に従います。

1. 前の手順でシミュレートされた OS ベースライン チェックの失敗に応じて、Defender for IoT の推奨事項を確認します。 スクリプトを実行した 30 分後に確認を開始してください。

## <a name="next-steps"></a>次のステップ

- [ソリューションを構成する](quickstart-configure-your-solution.md)
- [Defender-IoT-micro-agent の作成](quickstart-create-security-twin.md)
- [カスタム アラートを構成する](quickstart-create-custom-alerts.md)
- [セキュリティ エージェントをデプロイする](how-to-deploy-agent.md)