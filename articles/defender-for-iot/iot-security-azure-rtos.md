---
title: Azure RTOS 用の Defender-IoT-micro-agent の概要
description: Azure RTOS 用の Defender-IoT-micro-agent のサポートと、Azure Defender for IoT の一部としての実装について説明します。
ms.topic: conceptual
ms.date: 01/14/2021
ms.openlocfilehash: 8f162cc7e284abc9f1fdc08a10e62369855017c4
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104785905"
---
# <a name="overview-defender-for-iot-defender-iot-micro-agent-for-azure-rtos-preview"></a>概要: Azure RTOS 用の Defender for IoT Defender-IoT-micro-agentt (プレビュー)

Azure Defender for IoT マイクロ モジュールでは、Azure RTOS を使用するデバイス向けの包括的なセキュリティ ソリューションが提供されます。 リアルタイム オペレーティング システム (RTOS) デバイスに関する一般的な脅威と潜在的な悪意のあるアクティビティへの対応が提供されます。 現在、Azure RTOS には Azure IoT Defender-IoT-micro-agent が組み込みで付属しています。

:::image type="content" source="./media/architecture/azure-rtos-security-monitoring.png" alt-text="Defender for IoT Azure RTOS の図示。":::


Azure RTOS 用のマイクロ モジュールには、次の機能があります。

- 悪意のあるネットワーク アクティビティの検出
- カスタム アラート ベースのデバイス動作基準
- 強化されたデバイス セキュリティ検疫

## <a name="detect-malicious-network-activities"></a>悪意のあるネットワーク アクティビティを検出する

各デバイスの受信および送信ネットワーク アクティビティが監視されます。 サポートされているプロトコルは、IPv4 および IPv6 上の TCP、UDP、ICMP です。 Defender for IoT では、これらのネットワーク アクティビティのそれぞれが Microsoft 脅威インテリジェンス フィードに対して検査されます。 このフィードは、世界中で収集される数百万の固有の脅威インジケーターによってリアルタイムに更新されます。

## <a name="device-behavior-baselining-based-on-custom-alerts"></a>カスタム アラートに基づくデバイス動作基準

基準を使用すると、デバイスのセキュリティ グループへのクラスタリングと、各グループの想定される動作の定義が可能です。 IoT デバイスは、通常、明確に定義された限定的なシナリオで動作するように設計されているため、一連のパラメーターを使用して、想定される動作を定義するベースラインを作成することは容易です。 ベースラインからの逸脱があれば、アラートがトリガーされます。

## <a name="improve-your-device-security-hygiene"></a>デバイス セキュリティの検疫を強化する

Defender for IoT によって提供される推奨インフラストラクチャを使用することで、デバイスのセキュリティ態勢に影響や損害を与える環境内の問題について、知識や洞察を得ることができます。 IoT デバイスのセキュリティ態勢に問題があるときに、それが修正されないままになっていると、潜在的な攻撃の成功を許す可能性があります。 セキュリティの強度は常に組織内の最弱の輪によって測定されます。

## <a name="get-started-protecting-azure-rtos-devices"></a>Azure RTOS デバイスの保護を開始する

Azure RTOS 用の Defender-IoT-micro-agent は、デバイス用の無料ダウンロードとして提供されています。 Defender for IoT クラウド サービスは、Azure サブスクリプションごとに 30 日間の試用版で利用できます。 開始するには、[for Azure RTOS 用の Defender-IoT-micro-agent](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/defender-for-iot/iot-security-azure-rtos.md) をダウンロードします。 

## <a name="next-steps"></a>次のステップ

この記事では、Azure RTOS 用の Defender-IoT-micro-agent について学習しました。 Defender-IoT-micro-agent の詳細と開始方法については、次の記事を参照してください。

- [Azure RTOS IoT Defender-IoT-micro-agent の概念](concept-rtos-security-module.md)
- [クイックスタート: Azure RTOS IoT Defender-IoT-micro-agent](quickstart-azure-rtos-security-module.md)
