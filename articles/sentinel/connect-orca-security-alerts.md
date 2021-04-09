---
title: Orca Security アラートを Azure Sentinel に接続する | Microsoft Docs
description: Orca Security アラート データを Azure Sentinel に接続して、ダッシュボードの表示、カスタム アラートの作成、および調査の改善を行う方法を説明します。
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/17/2020
ms.author: yelevin
ms.openlocfilehash: 18a50e3d06135f2c17b7e7c5b2969be33f408c62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100093082"
---
# <a name="connect-your-orca-security-alerts-to-azure-sentinel"></a>Orca Security アラートを Azure Sentinel に接続する 

> [!IMPORTANT]
> Azure Sentinel の Orca Security アラート コネクタは、現在パブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Orca セキュリティ アラート コネクタを使用すると、[Orca アラート](https://orca.security/)のセキュリティ ソリューション アラートを Azure Sentinel に簡単に取り込むことができます。これにより、アラートをブック内で表示し、これを利用してカスタム アラートを作成し、調査を向上させる目的で組み入れることができます。 Orca セキュリティ アラートと Azure Sentinel 間の統合には、REST API を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-orca-security-alerts"></a>Orca セキュリティ アラートを構成して接続する

Orca セキュリティ アラートでは、ログを統合して、Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Orca Security alerts]\(Orca セキュリティ アラート\)** を選択して、 **[Open connector page]\(コネクタ ページを開く\)** を選択します。

2. Orca プラットフォームから統合を完了するには、 https://orcasecurity.zendesk.com/hc/en-us/articles/360043941992-Azure-Sentinel-integration を参照してください。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、**OrcaAlerts_CL** の表の **CustomLogs** にある Log Analytics にデータが表示されます。
Log Analytics で Orca アラートに関連するスキーマを使用するには、`OrcaAlerts_CL` を検索します。

## <a name="validate-connectivity"></a>接続の検証
ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。 


## <a name="next-steps"></a>次のステップ
このドキュメントでは、Orca セキュリティ アラートを Azure Sentinel に接続する方法について学習しました。 Azure Sentinel の詳細については、次の記事をご覧ください。
- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。

