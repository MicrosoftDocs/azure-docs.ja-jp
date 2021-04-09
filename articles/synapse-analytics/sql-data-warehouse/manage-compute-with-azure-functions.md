---
title: チュートリアル:Azure Functions を使用してコンピューティングを管理する
description: Azure Functions を使用して Azure Synapse Analytics で専用 SQL プール (旧称 SQL DW) のコンピューティングを管理する方法。
services: synapse-analytics
author: julieMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql-dw
ms.date: 04/27/2018
ms.author: jrasnick
ms.reviewer: igorstan
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: f0731f0deaf46ec419cfe43037804e10f2b73fd4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "96448377"
---
# <a name="use-azure-functions-to-manage-compute-resources-for-your-dedicated-sql-pool-formerly-sql-dw-in-azure-synapse-analytics"></a>Azure Functions を使用して Azure Synapse Analytics の専用 SQL プール (旧称 SQL DW) のコンピューティング リソースを管理する

このチュートリアルでは、Azure Synapse Analytics で Azure Functions を使用し、専用 SQL プール (旧称 SQL DW) のコンピューティング リソースを管理します。

専用 SQL プール (旧称 SQL DW) で Azure Function App を使用するには、[サービス プリンシパル アカウント](../../active-directory/develop/howto-create-service-principal-portal.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json)を作成する必要があります。 サービス プリンシパル アカウントには、専用 SQL プール (旧称 SQL DW) インスタンスと同じサブスクリプションで共同作成者のアクセス権が必要です。

## <a name="deploy-timer-based-scaling-with-an-azure-resource-manager-template"></a>Azure Resource Manager テンプレートを使用してタイマーベースのスケーリングを展開する

このテンプレートを展開するには、次の情報が必要です。

- 専用 SQL プール (旧称 SQL DW) インスタンスが存在するリソース グループの名前
- 専用 SQL プール (旧称 SQL DW) インスタンスがあるサーバーの名前
- 専用 SQL プール (旧称 SQL DW) インスタンスの名前
- Azure Active Directory のテナント ID (ディレクトリ ID)
- サブスクリプション ID
- サービス プリンシパルのアプリケーション ID
- サービス プリンシパルのシークレット キー

以上の情報が揃ったら、次のテンプレートを展開します。

