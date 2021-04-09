---
title: Azure Defender for IoT を Azure Sentinel に接続する | Microsoft Docs
description: Azure Sentinel に Azure Defender (旧称 Azure Security Center) for IoT からのデータを接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/20/2021
ms.author: yelevin
ms.openlocfilehash: 67bc104434dc0db30f5973bec0979afb7480fe4c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/19/2021
ms.locfileid: "98621381"
---
# <a name="connect-your-data-from-azure-defender-formerly-azure-security-center-for-iot-to-azure-sentinel"></a>Azure Defender (旧称 Azure Security Center) for IoT からのデータを Azure Sentinel に接続する 

Defender for IoT コネクタを使用して、Defender for IoT イベントをすべて Azure Sentinel にストリーミングします。 

この統合により、組織では、IT と OT の境界を越えることの多いマルチステージ攻撃を迅速に検出できます。 さらに、Defender for IoT と Azure Sentinel のセキュリティ オーケストレーション、オートメーション、および応答 (SOAR) 機能との統合により、OT 向けに最適化された組み込みのプレイブックを使って応答と防止を自動化できます。 
## <a name="prerequisites"></a>前提条件

- Azure Sentinel がデプロイされているワークスペースに対する **読み取り** および **書き込み** アクセス許可
- お使いの適切な IoT Hub で **Defender for IoT** を **有効** にする
- 接続する **サブスクリプション** に対する **共同作成者** アクセス許可

## <a name="connect-to-defender-for-iot"></a>Defender for IoT に接続する

1. Azure Sentinel で、 **[データ コネクタ]** を選択し、 **[Defender for IoT]** (名称は Azure Security Center for IoT のままの場合があります) をギャラリーから選択します。

1. 右側のペインの下部で、 **[コネクタ ページを開く]** をクリックします。 

1. アラートとデバイス アラートを Azure Sentinel にストリーム配信する各 IoT Hub サブスクリプションの横にある **[接続]** をクリックします。 
    - サブスクリプション内の少なくとも 1 つの IoT Hub で Defender for IoT が有効になっていない場合、エラーメッセージが表示されます。 エラーを削除するには、IoT Hub 内で Defender for IoT を有効にします。

1. Defender for IoT からのアラートによって Azure Sentinel でインシデントが生成されるようにするかどうかを指定できます。 **[インシデントの作成]** で **[有効]** を選択して、生成されたアラートからインシデントを自動的に作成する既定の分析ルールを有効にします。 このルールは、 **[分析]**  >  **[アクティブ]** ルールで変更または編集できます。

> [!NOTE]
> 接続を変更した後、**サブスクリプション** リストが更新されるまでに 10 秒以上かかる場合があります。 

## <a name="log-analytics-alert-view"></a>Log Analytics のアラートの表示

Log Analytics の適切なスキーマを使用して Defender for IoT のアラートを表示するには、次のようにします。

1. **[ログ]**  >  **[SecurityInsights]**  >  **[SecurityAlert]** を開くか、**SecurityAlert** を検索します。 

2. 次の kql フィルターを使用して、Defender for IoT によって生成されたアラートのみが表示されるようにします。

```kusto
SecurityAlert | where ProductName == "Azure Security Center for IoT"
``` 

### <a name="service-notes"></a>サービスに関する注意事項

**サブスクリプション** に接続してから約 15 分後に Azure Sentinel でハブ データを利用できるようになります。


## <a name="next-steps"></a>次のステップ

このドキュメントでは、Defender for IoT を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
