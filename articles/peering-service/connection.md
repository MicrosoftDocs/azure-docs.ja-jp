---
title: Azure Peering Service 接続
description: Microsoft Azure Peering Service 接続の詳細
services: peering-service
author: derekolo
ms.service: peering-service
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Infrastructure-services
ms.date: 05/18/2020
ms.author: derekol
ms.openlocfilehash: 0540fa9c7a29456aaf343adce83509b2d0e390e4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "91398922"
---
# <a name="peering-service-connection"></a>Peering Service 接続

通常、接続は、Peering Service を識別する論理情報セットを指します。 これは、次の属性を指定することによって定義されます。

- [論理名]
- 接続パートナー
- お客様の物理的な場所
- IP プレフィックス

お客様は、要件に従って、1 つまたは複数の接続を確立できます。 接続は、テレメトリ コレクションの 1 つの単位としても使用されます。 たとえば、テレメトリ アラートを選択するには、監視する接続をお客様が定義する必要があります。

> [!Note]
> Peering Service にサインアップすると、選択したプレフィックスの待ち時間の測定値を提供するために、Windows と Microsoft 365 のテレメトリが分析されます。
>接続テレメトリの詳細については、[Peering Service 接続テレメトリ](connection-telemetry.md)に関するページを参照してください。
>

>## <a name="how-to-register-a-connection"></a>接続を登録する方法

**シナリオ** - 次の図に示すように、支店が地理的に異なる場所に分散されているとします。 ここでは、お客様は、単一の接続に関連付けられている (お客様が所有しているか、サービス プロバイダーによって割り当てられた) 論理名、サービス プロバイダー (SP) 名、お客様の物理的な場所、および IP プレフィックスを指定する必要があります。 個別の geo 冗長接続に Peering Service を登録するには、このプロセスを繰り返す必要があります。

![geo 冗長接続](./media/peering-service-connection/peering-service-connections.png)

> [!Note]
> 接続が地理的に米国に配置されている場合は、お客様の物理的な場所に対して状態レベルのフィルター処理が考慮されます。
>

## <a name="next-steps"></a>次のステップ

Peering Service 接続を登録する手順の詳細については、[Azure portal を使用した Peering Service の登録](azure-portal.md)に関するページを参照してください。

Peering Service 接続のテレメトリの詳細については、「[Peering Service 接続のテレメトリ](connection-telemetry.md)」を参照してください。

テレメトリを測定する方法については、[接続のテレメトリの測定](measure-connection-telemetry.md)に関するページを参照してください。
