---
title: Azure Peering Service 接続テレメトリ
description: Microsoft Azure Peering Service 接続テレメトリの詳細
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0e54b20cc6aefa69226c24f557ae02166dc4b4b6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "84871302"
---
# <a name="peering-service-connection-telemetry"></a>Peering Service 接続テレメトリ

接続テレメトリは、お客様の場所と Microsoft ネットワークの間の接続に関して収集された分析情報を提供します。 お客様は、Azure portal で接続を登録することで、Azure Peering Service 接続のテレメトリを取得できます。 この機能により、ネットワーク パフォーマンスのプレフィックスのセキュリティと分析情報が得られます。


接続テレメトリは、次のスコープで構成されます。

### <a name="latency-measurement"></a>待ち時間の測定

 Peering Service 内の登録済みのプレフィックスについて、クライアントから Microsoft Edge PoP までの待ち時間が測定されます。

### <a name="route-prefix-monitoring-and-protection"></a>ルート プレフィックスの監視と保護

イベント ログに記録される疑わしいアクティビティのルーティング パスが監視されます。 たとえば、イベント ログは次のような要因の一部に対して作成されます。

- プレフィックスのハイジャック
- プレフィックスの取り消し
- ルート リーク

## <a name="next-steps"></a>次のステップ

- Peering Service 接続の詳細については、「[Peering Service 接続](connection.md)」を参照してください。
- Peering Service 接続をオンボードする場合は、[Peering Service モデルのオンボード](onboarding-model.md)に関するページを参照してください。
- テレメトリを測定する方法については、[接続のテレメトリの測定](measure-connection-telemetry.md)に関するページを参照してください。
