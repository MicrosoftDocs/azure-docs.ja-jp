---
title: Salesforce Service Cloud のデータを Azure Sentinel に接続する | Microsoft Docs
description: Salesforce Service Cloud データ コネクタを使用して、Salesforce のログを Azure Sentinel にプルする方法について説明します。 Salesforce データをブックに表示し、アラートを作成し、調査を改善します。
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
ms.openlocfilehash: 1efd91d92bac1bc1f39d82aaa0cc71daa0275f8e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "100570530"
---
# <a name="connect-your-salesforce-service-cloud-to-azure-sentinel"></a>Salesforce Service Cloud を Azure Sentinel に接続する

> [!IMPORTANT]
> Salesforce Service Cloud コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Salesforce Service Cloud ソリューションを Azure Sentinel に接続する方法について説明します。 Salesforce Service Cloud データ コネクタを使用すると、Salesforce のデータを Azure Sentinel に簡単に接続できます。これにより、そのデータをブックで表示したり、カスタム アラートの作成に利用したり、調査の改善のために取り込んだりできます。 Salesforce Service Cloud と Azure Sentinel 間の統合には、REST API を使用します。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。 ワークスペース キーの詳細については、[こちら](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)を参照してください。

- 関数アプリを作成するには、Azure Functions に対する読み取りと書き込みのアクセス許可が必要です。 [Azure Functions](../azure-functions/index.yml) の詳細を確認してください。

- 次の Salesforce REST API 資格情報が必要です。**Salesforce API ユーザー名**、**Salesforce API パスワード**、**Salesforce セキュリティ トークン**、**Salesforce コンシューマー キー**、**Salesforce コンシューマー シークレット**。 Salesforce REST API の詳細については、[こちら](https://developer.salesforce.com/docs/atlas.en-us.api_rest.meta/api_rest/quickstart.htm)を参照してください。

## <a name="configure-and-connect-salesforce-service-cloud"></a>Salesforce Service Cloud を構成して接続する

Salesforce Service Cloud では、ログを統合して、Azure Sentinel に直接エクスポートできます。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Salesforce Service Cloud (Preview)]\(Salesforce Service Cloud (プレビュー)\)** を選択し、 **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで説明されている手順に従います。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、`SalesforceServiceCloud_CL` テーブルの **[CustomLogs]** セクションの **[ログ]** にデータが表示されます。

便利なサンプル クエリについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="validate-connectivity"></a>接続の検証

ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Salesforce Service Cloud を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。