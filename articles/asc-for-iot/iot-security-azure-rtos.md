---
title: Azure RTOS のサポート
description: Azure Security Center for IoT サービスでの Azure RTOS のサポートについて説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/15/2020
ms.author: mlottner
ms.openlocfilehash: af4579d25b94fc12f67cfc91d0f6a5af4b35c810
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/23/2020
ms.locfileid: "87094613"
---
# <a name="azure-security-center-for-iot-security-solution-for-azure-rtos"></a>Azure RTOS 用の Azure Security Center for IoT セキュリティ ソリューション 

Azure Security Center for IoT セキュリティ モジュールでは、Azure RTOS デバイス向けの包括的なセキュリティ ソリューションが提供されます。 Azure RTOS には、リアルタイム オペレーティング システム デバイスでの一般的な脅威に対応する、組み込みのセキュリティ モジュールが付属しています。 

![Azure Security Center for IoT と Azure RTOS](./media/architecture/azure-rtos-security-monitoring.png)


Azure RTOS のサポートを含む Azure Security Center for IoT セキュリティ モジュールでは、以下の機能が提供されます。 
- 悪意のあるネットワーク アクティビティの検出
- カスタム アラート ベースのデバイス動作基準
- デバイス セキュリティの検疫の強化

### <a name="detection-of-malicious-network-activities"></a>悪意のあるネットワーク アクティビティの検出

各デバイスの受信および送信ネットワーク アクティビティが監視されます (サポートされるプロトコル:IPv4 および IPv6 上の TCP、UDP、ICMP)。 Azure Security Center for IoT は、これらのネットワーク アクティビティのそれぞれを、Microsoft 脅威インテリジェンスのフィードと比較して検査します。 このフィードは、世界中で収集される数百万の固有の脅威インジケーターによってリアルタイムに更新されます。 

### <a name="device-behavior-baselining-based-on-custom-alerts"></a>カスタム アラートに基づくデバイス動作基準

基準を使用すると、デバイスのセキュリティ グループへのクラスタリングと、各グループの想定される動作の定義が可能です。 IoT デバイスは、通常、明確に定義された限定的なシナリオで動作するように設計されているため、一連のパラメーターを使用して、想定される動作を定義するベースラインを作成することは容易です。 ベースラインからの逸脱があれば、アラートがトリガーされます。 

### <a name="improve-your-device-security-hygiene"></a>デバイス セキュリティの検疫を強化する

Azure Security Center for IoT によって提供される推奨インフラストラクチャを活用することで、デバイスのセキュリティ体制に影響や損害を与える環境内の問題について、知識や洞察を得てください。 どのような組織であってもセキュリティの強度は常に最も弱い輪によって決まるため、IoT デバイスのセキュリティ体制に問題があり、修正されないままになっていると、考えられる攻撃が成功するおそれがあります。 

## <a name="get-started-protecting-azure-rtos-devices"></a>Azure RTOS デバイスの保護を開始する

- Azure RTOS 用の Azure Security Center for IoT セキュリティ モジュールは、デバイス用の無料ダウンロードとして提供されています。 Azure Security Center for IoT クラウド サービスは、Azure サブスクリプションごとに 30 日間の試用版で利用できます。 [Azure RTOS 用の Azure Security Center for IoT セキュリティ モジュール](https://github.com/azure-rtos/iot-security-module-preview)をダウンロードして使用を開始してください。 


## <a name="next-steps"></a>次のステップ

この記事では、Azure Security Center for IoT の Azure RTOS のサポートについて説明しました。 IoT Hub でセキュリティ ソリューションの使用を開始し、有効化する方法については、以下の記事を参照してください。

- [サービスの前提条件](service-prerequisites.md)
- [作業の開始](getting-started.md)
- [ソリューションを構成する](quickstart-configure-your-solution.md)
- [IoT Hub でセキュリティを有効化する](quickstart-onboard-iot-hub.md)
- [Azure Security Center for IoT の FAQ](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT のセキュリティ アラート](concept-security-alerts.md)
