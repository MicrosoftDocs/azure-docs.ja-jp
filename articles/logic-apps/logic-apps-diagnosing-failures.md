---
title: ワークフローの問題のトラブルシューティングと診断
description: Azure Logic Apps でのワークフローの問題、エラー、および障害のトラブルシューティングと診断を行う方法について説明します
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 01/31/2020
ms.openlocfilehash: 1f83f13564a64a0d9d8a5e0144ca95af6a769d6c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "95995048"
---
# <a name="troubleshoot-and-diagnose-workflow-failures-in-azure-logic-apps"></a>Azure Logic Apps でのワークフローの問題のトラブルシューティングと診断

ロジック アプリでは、アプリの問題の診断とデバッグに役立つ情報が生成されます。 ロジック アプリを診断するには、Azure Portal でワークフローの各手順を確認します。 また、ランタイム デバッグのための手順をいくつかワークフローに追加することもできます。

<a name="check-trigger-history"></a>

## <a name="check-trigger-history"></a>トリガーの履歴を確認してください

各ロジックアプリの実行はトリガーの試行によって開始されるため、トリガーが起動しない場合は、次の手順を実行します。

1. トリガーの状態を確認するには、[トリガーの履歴を確認します](../logic-apps/monitor-logic-apps.md#review-trigger-history)。 トリガーの試行に関する詳細情報を表示するには、そのトリガーイベントを選択します。次に例を示します。

   ![トリガーステータスおよび履歴の表示](./media/logic-apps-diagnosing-failures/logic-app-trigger-history.png)

1. トリガー入力をチェックして、期待どおりに表示されることを確認します。 **入力リンク** で、**入力** ペインを表示するリンクを選択します。

   トリガー入力には、トリガーがワークフローの開始を要求するデータが含まれます。 これらの入力を確認すると、トリガー入力が正しいかどうか、およびワークフローを続行できるように条件が満たされたかどうかを判断するのに役立ちます。

   たとえば、ここにある`feedUrl`プロパティーのRSSフィード値が正しくありません:

   ![トリガー入力のエラーを確認する](./media/logic-apps-diagnosing-failures/review-trigger-inputs-for-errors.png)

1. トリガー出力がある場合は、それをチェックして、期待したとおりに表示されているか確認します。 **出力リンク** で、**出力** ペインを表示するリンクを選択します。

   トリガー出力には、トリガーがワークフローの次のステップに渡すデータが含まれます。 これらの出力を確認すると、ワークフローの次のステップに渡される値が正しい値か期待値かを判断するのに役立ちます。次に例を示します。

   ![トリガー出力のエラーを確認する](./media/logic-apps-diagnosing-failures/review-trigger-outputs-for-errors.png)

   > [!TIP]
   > 認識できないコンテンツが見つかった場合は、Azure Logic Apps の[さまざまなコンテンツタイプ](../logic-apps/logic-apps-content-type.md)について学習します。

<a name="check-runs-history"></a>

## <a name="check-runs-history"></a>実行履歴のチェック

トリガーがアイテムまたはイベントに対して起動するたびに、Logic Apps エンジンは、アイテムまたはイベントごとに個別のワークフローインスタンスを作成して実行します。 実行が失敗した場合は、次の手順に従って、ワークフローの各ステップのステータス、各手順の入力と出力など、実行中に発生した処理を確認します。

1. [実行履歴をチェックして](../logic-apps/monitor-logic-apps.md#review-runs-history)、ワークフローの実行状況をチェックします。 失敗した実行のすべての手順のステータスなど、失敗した実行の詳細を表示するには、失敗した実行を選択します。

   ![実行履歴の表示と失敗した実行の選択](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

1. 実行のすべての手順が表示されたら、最初の失敗した手順を展開します。

   ![最初の失敗した手順を展開する](./media/logic-apps-diagnosing-failures/logic-app-run-pane.png)

1. 失敗した手順の入力をチェックして、期待どおりに表示されるかどうかを確認します。

1. 特定の実行の手順ごとに詳細を確認します。 **[実行の履歴]** で、確認する実行を選択します。

   ![実行履歴を確認する](./media/logic-apps-diagnosing-failures/logic-app-runs-history.png)

   ![ロジック アプリの実行の詳細を表示する](./media/logic-apps-diagnosing-failures/logic-app-run-details.png)

1. 特定の手順の入出力と、エラー メッセージを確認するには、手順を選択します。これにより、シェイプを拡張され、詳細が表示されます。 次に例を示します。

   ![ステップの詳細を表示する](./media/logic-apps-diagnosing-failures/logic-app-run-details-expanded.png)

## <a name="perform-runtime-debugging"></a>ランタイム デバッグを実行する

デバッグに役立つように、ロジックアプリケーションのワークフローに診断手順を追加し、トリガーと実行履歴を確認できます。 たとえば、[Webhook Tester](https://webhook.site/) サービスを使用する手順を追加できます。これにより、HTTP 要求を調べて、正確なサイズ、シェイプ、および形式を特定できます。

1. [Webhook Tester](https://webhook.site/)サイトに移動し、生成された固有のURLをコピーします。

1. ロジックアプリで、HTTP POSTアクションとテストする本体コンテンツ (式や別の手順の出力など) を追加します。

1. Webhook TesterからHTTP POSTアクションにURLを貼り付けます。

1. Logic Apps エンジンから生成された要求がどのように形成されるかを確認するには、ロジックアプリを実行し、詳細については Webhook Tester サイトを再度参照してください。

## <a name="next-steps"></a>次のステップ

* [ロジック アプリを監視する](../logic-apps/monitor-logic-apps.md)
