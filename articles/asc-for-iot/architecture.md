---
title: Azure Security Center for IoT ソリューションのアーキテクチャについて (プレビュー) | Microsoft Docs
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
ms.date: 03/24/2019
ms.author: mlottner
ms.openlocfilehash: 5cab57343f9675df0702cf4281b3d74ca96d86ae
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618543"
---
# <a name="azure-security-center-for-iot-architecture"></a>Azure Security Center for IoT のアーキテクチャ

Azure Security Center (ASC) for IoT ソリューションの機能的なシステム アーキテクチャについて説明します。 

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="asc-for-iot-components"></a>ASC for IoT のコンポーネント

ASC for IoT は、次のコンポーネントで構成されています。
- デバイス エージェント
- セキュリティ メッセージの送信 SDK
- IoT Hub 統合
- 分析パイプライン
 
### <a name="asc-for-iot-workflow"></a>ASC for IoT ワークフロー

ASC for IoT デバイス エージェントを使用すると、デバイスから未加工のセキュリティ イベントを簡単に収集できます。 未加工のセキュリティ イベントには、IP 接続、プロセス作成、ユーザー ログイン、およびその他のセキュリティ関連情報が含まれる可能性があります。 また、ASC for IoT デバイス エージェントではイベント集計も処理され、高いネットワーク スループットを回避できます。 エージェントは高度にカスタマイズ可能です。これにより、最速の SLA で重要な情報のみを送信するなど、特定のタスクを実行したり、広範なセキュリティ情報とコンテキストをより大きなセグメントに集計して、高いサービス コストを回避したりできます。
 
デバイス エージェント、およびその他のアプリケーションでは、**Azure ASC セキュリティ メッセージの送信 SDK** を使用して、Azure IoT Hub にセキュリティ情報が送信されます。 IoT Hub ではこの情報が取得され、ASC for IoT サービスに転送されます。

ASC for IoT サービスが有効になると、IoT Hub により、転送されたデータに加え、そのすべての内部データが ASC for IoT での分析用に送信されます。 このデータには、デバイス クラウド操作ログ、デバイス ID、および Hub 構成が含まれます。 この情報はすべて、ASC for IoT 分析パイプラインを作成するのに役立ちます。
 
また、ASC for IoT 分析パイプラインは、Microsoft および Microsoft パートナー内のさまざまなソースから、その他の脅威インテリジェンス ストリームを受信します。 ASC for IoT の分析パイプライン全体が、サービスに対して行われたすべての顧客構成 (カスタム アラートや、セキュリティ メッセージの送信 SDK の使用など) と連携します。
 
ASC for IoT では、分析パイプラインを使用して、実用的な推奨事項およびアラートを生成するためにすべての情報ストリームが結合されます。 パイプラインには、セキュリティ研究者と専門家によって作成されたカスタム ルールと、標準的なデバイスの動作とリスクの分析からの偏差を検索する機械学習モデルの両方が含まれています。
 
ASC for IoT の推奨事項とアラート (分析パイプライン出力) は、ASC は、各顧客の Log Analytics ワークスペースに書き込まれます。 ワークスペースおよび未加工のイベントにアラートと推奨事項を含めると、検出された不審なアクティビティの正確な情報を使用して詳細な調査とクエリを行うことができます。  

## <a name="next-steps"></a>次の手順

この記事では、ASC for IoT ソリューションの基本的なアーキテクチャとワークフローについて説明しました。 IoT Hub でセキュリティ ソリューションの使用を開始し、有効化するための前提条件と方法の詳細については、次の記事を参照してください。

- [サービスの前提条件](service-prerequisites.md)
- [使用の開始](getting-started.md)
- [ソリューションを構成する](quickstart-configure-your-solution.md)
- [IoT Hub でセキュリティを有効化する](quickstart-onboard-iot-hub.md)
- [ASC for IoT についてのよく寄せられる質問](resources-frequently-asked-questions.md)
- [ASC for IoT のセキュリティ アラート](concept-security-alerts.md)

