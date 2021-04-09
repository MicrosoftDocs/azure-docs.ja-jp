---
title: Azure Sentinel に Cisco Umbrella を接続する | Microsoft Docs
description: Cisco Umbrella データ コネクタを使用して Umbrella のデータを Azure Sentinel にプルする方法について説明します。 Umbrella のデータをブックに表示し、アラートを作成し、調査を改善します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 0001cad6-699c-4ca9-b66c-80c194e439a5
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2021
ms.author: yelevin
ms.openlocfilehash: 22796134dae5c345e3f915e47bc1300affb9f60e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "99566643"
---
# <a name="connect-your-cisco-umbrella-to-azure-sentinel"></a>Azure Sentinel に Cisco Umbrella を接続する

> [!IMPORTANT]
> Cisco Umbrella コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

Cisco Umbrella コネクタを使用すると、Azure Sentinel にすべての Cisco Umbrella セキュリティ ソリューションのログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 Cisco Umbrella と Azure Sentinel の統合には、REST API を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-cisco-umbrella"></a>Cisco Umbrella を構成して接続する

Cisco Umbrella では、ログを統合して、Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Cisco Umbrella (Preview)]\(Cisco Umbrella (プレビュー)\)** を選択し、 **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで説明されている手順に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、 **[CustomLogs]** セクションの **[ログ]** で、次のうち 1 つ以上のテーブルにデータが表示されます。
- `Cisco_Umbrella_dns_CL`
- `Cisco_Umbrella_proxy_CL`
- `Cisco_Umbrella_ip_CL`
- `Cisco_Umbrella_cloudfirewall_CL`

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Cisco Umbrella のデータを Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