[![[Azure に配置する] というラベルの付いたボタンが示されている画像。](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FMicrosoft%2Fsql-data-warehouse-samples%2Fmaster%2Farm-templates%2FsqlDwTimerScaler%2Fazuredeploy.json)

テンプレートを展開すると、3 つのリソースが新たに追加されていることがわかります。無料の Azure App Service プランと、使用量ベースの Function App プラン、そして、ログと操作キューを担うストレージ アカウントです。 デプロイされた関数を実際の要件に合わせて変更する方法については、引き続き他のセクションもご覧ください。

## <a name="change-the-compute-level"></a>コンピューティング レベルを変更する

1. ご利用の Function App サービスに移動します。 テンプレートを既定値のままデプロイした場合、このサービスの名前は *DWOperations* になります。 Function App を開くと、Function App サービスに 5 つの関数がデプロイされていることがわかります。

   ![テンプレートでデプロイされる関数](./media/manage-compute-with-azure-functions/five-functions.png)

2. *DWScaleDownTrigger* または *DWScaleUpTrigger* のいすれかを選択して、スケールアップまたはスケールダウンします。 ドロップダウン メニューで [統合] を選択します。

   ![関数の統合を選択](./media/manage-compute-with-azure-functions/select-integrate.png)

3. この時点で表示される値は、 *%ScaleDownTime%* と *%ScaleUpTime%* のどちらかです。 これらの値は、スケジュールが [[アプリケーション設定]](../../azure-functions/functions-how-to-use-azure-function-app-settings.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) に定義された値に基づいていることを示します。 差し当たり、この値は無視してかまいません。以降の手順に基づき、必要な時刻に合わせてスケジュールを変更してください。

4. Azure Synapse Analytics のスケールアップ頻度を表す CRON 式をスケジュール領域に追加します。

   ![関数のスケジュールを変更](./media/manage-compute-with-azure-functions/change-schedule.png)

   `schedule` の値は、次の 6 個のフィールドが含まれる [CRON 式](https://en.wikipedia.org/wiki/Cron#CRON_expression)です。

   ```json
   {second} {minute} {hour} {day} {month} {day-of-week}
   ```

   たとえば、「*0 30 9 * * 1-5*」と入力した場合、毎平日の午前 9 時 30 分に実行されます。 詳細については、Azure Functions の[スケジュールの例](../../azure-functions/functions-bindings-timer.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json#example)を参照してください。

## <a name="change-the-time-of-the-scale-operation"></a>スケール操作の時間を変更する

1. ご利用の Function App サービスに移動します。 テンプレートを既定値のままデプロイした場合、このサービスの名前は *DWOperations* になります。 Function App を開くと、Function App サービスに 5 つの関数がデプロイされていることがわかります。

2. *DWScaleDownTrigger* または *DWScaleUpTrigger* のいすれかを選択して、コンピューティング値をスケールアップまたはスケールダウンします。 関数を選択すると、ウィンドウに *index.js* ファイルが表示されます。

   ![関数のトリガー コンピューティング レベルを変更](././media/manage-compute-with-azure-functions/index-js.png)

3. *ServiceLevelObjective* の値を目的のレベルに変更し、[保存] を選択します。 *ServiceLevelObjective* は、[統合] セクションで定義されたスケジュールに基づいてデータ ウェアハウス インスタンスをスケーリングする際の目標となるコンピューティング レベルです。

## <a name="use-pause-or-resume-instead-of-scale"></a>スケールではなく一時停止または再開を使用する

現在、既定で有効になっている関数は *DWScaleDownTrigger* と *DWScaleUpTrigger* です。 それらの代わりに一時停止と再開の機能を使用する場合は、*DWPauseTrigger* または *DWResumeTrigger* を有効にしてください。

1. [関数] ウィンドウに移動します。

   ![[関数] ウィンドウ](./media/manage-compute-with-azure-functions/functions-pane.png)

2. 有効にするトリガーに対応するスライド式のトグル ボタンを選択します。

3. それぞれのトリガーの *[統合]* タブに移動して、そのスケジュールを変更します。

   > [!NOTE]
   > スケーリング トリガーと一時停止/再開トリガーの機能上の違いは、キューに送信されるメッセージです。 詳細については、「[新しいトリガー関数を追加する](manage-compute-with-azure-functions.md#add-a-new-trigger-function)」を参照してください。

## <a name="add-a-new-trigger-function"></a>新しいトリガー関数を追加する

現在、テンプレートに含まれているスケール関数は 2 つだけです。 これらの関数を使用すると、1 日に実行できるスケールダウンとスケールアップはそれぞれ 1 回だけです。 1 日に複数回スケールダウンしたり、平日と週末とでスケーリングの動作に違いを設けたりするなど、さらに細かな制御が必要となる場合は、別途トリガーを追加する必要があります。

1. 新しく空の関数を作成します。 [関数] の横にある *+* ボタンを選択して、関数テンプレート ウィンドウを表示します。

   ![[関数アプリ] メニューを示すスクリーンショット。[関数] の横にあるプラス アイコンが選択されています。](./media/manage-compute-with-azure-functions/create-new-function.png)

2. [言語] から *[JavaScript]* を選択し、 *[TimerTrigger]* を選択します。

   ![新しい関数の作成](./media/manage-compute-with-azure-functions/timertrigger-js.png)

3. 関数に名前を付けてスケジュールを設定します。 この画像は、毎週土曜日の午前 0 時 (金曜日の深夜) に関数をトリガーするように設定する例です。

   ![土曜日にスケール ダウン](./media/manage-compute-with-azure-functions/scale-down-saturday.png)

4. *index.js* の内容を他のトリガー関数からコピーします。

   ![index js のコピー](././media/manage-compute-with-azure-functions/index-js.png)

5. 次のように operation 変数を目的の動作に設定します。

   ```JavaScript
   // Resume the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "ResumeDw"
   }

   // Pause the dedicated SQL pool (formerly SQL DW) instance
   var operation = {
       "operationType": "PauseDw"
   }

   // Scale the dedicated SQL pool (formerly SQL DW)l instance to DW600c
   var operation = {
       "operationType": "ScaleDw",
       "ServiceLevelObjective": "DW600c"
   }
   ```

## <a name="complex-scheduling"></a>複雑なスケジュール

このセクションでは、一時停止、再開、スケーリングの各機能に対して、より複雑なスケジュールを設定する方法を簡単に紹介します。

### <a name="example-1"></a>例 1

毎日午前 8 時に DW600c にスケールアップし、午後 8 時に DW200c にスケールダウンします。

| 機能  | スケジュール     | Operation                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW600c"}` |
| Function2 | 0 0 20 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-2"></a>例 2

毎日午前 8 時に DW1000c にスケールアップし、午後 4 時に DW600 にスケールダウンします。さらに、午後 10 時に DW200c にスケールダウンします。

| 機能  | スケジュール     | Operation                                |
| :-------- | :----------- | :--------------------------------------- |
| Function1 | 0 0 8 * * *  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Function2 | 0 0 16 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Function3 | 0 0 22 * * * | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW200c"}` |

### <a name="example-3"></a>例 3

平日の午前 8 時に DW1000c にスケールアップし、午後 4 時に 1 回 DW600c にスケールダウンします。 金曜日の午後 11 時に一時停止し、月曜朝の午前 7 時に再開します。

| 機能  | スケジュール       | Operation                                |
| :-------- | :------------- | :--------------------------------------- |
| Function1 | 0 0 8 * * 1-5  | `var operation = {"operationType": "ScaleDw",    "ServiceLevelObjective": "DW1000c"}` |
| Function2 | 0 0 16 * * 1-5 | `var operation = {"operationType": "ScaleDw", "ServiceLevelObjective": "DW600c"}` |
| Function3 | 0 0 23 * * 5   | `var operation = {"operationType": "PauseDw"}` |
| Function4 | 0 0 7 * * 1    | `var operation = {"operationType": "ResumeDw"}` |

## <a name="next-steps"></a>次のステップ

[タイマー トリガー](../../azure-functions/functions-create-scheduled-function.md?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json) Azure Functions について確認します。

専用 SQL プール (旧称 SQL DW) の[サンプル レポジトリ](https://github.com/Microsoft/sql-data-warehouse-samples)を参照してください。
