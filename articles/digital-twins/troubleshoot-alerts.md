---
title: アラートを設定する
titleSuffix: Azure Digital Twins
description: Azure Digital Twins メトリックでアラートを有効にする方法を確認します。
author: baanders
ms.author: baanders
ms.date: 7/28/2020
ms.topic: troubleshooting
ms.service: digital-twins
ms.openlocfilehash: ded2f54379e60e8e3fc26d9c2166930a6f953078
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854858"
---
# <a name="troubleshooting-azure-digital-twins-alerts"></a>Azure Digital Twins のトラブルシューティング: 警告

Azure Digital Twins では、リソースの状態に関する情報を提供する、サービス インスタンスの[メトリック](troubleshoot-metrics.md)が収集されます。 これらのメトリックを使用すると、Azure Digital Twins サービスやそれに接続されているリソースの全体的な正常性を評価できます。

**アラート**は、メトリック データで重要な状態が見つかると事前に通知します。 管理者は、その通知を見て、システムのユーザーが問題に気付く前に問題を識別して対処できます。 アラートについて詳しくは、[ *「Microsoft Azure のアラートの概要」* ](../azure-monitor/platform/alerts-overview.md)をご覧ください。

## <a name="turn-on-alerts"></a>アラートを有効にする

Azure Digital Twins インスタンスに対してアラートを有効にする方法は次のとおりです。

1. [Azure portal](https://portal.azure.com) にサインインし、Azure Digital Twins インスタンスに移動します。 その名前をポータルの検索バーに入力して、検索することができます。 

2. メニューから **[アラート]** を選択した後、 **[+ 新しいアラート ルール]** を選択します。

3. 続いて表示される *[アラート ルールの作成]* ページで、画面の指示に従って条件、トリガーされるアクション、アラートの詳細を定義できます。     
    * **[スコープ]** の詳細には、インスタンスの詳細が自動的に入力されます。
    * アラートのトリガーと応答をカスタマイズするには、**条件**と**アクション グループ**の詳細を定義します。
    * **[アラート ルールの詳細]** セクションで、 _[アラート ルール名]_ および _[説明] (省略可能)_ を入力します。 アラートが作成されるとすぐにアクティブになるようにするには、 _[作成時にアラート ルールを有効にする]_ チェックボックスをオンにします。
    * 追加したいくつかの条件とアクションに基づいて、各ドロップダウンから _[リソース グループにアラート ルールを保存します]_ と _[重要度]_ を選択する必要があります。

4. _[アラート ルールの作成]_ ボタンを選択して、アラート ルールを作成します。

:::image type="content" source="media/troubleshoot-alerts/create-alert-rule.png" alt-text="スコープ、条件、およびアクション グループのセクションが表示された [アラート ルールの作成] ページを示すスクリーンショット" lightbox="media/troubleshoot-alerts/create-alert-rule.png":::

:::image type="content" source="media/troubleshoot-alerts/alert-rule-details.png" alt-text="アラート ルールの作成に関するアラート ルールの詳細セクションを示すスクリーンショット" lightbox="media/troubleshoot-alerts/alert-rule-details.png":::

これらのフィールドへの入力方法のガイド付きチュートリアルについては、「[*Microsoft Azure のアラートの概要*](../azure-monitor/platform/alerts-overview.md)」をご覧ください。 以下に、Azure Digital Twins の手順の例をいくつか示します。

Azure Digital Twins で使用できるアラート シグナルの種類を示す*条件の選択*プロセスからの抜粋を次に示します。 このページでは、シグナルの種類をフィルター処理し、一覧から目的のシグナルを選択できます。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic.png" alt-text="最初の [シグナル ロジックの構成] ページを示すスクリーンショット。メトリックまたはアクティビティ ログを選択する [シグナルの種類] ボックスとその下の選択可能なメトリックの一覧が強調表示されています":::

シグナルを選択すると、アラートのロジックを構成するように求められます。 ディメンションをフィルター処理したり、アラートのしきい値を設定したり、条件をチェックする頻度を設定したりできます。 平均ルーティング エラー率メトリックが 5% を超えた場合のアラートの設定例を次に示します。

:::image type="content" source="media/troubleshoot-alerts/configure-signal-logic-2.png" alt-text="2 つ目の [シグナル ロジックの構成] ページを示すスクリーンショット。このページには、メトリックの履歴が表示され、Event Grid 操作などのディメンションをフィルター処理するための領域や、平均値は 5 より大きい などのアラート ロジックを定義するためのセクションがあります":::
 
アラートの設定後、それらがインスタンスの *[アラート]* ページに表示されます。
 
:::image type="content" source="media/troubleshoot-alerts/alerts-post.png" alt-text="[アラート] ページと追加するボタンを示すスクリーンショット。1 つのアラートが構成されています" lightbox="media/troubleshoot-alerts/alerts-post.png":::


## <a name="next-steps"></a>次の手順

* Azure Monitor でのアラートについて詳しくは、「[*Microsoft Azure のアラートの概要*](../azure-monitor/platform/alerts-overview.md)」を参照してください。
* Azure Digital Twins のメトリックについては、[*トラブルシューティング: Azure Monitor でメトリックを表示する方法*](troubleshoot-metrics.md)に関するページを参照してください。
* メトリックの診断ログを有効にする方法については、[*トラブルシューティング:診断の設定*](troubleshoot-diagnostics.md)に関するページを参照してください。
