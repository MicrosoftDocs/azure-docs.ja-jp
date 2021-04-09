---
title: F5 BIG IP データを Azure Sentinel に接続する | Microsoft Docs
description: F5 BIG IP データを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 17c31bbe54326962c26ab53f702cbd28d1e36c6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092793"
---
# <a name="connect-your-f5-big-ip-appliance"></a>F5 BIG-IP アプライアンスを接続する 

F5 BIG-IP コネクタを使用すると、すべての F5 BIG-IP のログを簡単に Azure Sentinel に接続して、ワークブックの表示、カスタム アラートの作成、調査の改善を行うことができます。 これにより、組織のネットワークに関するより詳しい分析情報が得られ、セキュリティ運用機能が向上します。 F5 BIG-IP と Azure Sentinel の統合には、REST API を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-f5-big-ip"></a>F5 BIG IP を構成して接続する 

F5 BIG-IP では、ログを統合して、Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel ポータルで、**[Data connectors]\(データ コネクタ\)** をクリックし、**[F5 BIG IP]** を選択して、**[Open connector page]\(コネクタ ページを開く\)** を選択します。 
1. F5 BIG IP を接続するには、JSON 宣言をシステムの API エンドポイントにポストする必要があります。 この実行方法の手順については、[F5 BIG-IP と Azure Sentinel との統合](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)に関する記事をご覧ください。
8. F5 BIG IP コネクタのページで、「[Streaming data to Azure Log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics)」の下の説明に従って、ワークスペース ID とプライマリ キーをコピーして貼り付けます。
1. F5 BIG IP の手順を完了すると、Azure Sentinel コネクタのページに、接続されたデータの種類が表示されます。
1. F5 BIG IP イベントに対して関連するスキーマを Log Analytics で使用するには、**F5Telemetry_LTM_CL**、**F5Telemetry_system_CL**、および **F5Telemetry_ASM_CL** を検索してください。


## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。 



## <a name="next-steps"></a>次のステップ
このドキュメントでは、F5 BIG IP を Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。


