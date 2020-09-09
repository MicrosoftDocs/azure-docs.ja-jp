---
title: ソリューションのアーキテクチャ
description: Azure Security Center for IoT サービスの情報フローについて説明します。
services: asc-for-iot
ms.service: asc-for-iot
documentationcenter: na
author: mlottner
manager: rkarlin
editor: ''
ms.assetid: 2cf6a49b-5d35-491f-abc3-63ec24eb4bc2
ms.subservice: asc-for-iot
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/23/2019
ms.author: mlottner
ms.openlocfilehash: 4cc7d1982555f058a4ea23f7d8a6fdc2d83e484d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/02/2020
ms.locfileid: "81311750"
---
# <a name="azure-security-center-for-iot-architecture"></a>Azure Security Center for IoT のアーキテクチャ

この記事では、IoT ソリューションの Azure Security Center の機能的なシステム アーキテクチャについて説明します。

## <a name="azure-security-center-for-iot-components"></a>Azure Security Center for IoT のコンポーネント

Azure Security Center for IoT は、次のコンポーネントで構成されています。

- IoT Hub 統合
- デバイス エージェント (オプション)
- セキュリティ メッセージの送信 SDK
- 分析パイプライン

### <a name="azure-security-center-for-iot-workflows"></a>Azure Security Center for IoT のワークフロー

Azure Security Center for IoT は、2 つの機能ワークフローのどちらかで動作します。組み込みと拡張です。

### <a name="built-in"></a>組み込み

**組み込み**モードでは、IoT Hub で **[セキュリティ]** オプションをオンにすると、Azure Security Center for IoT が有効になります。 リアルタイム監視、推奨事項の提示、アラート機能を備えた組み込みモードでは、デバイスの可視性と比類ないセキュリティが手軽に得られます。 組み込みモードでは、デバイスにエージェントをインストールする必要がありません。ログに記録されたアクティビティに対する高度な分析を使用してフィールド デバイスが分析され、保護されます。

### <a name="enhanced"></a>拡張

**拡張**モードでは、IoT Hub の **[セキュリティ]** オプションをオンにし、ご利用のデバイスに Azure Security Center for IoT デバイス エージェントをインストールすると、そのデバイスからの未加工のセキュリティ イベントがエージェントによって収集、集計、分析されます。 未加工のセキュリティ イベントには、IP 接続、プロセス作成、ユーザー ログイン、およびその他のセキュリティ関連情報が含まれる可能性があります。 また、Azure Security Center for IoT デバイス エージェントではイベント集計も処理され、高いネットワーク スループットを避けるのに役立ちます。 エージェントは高度にカスタマイズ可能です。これにより、最速の SLA で重要な情報のみを送信するなど、特定のタスクを実行したり、広範なセキュリティ情報とコンテキストをより大きなセグメントに集計して、高いサービス コストを回避したりできます。

![Azure Security Center for IoT のアーキテクチャ](./media/architecture/azure-iot-security-architecture.png)

デバイス エージェント、およびその他のアプリケーションでは、**Azure セキュリティ メッセージの送信 SDK** を使用して、Azure IoT Hub にセキュリティ情報が送信されます。 IoT Hub ではこの情報が取得され、Azure Security Center for IoT サービスに転送されます。

Azure Security Center for IoT サービスが有効になると、IoT Hub により、転送されたデータに加え、そのすべての内部データが Azure Security Center for IoT での分析用に送信されます。 このデータには、デバイス クラウド操作ログ、デバイス ID、および Hub 構成が含まれます。 この情報はすべて、Azure Security Center for IoT 分析パイプラインを作成するのに役立ちます。

また、Azure Security Center for IoT 分析パイプラインは、Microsoft および Microsoft パートナー内のさまざまなソースから、その他の脅威インテリジェンス ストリームを受信します。 Azure Security Center for IoT の分析パイプライン全体が、サービスに対して行われたすべての顧客構成 (カスタム アラートや、セキュリティ メッセージの送信 SDK の使用など) と連携します。

Azure Security Center for IoT では、分析パイプラインを使用して、実用的な推奨事項およびアラートを生成するためにすべての情報ストリームが結合されます。 パイプラインには、セキュリティ研究者と専門家によって作成されたカスタム ルールと、標準的なデバイスの動作とリスクの分析からの偏差を検索する機械学習モデルの両方が含まれています。

Azure Security Center for IoT の推奨事項とアラート (分析パイプライン出力) は、各顧客の Log Analytics ワークスペースに書き込まれます。 ワークスペースおよび未加工のイベントにアラートと推奨事項を含めると、検出された不審なアクティビティの正確な情報を使用して詳細な調査とクエリを行うことができます。

## <a name="next-steps"></a>次のステップ

この記事では、Azure Security Center for IoT ソリューションの基本的なアーキテクチャとワークフローについて説明しました。 IoT Hub でセキュリティ ソリューションの使用を開始し、有効化するための前提条件と方法の詳細については、次の記事を参照してください。

- [サービスの前提条件](service-prerequisites.md)
- [作業の開始](getting-started.md)
- [ソリューションを構成する](quickstart-configure-your-solution.md)
- [IoT Hub でセキュリティを有効化する](quickstart-onboard-iot-hub.md)
- [Azure Security Center for IoT の FAQ](resources-frequently-asked-questions.md)
- [Azure Security Center for IoT のセキュリティ アラート](concept-security-alerts.md)
