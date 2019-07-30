---
title: Azure Security Center for IoT とは (プレビュー) | Microsoft Docs
description: Azure Security Center for IoT の機能とサービスについて説明します。
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
ms.date: 04/29/2019
ms.author: mlottner
ms.openlocfilehash: 2e454d3be536d908003ad9163a44089eb3a5eb99
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/07/2019
ms.locfileid: "67618061"
---
# <a name="introducing-azure-security-center-for-iot"></a>Azure Security Center for IoT の概要

> [!IMPORTANT]
> Azure Security Center for IoT は現在、パブリック プレビュー段階です。
> このプレビュー バージョンはサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

## <a name="secure-your-azure-iot-solution"></a>Azure IoT ソリューションをセキュリティで保護する 

ハイブリッド クラウド ワークロードおよび Azure IoT ソリューション全体で、セキュリティ管理を統合し、エンド ツー エンドの脅威の分析と保護を有効化します。 

## <a name="protect-iot-and-hybrid-cloud-workloads-against-evolving-cyber-attacks"></a>進化するサイバー攻撃から IoT およびハイブリッド クラウド ワークロードを保護する

モノのインターネットにより、私たちの世界は急速に変化しています。 接続されたデバイスやバックエンド システムから分析情報を得ることができ、こうした情報を、組織が経営や事業の実績をこれまでにないレベルに引き上げるのに役立つ、強力なアプリケーションやサービスに作り替えることができます。 ただし、何十億もの新しいデバイスがインターネットに接続され、私たちの日常生活やビジネスに統合されているため、セキュリティ運用チームは、これらの新しい攻撃面をカバーできるよう、セキュリティ戦略を迅速に進化させる必要があります。 他のシステムと同様に、IoT ソリューションをセキュリティで保護するには、実装の各段階での保護が必要です。 

Azure Security Center (ASC) for IoT は、エッジ、オンプレミス、Azure、その他のクラウド上で実行されているワークロードにまたがる統一された可視性と制御、アダプティブな脅威の防止、およびインテリジェントな脅威の検出と対応を提供することによってハイブリッド ワークロード保護を簡素化します。 

![ASC for IoT のアーキテクチャ](./media/architecture/asc-for-iot-architecture.png)

### <a name="unified-visibility-and-control"></a>統一された可視性と制御

Azure IoT ソリューションなど、オンプレミスとクラウドのすべてのワークロードのセキュリティを、統合された 1 つのビューで確認できます。 新しいデバイスを自動で検索して使用を開始し、ワークロード (Leaf デバイス、Edge デバイス、IOT Hub) にセキュリティ ポリシーを適用して、セキュリティ標準を確実に遵守します。

### <a name="adaptive-threat-prevention"></a>アダプティブ脅威に対する防護

ASC for IoT を使用して、マシン、ネットワーク、および Azure サービス (エッジ デバイスからアプリケーションまでの Azure IoT ソリューションを含む) のセキュリティを継続的に監視します。 数百もの組み込みのセキュリティ評価を使用するか、または中央の ASC for IoT Hub ダッシュボードで独自のセキュリティ評価を作成するかを選択できます。 仮想マシン、ネットワーク、アプリ、およびデータの間で実用的な推奨事項を使用して、セキュリティ設定を最適化し、セキュリティ スコアを向上させます。 新しく追加された IoT 機能により、Azure IoT ソリューションの攻撃対象領域を削減し、攻撃を受ける前に問題を修復できるようになりました。

### <a name="intelligent-threat-detection-and-response"></a>インテリジェントな脅威検出と対応

高度な分析と Microsoft Intelligent Security Graph により、巧妙化していくサイバー攻撃に対応します。 組み込みの行動分析と機械学習により、各種攻撃やゼロデイ攻撃を特定します。 攻撃や侵害後のアクティビティがないか、IoT ソリューションを監視します。 対話型のツールと状況に応じた脅威インテリジェンスにより、デバイスの調査と修復を効率化します。

## <a name="next-steps"></a>次の手順

この概要では、ASC for IoT の機能とサービスについて説明しました。 ASC for IoT のアーキテクチャと前提条件の詳細、および使用を開始する方法については、次の記事を参照してください。

- [アーキテクチャ](architecture.md)
- [サービスの前提条件](service-prerequisites.md)
- [使用の開始](getting-started.md)
- [ソリューションを定義する](quickstart-configure-your-solution.md)
- [ASC for IoT についてのよく寄せられる質問](resources-frequently-asked-questions.md)
- [ASC for IoT アラート](concept-security-alerts.md)

