---
title: Proofpoint On Demand Email Security のデータを Azure Sentinel に接続する | Microsoft Docs
description: Proofpoint On Demand Email Security データ コネクタを使用して、POD Email Security のログを Azure Sentinel にプルする方法について説明します。 POD Email Security のデータをブックに表示し、アラートを作成し、調査を改善します。
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
ms.date: 01/17/2021
ms.author: yelevin
ms.openlocfilehash: 73a9d9c7ab321aebd615922e5d4395c0318e809c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100580444"
---
# <a name="connect-your-proofpoint-on-demand-email-security-pod-solution-to-azure-sentinel"></a>Proofpoint On Demand Email Security (POD) ソリューションを Azure Sentinel に接続する

> [!IMPORTANT]
> Proofpoint On Demand Email Security コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Proofpoint On Demand Email Security アプライアンスを Azure Sentinel に接続する方法について説明します。 POD データ コネクタを使用すると、POD のログを Azure Sentinel に簡単に接続できます。これにより、そのデータをブックで表示したり、カスタム アラートの作成に利用したり、調査の改善のために取り込んだりできます。  Proofpoint On Demand Email Security と Azure Sentinel の統合では、Websocket API を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。 ワークスペース キーの詳細については、[こちら](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)を参照してください。

- 関数アプリを作成するには、Azure Functions に対する読み取りと書き込みのアクセス許可が必要です。 [Azure Functions](../azure-functions/index.yml) の詳細を確認してください。

- 次の Websocket API 資格情報が必要です。ProofpointClusterID、ProofpointToken。 Websocket API の詳細については、[こちら](https://proofpointcommunities.force.com/community/s/article/Proofpoint-on-Demand-Pod-Log-API)を参照してください。

## <a name="configure-and-connect-proofpoint-on-demand-email-security"></a>Proofpoint On Demand Email Security を構成して接続する

Proofpoint On Demand Email Security では、ログを統合して、Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Proofpoint On Demand Email Security (Preview)]\(Proofpoint On Demand Email Security (プレビュー)\)** を選択し、 **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで説明されている手順に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、次のテーブルの *[カスタム ログ]* の下にある **[ログ]** にデータが表示されます。
- `ProofpointPOD_message_CL`
- `ProofpointPOD_maillog_CL`

便利なサンプル クエリについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="validate-connectivity"></a>接続の検証

Log Analytics でログの表示が開始されるまで、最大 60 分かかる場合があります。

## <a name="next-steps"></a>次の手順

このドキュメントでは、Proofpoint On Demand Email Security を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。