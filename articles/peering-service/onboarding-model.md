---
title: Azure Peering Service のモデルのオンボード
description: Azure Peering Service をオンボードする方法の詳細
services: peering-service
documentationcenter: na
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 7a257b4c2bea3bd3384a55c0a6b85d7fdd2ca583
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84871402"
---
# <a name="onboarding-peering-service-model"></a>Peering Service モデルのオンボード

Peering Service のオンボード プロセスは、次の 2 つのモデルで構成されています。

 - Peering Service 接続のオンボード

 - Peering Service 接続テレメトリのオンボード

上記のモデルのアクション プランは、次のように記述されています。

| **Step** | **操作**| **取得内容**|
|-----------|---------|---------|---------|
|1|お客様が接続パートナーからの接続をプロビジョニングします (Microsoft とのやりとりはありません)。 |Microsoft に適切に接続されており、Microsoft への高性能で信頼性の高い接続のための技術的な要件を満たすインターネット プロバイダー。  |
|2 (省略可能)|お客様が Azure portal に場所を登録します。 場所は次によって定義されます。ISP/IXP 名、お客様のサイトの物理的な場所 (州レベル)、サービス プロバイダーまたは企業によって場所に指定された IP プレフィックス。  |テレメトリ:インターネット ルートの監視、Microsoft からユーザーの最も近いエッジ POP の場所へのトラフィックの優先順位付け。 |



## <a name="onboarding-peering-service-connection"></a>Peering Service 接続のオンボード

Peering Service 接続をオンボードするには、以下を実行します。

インターネット サービス プロバイダー (ISP) またはインターネット交換 (IX) パートナーと協力して Peering Service を取得することで、お使いのネットワークを Microsoft ネットワークに接続します。

[接続プロバイダー](location-partners.md)が Peering Service のために Microsoft と提携していることを確認してください。 

## <a name="onboarding-peering-service-connection-telemetry"></a>Peering Service 接続テレメトリのオンボード

お客様は、Microsoft ネットワークにアクセスするときに、ネットワークの待機時間とパフォーマンスを監視するために、BGP ルーティングの分析などのテレメトリを選択できます。 これは、Azure portal に接続を登録することによって実現できます。

Peering Service 接続テレメトリをオンボードするには、お客様が Azure portal に Peering Service 接続を登録する必要があります。 手順については、[Azure portal を使用した Peering Service の登録](azure-portal.md)に関するページを参照してください。

次に、[こちら](measure-connection-telemetry.md)を参照してテレメトリを測定できます。

## <a name="next-steps"></a>次のステップ

Peering Service 接続を登録する手順の詳細については、[Azure portal を使用した Peering Service の登録](azure-portal.md)に関するページを参照してください。

接続テレメトリの測定の詳細については、[接続テレメトリの測定](measure-connection-telemetry.md)に関するページを参照してください。
