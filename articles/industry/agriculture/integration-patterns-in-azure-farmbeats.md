---
title: Azure FarmBeats アーキテクチャ
description: Azure FarmBeats のアーキテクチャについて説明します。
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: ebc1cdc7c762117851072037624f11e9fa98b6f1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "102182606"
---
# <a name="integration-patterns"></a>統合のパターン

Azure FarmBeats は、Azure Marketplace で入手できる企業間サービスです。 FarmBeats を利用すると、複数のプロバイダーを対象に農業データセットを集計したり、データセットを融合し、人工知能 (AI) または機械学習 (ML) モデルを構築することで実用的な分析情報を生成したりできます。

![プロジェクト (Farm Beats)](./media/architecture-for-farmbeats/farmbeats-architecture-1.png)

後続のセクションでは、Azure FarmBeats の統合パターンについて説明します。

## <a name="why-integrate-with-azure-farmbeats"></a>Azure FarmBeats と統合する理由

このセクションでは、データ システム (センサー、ドローン、気象観測所など) を Azure FarmBeats に統合するパートナーについて取り上げています。

Azure FarmBeats は拡張可能なサービスであり、農企業がさまざまな履歴とリアルタイムの農業データセットを 1 つのプラットフォームに追加することを可能にします。 Azure FarmBeats は、農企業が農場というコンテキストでそのデータを正規化し、状況に当てはめ、集計するのに役立ちます。

Azure FarmBeats でデータ パートナーになると、システムが採用する選択肢が増え、会社のデータ サービスの顧客を増やすことができます。 Azure FarmBeats には、データ ハブと呼ばれる拡張可能な API 層があります。これは、標準化されたスキーマにデバイスからデータを体系的に取り込むのに役立ちます。

顧客の Azure FarmBeats インスタンス内でデータが利用できるようになると、顧客は貴社のデータを基盤に、さらにリソースに恵まれた分析やツールを構築できます。

## <a name="next-steps"></a>次のステップ

センサー データの統合に関する詳細については、[センサー データの統合](sensor-partner-integration-in-azure-farmbeats.md)に関するページを、画像パートナーの統合に関する詳細については、「[画像パートナーの統合](imagery-partner-integration-in-azure-farmbeats.md)」を参照してください。
