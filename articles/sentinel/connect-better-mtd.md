---
title: BETTER Mobile Threat Defense (MTD) を Azure Sentinel に接続する | Microsoft Docs
description: BETTER Mobile Threat Defense (MTD) データ コネクタを使用して MTD のログを Azure Sentinel にプルする方法について説明します。 MTD のデータをブックに表示し、アラートを作成し、調査を改善します。
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
ms.date: 01/12/2021
ms.author: yelevin
ms.openlocfilehash: 4828e31b9d15f101740c158ee62c90c95673c9a7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "98541512"
---
# <a name="connect-your-better-mobile-threat-defense-mtd-to-azure-sentinel"></a>BETTER Mobile Threat Defense (MTD) を Azure Sentinel に接続する

> [!IMPORTANT]
> BETTER Mobile Threat Defense (MTD) コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

BETTER Mobile Threat Defense (MTD) コネクタを使用すると、お使いのBETTER MTD セキュリティ ソリューションのすべてのログを Azure Sentinel に簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 BETTER Mobile Threat Defense と Azure Sentinel の統合には、REST API を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-better-mobile-threat-defense"></a>BETTER Mobile Threat Defense を構成して接続する

BETTER MTD では、ログを統合して、Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[BETTER Mobile Threat Defense (MTD) (Preview)]\(BETTER Mobile Threat Defense (MTD) (プレビュー)\)** を選択し、 **[コネクタ ページを開く]** を選択します。

1. コネクタ ページ、および [BETTER MTD のドキュメントのこちらのページ](https://mtd-docs.bmobi.net/integrations/azure-sentinel/setup-integration#mtd-integration-configuration)の手順に従い、BETTER MTD コンソールで統合を完了します。

    **ワークスペース ID** と **主キー** の値を入力するよう要求されたら、それらを Azure Sentinel コネクタ ページからコピーし、BETTER MTD の構成に貼り付けます。

    :::image type="content" source="media/connectors/workspace-id-primary-key.png" alt-text="{ワークスペース ID と主キー}":::

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、 **[CustomLogs]** セクションの **[ログ]** で、次のうち 1 つ以上のテーブルにデータが表示されます。
- `BetterMTDDeviceLog_CL`
- `BetterMTDIncidentLog_CL`
- `BetterMTDAppLog_CL`
- `BetterMTDNetflowLog_CL`

分析ルール、ハンティング クエリ、または Azure Sentinel 内の他の任意の場所で BETTER MTD のログのクエリを実行するには、クエリ ウィンドウの上部で、上記のテーブル名のいずれかを入力します。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。 

## <a name="next-steps"></a>次のステップ

このドキュメントでは、BETTER Mobile Threat Defense (MTD) を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
