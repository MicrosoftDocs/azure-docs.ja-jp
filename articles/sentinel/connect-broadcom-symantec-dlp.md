---
title: Azure Sentinel に Broadcom Symantec データ損失防止 (DLP) データを接続する | Microsoft Docs
description: Broadcom Symantec DLP データ コネクタを使用して、Symantec DLP のログを Azure Sentinel にプルする方法について説明します。 Symantec DLP のデータをブックに表示し、アラートを作成し、調査を改善します。
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
ms.date: 03/02/2021
ms.author: yelevin
ms.openlocfilehash: 7f89780f2ed440898f5a28d78ec541c48a958b90
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/20/2021
ms.locfileid: "101743564"
---
# <a name="connect-your-broadcom-symantec-data-loss-prevention-dlp-to-azure-sentinel"></a>Azure Sentinel に Broadcom Symantec データ損失防止 (DLP) を接続する

> [!IMPORTANT]
> Broadcom Symantec DLP コネクタは、現在 **プレビュー** 段階です。 ベータ版、プレビュー版、または一般提供としてまだリリースされていない Azure の機能に適用されるその他の法律条項については、「[Microsoft Azure プレビューの追加使用条件](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)」を参照してください。

この記事では、Azure Sentinel に Broadcom Symantec DLP アプライアンスを接続する方法について説明します。 Broadcom Symantec DLP データ コネクタを使用すると、Azure Sentinel に Symantec DLP のログを簡単に接続でき、ダッシュボードの表示、カスタム アラートの作成、調査の改善を行うことができます。 この機能より、組織の情報、その情報の経路をより詳しく分析でき、セキュリティ運用機能が改善されます。 Broadcom Symantec DLP と Azure Sentinel の統合には、CEF 形式の Syslog、Linux ベースのログ フォワーダー、Log Analytics エージェントを使用します。 また、Kusto 関数に基づくカスタムビルドのログ パーサーも使用されます。

> [!NOTE]
> データは、Azure Sentinel を実行しているワークスペースの地理的な場所に格納されます。

## <a name="prerequisites"></a>前提条件

- Azure Sentinel ワークスペースに対する読み取りおよび書き込みアクセス許可が必要です。

- ワークスペースの共有キーに対する読み取りアクセス許可が必要です。 ワークスペース キーの詳細については、[こちら](../azure-monitor/platform/log-analytics-agent.md#workspace-id-and-key)を参照してください。

## <a name="send-broadcom-symantec-dlp-logs-to-azure-sentinel"></a>Broadcom Symantec DLP のログを Azure Sentinel に送信する

ログを Azure Sentinel に送るには、Linux ベースのログ転送サーバー (rsyslog または syslog-ng を実行) に Syslog メッセージを CEF 形式で送信するように Broadcom Symantec DLP アプライアンスを構成します。 このサーバーには Log Analytics エージェントをインストールします。このエージェントで、ログをお使いの Azure Sentinel ワークスペースに転送します。

1. Azure Sentinel のナビゲーション メニューで、 **[データ コネクタ]** を選択します。

1. **[データ コネクタ]** ギャラリーから、 **[Broadcom Symantec DLP (Preview)]\(Broadcom Symantec DLP (プレビュー)\)** コネクタを選択し、 **[コネクタ ページを開く]** を選択します。

1. **[手順]** タブの **[構成]** の手順に従います。

    1. **[1. Linux Syslog agent configuration]\(Linux Syslog エージェントの構成\)** - ログ フォワーダーをまだ実行していない場合、または別のものが必要な場合は、この手順を実行します。 詳細な手順および説明については、Azure Sentinel のドキュメントの「[手順 1: ログ フォワーダーをデプロイする](connect-cef-agent.md)」を参照してください。

    1. **[2. Forward Symantec DLP logs to a Syslog agent]\(2. Symantec DLP のログを Syslog エージェントに転送する\)** - Broadcom の指示に従い [Symantec DLP を構成](https://help.symantec.com/cs/DLP15.7/DLP/v27591174_v133697641/Configuring-the-Log-to-a-Syslog-Server-action?locale=EN_US)します。 この構成には、次の要素を含める必要があります。
        - ログの送信先 – お使いのログ転送サーバーのホスト名、IP アドレス、またはこれらの両方
        - プロトコルとポート – TCP 514 (これ以外が推奨されている場合は、お使いのログ転送サーバーの syslog デーモンで同様の変更を行ってください)
        - ログの形式 – CEF
        - ログの種類 – 使用可能なすべてのもの、または該当するすべてのもの

    1. **3. Validate connection\(接続を検証する\)** - コネクタ ページのコマンドをコピーし、ログ フォワーダーでそれを実行することで、データの取り込みを検証します。 詳細な手順および説明については、Azure Sentinel のドキュメントの「[手順 3: 接続を検証する](connect-cef-verify.md)」を参照してください。

        ログが Log Analytics に表示され始めるまで、最大 20 分かかることがあります。

## <a name="find-your-data"></a>データの検索

接続が正常に確立されると、 *[CommonSecurityLog]* テーブルにある **[Azure Sentinel]** セクションの **[ログ]** にデータが表示されます。

このデータ コネクタは、Kusto 関数に基づくパーサーに依存して正常に動作します。 [こちらの手順に従って](https://aka.ms/sentinel-symantecdlp-parser)、**SymantecDLP** Kusto 関数のエイリアスを作成します。

その後、Log Analytics で Broadcom Symantec DLP のデータを照会するには、クエリ ウィンドウの上部に「`SymantecDLP`」と入力します。

いくつかの便利なクエリ サンプルについては、コネクタ ページの **[Next steps]\(次の手順\)** タブを参照してください。

## <a name="next-steps"></a>次のステップ

このドキュメントでは、Symantec DLP を Azure Sentinel に接続する方法について説明しました。 Azure Sentinel の詳細については、次の記事をご覧ください。

- [データと潜在的な脅威を可視化](quickstart-get-visibility.md)する方法についての説明。
- [Azure Sentinel を使用した脅威の検出](tutorial-detect-threats-built-in.md)の概要。
- [ブックを使用](tutorial-monitor-your-data.md)してデータを監視する。
