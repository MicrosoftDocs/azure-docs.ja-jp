---
title: Azure Sentinel に Google Workspace (G Suite) データを接続する | Microsoft Docs
description: Google Workspace (G Suite) データ コネクタを使用して、Google Workspace アクティビティ イベントを Azure Sentinel に取り込む方法について説明します。 Google Workspace データをブックに表示し、アラートを作成し、調査を改善します。
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
ms.date: 02/28/2021
ms.author: yelevin
ms.openlocfilehash: 4ada570502d913283ba9ee4cc4c65b7bdd853935
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743657"
---
# <a name="connect-your-google-workspace-to-azure-sentinel"></a>Azure Sentinel に Google Workspace を接続する

> [!IMPORTANT]
> Google Workspace コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

[Google Workspace (旧称 G Suite)](https://workspace.google.com/) データ コネクタは、REST API を通じて Azure Sentinel に Google Workspace アクティビティ イベントを取り込む機能を提供します。 コネクタを使用すると、これらの[イベント](https://developers.google.com/admin-sdk/reports/reference/rest/v1/activities)を SOC で可視化できます。これにより、潜在的なセキュリティ リスクの調査、チームによるコラボレーションの使用の分析、構成の問題の診断、いつ誰がサインインしたかについての追跡、管理者のアクティビティの分析、ユーザーがコンテンツを作成および共有する方法についての理解、組織内の、より多くのイベントの確認が可能になります。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

Google Workspace のデータを収集するには、次のアクセス許可と資格情報が必要です。

- Azure Sentinel ワークスペースに対する読み取りと書き込みのアクセス許可。

- ワークスペースの共有キーに対する読み取りアクセス許可。 ワークスペース キーの詳細については、[こちら](../azure-monitor/agents/log-analytics-agent.md#workspace-id-and-key)を参照してください。

- 関数アプリを作成するための、Azure Functions に対する読み取りと書き込みのアクセス許可。 [Azure Functions](../azure-functions/index.yml) の詳細を確認してください。

- REST API には、**GooglePickleString** 資格情報が必要です。 Google REST API の詳細については、[こちら](https://developers.google.com/admin-sdk/reports/v1/reference/activities)を参照してください。 資格情報を取得する方法については、[こちら](https://developers.google.com/admin-sdk/reports/v1/quickstart/python)を参照してください。

## <a name="configure-and-connect-google-workspace"></a>Google Workspace を構成して接続する

Google Workspace では、Azure 関数アプリを使用してログを統合し、Azure Sentinel に直接エクスポートできます。

> [!NOTE]
> このコネクタは、Azure Functions を使用して Google Reports API に接続し、アクティビティ イベントを Azure Sentinel にプルします。 これにより、追加のデータ インジェスト コストが発生する可能性があります。 詳細については、「[Azure Functions の価格](https://azure.microsoft.com/pricing/details/functions/)」ページをご覧ください。

1. Azure Sentinel ポータルで **[Data connectors]\(データ コネクタ\)** をクリックします。 

1. コネクタ ギャラリーから **[Google Workspace (G Suite) (Preview)]\(Google Workspace (G Suite) (プレビュー)\)** を選択し、 **[コネクタ ページを開く]** を選択します。

1. コネクタ ページの **[構成]** セクションで説明されている手順に従います。

## <a name="validate-connectivity-and-find-your-data"></a>接続を検証してデータを検索する

Azure Sentinel に取り込まれたデータを表示できるようになるまで、最大で 20 分かかる場合があります。

接続が正常に確立されると、次のテーブルの **[カスタム ログ]** セクションの下にある **[ログ]** にデータが表示されます。
- `GWorkspace_ReportsAPI_admin_CL`
- `GWorkspace_ReportsAPI_calendar_CL`
- `GWorkspace_ReportsAPI_drive_CL`
- `GWorkspace_ReportsAPI_login_CL`
- `GWorkspace_ReportsAPI_mobile_CL`
- `GWorkspace_ReportsAPI_token_CL`
- `GWorkspace_ReportsAPI_user_accounts_CL`

このデータ コネクタは、Kusto 関数に基づくパーサーに依存して正常に動作します。 [こちらの手順に従い](https://aka.ms/sentinel-GWorkspaceReportsAPI-parser)、**GWorkspaceActivityReports** Kusto 関数のエイリアスを作成します。

いくつかの便利なサンプル クエリについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Google Workspace を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
