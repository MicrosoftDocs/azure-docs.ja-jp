---
title: エージェントベースのソリューション アーキテクチャ
description: Azure Defender for IoT のエージェントベースのアーキテクチャと情報フローについて説明します。
ms.topic: overview
ms.date: 1/25/2021
ms.openlocfilehash: b04e8fa6225aef9f3c228a44631f117fedffccff
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/22/2021
ms.locfileid: "104784885"
---
# <a name="agent-based-solution-for-device-builders"></a>デバイス ビルダー向けのエージェントベースのソリューション

この記事では、Defender for IoT エージェントベースのソリューションの機能的なシステム アーキテクチャについて説明します。 Azure Defender for IoT には、ご使用環境のニーズに適合するように、組織向けのエージェントレス ソリューションとデバイス ビルダー向けのエージェントベースのソリューションという 2 つの機能セットが用意されています。

## <a name="iot-hub-built-in-security"></a>IoT Hub の組み込みのセキュリティ

Defender for IoT は、作成されるすべての新しい IoT Hub で、既定で有効になっています。 Defender for IoT では、リアルタイム監視、推奨事項、アラートが提供され、デバイスにエージェントをインストールする必要はありません。また、ログに記録された IoT Hub メタ データに対して高度な分析を使用して、フィールド デバイスと IoT Hub を分析し、保護することができます。 

## <a name="defender-for-iot-micro-agent"></a>Defender for IoT マイクロ エージェント 

Defender for IoT マイクロ エージェントでは、高度なセキュリティ保護と、デバイス動作の可視性が提供されます。 デバイスから未加工のセキュリティ イベントを収集、集計、および分析します。 未加工のセキュリティ イベントには、IP 接続、プロセス作成、ユーザー ログイン、およびその他のセキュリティ関連情報が含まれる可能性があります。 また、Defender for IoT デバイス エージェントによってイベント集計も処理され、高いネットワーク スループットを回避できます。 エージェントは高度にカスタマイズ可能です。これにより、最速の SLA で重要な情報のみを送信するなど、特定のタスクを実行したり、広範なセキュリティ情報とコンテキストをより大きなセグメントに集計して、高いサービス コストを回避したりできます。

デバイス エージェント、およびその他のアプリケーションでは、**Azure セキュリティ メッセージの送信 SDK** を使用して、Azure IoT Hub にセキュリティ情報が送信されます。 IoT ハブではこの情報が取得され、Defender for IoT サービスに転送されます。

Defender for IoT サービスが有効になると、IoT ハブにより、転送されたデータに加え、そのすべての内部データが Defender for IoT での分析用に送信されます。 このデータには、デバイス クラウド操作ログ、デバイス ID、およびハブ構成が含まれます。 この情報はすべて、Defender for IoT 分析パイプラインを作成するのに役立ちます。

また、Defender for IoT 分析パイプラインにより、Microsoft および Microsoft パートナー内のさまざまなソースから、その他の脅威インテリジェンス ストリームが受信されます。 Defender for IoT の分析パイプライン全体が、サービスに対して行われたすべての顧客構成 (カスタム アラートや、セキュリティ メッセージの送信 SDK の使用など) と連携します。

Defender for IoT により、分析パイプラインを使用して、実用的な推奨事項およびアラートを生成するためにすべての情報ストリームが結合されます。 パイプラインには、セキュリティ研究者と専門家によって作成されたカスタム ルールと、標準的なデバイスの動作とリスクの分析からの偏差を検索する機械学習モデルの両方が含まれています。

Defender for IoT の推奨事項とアラート (分析パイプライン出力) は、各顧客の Log Analytics ワークスペースに書き込まれます。 ワークスペースおよび未加工のイベントにアラートと推奨事項を含めると、検出された不審なアクティビティの正確な情報を使用して詳細な調査とクエリを行うことができます。

:::image type="content" source="media/architecture/micro-agent-architecture.png" alt-text="マイクロ エージェントのアーキテクチャ。":::

## <a name="see-also"></a>関連項目

[Defender for IoT に関する FAQ](resources-frequently-asked-questions.md)

[システムの前提条件](quickstart-system-prerequisites.md)
