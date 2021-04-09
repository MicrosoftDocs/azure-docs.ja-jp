---
title: Azure RTOS 用の Defender-IoT-micro-agent の基本についての概念的な説明
description: Azure RTOS 用の Defender-IoT-micro-agent の概念とワークフローについての基本を説明します。
ms.topic: conceptual
ms.date: 09/09/2020
ms.openlocfilehash: 83557930aeeccbb557382583e4d6666a000ce52c
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779275"
---
# <a name="defender-iot-micro-agent-for-azure-rtos-preview"></a>Azure RTOS 用の Defender-IoT-micro-agent (プレビュー段階)

この記事を使用して、機能と利点に加え、関連する構成と参照リソースへのリンクなど、Azure RTOS 用の Defender-IoT-micro-agent について理解を深めてください。 

## <a name="azure-rtos-iot-defender-iot-micro-agent"></a>Azure RTOS IoT Defender-IoT-micro-agent

Azure RTOS 用の Defender-IoT-micro-agent では、NetX Duo オファリングの一部として、Azure RTOS デバイス向けの包括的なセキュリティ ソリューションが提供されます。 NetX Duo オファリングには Azure RTOS と組み込みの Azure IoT Defender-IoT-micro-agent が付属しており、アクティブ化されると、リアルタイムのオペレーティング システム デバイスでの一般的な脅威への対応が提供されます。 

Azure RTOS 用の Defender-IoT-micro-agent はバックグラウンドで実行され、各顧客の IoT Hub への一意の接続を使用してセキュリティ メッセージが送信されると同時に、シームレスなユーザー エクスペリエンスが提供されます。 Azure RTOS 用の Defender-IoT-micro-agent は、既定で有効になっています。  

## <a name="azure-rtos-netx-duo"></a>Azure RTOS NetX Duo

Azure RTOS NetX Duo は、特に深く埋め込まれるリアルタイムの IoT アプリケーション用に設計された高度な商用 TCP/IP ネットワーク スタックです。 Azure RTOS NetX Duo は、セキュリティやクラウドなどの豊富なプロトコル セットを備えたデュアル IPv4 および IPv6 ネットワーク スタックです。 詳しくは、[Azure RTOS NetX Duo](/azure/rtos/netx-duo/) ソリューションを参照してください。

このモジュールには、次の機能があります。

- **悪意のあるネットワーク アクティビティを検出する**
- **カスタム アラートに基づくデバイスの動作のベースライン**
- **デバイス セキュリティの検疫の強化**

## <a name="defender-iot-micro-agent-for-azure-rtos-architecture"></a>Azure RTOS 用の Defender-IoT-micro-agent のアーキテクチャ

Azure RTOS 用の Defender-IoT-micro-agent は Azure IoT ミドルウェア プラットフォームによって初期化され、IoT Hub クライアントを使用してセキュリティ テレメトリをハブに送信します。

:::image type="content" source="media/architecture/security-module-state-diagram.png" alt-text="Azure IoT Defender-IoT-micro-agent の状態の図と情報フロー":::

Azure RTOS 用の Defender-IoT-micro-agent では、3 つのコレクターを使用して、次のデバイスのアクティビティと情報が監視されます。
- デバイス ネットワーク アクティビティ **TCP**、**UDP**、および **ICM**
- **Threadx** と **NetX Duo** のバージョンとしてのシステム情報
- ハートビートのイベント

各コレクターは優先順位グループにリンクされ、各優先順位グループには独自の間隔 (可能な値は **[低]** 、 **[中]** 、 **[高]** ) があります。 間隔は、データの収集と送信が実行される時間間隔に影響します。

各時間間隔は構成可能であり、さらに[ソリューションをカスタマイズする](how-to-azure-rtos-security-module.md)ために IoT コネクタを有効または無効にすることができます。 

## <a name="supported-security-alerts-and-recommendations"></a>サポートされているセキュリティ アラートと推奨事項

Azure RTOS 用の Defender-IoT-micro-agent では、特定のセキュリティ アラートと推奨事項がサポートされています。 初期構成を完了した後、サービスに[関連するアラートと推奨事項の値を確認してカスタマイズ](concept-rtos-security-alerts-recommendations.md)します。

## <a name="ready-to-begin"></a>開始する準備ができましたか?

Azure RTOS 用の Defender-IoT-micro-agent は、IoT デバイス用の無料ダウンロードとして提供されています。 Defender for IoT クラウド サービスは、Azure サブスクリプションごとに 30 日間の試用版で利用できます。 [Defender-IoT-micro-agent を今すぐダウンロード](https://github.com/azure-rtos/azure-iot-preview/releases)して開始しましょう。 

## <a name="next-steps"></a>次のステップ

- Azure RTOS 用の Defender-IoT-micro-agent を使用して作業を開始する ([前提条件と設定](quickstart-azure-rtos-security-module.md))。
- Azure RTOS 用の Defender-IoT-micro-agent の[セキュリティ アラートと推奨事項のサポート](concept-rtos-security-alerts-recommendations.md)の詳細を確認する。 
- Azure RTOS 用の Defender-IoT-micro-agent の[リファレンス API](azure-rtos-security-module-api.md) を使用する。