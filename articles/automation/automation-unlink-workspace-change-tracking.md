---
title: 変更履歴とインベントリの Automation アカウントからワークスペースのリンクを解除する
description: この記事では、変更履歴とインベントリの Automation アカウントから Log Analytics ワークスペースのリンクを解除する方法について説明します。
services: automation
ms.date: 4/11/2019
ms.topic: conceptual
ms.custom: mvc
ms.openlocfilehash: 2be702ec6e820fe71dd8d2da7aa4cf831b52402e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/25/2020
ms.locfileid: "83828255"
---
# <a name="unlink-workspace-from-automation-account"></a>Automation アカウントからワークスペースのリンクを解除する

[変更履歴とインベントリ](change-tracking.md)の操作を有効にする際、Log Analytics ワークスペースに Automation アカウントを統合しないようにすることもできます。 この記事では、アカウントからワークスペースのリンクを解除する方法について説明します。

1. https://portal.azure.com で Azure にサインインします。

2. VM の Update Management を削除します。 「[変更履歴とインベントリから VM を削除する](automation-remove-vms-from-change-tracking.md)」を参照してください。

3. 変更履歴とインベントリに以前のバージョンの Azure SQL Monitoring が含まれている場合、機能のセットアップ時に、Automation の資産が作成されている可能性があります。それらの資産はご自身で削除する必要があります。 それらの資産を探して削除してください。

4. 自分の Automation アカウントを開いて、左側にある **[関連リソース]** の **[リンクされたワークスペース]** を選択します。

5. [ワークスペースのリンクを解除] ページ **[ワークスペースのリンクを解除]** をクリックして、プロンプトに応答します。

   ![[ワークスペースのリンクを解除] ページ](media/automation-unlink-workspace-change-tracking/automation-unlink-workspace-blade.png)。

6. Azure Automation によって Log Analytics ワークスペースとのリンクが解除されている間、メニューの **[通知]** で進行状況を追跡できます。

または、Automation アカウントと Log Analytics ワークスペースのリンクをワークスペース内から解除することもできます。

1. 自分のワークスペースで、 **[関連リソース]** の **[Automation アカウント]** を選択します。 
2. [Automation アカウント] ページで **[アカウントのリンク解除]** を選択します。

## <a name="next-steps"></a>次のステップ

* 変更履歴とインベントリの操作については、[変更履歴とインベントリの管理](change-tracking-file-contents.md)に関するページを参照してください。
* この機能に関する一般的な問題のトラブルシューティングについては、「[Change Tracking と Inventory に関する問題のトラブルシューティング](troubleshoot/change-tracking.md)」を参照してください。
