---
title: Proofpoint Targeted Attack Protection (TAP) データを Azure Sentinel に接続する | Microsoft Docs
description: Proofpoint Targeted Attack Protection (TAP) データを Azure Sentinel に接続する方法について説明します。
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
ms.openlocfilehash: 86018bafaa42eac01e5dccf8da1d290b64e2475c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092980"
---
# <a name="connect-your-proofpoint-tap-to-azure-sentinel-with-azure-function"></a>Azure Functions を使用して Proofpoint TAP を Azure Sentinel に接続する

> [!IMPORTANT]
> Azure Sentinel の Proofpoint TAP データ コネクタは、現在パブリック プレビュー段階にあります。
> この機能はサービス レベル アグリーメントなしで提供されています。運用環境のワークロードに使用することはお勧めできません。 特定の機能はサポート対象ではなく、機能が制限されることがあります。 詳しくは、[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)に関するページをご覧ください。

Proofpoint Targeted Attack Protection (TAP) コネクタを使用すると、Azure Sentinel にすべての [Proofpoint TAP](https://www.proofpoint.com/us/products/advanced-threat-protection/targeted-attack-protection) セキュリティ ソリューションのログを簡単に接続して、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 Proofpoint TAP と Azure Sentinel の統合では、Azure Functions を使用して REST API によりログ データをプルします。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="configure-and-connect-proofpoint-tap"></a>Proofpoint TAP を構成して接続する

Azure Functions では、Proofpoint TAP から直接イベントとログを統合およびプルし、Azure Sentinel に転送できます。

1. Azure Sentinel ポータルで、 **[Data connectors]\(データ コネクタ\)** をクリックし、 **[Proofpoint TAP]** コネクタを選択します。

1. **[Open connector page]\(コネクタ ページを開く\)** を選択します。

1. **[Proofpoint TAP]** ページの指示に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、Log Analytics の **ProofpointTAPMessagesBlocked_CL**、**ProofpointTAPMessagesDelivered_CL**、**ProofpointTAPClicksPermitted_CL**、および **ProofpointTAPClicksBlocked_CL** テーブルの下にデータが表示されます。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Azure Functions アプリを使用して Proofpoint TAP を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
