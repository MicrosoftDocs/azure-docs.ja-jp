---
title: Azure Pipelines と Azure Application Insights による DevOps リリース パイプラインの継続的監視 | Microsoft Docs
description: Application Insights で行う継続的な監視を迅速にセットアップする手順を説明します
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: fd7cd6a107ed45adb60167a57661b60be5dc8212
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/29/2021
ms.locfileid: "86517129"
---
# <a name="add-continuous-monitoring-to-your-release-pipeline"></a>リリース パイプラインに継続的監視を追加する

Azure Pipelines と Azure Application Insights を統合すると、ソフトウェア開発ライフ サイクルを通じて DevOps リリース パイプラインを継続的に監視できます。 

継続的な監視により、リリース パイプラインに Application Insights などの Azure リソースから監視データを組み込むことができます。 リリース パイプラインで Application Insights アラートが検出されると、アラートが解決されるまで、パイプラインによってデプロイがゲート処理またはロールバックされる可能性があります。 すべてのチェックが成功すると、展開はテストから実稼働まですべて自動で進められます。手動による介入は必要ありません。 

## <a name="configure-continuous-monitoring"></a>継続的監視を構成する

1. [Azure DevOps](https://dev.azure.com) で、組織とプロジェクトを選択します。
   
1. プロジェクト ページの左側のメニューで、 **[パイプライン]**  >  **[リリース]** を選択します。 
   
1. **[新規]** の横にあるドロップダウンの矢印を展開し、 **[新しいリリース パイプライン]** を選択します。 また、まだパイプラインがない場合は、表示されるページで **[新しいパイプライン]** を選択します。
   
1. **[テンプレートの選択]** ウィンドウで **[継続的監視を使用した Azure App Service の配置]** を探して選択し、 **[適用]** を選択します。 

   ![新しい Azure Pipelines のリリース パイプライン](media/continuous-monitoring/001.png)

1. **[ステージ 1]** ボックスで、 **[ステージ タスクを表示します]** のハイパーリンクを選択します。

   ![ステージ タスクの表示](media/continuous-monitoring/002.png)

1. **[ステージ 1]** 構成ウィンドウで、次のフィールドを入力します。 

    | パラメーター        | 値 |
   | ------------- |:-----|
   | **ステージ名**      | ステージ名を指定するか、 **[ステージ 1]** のままにします。 |
   | **Azure サブスクリプション** | ドロップダウンを展開して、使用するリンク済み Azure サブスクリプションを選択します。|
   | **アプリの種類** | ドロップダウンを展開して、アプリの種類を選択します。 |
   | **App Service の名前** | Azure App Service の名前を入力します。 |
   | **Application Insights 用のリソース グループ名**    | ドロップダウンを展開して、使用するリソース グループを選択します。 |
   | **Application Insights のリソース名** | ドロップダウンを展開して、選択したリソース グループの Application Insights リソースを選択します。

1. 既定のアラート ルール設定でパイプラインを保存するには、Azure DevOps ウィンドウの右上にある **[保存]** を選択します。 わかりやすいコメントを入力し、 **[OK]** を選択します。

## <a name="modify-alert-rules"></a>アラート ルールを変更する

すぐに使用できる **継続的監視を使用した Azure App Service の配置** テンプレートには、4 つのアラート ルールがあります。**可用性**、**失敗した要求**、**サーバー応答時間**、**サーバーの例外** です。 サービス レベルのニーズに合わせて、ルールを追加したり、ルール設定を変更したりすることができます。 

アラート ルール設定を変更するには:

リリース パイプライン ページの左側のウィンドウで、 **[Configure Application Insights Alerts]\(Application Insights アラートの構成\)** を選択します。

インライン スクリプトによって 4 つの既定のアラート ルールが作成されます。

```bash
$subscription = az account show --query "id";$subscription.Trim("`"");$resource="/subscriptions/$subscription/resourcegroups/"+"$(Parameters.AppInsightsResourceGroupName)"+"/providers/microsoft.insights/components/" + "$(Parameters.ApplicationInsightsResourceName)";
az monitor metrics alert create -n 'Availability_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg availabilityResults/availabilityPercentage < 99' --description "created from Azure DevOps";
az monitor metrics alert create -n 'FailedRequests_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count requests/failed > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerResponseTime_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'avg requests/duration > 5' --description "created from Azure DevOps";
az monitor metrics alert create -n 'ServerExceptions_$(Release.DefinitionName)' -g $(Parameters.AppInsightsResourceGroupName) --scopes $resource --condition 'count exceptions/server > 5' --description "created from Azure DevOps";
```

このスクリプトを変更してさらなるアラート ルールを追加したり、アラートの条件を変更したり、展開の目的に適していないアラート ルールを削除したりできます。

## <a name="add-deployment-conditions"></a>展開の条件を追加する

リリース パイプラインに配置ゲートを追加すると、設定したしきい値を超えるアラートによって、不要なリリース プロモーションが防止されます。 アラートを解決すると、配置は自動的に進められます。

配置ゲートを追加するには:

1. メイン パイプライン ページの **[ステージ]** で、どのステージで連続監視ゲートが必要かに応じて、 **[配置前条件]** または **[配置後の条件]** シンボルを選択します。
   
   ![配置前条件](media/continuous-monitoring/004.png)
   
1. **[配置前条件]** 構成ウィンドウで、 **[ゲート]** を **[有効]** に設定します。
   
1. **[ゲートの配置]** の横にある **[追加]** を選択します。
   
1. ドロップダウン メニューから **[Azure Monitor アラートのクエリ]** を選択します。 このオプションを使用すると、Azure Monitor と Application Insights の両方のアラートにアクセスできます。
   
   ![Azure Monitor アラートのクエリ](media/continuous-monitoring/005.png)
   
1. **[評価のオプション]** で、 **[ゲートの再評価までの時間]** や **[ゲートが失敗するまでのタイムアウト]** など、設定に必要な値を入力します。 

## <a name="view-release-logs"></a>リリース ログを表示する

リリース ログで、配置ゲートの動作やその他のリリース手順を確認できます。 ログを開くには:

1. パイプライン ページの左側のメニューから **[リリース]** を選択します。 
   
1. 任意のリリースを選択します。 
   
1. **[ステージ]** で、ステージを選択してリリースの概要を表示します。 
   
1. ログを表示するには、リリースの概要で **[ログの表示]** を選択するか、任意のステージで **[成功]** または **[失敗]** のハイパーリンクを選択するか、任意のステージにカーソルを合わせて **[ログ]** を選択します。 
   
   ![リリース ログを表示する](media/continuous-monitoring/006.png)

## <a name="next-steps"></a>次のステップ

Azure Pipelines の詳細については、[Azure Pipelines のドキュメント](/azure/devops/pipelines)を参照してください。
