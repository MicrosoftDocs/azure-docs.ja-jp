---
title: Microsoft Defender for Endpoint データを Azure Sentinel に接続する | Microsoft Docs
description: Microsoft Defender for Endpoint (以前の Microsoft Defender ATP) データを Azure Sentinel に接続する方法について説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2020
ms.author: yelevin
ms.openlocfilehash: 0db4e0fe0472c75f1eae392980ae697f53007244
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98623368"
---
# <a name="connect-alerts-from-microsoft-defender-for-endpoint-formerly-microsoft-defender-atp"></a>Microsoft Defender for Endpoint (以前の Microsoft Defender ATP) からアラートを接続する

> [!IMPORTANT]
>
> - **Microsoft Defender for Endpoint** は、以前は **Microsoft Defender Advanced Threat Protection** または **MDATP** と呼ばれていました。
>
>     一定の期間は、古い名前が製品 (Azure Sentinel のデータ コネクタなど)で引き続き使用される場合があります。

[Microsoft Defender for Endpoint](/windows/security/threat-protection/microsoft-defender-atp/microsoft-defender-advanced-threat-protection) コネクタを使用すると、Microsoft Defender for Endpoint から Azure Sentinel にアラートをストリーム配信できます。 これにより、組織全体のセキュリティ イベントをより包括的に分析し、効果的かつ迅速に対応するためのプレイブックを構築することができます。

> [!NOTE]
>
> Microsoft Defender for Endpoint の[高度な追及](/windows/security/threat-protection/microsoft-defender-atp/advanced-hunting-overview)から新しい生データ ログを取り込むには、Microsoft 365 Defender (以前の Microsoft Threat Protection、[ドキュメントを参照](./connect-microsoft-365-defender.md)) 用の新しいコネクタを使用します。

## <a name="prerequisites"></a>前提条件

- [Microsoft Defender for Endpoint 展開の設定](/windows/security/threat-protection/microsoft-defender-atp/licensing)に関する説明に従い、Microsoft Defender for Endpoint の有効なライセンスを持っている必要があります。 

- Azure Sentinel テナントのグローバル管理者またはセキュリティ管理者である必要があります。

## <a name="connect-to-microsoft-defender-for-endpoint"></a>Microsoft Defender for Endpoint に接続する

Microsoft Defender for Endpoint がデプロイされ、データの取り込みを行っている場合は、アラートを Azure Sentinel に簡単にストリーム配信できます。

1. Azure Sentinel で、 **[データ コネクタ]** を選択し、ギャラリーから **[Microsoft Defender for Endpoint]** (*Microsoft Defender Advanced Threat Protection* と引き続き呼ばれる場合もあります) を選択して、 **[コネクタ ページを開く]** を選択します。

1. **[接続]** をクリックします。 

1. **[ログ]** で Microsoft Defender for Endpoint アラートのクエリを実行するには、クエリ ウィンドウで「**SecurityAlert**」と入力し、 **[プロバイダー名]** が **[MDATP]** のフィルターを追加します。

## <a name="next-steps"></a>次のステップ
このドキュメントでは、Microsoft Defender for Endpoint を Azure Sentinel に接続する方法を学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](./tutorial-detect-threats-built-in.md)の概要。